<script lang="ts">
	import { browser } from '$app/environment';

	if (browser) {
		let deferredPrompt: any;
		const addBtn = document.querySelector('.add-button') as HTMLButtonElement;
		addBtn.style.display = 'none';

		if ('serviceWorker' in navigator) {
			navigator.serviceWorker.register('./sw.js');
		}

		window.addEventListener('beforeinstallprompt', (e) => {
			// Prevent Chrome 67 and earlier from automatically showing the prompt
			e.preventDefault();
			// Stash the event so it can be triggered later.
			deferredPrompt = e;
			// Update UI to notify the user they can add to home screen
			addBtn.style.display = 'block';

			addBtn.addEventListener('click', (e) => {
				// hide our user interface that shows our A2HS button
				addBtn.style.display = 'none';
				// Show the prompt
				deferredPrompt.prompt();
				// Wait for the user to respond to the prompt
				deferredPrompt.userChoice.then((choiceResult: { outcome: string }) => {
					if (choiceResult.outcome === 'accepted') {
						console.log('User accepted the A2HS prompt');
					} else {
						console.log('User dismissed the A2HS prompt');
					}
					deferredPrompt = null;
				});
			});
		});
	}
</script>

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
<button class="add-button">Add to home screen</button>

<style lang="scss">
	.add-button {
		position: absolute;
		top: 1px;
		left: 1px;
	}
</style>
