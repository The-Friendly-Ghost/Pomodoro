<script lang="ts">
	import '../app.css';
	let { children } = $props();
</script>

<header class="mb-10 flex flex-col items-center justify-center gap-5 p-5">
	<h1 class="text-4xl font-bold text-red-600">Just another pomodoro timer 🍅</h1>
</header>

{@render children()}

<footer class="mt-10 flex flex-col items-center justify-center gap-2 p-5 text-slate-600">
	<small>Made with Svelte by</small>
	<a href="https://github.com/The-Friendly-Ghost/Pomodoro">
		<small>https://github.com/the-friendly-ghost</small>
	</a>
</footer>
