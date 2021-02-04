<template>
	<div class="article">
		<h2 class="title">{{ about }}</h2>
		<div class="content font-s-20">{{ content }}</div>
		<nuxt-link :to="link" class="article-btn color-accent-1 u-animation-1 font-s-20">
			<i class="fas fa-arrow-right"></i> {{ linkText }}
		</nuxt-link>
	</div>
</template>
<script>
export default {
	props: {
		about: {
			type: String,
			default: 'Example',
		},
		content: {
			type: String,
			default: 'Sample content, it may be a little long. Yes indeed',
		},
		link: {
			type: String,
			default: '#',
		},
		linkText: {
			type: String,
			default: 'Super lien',
		},
	},
}
</script>
