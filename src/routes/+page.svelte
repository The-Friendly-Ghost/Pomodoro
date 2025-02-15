<script>
	import { onMount } from 'svelte';
	import Pomodoro from '$components/Pomodoro.svelte';

	let notificationPermission = $state('default');

	onMount(async () => {
		if ('Notification' in window) {
			notificationPermission = await Notification.requestPermission();
		}
	});

	const notifyUser = function (title, message) {
		if (notificationPermission === 'granted' && document.hidden) {
			new Notification(title, {
				body: message
			});
		}
	};
</script>

<Pomodoro />
