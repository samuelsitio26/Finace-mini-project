<script>
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
	import styles from './dashboard.module.css';
	import Logo from '$lib/Logo.svelte';

	// User data
	let currentUserEmail = '';
	let logout = null;

	// Reactive variables
	let projects = [
		{
			id: 1,
			name: 'Website E-Commerce',
			budget: 50000000,
			income: 75000000,
			status: 'completed',
			startDate: '2024-01-15'
		},
		{
			id: 2,
			name: 'Mobile App Development',
			budget: 80000000,
			income: 65000000,
			status: 'active',
			startDate: '2024-03-01'
		},
		{
			id: 3,
			name: 'Digital Marketing Campaign',
			budget: 25000000,
			income: 40000000,
			status: 'completed',
			startDate: '2024-02-10'
		},
		{
			id: 4,
			name: 'System Integration',
			budget: 120000000,
			income: 95000000,
			status: 'active',
			startDate: '2024-04-01'
		},
		{
			id: 5,
			name: 'Data Analytics Platform',
			budget: 95000000,
			income: 0,
			status: 'planning',
			startDate: '2024-07-01'
		}
	];

	let periodFilter = 'all';
	let statusFilter = 'all';
	let showModal = false;

	// Form variables
	let projectName = '';
	let projectBudget = 0;
	let projectIncome = 0;
	let projectStatus = 'planning';
	let projectStartDate = '';

	// Chart variables
	let comparisonChart, performanceChart, trendChart;
	let chartLoaded = false;

	// Computed values
	$: filteredProjects = projects.filter((project) => {
		const matchesPeriod = periodFilter === 'all' || project.startDate.includes(periodFilter);
		const matchesStatus = statusFilter === 'all' || project.status === statusFilter;
		return matchesPeriod && matchesStatus;
	});

	$: totalBudget = filteredProjects.reduce((sum, p) => sum + p.budget, 0);
	$: totalIncome = filteredProjects.reduce((sum, p) => sum + p.income, 0);
	$: totalProfit = totalIncome - totalBudget;
	$: roi = totalBudget > 0 ? (totalProfit / totalBudget) * 100 : 0;
	$: incomePercentage = totalBudget > 0 ? (totalIncome / totalBudget) * 100 : 0;
	$: profitMargin = totalIncome > 0 ? (totalProfit / totalIncome) * 100 : 0;

	// Functions
	function formatCurrency(amount) {
		return new Intl.NumberFormat('id-ID', {
			style: 'currency',
			currency: 'IDR',
			minimumFractionDigits: 0
		}).format(amount);
	}

	function getStatusEmoji(status) {
		const statusEmoji = {
			completed: '✅',
			active: '🔄',
			planning: '📋'
		};
		return statusEmoji[status];
	}

	function addProject() {
		showModal = true;
	}

	function closeModal() {
		showModal = false;
		resetForm();
	}

	function resetForm() {
		projectName = '';
		projectBudget = 0;
		projectIncome = 0;
		projectStatus = 'planning';
		projectStartDate = '';
	}

	function handleSubmit() {
		const newProject = {
			id: projects.length + 1,
			name: projectName,
			budget: parseInt(projectBudget),
			income: parseInt(projectIncome),
			status: projectStatus,
			startDate: projectStartDate
		};

		projects = [...projects, newProject];
		closeModal();
	}
	function createComparisonChart() {
		if (!chartLoaded || !window.Chart) return;

		const canvas = document.getElementById('comparisonChart');
		if (!canvas) {
			console.error('Canvas element comparisonChart not found');
			return;
		}

		const ctx = canvas.getContext('2d');
		if (!ctx) return;

		if (comparisonChart) {
			comparisonChart.destroy();
		}

		comparisonChart = new window.Chart(ctx, {
			type: 'bar',
			data: {
				labels: filteredProjects.map((p) => p.name),
				datasets: [
					{
						label: 'Budget',
						data: filteredProjects.map((p) => p.budget),
						backgroundColor: 'rgba(231, 76, 60, 0.7)',
						borderColor: 'rgba(231, 76, 60, 1)',
						borderWidth: 2
					},
					{
						label: 'Pemasukan',
						data: filteredProjects.map((p) => p.income),
						backgroundColor: 'rgba(39, 174, 96, 0.7)',
						borderColor: 'rgba(39, 174, 96, 1)',
						borderWidth: 2
					}
				]
			},
			options: {
				responsive: true,
				maintainAspectRatio: false,
				plugins: {
					legend: {
						position: 'top'
					}
				},
				scales: {
					y: {
						beginAtZero: true,
						ticks: {
							callback: function (value) {
								return 'Rp ' + value / 1000000 + 'M';
							}
						}
					}
				}
			}
		});
	}
	function createPerformanceChart() {
		if (!chartLoaded || !window.Chart) return;

		const canvas = document.getElementById('performanceChart');
		if (!canvas) {
			console.error('Canvas element performanceChart not found');
			return;
		}

		const ctx = canvas.getContext('2d');
		if (!ctx) return;

		if (performanceChart) {
			performanceChart.destroy();
		}

		const performanceData = filteredProjects.map((p) => {
			const roi = p.budget > 0 ? ((p.income - p.budget) / p.budget) * 100 : 0;
			return {
				project: p.name,
				roi: roi
			};
		});

		performanceChart = new window.Chart(ctx, {
			type: 'doughnut',
			data: {
				labels: performanceData.map((p) => p.project),
				datasets: [
					{
						data: performanceData.map((p) => Math.abs(p.roi)),
						backgroundColor: ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CEB4', '#FECA57'],
						borderWidth: 2,
						borderColor: '#fff'
					}
				]
			},
			options: {
				responsive: true,
				maintainAspectRatio: false,
				plugins: {
					legend: {
						position: 'bottom'
					}
				}
			}
		});
	}
	function createTrendChart() {
		if (!chartLoaded || !window.Chart) return;

		const canvas = document.getElementById('trendChart');
		if (!canvas) {
			console.error('Canvas element trendChart not found');
			return;
		}

		const ctx = canvas.getContext('2d');
		if (!ctx) return;

		if (trendChart) {
			trendChart.destroy();
		}

		const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'];
		const budgetTrend = [45, 52, 68, 84, 95, 110];
		const incomeTrend = [38, 48, 72, 89, 102, 125];

		trendChart = new window.Chart(ctx, {
			type: 'line',
			data: {
				labels: months,
				datasets: [
					{
						label: 'Budget Trend (Juta)',
						data: budgetTrend,
						borderColor: '#e74c3c',
						backgroundColor: 'rgba(231, 76, 60, 0.1)',
						tension: 0.4,
						fill: true
					},
					{
						label: 'Income Trend (Juta)',
						data: incomeTrend,
						borderColor: '#27ae60',
						backgroundColor: 'rgba(39, 174, 96, 0.1)',
						tension: 0.4,
						fill: true
					}
				]
			},
			options: {
				responsive: true,
				maintainAspectRatio: false,
				plugins: {
					legend: {
						position: 'top'
					}
				},
				scales: {
					y: {
						beginAtZero: true
					}
				}
			}
		});
	}

	function updateCharts() {
		if (chartLoaded) {
			createComparisonChart();
			createPerformanceChart();
			createTrendChart();
		}
	}

	// Reactive statements
	$: if (filteredProjects && chartLoaded) {
		updateCharts();
	}

	onMount(async () => {
		// Load user data from Firebase auth
		if (browser) {
			try {
				const authModule = await import('$lib/services/firebaseConfig');
				logout = authModule.logout;

				if (typeof window !== 'undefined' && window.localStorage) {
					currentUserEmail = window.localStorage.getItem('user_email') || '';
				}
			} catch (err) {
				console.error('Failed to load auth data:', err);
			}
		}
		// Load Chart.js library
		if (browser) {
			try {
				const ChartModule = await import('chart.js/auto');
				// Pastikan Chart tersedia sebagai global variable
				window.Chart = ChartModule.default || ChartModule;

				// Tunggu sebentar agar DOM siap
				setTimeout(() => {
					chartLoaded = true;
					// Create charts
					createComparisonChart();
					createPerformanceChart();
					createTrendChart();
				}, 500);
			} catch (err) {
				console.error('Failed to load Chart.js:', err);
			}
		}
	});

	// Logout function
	async function handleLogout() {
		if (logout) {
			await logout();
			window.location.href = '/login';
		}
	}
