<script lang="ts">
	import Pomodoro from '$components/Pomodoro.svelte';
	import ToggleNotifications from '$components/ToggleNotifications.svelte';

	let notificationPermission: string = $state('default');

	async function askPermission() {
		if ('Notification' in window) {
			notificationPermission = await Notification.requestPermission();
		}
	}

	const notifyUser = function (title: string, message: string) {
		if (notificationPermission === 'granted' && document.hidden) {
			const notification = new Notification(title, {
				body: message,
				requireInteraction: true
			});

			notification.onclick = function (event) {
				event.preventDefault();
				window.focus();
				this.close();
			};
		}
	};
</script>

<Pomodoro {notifyUser} />
<ToggleNotifications {askPermission} {notificationPermission} />
