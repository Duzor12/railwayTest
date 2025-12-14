<script lang="ts">
	import { onMount } from 'svelte';

	let canvas: HTMLCanvasElement;
	let width = $state(0);
	let height = $state(0);

	onMount(() => {
		const ctx = canvas.getContext('2d');
		if (!ctx) return;

		width = window.innerWidth;
		height = window.innerHeight;

		const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789アァカサタナハマヤャラワガザダバパイィキシチニヒミリヰギジヂビピウゥクスツヌフムユュルグズブヅプエェケセテネヘメレヱゲゼデベペオォコソトノホモヨョロヲゴゾドボポヴッン';
		const fontSize = 16;
		const columns = Math.floor(width / fontSize);
		const drops: number[] = new Array(columns).fill(1);

		const draw = () => {
			// Black BG for the canvas
			// Translucent BG to show trail
			ctx.fillStyle = 'rgba(0, 0, 0, 0.05)';
			ctx.fillRect(0, 0, width, height);

			ctx.fillStyle = '#0F0'; // Green text
			ctx.font = `${fontSize}px monospace`;

			for (let i = 0; i < drops.length; i++) {
				const text = characters.charAt(Math.floor(Math.random() * characters.length));
				ctx.fillText(text, i * fontSize, drops[i] * fontSize);

				// Reset drop to top randomly after it has crossed screen
				if (drops[i] * fontSize > height && Math.random() > 0.975) {
					drops[i] = 0;
				}

				drops[i]++;
			}
		};

		const interval = setInterval(draw, 33);

		const handleResize = () => {
			width = window.innerWidth;
			height = window.innerHeight;
			// Recalculate columns on resize
			const newColumns = Math.floor(width / fontSize);
			// Preserve existing drops if possible, or extend
			if (newColumns > drops.length) {
				const added = new Array(newColumns - drops.length).fill(1);
				drops.push(...added);
			} else if (newColumns < drops.length) {
				drops.length = newColumns;
			}
		};

		window.addEventListener('resize', handleResize);

		return () => {
			clearInterval(interval);
			window.removeEventListener('resize', handleResize);
		};
	});
</script>

<svelte:head>
	<title>Railway Matrix</title>
</svelte:head>

<div class="container">
	<canvas
		bind:this={canvas}
		{width}
		{height}
	></canvas>
	<div class="overlay">
		<h1>SYSTEM ONLINE</h1>
		<p>Running on Railway</p>
	</div>
</div>

<style>
	:global(body) {
		margin: 0;
		padding: 0;
		overflow: hidden;
		background: black;
		font-family: 'Courier New', Courier, monospace;
	}

	.container {
		position: relative;
		width: 100vw;
		height: 100vh;
	}

	canvas {
		display: block;
	}

	.overlay {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		color: #0f0;
		text-align: center;
		background: rgba(0, 0, 0, 0.7);
		padding: 2rem;
		border: 1px solid #0f0;
		box-shadow: 0 0 20px #0f0;
		text-shadow: 0 0 10px #0f0;
		z-index: 10;
		backdrop-filter: blur(2px);
	}

	h1 {
		margin: 0;
		font-size: 3rem;
		letter-spacing: 0.2rem;
		animation: pulse 2s infinite;
	}

	p {
		margin-top: 1rem;
		font-size: 1.2rem;
		opacity: 0.8;
	}

	@keyframes pulse {
		0% { opacity: 1; text-shadow: 0 0 10px #0f0; }
		50% { opacity: 0.8; text-shadow: 0 0 20px #0f0; }
		100% { opacity: 1; text-shadow: 0 0 10px #0f0; }
	}
</style>
