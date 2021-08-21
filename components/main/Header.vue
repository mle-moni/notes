<template>
	<nav id="main-navbar">
		<div class="container-90 m-auto">
			<div class="nav-logo">
				<nuxt-link to="/">
					mle-moni [<span class="italic color-accent-1">notes</span>]
				</nuxt-link>
			</div>

			<div id="mobileNav" class="nav-menu mobile-menu-closed">
				<div class="mobile">
					<div v-if="user.connected" class="link-container">
						<a class="u-animation-2" href="#"> Votre compte </a>
						<a
							class="u-animation-2"
							href="http://api.mle-moni.fr/logout?redir=http://notes.local.fr/"
						>
							Se déconnecter
						</a>
					</div>
					<div v-else class="link-container">
						<a
							class="u-animation-2"
							href="http://api.mle-moni.fr/login?redir=http://notes.local.fr/"
						>
							Se connecter
						</a>
						<a
							class="u-animation-2"
							href="http://api.mle-moni.fr/register?redir=http://notes.local.fr/"
						>
							Créer un compte
						</a>
					</div>
				</div>
			</div>
			<svg
				class="hamburger"
				viewBox="0 0 100 100"
				width="80"
				onclick="this.classList.toggle('active');document.getElementById('mobileNav').classList.toggle('mobile-menu-closed')"
			>
				<path
					class="hamburger-line top"
					d="m 30,33 h 40 c 0,0 9.044436,-0.654587 9.044436,-8.508902 0,-7.854315 -8.024349,-11.958003 -14.89975,-10.85914 -6.875401,1.098863 -13.637059,4.171617 -13.637059,16.368042 v 40"
				/>
				<path class="hamburger-line middle" d="m 30,50 h 40" />
				<path
					class="hamburger-line bottom"
					d="m 30,67 h 40 c 12.796276,0 15.357889,-11.717785 15.357889,-26.851538 0,-15.133752 -4.786586,-27.274118 -16.667516,-27.274118 -11.88093,0 -18.499247,6.994427 -18.435284,17.125656 l 0.252538,40"
				/>
			</svg>
		</div>
	</nav>
</template>

<script>
export default {
	data() {
		return {
			user: { connected: false },
		}
	},
	async fetch() {
		this.user = await fetch('http://api.mle-moni.fr/user-info', {
			credentials: 'include',
		}).then((res) => res.json())
		localStorage.setItem('user', JSON.stringify(this.user))
	},
	fetchOnServer: false,
}
</script>
