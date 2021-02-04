<!-- eslint-disable -->
<template>
	<div v-if="$fetchState.pending" class="container m-auto min-h-50vh flex flex-align-center">
		<Loader />
	</div>
	<div v-else class="container m-auto">
		<h1 class="text-align-center">{{ list.name }}</h1>
		<div>
			<li
				v-for="item in list.cart_items"
				:key="item.id"
				class="font-s-20 no-dash flex flex-space-between"
			>
				<label class="checkbox grow">
					<input v-if="item.checked" type="checkbox" checked />
					<input v-else type="checkbox" />
					<span></span>
					<span :class="{ 'line-through': item.checked }">
						{{ item.amount }} * {{ item.name }}
					</span>
				</label>
				<i class="fas fa-trash-alt pointer hov-color-accent-1 m-auto"></i>
			</li>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			list: null,
		}
	},
	async fetch() {
		const req = await fetch(`http://api.local.fr/shopping/${this.$route.params.id}`, {
			credentials: 'include',
		})
		if (!req.ok) {
			location.replace('/shopping')
			return
		}
		this.list = await req.json()
	},
	methods: {
		test: (e) => {},
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