</script>

<svelte:head>
	<title>Finance Dashboard - Budget vs Pemasukan</title>
</svelte:head>

<!-- Dashboard Header -->
<div class={styles.dashboardHeader}>
	<div class={styles.headerLeft}>
		<div class={styles.logoSection}>
			<Logo className={styles.companyLogo} height="45px" />
			<span class={styles.companyTitle}>PT. ELTAMA PRIMA INDO</span>
		</div>
	</div>

	<div class={styles.headerRight}>
		<div class={styles.userSection}>
			{#if currentUserEmail}
				<span class={styles.userEmail}>
					<svg
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						width="18"
						height="18"
						style="margin-right: 5px; vertical-align: middle;"
					>
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"
						/>
					</svg>
					{currentUserEmail}
				</span>
			{/if}
			<button class={styles.logoutBtn} on:click={handleLogout}>
				<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" class={styles.logoutIcon}>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"
					/>
				</svg>
				<span>Logout</span>
			</button>
		</div>
	</div>
</div>

<div class={styles.container}>
	<div class={styles.header}>
		<h1>Finance Performance Dashboard</h1>
		<p>Perbandingan Data Budget & Pemasukan Project</p>
	</div>

	<div class={styles.controls}>
		<div class={styles.controlGroup}>
			<label>Filter Periode:</label>
			<select bind:value={periodFilter}>
				<option value="all">Semua Periode</option>
				<option value="2024">2024</option>
				<option value="2023">2023</option>
			</select>
		</div>
		<div class={styles.controlGroup}>
			<label>Status Project:</label>
			<select bind:value={statusFilter}>
				<option value="all">Semua Status</option>
				<option value="active">Aktif</option>
				<option value="completed">Selesai</option>
				<option value="planning">Perencanaan</option>
			</select>
		</div>
		<div class={styles.controlGroup}>
			<label>&nbsp;</label>
			<button on:click={addProject}>+ Tambah Project</button>
		</div>
	</div>

	<div class={styles.summaryCards}>
		<div class={styles.summaryCard}>
			<h4>Total Budget</h4>
			<div class={styles.amount}>{formatCurrency(totalBudget)}</div>
			<div class={styles.percentage}>100% Alokasi</div>
		</div>
		<div class={styles.summaryCard}>
			<h4>Total Pemasukan</h4>
			<div class={styles.amount}>{formatCurrency(totalIncome)}</div>
			<div class={styles.percentage}>{incomePercentage.toFixed(1)}% dari budget</div>
		</div>
		<div class={styles.summaryCard}>
			<h4>Total Profit</h4>
			<div class={styles.amount}>{formatCurrency(totalProfit)}</div>
			<div class={styles.percentage}>{profitMargin.toFixed(1)}% margin</div>
		</div>
		<div class={styles.summaryCard}>
			<h4>ROI</h4>
			<div class={styles.amount}>{roi.toFixed(1)}%</div>
			<div class={styles.percentage}>Return on Investment</div>
		</div>
	</div>

	<div class={styles.dashboardGrid}>
		<div class={styles.card}>
			<h3>📈 Grafik Perbandingan</h3>
			<div class={styles.chartContainer}>
				<canvas id="comparisonChart"></canvas>
			</div>
		</div>

		<div class={styles.card}>
			<h3>🎯 Performance Chart</h3>
			<div class={styles.chartContainer}>
				<canvas id="performanceChart"></canvas>
			</div>
		</div>

		<div class={styles.card}>
			<h3>📋 Daftar Project</h3>
			<div class={styles.projectList}>
				{#each filteredProjects as project}
					<div class={styles.projectItem}>
						<div class={styles.projectInfo}>
							<h4>{getStatusEmoji(project.status)} {project.name}</h4>
							<span>Status: {project.status.charAt(0).toUpperCase() + project.status.slice(1)}</span
							>
						</div>
						<div class={styles.projectNumbers}>
							<div class={styles.budget}>Budget: {formatCurrency(project.budget)}</div>
							<div class={styles.income}>Income: {formatCurrency(project.income)}</div>
							<div
								class="{styles.profit} {project.income - project.budget >= 0
									? styles.income
									: styles.budget}"
							>
								Profit: {formatCurrency(project.income - project.budget)}
							</div>
						</div>
					</div>
				{/each}
			</div>
		</div>

		<div class={styles.card}>
			<h3>💹 Trend Analysis</h3>
			<div class={styles.chartContainer}>
				<canvas id="trendChart"></canvas>
			</div>
		</div>
	</div>
</div>

<!-- Modal for adding new project -->
{#if showModal}
	<div class={styles.modal} on:click={closeModal}>
		<div class={styles.modalContent} on:click|stopPropagation>
			<h3>Tambah Project Baru</h3>
			<form on:submit|preventDefault={handleSubmit}>
				<div class={styles.formGroup}>
					<label>Nama Project:</label>
					<input type="text" bind:value={projectName} required />
				</div>
				<div class={styles.formGroup}>
					<label>Budget (Rp):</label>
					<input type="number" bind:value={projectBudget} required />
				</div>
				<div class={styles.formGroup}>
					<label>Target Pemasukan (Rp):</label>
					<input type="number" bind:value={projectIncome} required />
				</div>
				<div class={styles.formGroup}>
					<label>Status:</label>
					<select bind:value={projectStatus} required>
						<option value="planning">Perencanaan</option>
						<option value="active">Aktif</option>
						<option value="completed">Selesai</option>
					</select>
				</div>
				<div class={styles.formGroup}>
					<label>Tanggal Mulai:</label>
					<input type="date" bind:value={projectStartDate} required />
				</div>
				<div class={styles.modalButtons}>
					<button type="button" class={styles.btnSecondary} on:click={closeModal}>Batal</button>
					<button type="submit">Simpan</button>
				</div>
			</form>
		</div>
	</div>
{/if}
