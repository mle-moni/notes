<template>
	<div>
		<h1>Connection status: {{ user.connected ? 'connected' : 'disconnected' }}</h1>
	</div>
</template>

<script>
export default {
	data() {
		return {
			user: 'Please wait...',
		}
	},
	async fetch() {
		this.user = await fetch('http://api.local.fr/connected', {
			credentials: 'include',
		}).then((res) => res.json())
	},
	// on appelle fetch uniquement du côté client
	fetchOnServer: false,
}
</script>

<style>
/** Sample `apply` at-rules with Tailwind CSS
.container {
@apply min-h-screen flex justify-center items-center text-center mx-auto;
}

* Sample appear animation
.NuxtLogo {
	animation: 1s appear;
	margin: auto;
}

@keyframes appear {
	0% {
		opacity: 0;
	}
}
*/
</style>
