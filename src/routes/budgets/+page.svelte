<script>
	import { onMount } from 'svelte';

	// Budget data
	let budgets = [
		{
			id: 1,
			name: 'April 2020',
			fiscalYear: 'Apr 2020 - Mar 2021',
			budgetPeriod: 'Monthly',
			active: true
		},
		{
			id: 2,
			name: 'Expense type based',
			fiscalYear: 'Apr 2020 - Mar 2021',
			budgetPeriod: 'Monthly',
			active: false
		}
	];

	// Dropdown state
	let activeDropdown = null;

	// Functions
	function toggleDropdown(budgetId) {
		activeDropdown = activeDropdown === budgetId ? null : budgetId;
	}

	function handleAction(action, budgetId) {
		console.log(`${action} budget ${budgetId}`);
		activeDropdown = null;

		switch (action) {
			case 'edit':
				// Handle edit action
				break;
			case 'view':
				// Handle view budget vs actuals
				break;
			case 'clone':
				// Handle clone action
				break;
			case 'delete':
				// Handle delete action
				budgets = budgets.filter((b) => b.id !== budgetId);
				break;
		}
	}

	function handleNewBudget() {
		console.log('Creating new budget');
	}

	// Close dropdown when clicking outside
	function handleClickOutside(event) {
		if (!event.target.closest('.dropdown')) {
			activeDropdown = null;
		}
	}

	onMount(() => {
		document.addEventListener('click', handleClickOutside);
		return () => {
			document.removeEventListener('click', handleClickOutside);
		};
	});
</script>

<svelte:head>
	<title>Expense Budgets</title>
	<link
		href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css"
		rel="stylesheet"
		integrity="sha384-T3c6CoIi6uLrA9TneNEoa7RxnatzjcDSCmG1MXxSR1GAsXEV/Dwwykc2MPK8M2HN"
		crossorigin="anonymous"
	/>
	<link
		rel="stylesheet"
		href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css"
	/>
</svelte:head>

<div class="d-flex">
	<!-- Sidebar -->
	<nav class="sidebar d-flex flex-column">
		<div class="logo">
			<svg
				fill="none"
				stroke-width="2"
				stroke-linecap="round"
				stroke-linejoin="round"
				stroke="#cbd2e1"
				viewBox="0 0 24 24"
				aria-hidden="true"
				focusable="false"
			>
				<path d="M3 12h18M3 6h18M3 18h18M6 6v12M18 6v12"></path>
			</svg>
			Expense
		</div>

		<div class="nav-section">
			<strong>My View <span>&#8250;</span></strong>
		</div>

		<div class="nav-section mt-3">
			<strong>
				Admin View
				<svg
					fill="none"
					stroke="#2a5ad6"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
					viewBox="0 0 24 24"
					aria-hidden="true"
					focusable="false"
				>
					<path d="M6 9l6 6 6-6"></path>
				</svg>
			</strong>

			<div class="nav-links mt-3">
				<a href="#" class="d-flex align-items-center">
					<i class="far fa-clock"></i> Dashboard
				</a>
				<a href="#" class="d-flex align-items-center">
					<i class="far fa-briefcase"></i> Trips
				</a>
				<a href="#" class="d-flex align-items-center">
					<i class="far fa-folder"></i> Reports
				</a>
				<a href="#" class="d-flex align-items-center">
					<i class="far fa-clock"></i> Advances
				</a>
				<a href="#" class="d-flex align-items-center">
					<i class="far fa-credit-card"></i> Purchase Requests
				</a>
				<a href="#" class="d-flex align-items-center">
					<i class="far fa-envelope"></i> Corporate Cards
				</a>
				<a href="#" class="budgets-link d-flex align-items-center">
					<i class="far fa-credit-card"></i> Budgets
				</a>
				<a href="#" class="d-flex align-items-center">
					<i class="far fa-chart-pie"></i> Analytics
				</a>
				<a href="#" class="settings-link d-flex align-items-center">
					<i class="fas fa-cog"></i> Settings
				</a>
			</div>
		</div>
	</nav>

	<!-- Main content area -->
	<div class="flex-grow-1 d-flex flex-column">
		<!-- Top bar -->
		<header class="topbar d-flex align-items-center">
			<div class="search-container position-relative">
				<i class="fas fa-search search-icon"></i>
				<input
					type="search"
					placeholder="Search Reports Approval"
					aria-label="Search Reports Approval"
				/>
				<div class="search-dropdown">
					<i class="fas fa-caret-down"></i>
				</div>
			</div>

			<div class="upgrade d-flex align-items-center">
				<svg
					fill="none"
					stroke="#fca311"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
					viewBox="0 0 24 24"
					aria-hidden="true"
					focusable="false"
					style="width:16px;height:16px;"
				>
					<path d="M12 2v6M12 22v-6M5 12h14"></path>
				</svg>
				UPGRADE NOW
			</div>

			<button class="btn-add" aria-label="Add new item">+</button>
			<i class="fas fa-cog icon-btn" title="Settings"></i>
			<i class="fas fa-bell icon-btn" title="Notifications"></i>
			<i class="fas fa-users icon-btn" title="Users"></i>

			<div class="user-section">
				ZYLKER <i class="fas fa-caret-down"></i>
				<div class="user-avatar" aria-label="User avatar"></div>
			</div>

			<i class="fas fa-th icon-btn" title="Apps"></i>
		</header>

		<!-- Main content -->
		<main class="content">
			<h2>Budgets</h2>
			<button class="btn btn-primary btn-new-budget" on:click={handleNewBudget}>
				+ New Budget
			</button>

			<table class="mt-4">
				<thead>
					<tr>
						<th style="width:33%;">Name</th>
						<th style="width:33%;">Fiscal Year</th>
						<th style="width:33%;">Budget Period</th>
						<th style="width:1%;"></th>
					</tr>
				</thead>
				<tbody>
					{#each budgets as budget}
						<tr class:active={budget.active}>
							<td>
								<a href="#">{budget.name}</a>
							</td>
							<td>{budget.fiscalYear}</td>
							<td>{budget.budgetPeriod}</td>
							<td class="text-end position-relative">
								<div class="dropdown">
									<button
										type="button"
										class="dropdown-toggle-btn"
										on:click={() => toggleDropdown(budget.id)}
										aria-expanded={activeDropdown === budget.id}
									>
										&#8230;
									</button>

									{#if activeDropdown === budget.id}
										<ul class="dropdown-menu dropdown-menu-end show">
											<li>
												<button
													class="dropdown-item d-flex align-items-center"
													on:click={() => handleAction('edit', budget.id)}
												>
													<i class="far fa-edit"></i> Edit
												</button>
											</li>
											<li>
												<button
													class="dropdown-item d-flex align-items-center view-budget"
													on:click={() => handleAction('view', budget.id)}
												>
													<i class="far fa-eye"></i> View Budget vs Actuals
												</button>
											</li>
											<li>
												<button
													class="dropdown-item d-flex align-items-center"
													on:click={() => handleAction('clone', budget.id)}
												>
													<i class="far fa-copy"></i> Clone
												</button>
											</li>
											<li>
												<button
													class="dropdown-item d-flex align-items-center delete"
													on:click={() => handleAction('delete', budget.id)}
												>
													<i class="fas fa-trash-alt"></i> Delete
												</button>
											</li>
										</ul>
									{/if}
								</div>
							</td>
						</tr>
					{/each}
				</tbody>
			</table>
		</main>
	</div>
</div>

<style>
	@import './budgets.module.css';
</style>
