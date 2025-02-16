<script lang="ts">
	// Props
	interface Props {
		askPermission: () => void;
		notificationPermission: string;
	}
	let { askPermission, notificationPermission }: Props = $props();

	// Toggle state
	let toggled = $derived(notificationPermission === 'granted' ? true : false);

	function handleClick(event: Event) {
		event.preventDefault();
		if (!toggled) {
			askPermission();
		}
	}
</script>

<section class="mt-10 flex flex-col items-center justify-center gap-2">
	<small class="text-slate-600">Notifications</small>
	<div class="switch-container">
		<input
			class="toggle-checkbox"
			id="toggle-switch"
			type="checkbox"
			checked={toggled}
			onclick={handleClick}
		/>
		<label class="switch" for="toggle-switch">
			<div class="toggle">
				<div class="led"></div>
			</div>
		</label>
	</div>
</section>

<style>
	/* Switch Container */
	.switch-container {
		position: relative;
		width: 130px;
		height: 40px;
		background: #e0e0e0;
		border-radius: 50px;
		box-shadow:
			inset -8px -8px 16px #ffffff,
			inset 8px 8px 16px #bebebe;
	}

	/* Hidden Checkbox */
	.toggle-checkbox {
		display: none;
	}

	/* Switch Label (Interactive Area) */
	.switch {
		position: absolute;
		top: 50%;
		left: 0;
		width: 100%;
		height: 100%;
		transform: translateY(-50%);
		border-radius: 50px;
		overflow: hidden;
		cursor: pointer;
	}

	/* Toggle Button */
	.toggle {
		position: absolute;
		width: 60px;
		height: 30px;
		background: #e8e8e8;
		border-radius: 50px;
		top: 5px;
		left: 5px;
		box-shadow:
			-4px -4px 8px #ffffff,
			4px 4px 8px #bebebe;
		transition: all 0.3s ease-in-out;
		display: flex;
		align-items: center;
		justify-content: flex-start;
		padding-left: 10px;
	}

	/* LED Light */
	.led {
		width: 10px;
		height: 10px;
		background: #bebebe;
		border-radius: 50%;
		transition: all 0.3s ease-in-out;
	}

	/* Active State for Toggle (ON) */
	.toggle-checkbox:checked + .switch .toggle {
		left: 65px;
	}

	.toggle-checkbox:checked + .switch .led {
		background: oklch(0.723 0.419 149.579);
		box-shadow:
			0 0 0px #22c55e,
			0 0 5px #22c55e,
			0 0 30px #22c55e;
	}

	/* Hover Effect */
	.switch:hover .toggle {
		box-shadow:
			-4px -4px 12px #ffffff,
			4px 4px 12px #bebebe;
	}
</style>
