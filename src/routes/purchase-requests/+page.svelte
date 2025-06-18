<script>
	import { onMount } from 'svelte';

	// Dashboard data
	let dashboardData = {
		fiscalYear: '2020 - 2021',
		totalExpenses: 230849.0,
		advances: 272866.4,
		reimbursements: 4439.0,
		totalTrips: 16,
		spendData: [
			{ month: 'APR', amount: 15000 },
			{ month: 'MAY', amount: 25000 },
			{ month: 'JUN', amount: 80000 },
			{ month: 'JUL', amount: 45000 },
			{ month: 'AUG', amount: 5000 },
			{ month: 'SEP', amount: 8000 },
			{ month: 'OCT', amount: 12000 },
			{ month: 'NOV', amount: 15000 },
			{ month: 'DEC', amount: 35000 },
			{ month: 'JAN', amount: 40000 },
			{ month: 'FEB', amount: 25000 },
			{ month: 'MAR', amount: 10000 }
		]
	};

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

	// Pending data
	let pendingTrips = [
		{ id: 1, approver: 'John', email: 'john.b@zylker.com', count: 1 },
		{ id: 2, approver: 'Patricia', email: 'patricia.b@zylker.com', count: 1 }
	];

	let pendingReports = {
		forApproval: { count: 10, amount: 0 },
		forReimbursement: { count: 0, amount: 1490051.91 }
	};

	let unreportedAdvances = [{ id: 1, user: 'Sam', email: 'sam.a@zylker.com', amount: 51038.01 }];

	// State management
	let activeDropdown = null;
	let currentView = 'dashboard';
	let sidebarCollapsed = false;

	// Functions
	function toggleDropdown(budgetId) {
		activeDropdown = activeDropdown === budgetId ? null : budgetId;
	}

	function handleAction(action, budgetId) {
		console.log(`${action} budget ${budgetId}`);
		activeDropdown = null;

		switch (action) {
			case 'edit':
				break;
			case 'view':
				break;
			case 'clone':
				break;
			case 'delete':
				budgets = budgets.filter((b) => b.id !== budgetId);
				break;
		}
	}

	function handleNewBudget() {
		console.log('Creating new budget');
	}

	function setActiveView(view) {
		currentView = view;
	}

	function toggleSidebar() {
		sidebarCollapsed = !sidebarCollapsed;
	}

	function formatCurrency(amount) {
		return new Intl.NumberFormat('en-US', {
			style: 'currency',
			currency: 'USD',
			minimumFractionDigits: 2
		}).format(amount);
	}

	function getMaxSpendValue() {
		return Math.max(...dashboardData.spendData.map((d) => d.amount));
	}

	// Close dropdown when clicking outside
	function handleClickOutside(event) {
		if (!event.target.closest('.dropdown')) {
			activeDropdown = null;
		}
	}

	function handleLogout() {
		// Clear session/localStorage and redirect to login
		localStorage.clear();
		sessionStorage.clear();
		window.location.href = '/login';
	}

	onMount(() => {
		document.addEventListener('click', handleClickOutside);
		return () => {
			document.removeEventListener('click', handleClickOutside);
		};
	});
</script>

<svelte:head>
	<title>Expense Dashboard</title>
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

