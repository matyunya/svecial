<script>

	// Documentation available here: https://discordapp.com/developers/docs/topics/oauth2
	// Using: https://github.com/DreamingBot/Login-With-Discord-DB

	import { onMount } from 'svelte';

	export let appId;
	export let scopes = 'identify'; // Seperated with a comma
	export let redirect = '/';
	export let color = 'dark';
	export let onLoginFailure = () => {console.log('Logged to discord with failure!')};
	export let onLoginSuccess = () => {console.log('Logged to discord with success!')};

	let data = {};
	let clazz = `discord-signin-button button is-${color}`;

	onMount(async () => {
		scopes = scopes.toLocaleLowerCase().replace(/\s/gmui, '').split(',');
		const script = document.createElement('script');
		const fontawesome = document.createElement('script');
		const bulma = document.createElement('link');

		script.src = 'https://cdn.jsdelivr.net/gh/dusterthefirst/Login-With-Discord/dist/lwd.js';
		fontawesome.src = 'https://use.fontawesome.com/releases/v5.3.1/js/all.js';
		bulma.href = 'https://unpkg.com/bulmaswatch/materia/bulmaswatch.min.css';
		bulma.rel = "stylesheet";
		bulma.type = 'text/css';

		document.getElementsByTagName('head')[0].appendChild(bulma);
		document.getElementsByTagName('head')[0].appendChild(script);
		document.getElementsByTagName('head')[0].appendChild(fontawesome);
	})

	async function login() {
		let discord = new LoginWithDiscord({
			clientID: appId,
			scopes,
			redirect_url: redirect
		});
		discord.init();
		discord.login()
			.then(async () => {
				if (scopes.includes('identify' || 'email')) {
					data = {...data, user: await discord.fetchUser()}
				}
				if (scopes.includes('connections')) {
					data = {...data, connections: await discord.fetchConnections()}
				}
				if (scopes.includes('guilds')) {
					data = {...data, guilds: await discord.fetchGuilds()}
				}
				onLoginSuccess(data);
			})
			.catch(err => onLoginFailure(err));
	}
	
</script>

<style>
	.is-black { background-color: black; color: white; }
	.is-light { background-color: white }
	.is-pink { background-color: pink }
	.is-yellow { background-color: yellow }
	.is-aqua { background-color: aqua }
	.is-lightblue { background-color: lightblue }
	.is-lightgreen { background-color: lightgreen }
	.is-brown { background-color: brown; color: white; }
	.is-blue { background-color: blue; color: white; }
	.is-gray { background-color: gray; color: white; }
	.is-green { background-color: green; color: white; }
	.is-purple { background-color: purple; color: white; }
</style>

<a href="#!" class={clazz} on:click={login}>
	<span class='icon'>
		<i class='fab fa-discord fa-lg'></i>
    </span>
    <span>Connexion</span>
</a>