<template>
	<transition name="fade">
		<div v-if="show" class="modal">
			<div class="modal__backdrop" @click="closeModal()" />

			<div class="modal__dialog">
				<div class="modal__header">
					<div class="flex" style="display: flex; width: 100%">
						<h1 style="margin: auto">{{ title }}</h1>
					</div>
					<div class="modal__close" @click="closeModal()">
						<i class="fas fa-times pointer hov-color-accent-1"></i>
					</div>
				</div>

				<div class="modal__body">
					<slot name="body" />
				</div>

				<div class="modal__footer">
					<slot name="footer" />
				</div>
			</div>
		</div>
	</transition>
</template>
<script>
export default {
	data() {
		return {
			show: false,
			title: '',
			store: {},
		}
	},
	methods: {
		closeModal() {
			this.show = false
			document.querySelector('body').classList.remove('overflow-hidden')
		},
		openModal(title = undefined) {
			if (title) {
				this.setTitle(title)
			}
			this.show = true
			document.querySelector('body').classList.add('overflow-hidden')
		},
		setTitle(title) {
			this.title = title
		},
		setItem(key, val) {
			this.store[key] = val
		},
		getItem(key) {
			return this.store[key]
		},
	},
}
</script>
<style scoped>
.modal {
	overflow-x: hidden;
	overflow-y: auto;
	position: fixed;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	z-index: 9;
}
.modal__backdrop {
	background-color: rgba(0, 0, 0, 0.3);
	position: fixed;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	z-index: 1;
}
.modal__dialog {
	background-color: var(--color-primary-1);
	position: relative;
	width: 600px;
	margin: 50px auto;
	display: flex;
	flex-direction: column;
	border-radius: 5px;
	z-index: 2;
}
@media screen and (max-width: 992px) {
	.modal__dialog {
		width: 90%;
	}
}
.modal__close {
	width: 10px !important;
	height: 10px !important;
}
.modal__header {
	padding: 20px 20px 10px;
	display: flex;
	align-items: flex-start;
	justify-content: space-between;
}
.modal__body {
	padding: 10px 20px 10px;
	overflow: auto;
	display: flex;
	flex-direction: column;
	align-items: stretch;
}
.modal__footer {
	padding: 10px 20px 20px;
}
.fade-enter-active,
.fade-leave-active {
	transition: opacity 0.2s;
}
.fade-enter,
.fade-leave-to {
	opacity: 0;
}
</style>
