<!-- src/routes/login/+page.svelte -->
<script>
	import { browser } from '$app/environment';
	import { onMount, onDestroy } from 'svelte';
	import styles from './login.module.css';
	import Logo from '$lib/Logo.svelte';

	let email = '';
	let password = '';
	let loading = false;
	let error = '';
	let isLoggedIn = false;
	let showPassword = false;
	let currentUserEmail = '';
	// Store references
	let login = null;
	let authError = null;
	let isAuthenticated = null;

	// Subscription cleanup
	let unsubscribeAuth = null;
	let unsubscribeError = null;

	// Initialize auth services on mount
	onMount(async () => {
		if (!browser) return;

		try {
			// Import auth services
			const authModule = await import('$lib/services/firebaseConfig');
			login = authModule.login;
			authError = authModule.authError;
			isAuthenticated = authModule.isAuthenticated; // Subscribe to authentication state
			if (isAuthenticated) {
				unsubscribeAuth = isAuthenticated.subscribe((value) => {
					isLoggedIn = value;
					// Redirect hanya jika sudah benar-benar login
					if (value === true && typeof window !== 'undefined') {
						const user = window.localStorage.getItem('user_email');
						currentUserEmail = user || '';
						window.location.replace('/dashboard');
					} else {
						currentUserEmail = '';
					}
				});
			}

			// Subscribe to auth errors
			if (authError) {
				unsubscribeError = authError.subscribe((value) => {
					error = value || '';
				});
			}
		} catch (err) {
			console.error('Failed to initialize auth services:', err);
			error = 'Gagal menginisialisasi sistem autentikasi';
		}
	});

	// Cleanup subscriptions
	onDestroy(() => {
		if (unsubscribeAuth) unsubscribeAuth();
		if (unsubscribeError) unsubscribeError();
	});
	async function handleLogin() {
		if (!email || !password) {
			error = 'Email dan password harus diisi';
			return;
		}

		if (!login) {
			error = 'Sistem autentikasi belum siap, silakan coba lagi';
			return;
		}

		loading = true;
		error = '';

		try {
			await login(email, password);
			// Always redirect to dashboard after successful login
			window.location.href = '/dashboard';
		} catch (e) {
			console.error('Login failed:', e);
		} finally {
			loading = false;
		}
	}
	function handleKeyPress(event) {
		if (event.key === 'Enter') {
			handleLogin();
		}
	}

	function togglePasswordVisibility() {
		showPassword = !showPassword;
	}
</script>

<svelte:head>
	<title>Login - Finance System</title>
	<meta name="description" content="Login to access your finance dashboard" />
</svelte:head>

