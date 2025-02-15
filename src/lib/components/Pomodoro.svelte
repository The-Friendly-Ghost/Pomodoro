<script>
	import pauseIcon from '$assets/pause.svg';
	import startIcon from '$assets/play.svg';
	import restartIcon from '$assets/restart.svg';

	const POMODORO_TIME = 25 * 60 * 1000;
	const SHORT_BREAK_TIME = 5 * 60 * 1000;
	const LONG_BREAK_TIME = 15 * 60 * 1000;
	let name = $state('Start working');

	// == Time variables
	const formatter = new Intl.DateTimeFormat('en', {
		hour12: false,
		minute: '2-digit',
		second: '2-digit'
	});
	let elapsed = $state(0);
	let timeLeft = $derived(POMODORO_TIME - elapsed);
	let timeStr = $derived(formatter.format(timeLeft));
	let fraction = $derived(timeLeft / POMODORO_TIME);
	let timerInterval = $state(null);
	// == End of Time variables
	let isRunning = $state(false);

	function handleStart() {
		if (timerInterval || !timeLeft) return; // Don't start if already running
		isRunning = true;
		name = 'Working';
		startTimer();
	}

	function handlePause() {
		isRunning = false;
		clearInterval(timerInterval);
		timerInterval = null;
		!timeLeft ? (name = 'Take a break!') : (name = 'Paused');
	}

	function resetTimer() {
		handlePause(); // Stop the timer first
		elapsed = 0;
		name = 'Start working';
	}

	function startTimer() {
		if (!isRunning) return;

		timerInterval = setInterval(() => {
			elapsed += 1000; // Increment by 1 second (1000ms)

			if (timeLeft <= 0) {
				// Timer completed
				handlePause();
			}
		}, 1000);
	}
</script>

<section class="items-middle flex justify-center">
	<div class="pomodoro-outer">
		<div class="name-input text-xl font-bold">
			{name}
		</div>
		<div
			class="timer-outer"
			class:timer-active={isRunning && timeLeft > 0}
			class:timer-paused={!isRunning && timeLeft && elapsed}
			class:timer-finished={!timeLeft}
			style="--progress: {fraction * 360}deg"
		>
			<div class="timer-inner">
				<p class="text-3xl font-bold">{timeStr}</p>
			</div>
		</div>
		<div class="buttons-container">
			<div class="flex flex-col items-center gap-4">
				<button onclick={isRunning ? handlePause : handleStart} aria-label="Pause Timer"
					><img src={isRunning ? pauseIcon : startIcon} alt="Pause icon" /></button
				>
				<div class=" h-2 w-2 rounded-full bg-[#bebebe] transition" class:glow={isRunning}></div>
			</div>
			<button onclick={resetTimer} aria-label="Restart Timer"
				><img src={restartIcon} alt="Restart icon" /></button
			>
		</div>
	</div>
</section>

<style>
	.buttons-container {
		display: flex;
		justify-content: space-between;
	}

	button {
		display: flex;
		justify-content: center;
		align-items: center;
		color: #090909;
		width: 50px;
		height: 50px;
		border-radius: 100%;
		background: #e8e8e8;
		cursor: pointer;
		border: 1px solid #e8e8e8;
		transition: all 0.3s;
		box-shadow:
			6px 6px 12px #c5c5c5,
			-6px -6px 12px #ffffff;
	}

	button:active {
		color: #666;
		box-shadow:
			inset 4px 4px 12px #c5c5c5,
			inset -4px -4px 12px #ffffff;
	}

	.glow {
		background: oklch(0.723 0.219 149.579);
		box-shadow:
			0 0 10px #22c55e,
			0 0 20px #22c55e,
			0 0 30px #22c55e;
	}

	.timer-outer {
		position: relative;
		height: 180px;
		width: 180px;
		border-radius: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		text-align: center;
		background: #cacaca;
		overflow: hidden;
		box-shadow: inset 0px 0px 8px #53535334;
		transition: background 0.1s ease;
	}

	.timer-outer::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		transition: background 0.1s ease;
	}

	.timer-active {
		background: rgb(0, 228, 0);
	}

	.timer-active::before {
		background: conic-gradient(transparent var(--progress), #cacaca var(--progress));
	}

	.timer-paused {
		background: rgb(255, 171, 44);
		box-shadow: inset 0 0 8px rgba(126, 126, 126, 0.527);
	}

	.timer-paused::before {
		background: conic-gradient(transparent var(--progress), #cacaca var(--progress));
	}

	.timer-finished {
		background: rgb(255, 44, 44);
		box-shadow: inset 0 0 8px rgba(126, 126, 126, 0.527);
	}

	.timer-inner {
		position: relative;
		height: 80%;
		width: 80%;
		background-color: #e0e0e0;
		border-radius: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		text-align: center;
		background: linear-gradient(145deg, #e0e0e0, #f0f0f0);
		box-shadow: 0px 0px 10px 3px #72727242;
		z-index: 1;
	}

	.pomodoro-outer {
		position: relative;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		width: 280px;
		max-width: 100%;
		height: 450px;
		padding: 50px;
		border-radius: 50px;
		background: #e0e0e0;
		box-shadow:
			20px 20px 60px #bebebe,
			-20px -20px 60px #ffffff;
	}

	.name-input {
		display: flex;
		justify-content: center;
		align-items: center;
		overflow: hidden;
		width: 100%;
		height: 50px;
		border-radius: 30px;
		background: lightgrey;
		box-shadow:
			rgba(50, 50, 93, 0.25) 0px 30px 50px -12px inset,
			rgba(0, 0, 0, 0.3) 0px 18px 26px -18px inset;
	}
</style>
