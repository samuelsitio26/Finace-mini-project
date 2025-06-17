<script>
	import logo from '$lib/assets/logo.png';

	// Company logo component
	export let height = '40px';
	export let alt = 'PT. ELTAMA PRIMA INDO';
	export let className = '';
</script>

<img src={logo} {alt} class="company-logo {className}" style="height: {height};" />

<style>
	.company-logo {
		display: block;
		width: auto;
		object-fit: contain;
		border-radius: 4px;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
	}

	:global(.centered-logo) {
		margin: 0 auto;
		display: block;
	}
</style>
