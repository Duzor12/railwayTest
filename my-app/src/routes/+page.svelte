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

		const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789<>{}[];/\\|=+-_*&^%$#@!CURSOR_AI_ENABLED';
		const fontSize = 14;
		const columns = Math.floor(width / fontSize);
		const drops: number[] = new Array(columns).fill(1);

		const draw = () => {
			// Black BG for the canvas
			// Translucent BG to show trail
			ctx.fillStyle = 'rgba(0, 0, 0, 0.05)';
			ctx.fillRect(0, 0, width, height);

			ctx.fillStyle = '#0ff'; // Cyan text
			ctx.font = `${fontSize}px 'JetBrains Mono', monospace`;

			for (let i = 0; i < drops.length; i++) {
				const text = characters.charAt(Math.floor(Math.random() * characters.length));
				// Randomly make some characters brighter
				if (Math.random() > 0.95) {
					ctx.fillStyle = '#fff';
				} else {
					ctx.fillStyle = '#0ff';
				}
				
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
	<title>Cursor AI Demo</title>
	<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&display=swap" rel="stylesheet">
</svelte:head>

<div class="container">
	<canvas
		bind:this={canvas}
		{width}
		{height}
	></canvas>
	<div class="overlay">
		<h1 class="glitch" data-text="CURSOR :: ONLINE">CURSOR :: ONLINE</h1>
		<p class="typing">AI-Powered Development Environment</p>
	</div>
</div>

<style>
	:global(body) {
		margin: 0;
		padding: 0;
		overflow: hidden;
		background: black;
		font-family: 'JetBrains Mono', 'Courier New', monospace;
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
		color: #0ff;
		text-align: center;
		background: rgba(0, 0, 10, 0.85);
		padding: 3rem;
		border: 1px solid #0ff;
		box-shadow: 0 0 30px rgba(0, 255, 255, 0.3);
		z-index: 10;
		backdrop-filter: blur(4px);
		border-radius: 4px;
	}

	h1 {
		margin: 0;
		font-size: 4rem;
		letter-spacing: 0.2rem;
		position: relative;
		color: #fff;
		text-shadow: 2px 2px #0ff;
	}

	/* Glitch Effect */
	.glitch {
		position: relative;
	}
	
	.glitch::before,
	.glitch::after {
		content: attr(data-text);
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
	}

	.glitch::before {
		left: 2px;
		text-shadow: -1px 0 #f0f;
		clip: rect(44px, 450px, 56px, 0);
		animation: glitch-anim 5s infinite linear alternate-reverse;
	}

	.glitch::after {
		left: -2px;
		text-shadow: -1px 0 #0ff;
		clip: rect(44px, 450px, 56px, 0);
		animation: glitch-anim2 5s infinite linear alternate-reverse;
	}

	p {
		margin-top: 1.5rem;
		font-size: 1.2rem;
		color: #0ff;
		opacity: 0.9;
		letter-spacing: 0.1rem;
		border-right: 2px solid #0ff;
		white-space: nowrap;
		overflow: hidden;
		animation: typing 3.5s steps(40, end), blink-caret .75s step-end infinite;
		display: inline-block;
	}

	@keyframes typing {
		from { width: 0 }
		to { width: 100% }
	}

	@keyframes blink-caret {
		from, to { border-color: transparent }
		50% { border-color: #0ff; }
	}

	@keyframes glitch-anim {
		0% { clip: rect(30px, 9999px, 10px, 0); }
		5% { clip: rect(70px, 9999px, 90px, 0); }
		10% { clip: rect(10px, 9999px, 80px, 0); }
		15% { clip: rect(50px, 9999px, 20px, 0); }
		20% { clip: rect(90px, 9999px, 10px, 0); }
		25% { clip: rect(20px, 9999px, 60px, 0); }
		30% { clip: rect(60px, 9999px, 30px, 0); }
		35% { clip: rect(80px, 9999px, 50px, 0); }
		40% { clip: rect(40px, 9999px, 70px, 0); }
		45% { clip: rect(10px, 9999px, 40px, 0); }
		50% { clip: rect(50px, 9999px, 90px, 0); }
		55% { clip: rect(20px, 9999px, 10px, 0); }
		60% { clip: rect(80px, 9999px, 60px, 0); }
		65% { clip: rect(30px, 9999px, 20px, 0); }
		70% { clip: rect(70px, 9999px, 50px, 0); }
		75% { clip: rect(10px, 9999px, 80px, 0); }
		80% { clip: rect(60px, 9999px, 30px, 0); }
		85% { clip: rect(90px, 9999px, 10px, 0); }
		90% { clip: rect(40px, 9999px, 70px, 0); }
		95% { clip: rect(20px, 9999px, 60px, 0); }
		100% { clip: rect(50px, 9999px, 40px, 0); }
	}

	@keyframes glitch-anim2 {
		0% { clip: rect(60px, 9999px, 30px, 0); }
		5% { clip: rect(20px, 9999px, 10px, 0); }
		10% { clip: rect(90px, 9999px, 70px, 0); }
		15% { clip: rect(10px, 9999px, 50px, 0); }
		20% { clip: rect(50px, 9999px, 20px, 0); }
		25% { clip: rect(80px, 9999px, 90px, 0); }
		30% { clip: rect(30px, 9999px, 60px, 0); }
		35% { clip: rect(70px, 9999px, 10px, 0); }
		40% { clip: rect(40px, 9999px, 80px, 0); }
		45% { clip: rect(60px, 9999px, 30px, 0); }
		50% { clip: rect(10px, 9999px, 70px, 0); }
		55% { clip: rect(90px, 9999px, 20px, 0); }
		60% { clip: rect(30px, 9999px, 50px, 0); }
		65% { clip: rect(80px, 9999px, 10px, 0); }
		70% { clip: rect(20px, 9999px, 60px, 0); }
		75% { clip: rect(70px, 9999px, 40px, 0); }
		80% { clip: rect(50px, 9999px, 80px, 0); }
		85% { clip: rect(10px, 9999px, 30px, 0); }
		90% { clip: rect(60px, 9999px, 90px, 0); }
		95% { clip: rect(40px, 9999px, 50px, 0); }
		100% { clip: rect(20px, 9999px, 10px, 0); }
	}
</style>