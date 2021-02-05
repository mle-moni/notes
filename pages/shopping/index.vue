<template>
	<div v-if="$fetchState.pending" class="container m-auto min-h-50vh flex flex-align-center">
		<Loader />
	</div>
	<div v-else-if="$fetchState.error">
		<h1 class="text-align-center">Veuillez vous connecter</h1>
		<div class="flex flex-space-around">
			<a
				class="u-animation-2 font-s-20"
				href="http://api.local.fr/login?redir=http://notes.local.fr/shopping"
			>
				Se connecter
			</a>
			<a
				class="u-animation-2 font-s-20"
				href="http://api.local.fr/register?redir=http://notes.local.fr/shopping"
			>
				Créer un compte
			</a>
		</div>
	</div>
	<div v-else class="container m-auto flex flex-column">
		<h1 class="text-align-center">Mes listes de course</h1>
		<Modal ref="createListModal">
			<template #body>
				<form
					id="new-list-form"
					class="form container-50"
					autocomplete="off"
					@submit.prevent="createList"
				>
					<p class="text-align-center font-s-20 color-error none"></p>
					<div class="form-field">
						<label for="list_name"> Nom de la liste </label>
						<input class="new-list-name" type="text" name="list_name" autofocus />
					</div>
					<br />
					<div class="flex flex-space-around">
						<button type="submit">Créer</button>
					</div>
				</form>
			</template>
		</Modal>
		<Modal ref="deleteListModal">
			<template #body>
				<div class="flex flex-space-around">
					<button @click="deleteList">Supprimer ?</button>
				</div>
			</template>
		</Modal>
		<Modal ref="editListModal">
			<template #body>
				<form
					id="edit-list-form"
					class="form container-50"
					autocomplete="off"
					@submit.prevent="editList"
				>
					<p class="text-align-center font-s-20 color-error none"></p>
					<div class="form-field">
						<label for="list_name"> Nouveau nom </label>
						<input class="new-list-name" type="text" name="list_name" autofocus />
					</div>
					<br />
					<div class="flex flex-space-around">
						<button type="submit">Mettre à jour</button>
					</div>
				</form>
			</template>
		</Modal>
		<i
			class="fas fa-plus pointer hov-color-accent-1 text-align-center font-s-30"
			@click="$refs.createListModal.openModal('Nouvelle liste :')"
		></i>
		<div
			v-for="list in lists"
			id="shop-list-link-container"
			:key="list.id"
			class="flex flex-space-between"
		>
			<div class="grow">
				<nuxt-link
					:to="`/shopping/${list.id}`"
					class="m-20 color-accent-1 u-animation-1 font-s-20"
					>{{ list.name }}</nuxt-link
				>
				<span class="grow"></span>
			</div>
			<i class="fas fa-edit pointer hov-color-accent-1 m-auto p-10" @click="showEditModal">
				<span class="data" :data-list="JSON.stringify(list)"></span>
			</i>
			<i
				class="fas fa-trash-alt pointer hov-color-accent-1 m-auto p-10"
				@click="showDeleteModal"
			>
				<span class="data" :data-list="JSON.stringify(list)"></span>
			</i>
		</div>
	</div>
</template>
<script>
export default {
	data() {
		return {
			lists: [],
		}
	},
	async fetch() {
		const req = await fetch('http://api.local.fr/shopping-lists', {
			credentials: 'include',
		})
		if (!req.ok) {
			throw new Error('Erreur lors du fetch')
		}
		this.lists = await req.json()
	},
	methods: {
		async createList() {
			const form = document.getElementById('new-list-form')
			const error = form.getElementsByClassName('color-error')[0]
			const req = await fetch('http://api.local.fr/shopping-lists', {
				credentials: 'include',
				method: 'POST',
				body: new FormData(form),
			})
			if (!req.ok) {
				error.classList.remove('none')
				if (req.status === 400) {
					error.textContent = `Le nom doit faire entre 1 et 20 lettres`
				} else {
					error.textContent = `Erreur lors de l'envoi du formulaire, le serveur a repondu : ${req.statusText} (${req.status})`
				}
				return
			}
			this.lists.push(await req.json())
			this.$refs.createListModal.closeModal()
		},
		showDeleteModal(e) {
			const list = JSON.parse(
				e.target.getElementsByClassName('data')[0].getAttribute('data-list')
			)
			this.$refs.deleteListModal.openModal(list.name)
			this.$refs.deleteListModal.setItem('id', list.id)
		},
		async deleteList(e) {
			const id = this.$refs.deleteListModal.getItem('id')
			const req = await fetch(`http://api.local.fr/shopping-lists/${id}?_method=DELETE`, {
				credentials: 'include',
				method: 'POST',
			})
			if (!req.ok) {
				return
			}
			for (let i = 0; i < this.lists.length; i++) {
				if (this.lists[i].id === id) {
					this.lists.splice(i, 1)
					break
				}
			}
			this.$refs.deleteListModal.closeModal()
		},
		showEditModal(e) {
			const list = JSON.parse(
				e.target.getElementsByClassName('data')[0].getAttribute('data-list')
			)
			this.$refs.editListModal.openModal(list.name)
			this.$refs.editListModal.setItem('id', list.id)
		},
		async editList() {
			const id = this.$refs.editListModal.getItem('id')
			const form = document.getElementById('edit-list-form')
			const error = form.getElementsByClassName('color-error')[0]
			const req = await fetch(`http://api.local.fr/shopping-lists/${id}?_method=PATCH`, {
				credentials: 'include',
				method: 'POST',
				body: new FormData(form),
			})
			if (!req.ok) {
				error.classList.remove('none')
				if (req.status === 400) {
					error.textContent = `Le nom doit faire entre 1 et 20 lettres`
				} else {
					error.textContent = `Erreur lors de l'envoi du formulaire, le serveur a repondu : ${req.statusText} (${req.status})`
				}
				return
			}
			const list = await req.json()
			for (let i = 0; i < this.lists.length; i++) {
				if (this.lists[i].id === list.id) {
					this.lists[i].name = list.name
					break
				}
			}
			this.$refs.editListModal.closeModal()
		},
	},
	fetchOnServer: false,
}
</script>