<div class={styles.container}>
	<!-- Background Elements -->
	<div class={styles.backgroundShapes}>
		<div class={styles.shape1}></div>
		<div class={styles.shape2}></div>
		<div class={styles.shape3}></div>
		<div class={styles.floatingElements}>
			<div class={styles.float1}></div>
			<div class={styles.float2}></div>
			<div class={styles.float3}></div>
		</div>
	</div>

	<div class={styles.loginWrapper}>
		<!-- Logo Section -->
		<div class={styles.logoSection}>
			<div class={styles.logoContainer}>
				<div class={styles.logoIcon}>
					<Logo height="60px" className="centered-logo" />
				</div>
			</div>
			<h1 class={styles.title}>Welcome Back</h1>
		</div>

		<!-- Login Form -->
		<div class={styles.formContainer}>
			<form class={styles.form} on:submit|preventDefault={handleLogin}>
				<!-- Error Message -->
				{#if error}
					<div class={styles.errorMessage}>
						<div class={styles.errorIcon}>
							<svg viewBox="0 0 20 20" fill="currentColor">
								<path
									fill-rule="evenodd"
									d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z"
									clip-rule="evenodd"
								/>
							</svg>
						</div>
						<span>{error}</span>
					</div>
				{/if}

				<!-- Email Field -->
				<div class={styles.fieldGroup}>
					<label for="email" class={styles.label}>Email Address</label>
					<div class={styles.inputWrapper}>
						<div class={styles.inputIcon}>
							<svg viewBox="0 0 24 24" fill="none" stroke="currentColor">
								<path
									stroke-linecap="round"
									stroke-linejoin="round"
									stroke-width="2"
									d="M16 12a4 4 0 10-8 0 4 4 0 008 0zm0 0v1.5a2.5 2.5 0 005 0V12a9 9 0 10-9 9m4.5-1.206a8.959 8.959 0 01-4.5 1.207"
								/>
							</svg>
						</div>
						<input
							id="email"
							name="email"
							type="email"
							autocomplete="email"
							required
							bind:value={email}
							on:keypress={handleKeyPress}
							disabled={loading}
							class={styles.input}
							placeholder="Enter your email"
						/>
					</div>
				</div>

				<!-- Password Field -->
				<div class={styles.fieldGroup}>
					<label for="password" class={styles.label}>Password</label>
					<div class={styles.inputWrapper}>
						<div class={styles.inputIcon}>
							<svg viewBox="0 0 24 24" fill="none" stroke="currentColor">
								<path
									stroke-linecap="round"
									stroke-linejoin="round"
									stroke-width="2"
									d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"
								/>
							</svg>
						</div>
						<input
							id="password"
							name="password"
							type={showPassword ? 'text' : 'password'}
							autocomplete="current-password"
							required
							bind:value={password}
							on:keypress={handleKeyPress}
							disabled={loading}
							class={styles.input}
							placeholder="Enter your password"
						/>
						<button
							type="button"
							class={styles.passwordToggle}
							on:click={togglePasswordVisibility}
							tabindex="-1"
						>
							{#if showPassword}
								<svg viewBox="0 0 24 24" fill="none" stroke="currentColor">
									<path
										stroke-linecap="round"
										stroke-linejoin="round"
										stroke-width="2"
										d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.543-7a9.97 9.97 0 011.563-3.029m5.858.908a3 3 0 114.243 4.243M9.878 9.878l4.242 4.242M9.878 9.878L3 3m6.878 6.878L21 21"
									/>
								</svg>
							{:else}
								<svg viewBox="0 0 24 24" fill="none" stroke="currentColor">
									<path
										stroke-linecap="round"
										stroke-linejoin="round"
										stroke-width="2"
										d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"
									/>
									<circle cx="12" cy="12" r="3" />
								</svg>
							{/if}
						</button>
					</div>
				</div>

				<!-- Login Button -->
				<button type="submit" disabled={loading || !login} class={styles.loginButton}>
					{#if loading}
						<div class={styles.spinner}>
							<div class={styles.spinnerRing}></div>
						</div>
						<span>Signing In...</span>
					{:else if !login}
						<div class={styles.loadingIcon}>
							<svg viewBox="0 0 24 24" fill="none" stroke="currentColor">
								<path
									stroke-linecap="round"
									stroke-linejoin="round"
									stroke-width="2"
									d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.964-.833-2.732 0L4.082 16.5c-.77.833.192 2.5 1.732 2.5z"
								/>
							</svg>
						</div>
						<span>Loading...</span>
					{:else}
						<div class={styles.loginIcon}>
							<svg viewBox="0 0 24 24" fill="none" stroke="currentColor">
								<path
									stroke-linecap="round"
									stroke-linejoin="round"
									stroke-width="2"
									d="M11 16l-4-4m0 0l4-4m-4 4h14m-5 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h7a3 3 0 013 3v1"
								/>
							</svg>
						</div>
						<span>Sign In</span>
					{/if}
				</button>
			</form>
		</div>

		<!-- Footer -->
		<div class={styles.footer}>
			<p>© 2025 Samuel Sitio. All rights reserved.</p>
		</div>
	</div>
</div>
