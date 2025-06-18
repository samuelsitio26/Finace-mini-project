<!-- <script>
	import { onMount } from 'svelte';

	let imageLoadSuccess = false;

	onMount(() => {
		// Check if the image file exists
		const img = new Image();
		img.onload = () => {
			imageLoadSuccess = true;
			console.log('Image loaded successfully!');
		};
		img.onerror = () => {
			console.error('Failed to load image');
			imageLoadSuccess = false;
		};
		img.src = 'Logo-Eltama-Prima-Indo-01.png';
	});
</script>

<h1>Static Asset Test</h1>

<div>
	<h2>Image with root path (absolute)</h2>
	<img src="/Logo-Eltama-Prima-Indo-01.png" alt="Logo (absolute)" width="200" />
</div>

<div>
	<h2>Image with relative path</h2>
	<img src="Logo-Eltama-Prima-Indo-01.png" alt="Logo (relative)" width="200" />
</div>

<div>
	<h2>Image with static path</h2>
	<img src="static/Logo-Eltama-Prima-Indo-01.png" alt="Logo (static folder)" width="200" />
</div>

<div>
	<h2>Load status: {imageLoadSuccess ? '✅ Image loads correctly' : '❌ Image not loading'}</h2>
</div>

<style>
	div {
		margin: 20px;
		padding: 20px;
		border: 1px solid #ccc;
	}
	img {
		display: block;
		max-width: 100%;
	}
</style> -->
