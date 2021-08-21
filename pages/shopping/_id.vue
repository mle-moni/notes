<template>
	<div v-if="$fetchState.pending" class="container m-auto min-h-50vh flex flex-align-center">
		<Loader />
	</div>
	<div v-else class="container m-auto">
		<h1 class="text-align-center">{{ list.name }}</h1>
		<Modal ref="createItemModal">
			<template #body>
				<form
					id="new-item-form"
					class="form container-50"
					autocomplete="off"
					@submit.prevent="createItem"
				>
					<p class="text-align-center font-s-20 color-error none"></p>
					<div class="form-field">
						<label for="item_name"> Objet </label>
						<input class="new-item-name" type="text" name="item_name" autofocus />
						<label for="item_amount"> Quantité </label>
						<input
							class="new-item-amount"
							type="number"
							name="item_amount"
							value="1"
							min="1"
						/>
						<input type="number" class="new-item-list-id" name="item_list_id" hidden />
					</div>
					<br />
					<div class="flex flex-space-around">
						<button type="submit">Ajouter</button>
					</div>
				</form>
			</template>
		</Modal>
		<div class="flex flex-column">
			<i
				class="fas fa-plus pointer hov-color-accent-1 text-align-center font-s-30 p-auto"
				@click="$refs.createItemModal.openModal('Ajouter un objet :')"
			></i>
			<li
				v-for="item in sortedItems"
				:key="item.id"
				class="font-s-20 no-dash flex flex-space-between"
			>
				<label class="checkbox grow">
					<input
						v-if="item.checked"
						type="checkbox"
						checked
						:data-item="JSON.stringify(item)"
						@change="toggleItem"
					/>
					<input
						v-else
						type="checkbox"
						:data-item="JSON.stringify(item)"
						@change="toggleItem"
					/>
					<span></span>
					<span
						v-if="item.amount === 1"
						:id="`item-${list.id}-${item.id}`"
						:class="{ 'line-through': item.checked }"
					>
						{{ item.name }}
					</span>
					<span
						v-else
						:id="`item-${list.id}-${item.id}`"
						:class="{ 'line-through': item.checked }"
					>
						{{ item.amount }} * {{ item.name }}
					</span>
				</label>
				<i class="fas fa-trash-alt pointer hov-color-accent-1 m-auto" @click="deleteItem">
					<span class="none data" :data-item="JSON.stringify(item)"></span>
				</i>
			</li>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			list: null,
			items: [],
		}
	},
	computed: {
		sortedItems: function () {
			return this.items.sort((a, b) => +a.checked - +b.checked)
		},
	},
	async fetch() {
		const req = await fetch(`http://api.mle-moni.fr/shopping-lists/${this.$route.params.id}`, {
			credentials: 'include',
		})
		if (!req.ok) {
			location.replace('/shopping')
			return
		}
		this.list = await req.json()
		this.items = this.list.cart_items
	},
	methods: {
		async deleteItem(e) {
			const item = JSON.parse(
				e.target.getElementsByClassName('data')[0].getAttribute('data-item')
			)
			const req = await fetch(`http://api.mle-moni.fr/cart-items/${item.id}?_method=DELETE`, {
				credentials: 'include',
				method: 'POST',
			})
			if (!req.ok) {
				return
			}
			for (let i = 0; i < this.items.length; i++) {
				if (this.items[i].id === item.id) {
					this.items.splice(i, 1)
					break
				}
			}
		},
		async toggleItem(e) {
			const item = JSON.parse(e.target.getAttribute('data-item'))
			const req = await fetch(`http://api.mle-moni.fr/cart-items/${item.id}?_method=PATCH`, {
				credentials: 'include',
				method: 'POST',
			})
			if (!req.ok) {
				return
			}
			document
				.getElementById(`item-${this.list.id}-${item.id}`)
				.classList.toggle('line-through')
		},
		async createItem() {
			const form = document.getElementById('new-item-form')
			form.getElementsByClassName('new-item-list-id')[0].value = `${this.$route.params.id}`
			const error = form.getElementsByClassName('color-error')[0]
			const req = await fetch(`http://api.mle-moni.fr/cart-items`, {
				credentials: 'include',
				method: 'POST',
				body: new FormData(form),
			})
			if (!req.ok) {
				error.classList.remove('none')
				if (req.status === 400) {
					error.textContent = `Attention, la quantité doit être un nombre, le nom ne peut pas être vide ni être trop grand.`
				} else {
					error.textContent = `Erreur lors de l'envoi du formulaire, le serveur a repondu : ${req.statusText} (${req.status})`
				}
				return
			}
			this.items.push(await req.json())
			this.$refs.createItemModal.closeModal()
		},
	},
	fetchOnServer: false,
}
</script>
<style scoped>
.min-h-50vh {
	min-height: 50vh;
}
.min-w-50vw {
	min-width: 50vw;
}
</style>
