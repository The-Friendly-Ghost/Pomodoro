<script lang="ts">
	// Imports
	import pauseIcon from '$assets/pause.svg';
	import startIcon from '$assets/play.svg';
	import ffIcon from '$assets/fast-forward.svg';

	// Constants
	const POMODORO_TIME: number = 25 * 60 * 1000;
	const SHORT_BREAK_TIME: number = 5 * 60 * 1000;
	const LONG_BREAK_TIME: number = 15 * 60 * 1000;

	// Props
	interface Props {
		notifyUser: (title: string, message: string) => void;
	}
	let { notifyUser }: Props = $props();

	// Date Time Variable
	const formatter: Intl.DateTimeFormat = new Intl.DateTimeFormat('en', {
		hour12: false,
		minute: '2-digit',
		second: '2-digit'
	});

	// State Variables
	let isWorkMode: boolean = $state(true);
	let currentBreak: number = $state(0);
	let elapsed: number = $state(0);
	let timerInterval: number | undefined = $state(undefined);
	let isRunning: boolean = $state(false);

	// Derived values
	let currentPeriod: number = $derived(
		isWorkMode ? POMODORO_TIME : currentBreak === 3 ? LONG_BREAK_TIME : SHORT_BREAK_TIME
	);
	let timeLeft: number = $derived(currentPeriod - elapsed);
	let timeStr: string = $derived(formatter.format(timeLeft));
	let fraction: number = $derived(timeLeft / currentPeriod);

	// Tab title text
	$effect(() => {
		const mode = isWorkMode ? 'Work' : 'Break';
		if (isRunning) {
			document.title = `🟢 ${timeStr} - ${mode}`;
		} else if (!isRunning && elapsed) {
			document.title = `🟠 ${timeStr} - ${mode}`;
		} else {
			document.title = `🔴 Start ${mode}`;
		}
	});

	// functions
	function handleStart() {
		if (timerInterval || !timeLeft) return;
		isRunning = true;
		startTimer();
	}

	function handlePause() {
		isRunning = false;
		clearInterval(timerInterval);
		timerInterval = undefined;
	}

	function handleNext() {
		handlePause();
		switchMode();
	}

	function switchMode() {
		elapsed = 0;
		if (isWorkMode) {
			isWorkMode = false;
		} else {
			isWorkMode = true;
			currentBreak = (currentBreak + 1) % 4;
		}
	}

	function startTimer() {
		if (!isRunning) return;

		timerInterval = setInterval(() => {
			elapsed += 1000;

			if (timeLeft <= 0) {
				handlePause();
				isWorkMode
					? notifyUser('Work finished', 'Click here to start your break')
					: notifyUser('Break finished', 'Click here to start working');
				switchMode();
			}
		}, 1000);
	}
</script>

<section class="items-middle flex justify-center">
	<div class="pomodoro-outer">
		<div class="modes-container">
			<div id="mode-work" class="mode">
				<p class="mode-text" class:active-mode={isWorkMode}>Work</p>
				<div class="mode-line" class:active-line={isWorkMode}></div>
			</div>
			<div id="mode-break" class="mode">
				<p class="mode-text" class:active-mode={!isWorkMode}>Break</p>
				<div class="flex gap-1">
					{#each Array(4) as _, i}
						<div
							class="mode-line"
							class:active-break={!isWorkMode && i === currentBreak}
							class:completed-break={!isWorkMode && i < currentBreak}
							class:big-break={!isWorkMode && currentBreak === 3 && i === 3}
						></div>
					{/each}
				</div>
			</div>
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
			<button onclick={handleStart} aria-label="Start Timer"
				><img src={startIcon} alt="Start icon" />
				<div
					class=" h-2 w-2 rounded-full bg-[#bebebe] transition"
					class:glow={isRunning}
					class:blue-flicker={!isRunning && elapsed === 0}
				></div></button
			>

			<button onclick={handlePause} aria-label="Pause Timer"
				><img src={pauseIcon} alt="Pause icon" />
				<div
					class=" h-2 w-2 rounded-full bg-[#bebebe] transition"
					class:glow={!isRunning && elapsed}
				></div></button
			>

			<button onclick={handleNext} aria-label="Next Timer"
				><img src={ffIcon} alt="Restart icon" /></button
			>
		</div>
	</div>
</section>

<style>
	/* Container Styles */
	.pomodoro-outer {
		position: relative;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		align-items: center;
		width: 300px;
		max-width: 100%;
		height: 450px;
		padding: 50px;
		border-radius: 50px;
		background: #e0e0e0;
		box-shadow:
			20px 20px 60px #bebebe,
			-20px -20px 60px #ffffff;
	}

	/* Mode Indicator Styles */
	.modes-container {
		display: flex;
		justify-content: space-between;
		width: 100%;
		gap: 20px;
	}

	.mode {
		width: 100%;
		text-align: center;
		font-weight: bold;
		font-size: x-large;
		color: #bebebe;
	}

	.mode-line {
		height: 3px;
		width: 100%;
		background-color: #bebebe;
		border-radius: 50px;
		transition: all 0.3s ease;
	}

	/* Active States */
	.active-mode {
		color: oklch(0.723 0.419 149.579);
		text-shadow: 0 10px 80px #22c55e;
	}

	.active-line,
	.active-break,
	.completed-break {
		background-color: oklch(0.723 0.419 149.579);
		box-shadow:
			0 0 0px #22c55e,
			0 0 1px #22c55e,
			0 0 80px #22c55e;
	}

	.big-break {
		background-color: oklch(0.623 0.619 199.579);
	}

	/* Timer Styles */
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
		transition: background 0.3s ease;
	}

	.timer-outer::before {
		content: '';
		position: absolute;
		inset: 0;
		transition: background 0.1s ease;
	}

	.timer-inner {
		position: relative;
		height: 80%;
		width: 80%;
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

	/* Timer States */

	.timer-active {
		background: oklch(0.723 0.419 149.579);
	}

	.timer-active::before {
		background: conic-gradient(transparent var(--progress), #cacaca var(--progress));
	}

	.timer-paused {
		background: rgb(255, 171, 44);
	}

	.timer-paused::before {
		background: conic-gradient(transparent var(--progress), #cacaca var(--progress));
	}

	.timer-finished {
		background: rgb(255, 44, 44);
	}

	.timer-paused,
	.timer-finished {
		box-shadow: inset 0 0 8px rgba(126, 126, 126, 0.527);
	}

	/* Button Styles */
	.buttons-container {
		display: flex;
		justify-content: space-between;
		width: 100%;
	}

	button {
		display: flex;
		justify-content: center;
		gap: 5px;
		align-items: center;
		width: 60px;
		height: 40px;
		border-radius: 15px;
		background: #e8e8e8;
		border: 1px solid #e8e8e8;
		cursor: pointer;
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

	.blue-flicker {
		animation: flicker 1s infinite step-end;
	}

	@keyframes flicker {
		0%,
		100% {
			background: #bebebe;
			box-shadow: none;
		}
		50% {
			background: oklch(0.623 0.619 199.579);
			box-shadow:
				0 0 0px oklch(0.623 0.619 199.579),
				0 0 5px oklch(0.623 0.619 199.579),
				0 0 30px oklch(0.623 0.619 199.579);
		}
	}

	/* Glow Effect */
	.glow {
		background: oklch(0.723 0.419 149.579);
		box-shadow:
			0 0 0px #22c55e,
			0 0 5px #22c55e,
			0 0 30px #22c55e;
	}
</style>