<div class="dashboard-layout">
	<!-- Sidebar -->
	<nav class="sidebar {sidebarCollapsed ? 'collapsed' : ''}">
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
			{#if !sidebarCollapsed}
				Expense
			{/if}
		</div>

		<div class="nav-section">
			<strong>
				{#if !sidebarCollapsed}My View{/if}
				<span>&#8250;</span>
			</strong>
		</div>

		<div class="nav-section mt-3">
			<strong>
				{#if !sidebarCollapsed}Admin View{/if}
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
				<a href="/dashboard" class="nav-link {currentView === 'dashboard' ? 'active' : ''}">
					<i class="far fa-clock"></i> Dashboard
				</a>
				<a href="/reports" class="nav-link {currentView === 'reports' ? 'active' : ''}">
					<i class="far fa-folder"></i> Reports
				</a>
				<a href="/purchase-requests" class="nav-link {currentView === 'purchase' ? 'active' : ''}">
					<i class="far fa-credit-card"></i> Purchase Requests
				</a>
				<a href="/budgets" class="nav-link {currentView === 'budgets' ? 'active' : ''}">
					<i class="far fa-credit-card"></i> Budgets
				</a>
				<a href="/analytics" class="nav-link {currentView === 'analytics' ? 'active' : ''}">
					<i class="far fa-chart-pie"></i> Analytics
				</a>
				<a
					href="/settings"
					class="nav-link settings-link {currentView === 'settings' ? 'active' : ''}"
				>
					<i class="fas fa-cog"></i> Settings
				</a>
			</div>
		</div>
	</nav>

	<!-- Main content area -->
	<div class="main-content">
		<!-- Top bar -->
		<header class="topbar">
			<button class="sidebar-toggle" on:click={toggleSidebar}>
				<i class="fas fa-bars"></i>
			</button>

			<div class="search-container">
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

			<div class="topbar-actions">
				<div class="upgrade">
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
				<button class="btn btn-link logout-btn" on:click={handleLogout}>
					<i class="fas fa-sign-out-alt"></i> Logout
				</button>
			</div>
		</header>

		<!-- Content Area -->
		<main class="content">
			{#if currentView === 'dashboard'}
				<div class="dashboard-header">
					<h2>Dashboard</h2>
					<button class="btn btn-link getting-started">Getting Started</button>
				</div>

				<div class="dashboard-grid">
					<!-- Spend Summary -->
					<div class="card spend-summary">
						<div class="card-header">
							<h3>SPEND SUMMARY</h3>
							<select class="form-select">
								<option>Previous Year</option>
							</select>
						</div>
						<div class="card-body">
							<div class="fiscal-year">FISCAL YEAR: {dashboardData.fiscalYear}</div>
							<div class="chart-container">
								<svg class="spend-chart" viewBox="0 0 400 200">
									{#each dashboardData.spendData as point, i}
										<circle
											cx={30 + i * 30}
											cy={150 - (point.amount / getMaxSpendValue()) * 120}
											r="3"
											fill="#4CAF50"
										/>
										{#if i > 0}
											<line
												x1={30 + (i - 1) * 30}
												y1={150 -
													(dashboardData.spendData[i - 1].amount / getMaxSpendValue()) * 120}
												x2={30 + i * 30}
												y2={150 - (point.amount / getMaxSpendValue()) * 120}
												stroke="#4CAF50"
												stroke-width="2"
											/>
										{/if}
										<text x={30 + i * 30} y="170" text-anchor="middle" class="chart-label">
											{point.month}
										</text>
									{/each}
								</svg>
							</div>
						</div>
					</div>

					<!-- Overall Summary -->
					<div class="card overall-summary">
						<div class="card-header">
							<h3>OVERALL SUMMARY</h3>
							<select class="form-select">
								<option>Previous Year</option>
							</select>
						</div>
						<div class="card-body">
							<div class="summary-items">
								<div class="summary-item">
									<div class="summary-icon expenses">
										<i class="fas fa-receipt"></i>
									</div>
									<div class="summary-content">
										<div class="summary-label">Total Expenses</div>
										<div class="summary-value">{formatCurrency(dashboardData.totalExpenses)}</div>
									</div>
								</div>

								<div class="summary-item">
									<div class="summary-icon advances">
										<i class="fas fa-clock"></i>
									</div>
									<div class="summary-content">
										<div class="summary-label">Advances</div>
										<div class="summary-value">{formatCurrency(dashboardData.advances)}</div>
									</div>
								</div>

								<div class="summary-item">
									<div class="summary-icon reimbursements">
										<i class="fas fa-money-bill"></i>
									</div>
									<div class="summary-content">
										<div class="summary-label">Reimbursements</div>
										<div class="summary-value">{formatCurrency(dashboardData.reimbursements)}</div>
									</div>
								</div>

								<div class="summary-item">
									<div class="summary-icon trips">
										<i class="fas fa-suitcase"></i>
									</div>
									<div class="summary-content">
										<div class="summary-label">Total Trips</div>
										<div class="summary-value">{dashboardData.totalTrips}</div>
									</div>
								</div>
							</div>
						</div>
					</div>

					<!-- Pending Trips -->
					<div class="card pending-trips">
						<div class="card-header">
							<h3><i class="fas fa-clock"></i> PENDING TRIPS</h3>
						</div>
						<div class="card-body">
							<div class="table-container">
								<table class="pending-table">
									<thead>
										<tr>
											<th>APPROVER</th>
											<th>COUNT</th>
										</tr>
									</thead>
									<tbody>
										{#each pendingTrips as trip}
											<tr>
												<td>
													<div class="approver-info">
														<div class="avatar">{trip.approver.charAt(0)}</div>
														<div>
															<div class="approver-name">{trip.approver}</div>
															<div class="approver-email">{trip.email}</div>
														</div>
													</div>
												</td>
												<td class="count">{trip.count}</td>
											</tr>
										{/each}
									</tbody>
								</table>
							</div>
						</div>
					</div>

					<!-- Pending Reports -->
					<div class="card pending-reports">
						<div class="card-header">
							<h3><i class="fas fa-file-alt"></i> PENDING REPORTS</h3>
						</div>
						<div class="card-body">
							<div class="pending-stats">
								<div class="pending-stat">
									<button class="pending-btn approval">
										For Approval<br />
										({pendingReports.forApproval.count} Reports)
									</button>
									<div class="pending-amount">
										{formatCurrency(pendingReports.forApproval.amount)}
									</div>
								</div>
								<div class="pending-stat">
									<div class="pending-label">For Reimbursement</div>
									<div class="pending-amount">
										{formatCurrency(pendingReports.forReimbursement.amount)}
									</div>
								</div>
							</div>

							<div class="approver-section">
								<div class="approver-header">
									<span>APPROVER</span>
									<span>COUNT</span>
								</div>
								<div class="approver-item">
									<div class="approver-info">
										<div class="avatar">J</div>
										<div>
											<div class="approver-name">John</div>
											<div class="approver-email">john.b@zylker.com</div>
										</div>
									</div>
									<div class="count">9</div>
								</div>
							</div>
						</div>
					</div>

					<!-- Unreported Advances -->
					<div class="card unreported-advances">
						<div class="card-header">
							<h3><i class="fas fa-exclamation-circle"></i> UNREPORTED ADVANCES</h3>
						</div>
						<div class="card-body">
							<div class="table-container">
								<table class="pending-table">
									<thead>
										<tr>
											<th>USER NAME</th>
											<th>AMOUNT</th>
										</tr>
									</thead>
									<tbody>
										{#each unreportedAdvances as advance}
											<tr>
												<td>
													<div class="approver-info">
														<div class="avatar">{advance.user.charAt(0)}</div>
														<div>
															<div class="approver-name">{advance.user}</div>
															<div class="approver-email">{advance.email}</div>
														</div>
													</div>
												</td>
												<td class="amount">{formatCurrency(advance.amount)}</td>
											</tr>
										{/each}
									</tbody>
								</table>
							</div>
						</div>
					</div>
				</div>
			{:else if currentView === 'budgets'}
				<div class="budgets-header">
					<h2>Budgets</h2>
					<button class="btn btn-primary btn-new-budget" on:click={handleNewBudget}>
						+ New Budget
					</button>
				</div>

				<div class="budgets-table-container">
					<table class="budgets-table">
						<thead>
							<tr>
								<th>Name</th>
								<th>Fiscal Year</th>
								<th>Budget Period</th>
								<th></th>
							</tr>
						</thead>
						<tbody>
							{#each budgets as budget}
								<tr class:active={budget.active}>
									<td>
										<a href="#" class="budget-link">{budget.name}</a>
									</td>
									<td>{budget.fiscalYear}</td>
									<td>{budget.budgetPeriod}</td>
									<td class="actions-cell">
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
												<ul class="dropdown-menu show">
													<li>
														<button
															class="dropdown-item"
															on:click={() => handleAction('edit', budget.id)}
														>
															<i class="far fa-edit"></i> Edit
														</button>
													</li>
													<li>
														<button
															class="dropdown-item view-budget"
															on:click={() => handleAction('view', budget.id)}
														>
															<i class="far fa-eye"></i> View Budget vs Actuals
														</button>
													</li>
													<li>
														<button
															class="dropdown-item"
															on:click={() => handleAction('clone', budget.id)}
														>
															<i class="far fa-copy"></i> Clone
														</button>
													</li>
													<li>
														<button
															class="dropdown-item delete"
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
				</div>
			{:else}
				<div class="placeholder-content">
					<h2>{currentView.charAt(0).toUpperCase() + currentView.slice(1)}</h2>
					<p>This section is under construction.</p>
				</div>
			{/if}
		</main>
	</div>
</div>

<style>
	@import './dashboard.module.css';
</style>
