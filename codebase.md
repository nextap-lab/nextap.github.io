# admin.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Admin Dashboard - Nextap AI</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.css" rel="stylesheet">
    <style>
        :root {
            --primary-color: #171717;
            --secondary-color: #404041;
            --accent-color: #3b82f6;
            --text-color: #f3f4f6;
        }

        body {
            background-color: #000;
            color: var(--text-color);
            min-height: 100vh;
        }

        .nav-blur {
            background: rgba(0, 0, 0, 0.8);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
        }

        .glass-card {
            background: rgba(23, 23, 23, 0.95);
            border: 1px solid rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
        }

        .admin-table thead th {
            background: rgba(23, 23, 23, 0.98);
            position: sticky;
            top: 64px;
            z-index: 10;
        }

        .status-badge {
            padding: 0.5rem 1rem;
            border-radius: 9999px;
            font-size: 0.875rem;
            display: inline-flex;
            align-items: center;
            gap: 0.5rem;
            transition: all 0.3s ease;
        }

        .status-pending {
            background: rgba(234, 179, 8, 0.1);
            color: #fbbf24;
            border: 1px solid rgba(251, 191, 36, 0.2);
        }

        .status-active {
            background: rgba(34, 197, 94, 0.1);
            color: #4ade80;
            border: 1px solid rgba(74, 222, 128, 0.2);
        }

        .status-completed {
            background: rgba(59, 130, 246, 0.1);
            color: #60a5fa;
            border: 1px solid rgba(96, 165, 250, 0.2);
        }

        .project-row {
            transition: all 0.3s ease;
            animation: fadeIn 0.5s ease-out;
        }

        .project-row:hover {
            background: rgba(255, 255, 255, 0.02);
        }

        .custom-input {
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.1);
            color: var(--text-color);
            transition: all 0.3s ease;
        }

        .custom-input:focus {
            border-color: var(--accent-color);
            background: rgba(255, 255, 255, 0.08);
            outline: none;
        }

        .stats-card {
            background: linear-gradient(145deg, rgba(30, 41, 59, 0.7), rgba(15, 23, 42, 0.7));
            backdrop-filter: blur(10px);
            border: 1px solid rgba(59, 130, 246, 0.2);
            transition: all 0.3s ease;
        }

        .stats-card:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 30px rgba(59, 130, 246, 0.15);
        }

        .login-container {
            background: linear-gradient(145deg, rgba(0, 0, 0, 0.7), rgba(0, 0, 0, 0.9));
            backdrop-filter: blur(10px);
            animation: fadeScale 0.5s ease-out;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @keyframes fadeScale {
            from { opacity: 0; transform: scale(0.95); }
            to { opacity: 1; transform: scale(1); }
        }

        .save-button {
            position: relative;
            overflow: hidden;
        }

        .save-button::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(45deg, transparent, rgba(255, 255, 255, 0.1), transparent);
            transform: translateX(-100%);
            transition: transform 0.6s ease;
        }

        .save-button:hover::after {
            transform: translateX(100%);
        }
    </style>
</head>
<body>
    <!-- Login Modal -->
    <div id="loginModal" class="fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center z-50">
        <div class="login-container w-full max-w-md p-8 rounded-2xl">
            <div class="text-center mb-8">
                <h2 class="text-3xl font-bold bg-gradient-to-r from-blue-400 to-blue-600 text-transparent bg-clip-text">Admin Login</h2>
                <p class="text-gray-400 mt-2">Enter your credentials to continue</p>
            </div>
            <form id="loginForm" class="space-y-6">
                <div>
                    <label class="block text-sm font-medium mb-2">Username</label>
                    <input type="text" id="username" required
                        class="custom-input w-full px-4 py-3 rounded-lg text-lg">
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2">Password</label>
                    <input type="password" id="password" required
                        class="custom-input w-full px-4 py-3 rounded-lg text-lg">
                </div>
                <button type="submit"
                    class="w-full bg-blue-600 text-white py-4 rounded-lg hover:bg-blue-700 transition-all text-lg font-medium save-button">
                    Login to Dashboard
                </button>
            </form>
        </div>
    </div>

    <!-- Main Dashboard -->
    <div id="adminDashboard" class="hidden">
        <!-- Navigation -->
        <nav class="fixed w-full z-50 nav-blur">
            <div class="max-w-7xl mx-auto px-4">
                <div class="flex justify-between h-16 items-center">
                    <a href="#" class="text-2xl font-bold bg-gradient-to-r from-blue-400 to-blue-600 text-transparent bg-clip-text">
                        Nextap AI Admin
                    </a>
                    <div class="flex items-center space-x-6">
                        <button onclick="refresh()" class="text-gray-400 hover:text-white transition-colors">
                            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
                            </svg>
                        </button>
                        <button onclick="logout()" class="text-gray-400 hover:text-white transition-colors flex items-center gap-2">
                            <span>Logout</span>
                            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" />
                            </svg>
                        </button>
                    </div>
                </div>
            </div>
        </nav>

        <!-- Dashboard Content -->
        <div class="pt-20 pb-12 max-w-7xl mx-auto px-4">
            <!-- Stats Cards -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-12">
                <div class="stats-card rounded-xl p-6">
                    <div class="flex items-center justify-between">
                        <div>
                            <div class="text-4xl font-bold text-blue-400 mb-2">24</div>
                            <div class="text-gray-400">Active Projects</div>
                        </div>
                        <div class="text-blue-400">
                            <svg class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
                            </svg>
                        </div>
                    </div>
                </div>
                <div class="stats-card rounded-xl p-6">
                    <div class="flex items-center justify-between">
                        <div>
                            <div class="text-4xl font-bold text-blue-400 mb-2">8</div>
                            <div class="text-gray-400">Pending Approvals</div>
                        </div>
                        <div class="text-blue-400">
                            <svg class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                            </svg>
                        </div>
                    </div>
                </div>
                <div class="stats-card rounded-xl p-6">
                    <div class="flex items-center justify-between">
                        <div>
                            <div class="text-4xl font-bold text-blue-400 mb-2">92%</div>
                            <div class="text-gray-400">Completion Rate</div>
                        </div>
                        <div class="text-blue-400">
                            <svg class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />
                            </svg>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Project Management Section -->
            <div class="glass-card rounded-xl p-6 mb-8">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4 mb-8">
                    <h1 class="text-2xl font-bold bg-gradient-to-r from-blue-400 to-blue-600 text-transparent bg-clip-text">
                        Project Management
                    </h1>
                    <div class="flex flex-col md:flex-row gap-4 w-full md:w-auto">
                        <select id="statusFilter" class="custom-input rounded-lg px-4 py-2 min-w-[160px]">
                            <option value="">All Status</option>
                            <option value="active">Active</option>
                            <option value="pending">Pending</option>
                            <option value="completed">Completed</option>
                        </select>
                        <div class="relative">
                            <input type="text" id="searchInput" placeholder="Search projects..."
                                class="custom-input rounded-lg pl-10 pr-4 py-2 w-full md:w-64">
                            <svg class="w-5 h-5 text-gray-400 absolute left-3 top-1/2 transform -translate-y-1/2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
                            </svg>
                        </div>
                    </div>
                </div>

                <!-- Projects Table -->
                <div class="overflow-x-auto">
                    <table class="w-full admin-table">
                        <thead>
                            <tr class="border-b border-gray-800">
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Project Name</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Status</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Notes</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Actions</th>
                            </tr>
                        </thead>
                        <tbody id="projectsList" class="divide-y divide-gray-800">
                            <!-- Projects will be loaded here -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Constants
        const SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbxg8EzW2SELN_MTWrHloPAlryH1YmzT-e3QbAiO8z3RJg89QdovVcI0S8d0iL9agAzbxg/exec';
        const ADMIN_CREDENTIALS = {
            username: 'admin',
            password: 'password'
        };

        // State management
        let projectsData = [];
        let saveTimeouts = {};

        // Initialize dashboard
        function initAdminDashboard() {
            const isLoggedIn = sessionStorage.getItem('adminLoggedIn');
            if (!isLoggedIn) {
                showLoginModal();
            } else {
                hideLoginModal();
                loadProjects();
                initializeEventListeners();
            }
        }

        // Event Listeners
        function initializeEventListeners() {
            document.getElementById('searchInput').addEventListener('input', filterProjects);
            document.getElementById('statusFilter').addEventListener('change', filterProjects);
            
            // Auto-save functionality for notes
            document.addEventListener('input', (e) => {
                if (e.target.classList.contains('notes-input')) {
                    const projectId = e.target.closest('tr').dataset.projectId;
                    handleAutoSave(projectId, e.target.value);
                }
            });
        }

        // Login handling
        document.getElementById('loginForm').onsubmit = function(e) {
            e.preventDefault();
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;

            if (username === ADMIN_CREDENTIALS.username && 
                password === ADMIN_CREDENTIALS.password) {
                sessionStorage.setItem('adminLoggedIn', 'true');
                hideLoginModal();
                loadProjects();
            } else {
                showError('Invalid credentials');
            }
        };

        // Project loading and rendering
        async function loadProjects() {
            showLoadingState();
            try {
                const response = await fetch(`${SCRIPT_URL}?action=getProjects`);
                const data = await response.json();
                
                if (!data.success) {
                    throw new Error(data.message || 'Failed to load projects');
                }

                projectsData = data.projects;
                renderProjects(projectsData);
                updateStatistics();
            } catch (error) {
                showError('Error loading projects: ' + error.message);
            } finally {
                hideLoadingState();
            }
        }

        function renderProjects(projects) {
            const tbody = document.getElementById('projectsList');
            tbody.innerHTML = projects.map((project, index) => `
                <tr class="project-row" 
                    data-project-id="${project.project_id}"
                    style="animation-delay: ${index * 0.05}s">
                    <td class="px-6 py-4 whitespace-nowrap text-sm">
                        <span class="font-medium">${project.project_id}</span>
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm">
                        <div class="flex flex-col">
                            <span class="font-medium">${project.client_email}</span>
                            <span class="text-gray-400 text-xs">${project.client_name || ''}</span>
                        </div>
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm">
                        ${project.project_name}
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap">
                        <select class="status-select custom-input rounded-lg px-3 py-2 text-sm"
                                onchange="handleStatusChange(event, '${project.project_id}')">
                            <option value="pending" ${project.status === 'pending' ? 'selected' : ''}>Pending</option>
                            <option value="active" ${project.status === 'active' ? 'selected' : ''}>Active</option>
                            <option value="completed" ${project.status === 'completed' ? 'selected' : ''}>Completed</option>
                        </select>
                    </td>
                    <td class="px-6 py-4">
                        <textarea class="notes-input custom-input w-full rounded-lg px-3 py-2 text-sm min-h-[80px]">${project.proj_notes || ''}</textarea>
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm">
                        <button onclick="saveChanges('${project.project_id}')"
                                class="save-button bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition-all flex items-center gap-2">
                            <span>Save</span>
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
                            </svg>
                        </button>
                    </td>
                </tr>
            `).join('');
        }

        // Project updates
        async function handleStatusChange(event, projectId) {
            const status = event.target.value;
            try {
                await updateProjectStatus(projectId, status);
                showSuccess('Status updated successfully');
            } catch (error) {
                showError(error.message);
                // Revert the select to previous value
                event.target.value = projectsData.find(p => p.project_id === projectId).status;
            }
        }

        function handleAutoSave(projectId, notes) {
            // Clear existing timeout for this project
            if (saveTimeouts[projectId]) {
                clearTimeout(saveTimeouts[projectId]);
            }

            // Set new timeout
            saveTimeouts[projectId] = setTimeout(async () => {
                try {
                    await updateProjectNotes(projectId, notes);
                    showSuccess('Notes saved automatically', 2000);
                } catch (error) {
                    showError(error.message);
                }
            }, 1000);
        }

        async function saveChanges(projectId) {
            const row = document.querySelector(`tr[data-project-id="${projectId}"]`);
            const button = row.querySelector('button');
            const status = row.querySelector('.status-select').value;
            const notes = row.querySelector('.notes-input').value;

            try {
                button.disabled = true;
                button.innerHTML = '<span>Saving...</span>';

                await Promise.all([
                    updateProjectStatus(projectId, status),
                    updateProjectNotes(projectId, notes)
                ]);

                showSuccess('Changes saved successfully');
                button.innerHTML = '<span>Saved!</span><svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg>';
            } catch (error) {
                showError(error.message);
                button.innerHTML = '<span>Save</span>';
            } finally {
                setTimeout(() => {
                    button.disabled = false;
                    button.innerHTML = '<span>Save</span><svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg>';
                }, 2000);
            }
        }

        // API calls
        async function updateProjectStatus(projectId, status) {
            const response = await fetch(`${SCRIPT_URL}?action=updateProject`, {
                method: 'POST',
                body: new URLSearchParams({
                    project_id: projectId,
                    status: status
                })
            });

            const data = await response.json();
            if (!data.success) {
                throw new Error(data.message || 'Failed to update status');
            }

            // Update local data
            const project = projectsData.find(p => p.project_id === projectId);
            if (project) {
                project.status = status;
            }
        }

        async function updateProjectNotes(projectId, notes) {
            const response = await fetch(`${SCRIPT_URL}?action=updateProject`, {
                method: 'POST',
                body: new URLSearchParams({
                    project_id: projectId,
                    notes: notes
                })
            });

            const data = await response.json();
            if (!data.success) {
                throw new Error(data.message || 'Failed to update notes');
            }

            // Update local data
            const project = projectsData.find(p => p.project_id === projectId);
            if (project) {
                project.proj_notes = notes;
            }
        }

        // UI helpers
        function showLoginModal() {
            document.getElementById('loginModal').classList.remove('hidden');
            document.getElementById('adminDashboard').classList.add('hidden');
        }

        function hideLoginModal() {
            document.getElementById('loginModal').classList.add('hidden');
            document.getElementById('adminDashboard').classList.remove('hidden');
        }

        function logout() {
            sessionStorage.removeItem('adminLoggedIn');
            showLoginModal();
        }

        function refresh() {
            loadProjects();
        }

        function showLoadingState() {
            // Add loading state UI if needed
        }

        function hideLoadingState() {
            // Remove loading state UI if needed
        }

        function showSuccess(message, duration = 3000) {
            // Implement toast notification
            console.log('Success:', message);
        }

        function showError(message) {
            // Implement error notification
            console.error('Error:', message);
        }

        // Search and filter
        function filterProjects() {
            const searchTerm = document.getElementById('searchInput').value.toLowerCase();
            const statusFilter = document.getElementById('statusFilter').value;

            const filteredProjects = projectsData.filter(project => {
                const matchesSearch = 
                    project.project_id.toLowerCase().includes(searchTerm) ||
                    project.client_email.toLowerCase().includes(searchTerm) ||
                    project.project_name.toLowerCase().includes(searchTerm);
                const matchesStatus = !statusFilter || project.status === statusFilter;
                return matchesSearch && matchesStatus;
            });

            renderProjects(filteredProjects);
        }

        // Statistics
        function updateStatistics() {
            const activeProjects = projectsData.filter(p => p.status === 'active').length;
            const pendingProjects = projectsData.filter(p => p.status === 'pending').length;
            const completedProjects = projectsData.filter(p => p.status === 'completed').length;
            const totalProjects = projectsData.length;

            const completionRate = totalProjects ? Math.round((completedProjects / totalProjects) * 100) : 0;

            // Update stats in the UI
            document.querySelector('.stats-card:nth-child(1) .text-4xl').textContent = activeProjects;
            document.querySelector('.stats-card:nth-child(2) .text-4xl').textContent = pendingProjects;
            document.querySelector('.stats-card:nth-child(3) .text-4xl').textContent = `${completionRate}%`;
        }

        // Initialize on load
        window.addEventListener('load', initAdminDashboard);
    </script>
</body>
</html>

```

# blogs.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nextap AI - Blog</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.css" rel="stylesheet">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/aos/2.3.4/aos.css" rel="stylesheet">
    <style>
        :root {
            --primary-bg: #0a0a0a;
            --secondary-bg: #171717;
            --text-primary: #f3f4f6;
            --text-secondary: #9ca3af;
            --accent: #3b82f6;
        }

        body {
            background: var(--primary-bg);
            color: var(--text-primary);
            font-family: 'Inter', sans-serif;
        }

        /* Smooth scrolling */
        html {
            scroll-behavior: smooth;
        }

        /* Reading Progress Bar */
        .progress-bar {
            position: fixed;
            top: 0;
            left: 0;
            width: 0%;
            height: 3px;
            background: linear-gradient(to right, var(--accent), #60a5fa);
            z-index: 1000;
            transition: width 0.1s ease;
        }

        /* Blog Card Animations */
        .blog-card {
            background: var(--secondary-bg);
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            overflow: hidden;
        }

        .blog-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(
                45deg,
                transparent,
                rgba(59, 130, 246, 0.1),
                transparent
            );
            transform: translateX(-100%);
            transition: transform 0.6s;
        }

        .blog-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
        }

        .blog-card:hover::before {
            transform: translateX(100%);
        }

        /* Tag Pills */
        .tag {
            background: rgba(59, 130, 246, 0.1);
            border: 1px solid rgba(59, 130, 246, 0.2);
            transition: all 0.3s ease;
        }

        .tag:hover {
            background: rgba(59, 130, 246, 0.2);
            transform: translateY(-1px);
        }

        /* Search Bar Animation */
        .search-input {
            background: var(--secondary-bg);
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: all 0.3s ease;
        }

        .search-input:focus {
            border-color: var(--accent);
            box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.2);
        }

        /* Table of Contents */
        .toc {
            position: sticky;
            top: 100px;
            max-height: calc(100vh - 120px);
            overflow-y: auto;
            scrollbar-width: thin;
            scrollbar-color: var(--accent) var(--secondary-bg);
        }

        .toc::-webkit-scrollbar {
            width: 6px;
        }

        .toc::-webkit-scrollbar-track {
            background: var(--secondary-bg);
        }

        .toc::-webkit-scrollbar-thumb {
            background-color: var(--accent);
            border-radius: 3px;
        }

        .toc-link {
            position: relative;
            transition: all 0.3s ease;
        }

        .toc-link::before {
            content: '';
            position: absolute;
            left: -10px;
            top: 50%;
            transform: translateY(-50%);
            width: 3px;
            height: 0;
            background: var(--accent);
            transition: height 0.3s ease;
        }

        .toc-link.active {
            color: var(--accent);
            transform: translateX(5px);
        }

        .toc-link.active::before {
            height: 100%;
        }

        /* Newsletter form animation */
        .newsletter-input {
            background: var(--secondary-bg);
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: all 0.3s ease;
        }

        .newsletter-input:focus {
            border-color: var(--accent);
            box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.2);
        }

        /* Reading Time Indicator */
        .reading-time {
            font-size: 0.875rem;
            color: var(--text-secondary);
            display: flex;
            align-items: center;
            gap: 0.5rem;
        }

        /* Share Button Animation */
        .share-button {
            transition: all 0.3s ease;
        }

        .share-button:hover {
            transform: translateY(-2px);
            color: var(--accent);
        }

        /* Mobile Menu */
        .mobile-menu {
            transform: translateY(-100%);
            transition: transform 0.3s ease-in-out;
        }

        .mobile-menu.active {
            transform: translateY(0);
        }

        /* Quick Read Modal */
        .modal-overlay {
            background: rgba(0, 0, 0, 0.7);
            backdrop-filter: blur(5px);
        }

        .modal-content {
            background: var(--secondary-bg);
            border: 1px solid rgba(255, 255, 255, 0.1);
            max-width: 800px;
            width: 90%;
            max-height: 90vh;
            overflow-y: auto;
        }

        /* Tooltip styles */
        [data-tooltip] {
            position: relative;
        }

        [data-tooltip]:before {
            content: attr(data-tooltip);
            position: absolute;
            bottom: 100%;
            left: 50%;
            transform: translateX(-50%);
            padding: 0.5rem;
            background: rgba(0, 0, 0, 0.8);
            color: white;
            font-size: 0.875rem;
            border-radius: 0.25rem;
            white-space: nowrap;
            visibility: hidden;
            opacity: 0;
            transition: opacity 0.2s ease;
        }

        [data-tooltip]:hover:before {
            visibility: visible;
            opacity: 1;
        }
    </style>
</head>
<body>
    <!-- Reading Progress Bar -->
    <div class="progress-bar"></div>

    <!-- Navigation -->
    <nav class="fixed w-full z-40 bg-black bg-opacity-90 backdrop-blur-md">
        <div class="max-w-7xl mx-auto px-4">
            <div class="flex justify-between h-16 items-center">
                <a href="javascript:void(0)" onclick="navigateToIndex()" class="text-2xl font-bold bg-gradient-to-r from-blue-400 to-blue-600 text-transparent bg-clip-text">
                    Nextap AI
                </a>
                <div class="hidden md:flex items-center space-x-8">
                    <a href="javascript:void(0)" onclick="navigateToIndex()" class="text-gray-300 hover:text-white">Home</a>
                    <a href="#featured" class="text-gray-300 hover:text-white">Featured</a>
                    <a href="#categories" class="text-gray-300 hover:text-white">Categories</a>
                </div>
                <button class="md:hidden text-white" onclick="toggleMobileMenu()" data-tooltip="Toggle menu">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7"/>
                    </svg>
                </button>
            </div>
        </div>
        <!-- Mobile Menu -->
        <div class="mobile-menu md:hidden">
            <div class="flex flex-col space-y-4 px-4 py-2">
                <a href="javascript:void(0)" onclick="navigateToIndex()" class="text-gray-300 hover:text-white">Home</a>
                <a href="#featured" class="text-gray-300 hover:text-white">Featured</a>
                <a href="#categories" class="text-gray-300 hover:text-white">Categories</a>
            </div>
        </div>
    </nav>

    <!-- Quick Read Modal -->
    <div id="quickReadModal" class="fixed inset-0 z-50 hidden">
        <div class="modal-overlay absolute inset-0"></div>
        <div class="flex items-center justify-center min-h-screen px-4">
            <div class="modal-content w-full max-w-2xl bg-[#1a1a1a] rounded-2xl relative">
                <button onclick="closeQuickRead()" class="absolute top-4 left-4 p-2 bg-gray-800 hover:bg-gray-700 rounded-full transition-colors">
                    <svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
                    </path>
                </svg>
                <span class="sr-only">Close modal</span>
                <div class="p-8">
                    <!-- Header with icon and quick read label -->
                    <div class="flex items-center gap-3 mb-8">
                        <div class="bg-blue-600 w-12 h-12 flex items-center justify-center rounded-2xl">
                            <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                            </svg>
                        </div>
                        <div class="flex flex-col">
                            <span class="text-sm font-medium text-gray-200">QUICK READ</span>
                            <span id="quickReadDate" class="text-sm text-gray-500"></span>
                        </div>
                    </div>
                    
                    <!-- Article content -->
                    <div class="space-y-6">
                        <h2 id="quickReadTitle" class="text-2xl font-bold text-gray-100"></h2>
                        <div class="max-w-2xl mx-auto">
                            <img src="https://images.unsplash.com/photo-1523961131990-5ea7c61b2107" alt="AI Research" class="w-full h-48 object-cover rounded-xl" />
                        </div>
                        <div id="quickReadContent" class="prose prose-invert prose-gray max-w-none">
                            <!-- Content will be inserted here -->
                        </div>
                    </div>
                    
                    <!-- Footer with read more button -->
                    <div class="mt-8 space-y-4">
                        <a href="#" id="readFullArticle" class="inline-flex items-center justify-center px-6 py-2.5 bg-blue-600 hover:bg-blue-700 text-white font-medium rounded-lg transition-all duration-200 group">
                            <span>Read Full Article</span>
                            <svg class="w-5 h-5 ml-2 transform transition-transform group-hover:translate-x-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3"/>
                            </svg>
                        </a>
                        <div class="flex items-center gap-2 text-sm text-gray-400">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                            </svg>
                            <span>AI-powered summary for quick reading</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <style>
        .group:hover svg {
            transform: translateX(8px);
            transition: transform 0.2s ease-in-out;
        }
        
        .modal-content {
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
        }
        
        .modal-overlay {
            background: rgba(0, 0, 0, 0.7);
            backdrop-filter: blur(5px);
        }
        
        .prose-gray {
            --tw-prose-invert-body: #9ca3af;
            --tw-prose-invert-headings: #f3f4f6;
            --tw-prose-invert-links: #60a5fa;
        }
    </style>

    <!-- Hero Section -->
    <section class="pt-24 pb-12">
        <div class="max-w-4xl mx-auto px-4">
            <h1 class="text-4xl md:text-6xl font-bold text-center mb-6" data-aos="fade-up">
                Insights & Innovations
            </h1>
            <p class="text-xl text-gray-400 text-center mb-8" data-aos="fade-up" data-aos-delay="100">
                Explore the latest in academic solutions and technological advancements
            </p>
            <!-- Search Bar -->
            <div class="max-w-2xl mx-auto" data-aos="fade-up" data-aos-delay="200">
                <div class="relative">
                    <input type="text" 
                           class="search-input w-full px-4 py-3 rounded-lg text-white" 
                           placeholder="Search articles..."
                           data-tooltip="Search blog posts">
                    <svg class="absolute right-4 top-1/2 transform -translate-y-1/2 w-5 h-5 text-gray-400" 
                         fill="none" 
                         stroke="currentColor" 
                         viewBox="0 0 24 24">
                        <path stroke-linecap="round" 
                              stroke-linejoin="round" 
                              stroke-width="2" 
                              d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/>
                    </svg>
                </div>
            </div>
        </div>
    </section>

    <!-- Blog Sections -->
    <style>
    .blog-card {
        height: 450px;
        background: rgba(31, 41, 55, 0.5);
        transition: transform 0.3s ease;
    }

    .blog-card:hover {
        transform: translateY(-4px);
    }

    .image-container {
        height: 200px;
    }

    .content-container {
        height: 250px;
        display: flex;
        flex-direction: column;
    }

    .blog-card h3 {
        display: -webkit-box;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;
        overflow: hidden;
    }

    .blog-card p {
        display: -webkit-box;
        -webkit-line-clamp: 3;
        -webkit-box-orient: vertical;
        overflow: hidden;
    }

    .category-filter {
        padding: 0.5rem 1rem;
        border-radius: 0.5rem;
        font-size: 0.875rem;
        color: #9CA3AF;
        background: rgba(31, 41, 55, 0.5);
        transition: all 0.3s ease;
    }

    .category-filter:hover {
        background: rgba(37, 99, 235, 0.1);
        color: #60A5FA;
    }

    .category-filter.active {
        background: rgba(37, 99, 235, 0.2);
        color: #60A5FA;
    }

    .quick-read-data {
        display: none;
    }
    </style>
    <!-- Featured Posts -->
    <section id="featured" class="py-12 bg-black">
        <div class="max-w-7xl mx-auto px-4">
            <h2 class="text-3xl font-bold mb-8 bg-clip-text text-transparent bg-gradient-to-r from-blue-400 to-purple-600">Editor's Pick</h2>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
                <a href="blog1.html" class="block">
                    <article class="blog-card rounded-xl overflow-hidden">
                        <div class="image-container relative overflow-hidden">
                            <img src="https://images.unsplash.com/photo-1523961131990-5ea7c61b2107" 
                                 alt="AI Research" 
                                 class="w-full h-full object-cover transform group-hover:scale-110 transition-transform duration-500">
                            <div class="absolute top-4 right-4">
                                <span class="tag px-3 py-1 bg-blue-600 text-white text-sm rounded-full">Featured</span>
                            </div>
                        </div>
                        <div class="p-6 bg-gray-800/50 content-container">
                            <h3 class="text-xl font-bold text-white mb-3">The Rise of AI in Healthcare</h3>
                            <p class="text-gray-400 mb-4">Revolutionary developments in AI-powered diagnostics and personalized medicine are transforming patient care...</p>
                            <div class="flex items-center justify-between mt-auto">
                                <div class="flex items-center gap-2">
                                    <div class="w-8 h-8 rounded-full bg-gray-700"></div>
                                    <span class="text-sm text-gray-400">Dr. Sarah Miller</span>
                                </div>
                                <button onclick="openQuickRead(this); event.preventDefault();" class="quick-read-btn p-2 hover:bg-gray-700 rounded-full transition-colors">
                                    <svg class="w-5 h-5 text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                                    </svg>
                                </button>
                            </div>
                            <div class="quick-read-data">
                                {
                                    "title": "The Rise of AI in Healthcare",
                                    "date": "January 18, 2025",
                                    "summary": "<p>Artificial Intelligence is revolutionizing healthcare delivery and patient outcomes.</p><p>Key breakthroughs:</p><ul><li>Early disease detection with 99% accuracy</li><li>Personalized treatment plans based on genetic markers</li><li>Real-time health monitoring via AI-powered wearables</li><li>Automated drug discovery reducing research time by 60%</li><li>AI-assisted surgeries with enhanced precision</li></ul><p>Impact: 45% reduction in diagnostic errors and 30% improvement in treatment efficacy.</p>"
                                }
                            </div>
                        </div>
                    </article>
                </a>
            </div>
        </div>
    </section>
    <!-- Quick Read Modal -->
    <div id="quickReadModal" class="hidden fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center">
        <div class="bg-gray-900 rounded-xl max-w-2xl w-full mx-4 overflow-hidden">
            <div class="p-6">
                <div class="flex items-center gap-3 mb-6">
                    <div class="w-10 h-10 bg-blue-600 rounded-xl flex items-center justify-center">
                        <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                        </svg>
                    </div>
                    <h2 class="text-xl font-bold text-white">Quick Read</h2>
                    <button onclick="closeQuickRead()" class="ml-auto hover:bg-gray-800 p-2 rounded-lg">
                        <svg class="w-6 h-6 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
                        </svg>
                    </button>
                </div>
                <div class="space-y-4">
                    <h3 id="quickReadTitle" class="text-2xl font-bold text-white"></h3>
                    <p id="quickReadDate" class="text-gray-400"></p>
                    <div id="quickReadContent" class="prose prose-invert max-w-none"></div>
                </div>
                <a href="#" class="inline-flex items-center gap-2 bg-blue-600 text-white px-6 py-3 rounded-lg mt-6">
                    Read Full Article
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"/>
                    </svg>
                </a>
            </div>
        </div>
    </div>
    <script>
    function closeQuickRead() {
        const modal = document.getElementById('quickReadModal');
        modal.classList.add('hidden');
        document.body.style.overflow = 'auto';
    }
    
    // Close modal when clicking outside
    document.getElementById('quickReadModal').addEventListener('click', function(e) {
        if (e.target === this) {
            closeQuickRead();
        }
    });
    </script>

    <!-- Innovation Hub -->
    <section class="py-16 bg-gradient-to-b from-gray-900 to-black">
        <div class="max-w-7xl mx-auto px-4">
            <div class="flex items-center justify-between mb-12">
                <h2 class="text-3xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-blue-400 to-purple-600">
                    ⚡ Innovation Hub
                </h2>
                <div class="flex gap-3">
                    <button class="category-filter active" data-category="all">All</button>
                    <button class="category-filter" data-category="ai">AI</button>
                    <button class="category-filter" data-category="tech">Tech</button>
                    <button class="category-filter" data-category="research">Research</button>
                </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-8" id="insights-grid">
                <!-- Innovation Hub Cards -->
                    <!-- Card 1 - AI -->
                    <a href="blog1.html" class="block">
                        <article class="blog-card rounded-xl overflow-hidden group" data-category="ai">
                            <div class="image-container relative overflow-hidden">
                                <img src="https://images.unsplash.com/photo-1523961131990-5ea7c61b2107" 
                                     alt="AI Research" 
                                     class="w-full h-full object-cover transform group-hover:scale-110 transition-transform duration-500">
                                <div class="absolute top-4 right-4">
                                    <span class="tag px-3 py-1 bg-blue-600 text-white text-sm rounded-full">AI</span>
                                </div>
                            </div>
                            <div class="p-6 bg-gray-800/50 content-container">
                                <h3 class="text-xl font-bold text-white mb-3">The Future of Academic Research</h3>
                                <p class="text-gray-400 mb-4">Exploring how AI is transforming scholarly research and academic writing with groundbreaking innovations in natural language processing...</p>
                                <div class="flex items-center justify-between mt-auto">
                                    <div class="flex items-center gap-2">
                                        <div class="w-8 h-8 rounded-full bg-gray-700"></div>
                                        <span class="text-sm text-gray-400">John Doe</span>
                                    </div>
                                    <button onclick="openQuickRead(this); event.preventDefault();" class="quick-read-btn p-2 hover:bg-gray-700 rounded-full transition-colors">
                                        <svg class="w-5 h-5 text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                                        </svg>
                                    </button>
                                </div>
                                <div class="quick-read-data">
                                    {
                                        "title": "The Future of Academic Research",
                                        "date": "January 18, 2025",
                                        "summary": "<p>The landscape of academic research is undergoing a profound transformation with AI integration.</p><p>Key developments include:</p><ul><li>AI-powered literature reviews</li><li>Advanced NLP for writing</li><li>Automated analysis</li><li>Enhanced collaboration</li></ul>"
                                    }
                                </div>
                            </div>
                        </article>
                    </a>
                
                    <!-- Card 2 - Tech -->
                    <a href="blog2.html" class="block">
                        <article class="blog-card rounded-xl overflow-hidden group" data-category="tech">
                            <div class="image-container relative overflow-hidden">
                                <img src="https://images.unsplash.com/photo-1558346547-4439467bd1d5" 
                                     alt="Edge Computing" 
                                     class="w-full h-full object-cover transform group-hover:scale-110 transition-transform duration-500">
                                <div class="absolute top-4 right-4">
                                    <span class="tag px-3 py-1 bg-green-600 text-white text-sm rounded-full">Tech</span>
                                </div>
                            </div>
                            <div class="p-6 bg-gray-800/50 content-container">
                                <h3 class="text-xl font-bold text-white mb-3">Edge Computing Revolution</h3>
                                <p class="text-gray-400 mb-4">Exploring how edge computing is transforming real-time data processing and enabling new IoT applications...</p>
                                <div class="flex items-center justify-between mt-auto">
                                    <div class="flex items-center gap-2">
                                        <div class="w-8 h-8 rounded-full bg-gray-700"></div>
                                        <span class="text-sm text-gray-400">Sarah Chen</span>
                                    </div>
                                    <button onclick="openQuickRead(this); event.preventDefault();" class="quick-read-btn p-2 hover:bg-gray-700 rounded-full transition-colors">
                                        <svg class="w-5 h-5 text-green-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                                        </svg>
                                    </button>
                                </div>
                                <div class="quick-read-data">
                                    {
                                        "title": "Edge Computing Revolution",
                                        "date": "January 17, 2025",
                                        "summary": "<p>Edge computing is revolutionizing data processing.</p><p>Key innovations:</p><ul><li>5G integration</li><li>AI-powered analytics</li><li>Distributed architectures</li><li>Enhanced security</li></ul>"
                                    }
                                </div>
                            </div>
                        </article>
                    </a>
                
                    <!-- Card 3 - Research -->
                    <a href="blog3.html" class="block">
                        <article class="blog-card rounded-xl overflow-hidden group" data-category="research">
                            <div class="image-container relative overflow-hidden">
                                <img src="https://images.unsplash.com/photo-1532094349884-543bc11b234d" 
                                     alt="Quantum Computing" 
                                     class="w-full h-full object-cover transform group-hover:scale-110 transition-transform duration-500">
                                <div class="absolute top-4 right-4">
                                    <span class="tag px-3 py-1 bg-purple-600 text-white text-sm rounded-full">Research</span>
                                </div>
                            </div>
                            <div class="p-6 bg-gray-800/50 content-container">
                                <h3 class="text-xl font-bold text-white mb-3">Quantum Computing Breakthrough</h3>
                                <p class="text-gray-400 mb-4">New research reveals potential for stable quantum bits at room temperature, revolutionizing quantum computing...</p>
                                <div class="flex items-center justify-between mt-auto">
                                    <div class="flex items-center gap-2">
                                        <div class="w-8 h-8 rounded-full bg-gray-700"></div>
                                        <span class="text-sm text-gray-400">Dr. Alex Morgan</span>
                                    </div>
                                    <button onclick="openQuickRead(this); event.preventDefault();" class="quick-read-btn p-2 hover:bg-gray-700 rounded-full transition-colors">
                                        <svg class="w-5 h-5 text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                                        </svg>
                                    </button>
                                </div>
                                <div class="quick-read-data">
                                    {
                                        "title": "Quantum Computing Breakthrough",
                                        "date": "January 16, 2025",
                                        "summary": "<p>Revolutionary breakthrough in quantum computing stability.</p><p>Key findings:</p><ul><li>Room temperature operation</li><li>Extended coherence time</li><li>Scalable architecture</li><li>Practical applications</li></ul>"
                                    }
                                </div>
                            </div>
                        </article>
                    </a>
                
                    <!-- Card 4 - AI -->
                    <a href="blog4.html" class="block">
                        <article class="blog-card rounded-xl overflow-hidden group" data-category="ai">
                            <div class="image-container relative overflow-hidden">
                                <img src="https://images.unsplash.com/photo-1485827404703-89b55fcc595e" 
                                     alt="Neural Networks" 
                                     class="w-full h-full object-cover transform group-hover:scale-110 transition-transform duration-500">
                                <div class="absolute top-4 right-4">
                                    <span class="tag px-3 py-1 bg-blue-600 text-white text-sm rounded-full">AI</span>
                                </div>
                            </div>
                            <div class="p-6 bg-gray-800/50 content-container">
                                <h3 class="text-xl font-bold text-white mb-3">Neural Networks Evolution</h3>
                                <p class="text-gray-400 mb-4">Breaking down the latest advancements in neural architecture search and self-adapting networks...</p>
                                <div class="flex items-center justify-between mt-auto">
                                    <div class="flex items-center gap-2">
                                        <div class="w-8 h-8 rounded-full bg-gray-700"></div>
                                        <span class="text-sm text-gray-400">Maria Torres</span>
                                    </div>
                                    <button onclick="openQuickRead(this); event.preventDefault();" class="quick-read-btn p-2 hover:bg-gray-700 rounded-full transition-colors">
                                        <svg class="w-5 h-5 text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                                        </svg>
                                    </button>
                                </div>
                                <div class="quick-read-data">
                                    {
                                        "title": "Neural Networks Evolution",
                                        "date": "January 15, 2025",
                                        "summary": "<p>Neural networks are becoming more sophisticated and efficient.</p><p>Recent advances:</p><ul><li>Automated architecture design</li><li>Self-optimizing networks</li><li>Reduced training time</li><li>Improved accuracy</li></ul>"
                                    }
                                </div>
                            </div>
                        </article>
                    </a>
                
                    <!-- Card 5 - Tech -->
                    <a href="blog5.html" class="block">
                        <article class="blog-card rounded-xl overflow-hidden group" data-category="tech">
                            <div class="image-container relative overflow-hidden">
                                <img src="https://images.unsplash.com/photo-1451187580459-43490279c0fa" 
                                     alt="Cloud Computing" 
                                     class="w-full h-full object-cover transform group-hover:scale-110 transition-transform duration-500">
                                <div class="absolute top-4 right-4">
                                    <span class="tag px-3 py-1 bg-green-600 text-white text-sm rounded-full">Tech</span>
                                </div>
                            </div>
                            <div class="p-6 bg-gray-800/50 content-container">
                                <h3 class="text-xl font-bold text-white mb-3">Next-Gen Cloud Architecture</h3>
                                <p class="text-gray-400 mb-4">Exploring the future of cloud computing with quantum-ready infrastructure and advanced security...</p>
                                <div class="flex items-center justify-between mt-auto">
                                    <div class="flex items-center gap-2">
                                        <div class="w-8 h-8 rounded-full bg-gray-700"></div>
                                        <span class="text-sm text-gray-400">James Wilson</span>
                                    </div>
                                    <button onclick="openQuickRead(this); event.preventDefault();" class="quick-read-btn p-2 hover:bg-gray-700 rounded-full transition-colors">
                                        <svg class="w-5 h-5 text-green-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                                        </svg>
                                    </button>
                                </div>
                                <div class="quick-read-data">
                                    {
                                        "title": "Next-Gen Cloud Architecture",
                                        "date": "January 14, 2025",
                                        "summary": "<p>Cloud computing is evolving rapidly.</p><p>Key developments:</p><ul><li>Quantum integration</li><li>Zero-trust security</li><li>Automated scaling</li><li>Green computing</li></ul>"
                                    }
                                </div>
                            </div>
                        </article>
                    </a>
                
                    <!-- Card 6 - Research -->
                    <a href="blog6.html" class="block">
                        <article class="blog-card rounded-xl overflow-hidden group" data-category="research">
                            <div class="image-container relative overflow-hidden">
                                <img src="https://images.unsplash.com/photo-1507668077129-56e32842fceb" 
                                     alt="Sustainable Computing" 
                                     class="w-full h-full object-cover transform group-hover:scale-110 transition-transform duration-500">
                                <div class="absolute top-4 right-4">
                                    <span class="tag px-3 py-1 bg-purple-600 text-white text-sm rounded-full">Research</span>
                                </div>
                            </div>
                            <div class="p-6 bg-gray-800/50 content-container">
                                <h3 class="text-xl font-bold text-white mb-3">Green Computing Initiative</h3>
                                <p class="text-gray-400 mb-4">Revolutionary approaches to sustainable computing and eco-friendly data center management...</p>
                                <div class="flex items-center justify-between mt-auto">
                                    <div class="flex items-center gap-2">
                                        <div class="w-8 h-8 rounded-full bg-gray-700"></div>
                                        <span class="text-sm text-gray-400">Emma Green</span>
                                    </div>
                                    <button onclick="openQuickRead(this); event.preventDefault();" class="quick-read-btn p-2 hover:bg-gray-700 rounded-full transition-colors">
                                        <svg class="w-5 h-5 text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                                        </svg>
                                    </button>
                                </div>
                                <div class="quick-read-data">
                                    {
                                        "title": "Green Computing Initiative",
                                        "date": "January 13, 2025",
                                        "summary": "<p>Sustainable computing is becoming a priority.</p><p>Focus areas:</p><ul><li>Energy efficiency</li><li>Carbon-neutral operations</li><li>Waste reduction</li><li>Renewable integration</li></ul>"
                                    }
                                </div>
                            </div>
                        </article>
                    </a>
                </div>
            </div>
        </div>
    </section>

    <script>
    function openQuickRead(button) {
        try {
            const article = button.closest('article');
            const dataElement = article.querySelector('.quick-read-data');
            if (!dataElement) throw new Error('No quick read data found');
            
            const data = JSON.parse(dataElement.textContent.trim());
            const modal = document.getElementById('quickReadModal');
            
            if (!data.title || !data.summary) throw new Error('Invalid quick read data');
            
            document.getElementById('quickReadTitle').textContent = data.title;
            document.getElementById('quickReadContent').innerHTML = data.summary;
            document.getElementById('quickReadDate').textContent = data.date || '';
            
            modal.classList.remove('hidden');
            document.body.style.overflow = 'hidden';
        } catch (error) {
            console.error('Quick read error:', error);
        }
    }
    </script>

    <!-- Newsletter Section -->
    <section class="py-12 bg-secondary-bg">
        <div class="max-w-4xl mx-auto px-4 text-center">
            <h2 class="text-2xl font-bold mb-4" data-aos="fade-up">Stay Updated</h2>
            <p class="text-gray-400 mb-8" data-aos="fade-up" data-aos-delay="100">
                Subscribe to our newsletter for the latest insights and updates
            </p>
            <form class="max-w-md mx-auto" data-aos="fade-up" data-aos-delay="200">
                <div class="flex gap-4">
                    <input type="email" 
                           class="newsletter-input flex-1 px-4 py-2 rounded-lg text-white" 
                           placeholder="Enter your email"
                           data-tooltip="Enter email to subscribe">
                    <button type="submit" 
                            class="bg-blue-600 px-6 py-2 rounded-lg hover:bg-blue-700 transition-all"
                            data-tooltip="Subscribe to newsletter">
                        Subscribe
                    </button>
                </div>
            </form>
        </div>
    </section>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/aos/2.3.4/aos.js"></script>
    <script>
        // Initialize AOS
        AOS.init({
            duration: 800,
            once: true
        });
        function navigateToIndex() {
            const currentPath = window.location.pathname;
            const targetUrl = currentPath.substring(0, currentPath.lastIndexOf('/')) + '/index.html';
            window.location.href = targetUrl;
        }

        // Reading Progress Bar
        window.addEventListener('scroll', () => {
            const winScroll = document.body.scrollTop || document.documentElement.scrollTop;
            const height = document.documentElement.scrollHeight - document.documentElement.clientHeight;
            const scrolled = (winScroll / height) * 100;
            document.querySelector('.progress-bar').style.width = scrolled + '%';
        });

        // Mobile Menu Toggle
        function toggleMobileMenu() {
            const mobileMenu = document.querySelector('.mobile-menu');
            mobileMenu.classList.toggle('active');
        }
        
        function closeQuickRead() {
            const modal = document.getElementById('quickReadModal');
            modal.classList.add('hidden');
            document.body.style.overflow = '';
        }

        // Example quick read data
        const quickReadData = {
            title: "The Future of Academic Research",
            date: "January 18, 2025",
            summary: `
                <p>The landscape of academic research is undergoing a profound transformation with the integration of artificial intelligence and machine learning technologies.</p>
                <p>Key developments include:</p>
                <ul>
                    <li>AI-powered literature reviews and meta-analyses</li>
                    <li>Advanced natural language processing for academic writing</li>
                    <li>Automated data collection and analysis tools</li>
                    <li>Enhanced collaboration through AI platforms</li>
                </ul>
                <p>These innovations are reshaping how researchers approach their work, leading to more efficient and comprehensive studies.</p>
            `,
            url: "blog/full-article.html"
        };

        // Share functionality
        document.querySelectorAll('.share-button').forEach(button => {
            button.addEventListener('click', async () => {
                const post = button.closest('article');
                const title = post.querySelector('h3').textContent;
                const url = window.location.href;

                try {
                    if (navigator.share) {
                        await navigator.share({
                            title: title,
                            url: url
                        });
                    } else {
                        // Fallback for browsers that don't support Web Share API
                        const tempInput = document.createElement('input');
                        tempInput.value = url;
                        document.body.appendChild(tempInput);
                        tempInput.select();
                        document.execCommand('copy');
                        document.body.removeChild(tempInput);
                        
                        // Show tooltip
                        button.setAttribute('data-tooltip', 'Link copied!');
                        setTimeout(() => {
                            button.setAttribute('data-tooltip', 'Share article');
                        }, 2000);
                    }
                } catch (err) {
                    console.error('Share failed:', err);
                }
            });
        });

        // Search functionality with debouncing
        const searchInput = document.querySelector('.search-input');
        const blogPosts = document.querySelectorAll('.blog-card');
        let searchTimeout;

        searchInput.addEventListener('input', (e) => {
            clearTimeout(searchTimeout);
            searchTimeout = setTimeout(() => {
                const searchTerm = e.target.value.toLowerCase();
                
                blogPosts.forEach(post => {
                    const titleElement = post.querySelector('h3');
                    const contentElement = post.querySelector('p');
                    const tagElement = post.querySelector('.tag'); // Ensure .tag exists
                    
                    const title = titleElement ? titleElement.textContent.toLowerCase() : '';
                    const content = contentElement ? contentElement.textContent.toLowerCase() : '';
                    const tags = tagElement ? tagElement.textContent.toLowerCase() : '';
                    
                    const matches = title.includes(searchTerm) || 
                                    content.includes(searchTerm) || 
                                    tags.includes(searchTerm);
                    
                    post.style.display = matches ? 'block' : 'none';
                    post.style.opacity = matches ? '1' : '0';
                    post.style.transform = matches ? 'translateY(0)' : 'translateY(20px)';
                });
            }, 300);
        });

        // Newsletter form submission
        const newsletterForm = document.querySelector('form');
        newsletterForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const emailInput = newsletterForm.querySelector('input[type="email"]');
            const email = emailInput.value;

            try {
                // Here you would typically send this to your backend
                console.log('Newsletter subscription:', email);
                
                // Show success message
                const successMessage = document.createElement('div');
                successMessage.className = 'text-green-500 mt-2';
                successMessage.textContent = 'Successfully subscribed!';
                newsletterForm.appendChild(successMessage);
                
                // Clear input
                emailInput.value = '';
                
                // Remove success message after 3 seconds
                setTimeout(() => {
                    successMessage.remove();
                }, 3000);
            } catch (error) {
                console.error('Newsletter subscription failed:', error);
            }
        });
        
        // Close modal when clicking outside
        document.getElementById('quickReadModal').addEventListener('click', (e) => {
            if (e.target.classList.contains('modal-overlay')) {
                closeQuickRead();
            }
        });

        // Escape key to close modal
        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape') {
                closeQuickRead();
            }
        });
    </script>
</body>
</html>

```

# dashboard.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Project Dashboard - Nextap AI</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.css" rel="stylesheet">
    <style>
        :root {
            --primary-color: #171717;
            --secondary-color: #404040;
            --accent-color: #3b82f6;
            --text-color: #f3f4f6;
        }

        body {
            background-color: var(--primary-color);
            color: var(--text-color);
        }

        .glass-card {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: transform 0.3s ease;
        }

        .glass-card:hover {
            transform: translateY(-2px);
        }

        .status-badge {
            padding: 0.25rem 0.75rem;
            border-radius: 9999px;
            font-size: 0.875rem;
        }

        .status-active {
            background-color: rgba(34, 197, 94, 0.1);
            color: #22c55e;
        }

        .status-pending {
            background-color: rgba(234, 179, 8, 0.1);
            color: #eab308;
        }

        .status-completed {
            background-color: rgba(59, 130, 246, 0.1);
            color: #3b82f6;
        }

        .status-cancelled {
            background-color: rgba(239, 68, 68, 0.1);
            color: #ef4444;
        }

        .timeline-item {
            position: relative;
            padding-left: 2rem;
            padding-bottom: 2rem;
            animation: slideIn 0.3s ease-out forwards;
        }

        .timeline-dot {
            position: absolute;
            left: -0.375rem;
            top: 0.25rem;
            width: 1rem;
            height: 1rem;
            border-radius: 9999px;
            background: var(--accent-color);
            border: 2px solid rgba(255, 255, 255, 0.1);
        }

        .timeline-line {
            position: absolute;
            left: 0;
            top: 0;
            bottom: 0;
            width: 2px;
            background: rgba(255, 255, 255, 0.1);
        }

        .message-bubble {
            max-width: 80%;
            padding: 1rem;
            border-radius: 1rem;
            margin-bottom: 1rem;
        }

        .message-client {
            background: rgba(59, 130, 246, 0.1);
            margin-left: auto;
            border-bottom-right-radius: 0.25rem;
        }

        .message-team {
            background: rgba(255, 255, 255, 0.05);
            margin-right: auto;
            border-bottom-left-radius: 0.25rem;
        }

        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateX(-20px);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }

        .loading-shimmer {
            background: linear-gradient(
                90deg,
                rgba(255, 255, 255, 0.05) 25%,
                rgba(255, 255, 255, 0.1) 50%,
                rgba(255, 255, 255, 0.05) 75%
            );
            background-size: 200% 100%;
            animation: shimmer 1.5s infinite;
        }

        @keyframes shimmer {
            to {
                background-position: -200% 0;
            }
        }

        .toast {
            position: fixed;
            bottom: 1rem;
            right: 1rem;
            padding: 1rem;
            border-radius: 0.5rem;
            background: rgba(59, 130, 246, 0.9);
            color: white;
            transform: translateY(100%);
            opacity: 0;
            transition: all 0.3s ease;
            z-index: 1000;
        }

        .toast.show {
            transform: translateY(0);
            opacity: 1;
        }

        /* Search bar enhancement */
        .search-container {
            position: relative;
        }

        .search-container input {
            padding-left: 2.5rem;
            transition: all 0.3s ease;
        }

        .search-container svg {
            position: absolute;
            left: 0.75rem;
            top: 50%;
            transform: translateY(-50%);
            color: #6b7280;
            transition: all 0.3s ease;
        }

        .search-container input:focus {
            border-color: var(--accent-color);
            box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.1);
        }

        .search-container input:focus + svg {
            color: var(--accent-color);
        }

        /* Enhanced scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
        }

        ::-webkit-scrollbar-track {
            background: rgba(255, 255, 255, 0.05);
            border-radius: 4px;
        }

        ::-webkit-scrollbar-thumb {
            background: rgba(59, 130, 246, 0.3);
            border-radius: 4px;
        }

        ::-webkit-scrollbar-thumb:hover {
            background: rgba(59, 130, 246, 0.5);
        }
    </style>
</head>
<body class="min-h-screen">
    <!-- Navigation -->
    <nav class="fixed w-full z-40 bg-black bg-opacity-90 backdrop-blur-md">
        <div class="max-w-7xl mx-auto px-4">
            <div class="flex justify-between h-16 items-center">
                <a href="index.html" class="text-2xl font-bold bg-gradient-to-r from-blue-400 to-blue-600 text-transparent bg-clip-text">
                    Nextap AI
                </a>
                <div class="flex items-center gap-4">
                    <div id="userEmail" class="text-gray-400"></div>
                    <button onclick="logout()" class="text-gray-400 hover:text-white transition-colors">
                        Logout
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- Main Content -->
    <div class="pt-20 max-w-7xl mx-auto px-4">
        <!-- Project Lookup -->
        <div class="mb-12">
            <div class="glass-card p-6 rounded-xl">
                <div class="search-container">
                    <input type="text" id="lookupInput" 
                           placeholder="Enter Request ID or Email" 
                           class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white"
                           onkeypress="if(event.key === 'Enter') lookupProject()">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
                              d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/>
                    </svg>
                    <button onclick="lookupProject()" 
                            class="absolute right-3 top-1/2 transform -translate-y-1/2 bg-blue-600 text-white px-6 py-2 rounded-lg hover:bg-blue-700 transition-all">
                        Look Up
                    </button>
                </div>
            </div>
        </div>

        <!-- Project Overview -->
        <div id="projectOverview" class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-12">
            <!-- Project cards will be dynamically inserted here -->
        </div>

        <!-- Project Details -->
        <div id="projectDetails" class="grid grid-cols-1 md:grid-cols-2 gap-8 opacity-0 transition-opacity duration-300">
            <!-- Timeline -->
            <div class="glass-card p-6 rounded-xl">
                <div class="flex justify-between items-center mb-6">
                    <h3 class="text-xl font-semibold">Project Timeline</h3>
                    <div id="statusControls" class="flex gap-2">
                        <!-- Status update buttons will be dynamically inserted here -->
                    </div>
                </div>
                <div id="timeline" class="space-y-6">
                    <!-- Timeline items will be dynamically inserted here -->
                </div>
            </div>

            <!-- Communication -->
            <div class="glass-card p-6 rounded-xl">
                <h3 class="text-xl font-semibold mb-6">Project Communication</h3>
                <div id="messageContainer" class="h-96 overflow-y-auto mb-6 space-y-4 p-4">
                    <!-- Messages will be dynamically inserted here -->
                </div>
                <div class="flex gap-4">
                    <textarea id="messageInput" 
                              placeholder="Type your message..." 
                              class="flex-1 bg-gray-800 border border-gray-700 rounded-lg p-3 text-white resize-none"
                              rows="3"></textarea>
                    <button onclick="sendMessage()" 
                            class="bg-blue-600 text-white px-6 self-end py-3 rounded-lg hover:bg-blue-700 transition-all flex items-center gap-2">
                        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
                                  d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"/>
                        </svg>
                        Send
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- Toast Notification -->
    <div id="toast" class="toast">
        <span id="toastMessage"></span>
    </div>

    <!-- Loading Spinner -->
    <div id="loadingSpinner" class="hidden fixed inset-0 bg-black bg-opacity-50 backdrop-blur-sm flex items-center justify-center z-50">
        <div class="animate-spin rounded-full h-16 w-16 border-t-2 border-b-2 border-blue-500"></div>
    </div>

    <script>
        // Configuration
        const CONFIG = {
            SCRIPT_URL: 'https://script.google.com/macros/s/AKfycbxg8EzW2SELN_MTWrHloPAlryH1YmzT-e3QbAiO8z3RJg89QdovVcI0S8d0iL9agAzbxg/exec', // Replace with your actual deployed script URL
            STATUSES: {
                'Pending': { color: 'yellow', next: ['Consultation Scheduled', 'Cancelled'] },
                'Consultation Scheduled': { color: 'blue', next: ['In Progress', 'Cancelled'] },
                'In Progress': { color: 'green', next: ['Under Review', 'Cancelled'] },
                'Under Review': { color: 'purple', next: ['Completed', 'In Progress'] },
                'Completed': { color: 'green', next: [] },
                'Cancelled': { color: 'red', next: ['Pending'] }
            }
        };

        // State Management
        let currentProject = null;
        let messagePollingInterval = null;

        // Initialize
        document.addEventListener('DOMContentLoaded', () => {
            // checkAuth();
            initializeEventListeners();
            
            // Check URL parameters for direct project lookup
            const urlParams = new URLSearchParams(window.location.search);
            const lookupValue = urlParams.get('lookup');
            if (lookupValue) {
                document.getElementById('lookupInput').value = lookupValue;
                lookupProject();
            }
        });

        // Authentication
        // function checkAuth() {
        //     const email = localStorage.getItem('userEmail');
        //     if (!email) {
        //         window.location.href = 'index.html';
        //         return;
        //     }
        //     document.getElementById('userEmail').textContent = email;
        // }

        // function logout() {
        //     localStorage.removeItem('userEmail');
        //     window.location.href = 'index.html';
        // }

        // Event Listeners
        function initializeEventListeners() {
            const messageInput = document.getElementById('messageInput');
            messageInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter' && !e.shiftKey) {
                    e.preventDefault();
                    sendMessage();
                }
            });

            const lookupInput = document.getElementById('lookupInput');
            lookupInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    lookupProject();
                }
            });
        }

        // Project Lookup
        async function lookupProject() {
            const input = document.getElementById('lookupInput').value.trim();
            if (!input) {
                showToast('Please enter a Request ID or Email', 'error');
                return;
            }

            showSpinner();
            try {
                const response = await fetch(`${CONFIG.SCRIPT_URL}?action=lookup&query=${encodeURIComponent(input)}`);
                const data = await response.json();
                
                if (!data.success) {
                    throw new Error(data.message || 'Failed to find project');
                }

                updateDashboard(data.projects);
            } catch (error) {
                console.error('Lookup error:', error);
                showToast(error.message || 'Error looking up project', 'error');
            } finally {
                hideSpinner();
            }
        }

        // Dashboard Updates
        function updateDashboard(projects) {
            if (!projects.length) {
                showToast('No projects found', 'info');
                return;
            }

            const overview = document.getElementById('projectOverview');
            overview.innerHTML = '';
            
            projects.forEach((project, index) => {
                overview.appendChild(createProjectCard(project));
            });

            // Show first project details
            showProjectDetails(projects[0]);
            
            // Show project details section
            document.getElementById('projectDetails').style.opacity = '1';
        }

        function createProjectCard(project) {
            const card = document.createElement('div');
            card.className = 'glass-card p-6 rounded-xl cursor-pointer';
            card.onclick = () => showProjectDetails(project);

            const statusClass = `status-${project.status.toLowerCase().replace(/\s+/g, '-')}`;
            
            card.innerHTML = `
                <div class="flex justify-between items-start mb-4">
                    <h3 class="text-lg font-semibold">${project.project_name}</h3>
                    <span class="status-badge ${statusClass}">${project.status}</span>
                </div>
                <div class="space-y-2 text-gray-400">
                    <p>Request ID: ${project.request_id}</p>
                    <p>Started: ${new Date(project.start_date).toLocaleDateString()}</p>
                    <p>Type: ${project.project_type}</p>
                </div>
            `;

            return card;
        }

        function showProjectDetails(project) {
            currentProject = project;
            updateTimeline(project);
            updateCommunication(project);
            startMessagePolling();
        }

        // Timeline Management
        function generateTimelineEvents(project) {
            const events = [
                {
                    title: 'Project Initiated',
                    date: new Date(project.start_date).toLocaleDateString(),
                    description: 'Project request received and registered in our system',
                    completed: true
                }
            ];

            switch (project.status) {
                case 'Consultation Scheduled':
                    events.push({
                        title: 'Consultation Scheduled',
                        date: new Date().toLocaleDateString(),
                        description: 'Initial consultation meeting has been set up',
                        completed: true
                    });
                    break;

                case 'In Progress':
                    events.push(
                        {
                            title: 'Consultation Completed',
                            date: new Date().toLocaleDateString(),
                            description: 'Initial consultation meeting completed successfully',
                            completed: true
                        },
                        {
                            title: 'Development Started',
                            date: new Date().toLocaleDateString(),
                            description: 'Team has begun working on project deliverables',
                            completed: true
                        }
                    );
                    break;

                case 'Under Review':
                    events.push(
                        {
                            title: 'Consultation Completed',
                            date: new Date().toLocaleDateString(),
                            description: 'Initial consultation meeting completed successfully',
                            completed: true
                        },
                        {
                            title: 'Development Started',
                            date: new Date().toLocaleDateString(),
                            description: 'Team has begun working on project deliverables',
                            completed: true
                        },
                        {
                            title: 'Quality Review',
                            date: new Date().toLocaleDateString(),
                            description: 'Project submitted for quality assurance review',
                            completed: true
                        }
                    );
                    break;

                case 'Completed':
                    events.push(
                        {
                            title: 'Consultation Completed',
                            date: new Date().toLocaleDateString(),
                            description: 'Initial consultation meeting completed successfully',
                            completed: true
                        },
                        {
                            title: 'Development Completed',
                            date: new Date().toLocaleDateString(),
                            description: 'All project deliverables have been completed',
                            completed: true
                        },
                        {
                            title: 'Quality Review Passed',
                            date: new Date().toLocaleDateString(),
                            description: 'Project has passed quality assurance review',
                            completed: true
                        },
                        {
                            title: 'Project Delivered',
                            date: new Date().toLocaleDateString(),
                            description: 'All project materials have been delivered to client',
                            completed: true
                        }
                    );
                    break;

                case 'Cancelled':
                    events.push({
                        title: 'Project Cancelled',
                        date: new Date().toLocaleDateString(),
                        description: 'Project has been cancelled',
                        completed: true
                    });
                    break;
            }

            return events;
        }

        function updateTimeline(project) {
            const timeline = document.getElementById('timeline');
            const events = generateTimelineEvents(project);
            
            timeline.innerHTML = events.map((event, index) => `
                <div class="timeline-item" style="--animation-order: ${index}">
                    <div class="timeline-line"></div>
                    <div class="timeline-dot"></div>
                    <div class="ml-6">
                        <h4 class="font-medium text-white">${event.title}</h4>
                        <p class="text-sm text-gray-400 mt-1">${event.date}</p>
                        <p class="mt-2 text-gray-300">${event.description}</p>
                    </div>
                </div>
            `).join('');

            updateStatusControls(project);
        }

        function updateStatusControls(project) {
            const controls = document.getElementById('statusControls');
            const nextStatuses = CONFIG.STATUSES[project.status]?.next || [];
            
            if (!nextStatuses.length) {
                controls.innerHTML = '';
                return;
            }

            controls.innerHTML = nextStatuses.map(status => `
                <button 
                    onclick="updateProjectStatus('${status}')"
                    class="px-3 py-1 text-sm rounded-full border border-gray-600 hover:bg-gray-700 transition-colors">
                    ${status}
                </button>
            `).join('');
        }

        async function updateProjectStatus(newStatus) {
            if (!currentProject) return;

            showSpinner();
            try {
                const response = await fetch(CONFIG.SCRIPT_URL, {
                    method: 'POST',
                    body: new URLSearchParams({
                        action: 'updateProject',
                        project_id: currentProject.project_id,
                        status: newStatus
                    })
                });

                const data = await response.json();
                if (!data.success) throw new Error(data.message);

                currentProject.status = newStatus;
                updateDashboard([currentProject]);
                showToast('Project status updated successfully');
            } catch (error) {
                console.error('Status update error:', error);
                showToast(error.message || 'Error updating project status', 'error');
            } finally {
                hideSpinner();
            }
        }

        // Communication Management
        function updateCommunication(project) {
            const container = document.getElementById('messageContainer');
            container.innerHTML = '';

            if (project.communications) {
                project.communications.forEach((msg, index) => {
                    const messageDiv = document.createElement('div');
                    messageDiv.className = `message-bubble ${msg.from === 'client' ? 'message-client' : 'message-team'}`;
                    messageDiv.innerHTML = `
                        <p class="text-sm text-gray-400 mb-1">${msg.from === 'client' ? 'You' : 'Team'} - ${new Date(msg.timestamp).toLocaleString()}</p>
                        <p>${msg.message}</p>
                    `;
                    container.appendChild(messageDiv);
                });

                // Scroll to bottom
                container.scrollTop = container.scrollHeight;
            }
        }

        async function sendMessage() {
            if (!currentProject) return;

            const input = document.getElementById('messageInput');
            const message = input.value.trim();
            
            if (!message) return;

            showSpinner();
            try {
                const response = await fetch(CONFIG.SCRIPT_URL, {
                    method: 'POST',
                    body: new URLSearchParams({
                        action: 'sendMessage',
                        project_id: currentProject.project_id,
                        message: message
                    })
                });

                const data = await response.json();
                if (!data.success) throw new Error(data.message);

                input.value = '';
                await refreshProjectData();
                showToast('Message sent successfully');
            } catch (error) {
                console.error('Message send error:', error);
                showToast(error.message || 'Error sending message', 'error');
            } finally {
                hideSpinner();
            }
        }

        function startMessagePolling() {
            if (messagePollingInterval) {
                clearInterval(messagePollingInterval);
            }

            messagePollingInterval = setInterval(refreshProjectData, 30000); // Poll every 30 seconds
        }

        async function refreshProjectData() {
            if (!currentProject) return;

            try {
                const response = await fetch(`${CONFIG.SCRIPT_URL}?action=getProject&id=${currentProject.project_id}`);
                const data = await response.json();
                
                if (data.success) {
                    currentProject = data.project;
                    updateCommunication(currentProject);
                }
            } catch (error) {
                console.error('Refresh error:', error);
            }
        }

        // Utility Functions
        function showSpinner() {
            document.getElementById('loadingSpinner').classList.remove('hidden');
        }

        function hideSpinner() {
            document.getElementById('loadingSpinner').classList.add('hidden');
        }

        function showToast(message, type = 'success') {
            const toast = document.getElementById('toast');
            const toastMessage = document.getElementById('toastMessage');
            
            toastMessage.textContent = message;
            toast.className = `toast show ${type}`;
            
            setTimeout(() => {
                toast.className = 'toast';
            }, 3000);
        }
    </script>
</body>
</html>

```

# index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nextap AI - Premium Academic Solutions</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.css" rel="stylesheet">
    <style>
        :root {
            --primary-color: #171717;
            --secondary-color: #404041;
            --accent-color: #3b82f6;
            --text-color: #f3f4f6;
        }

        body {
            background-color: var(--primary-color);
            color: var(--text-color);
            font-family: 'Gotham Book', -apple-system, BlinkMacSystemFont, sans-serif;
        }

        .hero-gradient {
            background: linear-gradient(rgba(0, 0, 0, 0.7), rgba(0, 0, 0, 0.9)), url('https://img.freepik.com/free-vector/abstract-vector-colorful-mesh-dark-background-futuristic-style-card-elegant-background-business-presentations-corrupted-point-sphere-chaos-aesthetics_1217-5359.jpg');
            background-size: cover;
            background-position: center;
        }

        .glass-card {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        .hover-scale {
            transition: transform 0.3s ease;
        }

        .hover-scale:hover {
            transform: scale(1.02);
        }

        /* Roadmap styling */
        .roadmap {
            position: relative;
            max-width: 800px;
            margin: 0 auto;
            padding: 40px 0;
        }

        .roadmap-line {
            position: absolute;
            left: 50%;
            top: 0;
            bottom: 0;
            width: 2px;
            background: var(--accent-color);
            transform: translateX(-50%);
        }

        .roadmap-item {
            position: relative;
            margin-bottom: 30px;
            width: 100%;
            display: flex;
            align-items: center;
        }

        .roadmap-bead {
            width: 20px;
            height: 20px;
            background: var(--accent-color);
            border-radius: 50%;
            position: absolute;
            left: 50%;
            transform: translateX(-50%);
            z-index: 2;
        }

        .roadmap-content {
            width: calc(50% - 30px);
            padding: 15px;
            background: rgba(59, 130, 246, 0.1);
            border-radius: 8px;
        }

        /* Adjust odd (left) items to be right-aligned */
        .roadmap-item:nth-child(odd) .roadmap-content {
          margin-right: auto;
          margin-left: 0;
          text-align: right;
        }
        
        /* Adjust even (right) items to be left-aligned */
        .roadmap-item:nth-child(even) .roadmap-content {
          margin-left: auto;
          text-align: left;
        }

        @media (max-width: 768px) {
            .roadmap-line {
                left: 20px;
            }

            .roadmap-bead {
                left: 20px;
            }

            .roadmap-content {
                width: calc(100% - 50px);
                margin-left: 40px !important;
            }
        }

        /* Price disclaimer */
        .price-disclaimer {
            font-size: 0.65rem;
            color: #9ca3af;
        }

        /* Mobile menu adjustments */
        @media (max-width: 768px) {
            .mobile-menu {
                position: fixed;
                top: 64px;
                left: 0;
                right: 0;
                background: var(--primary-color);
                padding: 1rem;
                transform: translateY(-100%);
                transition: transform 0.3s ease;
                z-index: 50;
            }

            .mobile-menu.active {
                transform: translateY(0);
            }
        }
        /* Core chatbot styles */
        .chatbot-widget {
            position: fixed;
            bottom: 24px;
            right: 24px;
            z-index: 1000;
            display: flex;
            flex-direction: column;
            align-items: flex-end;
            font-family: 'Gotham Book', -apple-system, BlinkMacSystemFont, sans-serif;
        }

        .chatbot-button {
            width: 56px;
            height: 56px;
            border-radius: 50%;
            background: rgba(23, 23, 23, 0.95);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2);
        }

        .chatbot-button:hover {
            transform: translateY(-2px);
            background: rgba(23, 23, 23, 1);
            box-shadow: 0 8px 24px rgba(0, 0, 0, 0.3);
        }

        .chatbot-window {
            position: fixed;
            bottom: 96px;
            right: 24px;
            width: 380px;
            height: 600px;
            background: rgba(23, 23, 23, 0.95);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 16px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
            display: none;
            flex-direction: column;
            overflow: hidden;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .chatbot-header {
            padding: 20px;
            background: transparent;
            color: white;
            font-weight: 500;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .chatbot-close {
            cursor: pointer;
            padding: 8px;
            border-radius: 50%;
            transition: background 0.3s ease;
        }

        .chatbot-close:hover {
            background: rgba(255, 255, 255, 0.1);
        }

        .chatbot-messages {
            flex-grow: 1;
            padding: 20px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 16px;
            scrollbar-width: thin;
            scrollbar-color: rgba(255, 255, 255, 0.2) transparent;
        }

        .chatbot-messages::-webkit-scrollbar {
            width: 6px;
        }

        .chatbot-messages::-webkit-scrollbar-thumb {
            background: rgba(255, 255, 255, 0.2);
            border-radius: 3px;
        }

        .message {
            max-width: 85%;
            padding: 14px 18px;
            border-radius: 16px;
            margin-bottom: 4px;
            font-size: 14px;
            line-height: 1.5;
            transition: all 0.3s ease;
            opacity: 0;
            transform: translateY(10px);
            animation: messageAppear 0.3s ease forwards;
        }

        .user-message {
            background: rgba(255, 255, 255, 0.1);
            color: white;
            align-self: flex-end;
            border-bottom-right-radius: 4px;
        }

        .bot-message {
            background: rgba(255, 255, 255, 0.05);
            color: white;
            align-self: flex-start;
            border-bottom-left-radius: 4px;
        }

        .chatbot-input {
            padding: 20px;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
            display: flex;
            gap: 12px;
            background: rgba(23, 23, 23, 0.98);
        }

        .chatbot-input input {
            flex-grow: 1;
            padding: 12px 16px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 8px;
            background: rgba(255, 255, 255, 0.05);
            color: white;
            font-size: 14px;
            transition: all 0.3s ease;
        }

        .chatbot-input input:focus {
            outline: none;
            border-color: rgba(255, 255, 255, 0.2);
            background: rgba(255, 255, 255, 0.08);
        }

        .chatbot-input button {
            padding: 12px 20px;
            background: rgba(255, 255, 255, 0.1);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 8px;
            cursor: pointer;
            font-size: 14px;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .chatbot-input button:hover {
            background: rgba(255, 255, 255, 0.15);
            transform: translateY(-1px);
        }

        .chat-action-button {
            display: flex;
            justify-content: center;
            margin: 12px 0;
            opacity: 0;
            transform: translateY(10px);
            animation: actionAppear 0.3s cubic-bezier(0.4, 0, 0.2, 1) forwards;
        }

        .chat-action-button button {
            background: transparent;
            border: 1px solid rgba(255, 255, 255, 0.1);
            color: white;
            padding: 10px 20px;
            border-radius: 8px;
            font-size: 14px;
            cursor: pointer;
            transition: all 0.3s ease;
            backdrop-filter: blur(5px);
        }

        .chat-action-button button:hover {
            background: rgba(255, 255, 255, 0.1);
            transform: translateY(-1px);
        }

        .typing-indicator {
            display: none;
            align-self: flex-start;
            background: rgba(255, 255, 255, 0.05);
            padding: 12px 16px;
            border-radius: 16px;
            border-bottom-left-radius: 4px;
            color: rgba(255, 255, 255, 0.7);
            font-size: 14px;
            margin-bottom: 8px;
            animation: pulseTyping 1.5s ease-in-out infinite;
        }

        @keyframes messageAppear {
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        @keyframes actionAppear {
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        @keyframes pulseTyping {
            0% { opacity: 0.5; }
            50% { opacity: 1; }
            100% { opacity: 0.5; }
        }

        /* Footer Styling */
        .footer {
            background-color: var(--primary-color);
            color: var(--text-color);
            padding: 40px 20px;
        }

        .footer-links {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 20px;
            margin-bottom: 20px;
        }

        .footer-links a {
            color: var(--text-color);
            text-decoration: none;
            transition: color 0.3s ease;
        }

        .footer-links a:hover {
            color: var(--accent-color);
        }

        .footer-bottom {
            text-align: center;
            font-size: 0.875rem;
            color: var(--secondary-color);
        }

        /* Language and Currency Capsule */
        .lang-currency-capsule {
            display: flex;
            align-items: center;
            gap: 8px;
            background: linear-gradient(90deg, var(--secondary-color), var(--accent-color));
            border-radius: 9999px;
            padding: 6px 16px;
            color: white;
            font-size: 0.875rem;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        .lang-currency-capsule select {
            background: transparent;
            border: none;
            color: white;
            appearance: none;
            cursor: pointer;
            font-size: 0.875rem;
        }

        .lang-currency-capsule select:focus {
            outline: none;
        }

        /* Highlight for Calculator Section */
        .highlight-section {
            animation: highlightFade 2s forwards;
            background-color: rgba(59, 130, 246, 0.1);
            border-radius: 8px;
        }

        @keyframes highlightFade {
            from { background-color: rgba(59, 130, 246, 0.3); }
            to { background-color: rgba(59, 130, 246, 0.1); }
        }

        /* Ensure Google Translate dropdown fits well */
        .goog-te-gadget {
            display: none;
        }

        /* Responsive Adjustments */
        @media (max-width: 768px) {
            .lang-currency-capsule {
                display: none;
            }
        }

        /* Calculator Section Styles */
        .calculator-form label {
            display: block;
            margin-bottom: 0.5rem;
            color: #d1d5db;
        }

        .calculator-form input, .calculator-form select {
            width: 100%;
            padding: 0.75rem;
            border: 1px solid #4b5563;
            border-radius: 0.5rem;
            background-color: #1f2937;
            color: #f3f4f6;
            margin-bottom: 1.5rem;
        }

        .calculator-form input:focus, .calculator-form select:focus {
            outline: none;
            border-color: #3b82f6;
            background-color: #111827;
        }

        .calculator-result {
            font-size: 1.5rem;
            font-weight: 600;
            text-align: center;
            color: #3b82f6;
            margin-bottom: 1.5rem;
        }

        /* Request Quote Form Styles */
        .quote-form {
            display: none;
        }

        .quote-form.active {
            display: block;
        }

        .quote-form label {
            display: block;
            margin-bottom: 0.5rem;
            color: #d1d5db;
        }

        .quote-form input, .quote-form textarea, .quote-form select {
            width: 100%;
            padding: 0.75rem;
            border: 1px solid #4b5563;
            border-radius: 0.5rem;
            background-color: #1f2937;
            color: #f3f4f6;
            margin-bottom: 1.5rem;
        }

        .quote-form input:focus, .quote-form textarea:focus, .quote-form select:focus {
            outline: none;
            border-color: #3b82f6;
            background-color: #111827;
        }

        /* Button Styles */
        .btn-primary {
            background-color: #3b82f6;
            color: white;
            padding: 0.75rem 1.5rem;
            border-radius: 0.5rem;
            font-size: 1rem;
            font-weight: 600;
            transition: background-color 0.3s ease;
        }

        .btn-primary:hover {
            background-color: #2563eb;
        }

        .btn-secondary {
            background-color: transparent;
            color: white;
            padding: 0.75rem 1.5rem;
            border: 1px solid white;
            border-radius: 0.5rem;
            font-size: 1rem;
            font-weight: 600;
            transition: background-color 0.3s ease, color 0.3s ease;
        }

        .btn-secondary:hover {
            background-color: white;
            color: #1f2937;
        }

        /* Animations */
        .group {
            opacity: 0;
            transform: translateY(20px);
            transition: opacity 0.6s ease-out, transform 0.6s ease-out;
        }

        .group.fade-in {
            opacity: 1;
            transform: translateY(0);
        }

        .group:nth-child(1) {
            transition-delay: 0.1s;
        }

        .group:nth-child(2) {
            transition-delay: 0.2s;
        }

        .group:nth-child(3) {
            transition-delay: 0.3s;
        }

        .group > div {
            transform: translateY(0);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .group:hover > div {
            transform: translateY(-4px);
        }

        /* Pricing animation */
        .pricing-tier {
            position: relative;
            overflow: hidden;
        }

        .pricing-tier::after {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(
                45deg,
                transparent,
                rgba(59, 130, 246, 0.1),
                transparent
            );
            transform: translateX(-100%);
            transition: transform 0.6s;
        }

        .group:hover .pricing-tier::after {
            transform: translateX(100%);
        }

        /* Responsive Typography */
        @media (max-width: 768px) {
            .text-4xl {
                font-size: 2.25rem;
            }
            .text-7xl {
                font-size: 3rem;
            }
        }
    </style>
</head>
<body>
    <!-- Google Translate Element -->
    <div id="google_translate_element"></div>
    <script type="text/javascript">
      function googleTranslateElementInit() {
        new google.translate.TranslateElement(
          {
            pageLanguage: 'en',
            includedLanguages: 'en,es,fr,de,zh,ar,hi,ja,ru,pt',
            layout: google.translate.TranslateElement.InlineLayout.HORIZONTAL
          },
          'google_translate_element'
        );
      }
    </script>
    <script type="text/javascript" src="//translate.google.com/translate_a/element.js?cb=googleTranslateElementInit"></script>

    <!-- Navigation -->
    <nav class="fixed w-full z-40 bg-black bg-opacity-90 backdrop-blur-md">
        <div class="max-w-7xl mx-auto px-4">
            <div class="flex justify-between h-16 items-center">
                <a href="#" class="text-2xl font-bold bg-gradient-to-r from-blue-400 to-blue-600 text-transparent bg-clip-text notranslate">
                    Nextap AI
                </a>
                <div class="hidden md:flex items-center space-x-4">
                    <a href="#services" class="text-gray-300 hover:text-white">Services</a>
                    <a href="#testimonials" class="text-gray-300 hover:text-white">Testimonials</a>
                    <a href="#calculator" class="text-gray-300 hover:text-white">Get Quote</a>
                    <a href="dashboard.html" class="text-gray-300 hover:text-white">Dashboard</a>
                    <a href="admin.html" class="text-gray-300 hover:text-white">Admin</a>
                    <a href="blogs.html" class="text-gray-300 hover:text-white">Blogs</a>
                    <!-- Language and Currency Capsule -->
                    <div class="lang-currency-capsule">
                        <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5" fill="none" viewBox="0 0 24 24"
                            stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M12 4v16m8-8H4" />
                        </svg>
                        <select id="currencySelect" onchange="changeCurrency(this.value)">
                            <option value="USD">USD</option>
                            <option value="EUR">EUR</option>
                            <option value="GBP">GBP</option>
                            <option value="AUD">AUD</option>
                            <option value="JPY">JPY</option>
                            <option value="AED">AED</option>
                            <option value="MYR">MYR</option>
                            <option value="INR">INR</option>
                            <option value="CAD">CAD</option>
                            <option value="QAR">QAR</option>
                            <option value="SAR">SAR</option>
                            <option value="KWD">KWD</option>
                            <!-- Add more currencies as needed -->
                        </select>
                        <select id="languageSelect" onchange="changeLanguage(this.value)">
                            <option value="en">EN</option>
                            <option value="es">ES</option>
                            <option value="fr">FR</option>
                            <option value="de">DE</option>
                            <option value="zh">ZH</option>
                            <option value="ar">AR</option>
                            <option value="hi">HI</option>
                            <option value="ja">JA</option>
                            <option value="ru">RU</option>
                            <option value="pt">PT</option>
                        </select>
                    </div>
                </div>
                <button class="md:hidden text-white" onclick="toggleMobileMenu()">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                            d="M4 6h16M4 12h16M4 18h16" />
                    </svg>
                </button>
            </div>
        </div>
        <!-- Mobile Menu -->
        <div class="mobile-menu md:hidden">
            <div class="flex flex-col space-y-4 px-4">
                <a href="#services" class="text-gray-300 hover:text-white" onclick="toggleMobileMenu()">Services</a>
                <a href="#testimonials" class="text-gray-300 hover:text-white" onclick="toggleMobileMenu()">Testimonials</a>
                <a href="#calculator" class="text-gray-300 hover:text-white" onclick="toggleMobileMenu()">Get Quote</a>
                <a href="dashboard.html" class="text-gray-300 hover:text-white" onclick="toggleMobileMenu()">Dashboard</a>
                <a href="admin.html" class="text-gray-300 hover:text-white" onclick="toggleMobileMenu()">Admin</a>
                <a href="blogs.html" class="text-gray-300 hover:text-white" onclick="toggleMobileMenu()">Blogs</a>
                <!-- Language and Currency Selection in Mobile Menu -->
                <div class="mt-4">
                    <label class="block mb-2 text-gray-300">Currency</label>
                    <select id="mobileCurrencySelect" class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white" onchange="changeCurrency(this.value)">
                        <option value="USD">USD</option>
                        <option value="EUR">EUR</option>
                        <option value="GBP">GBP</option>
                        <option value="AUD">AUD</option>
                        <option value="JPY">JPY</option>
                        <option value="AED">AED</option>
                        <option value="MYR">MYR</option>
                        <option value="INR">INR</option>
                        <option value="CAD">CAD</option>
                        <option value="QAR">QAR</option>
                        <option value="SAR">SAR</option>
                        <option value="KWD">KWD</option>
                        <!-- Add more currencies as needed -->
                    </select>
                </div>
                <div class="mt-4">
                    <label class="block mb-2 text-gray-300">Language</label>
                    <select id="mobileLanguageSelect" class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white" onchange="changeLanguage(this.value)">
                        <option value="en">English</option>
                        <option value="es">Español</option>
                        <option value="fr">Français</option>
                        <option value="de">Deutsch</option>
                        <option value="zh">中文</option>
                        <option value="ar">العربية</option>
                        <option value="hi">हिन्दी</option>
                        <option value="ja">日本語</option>
                        <option value="ru">Русский</option>
                        <option value="pt">Português</option>
                    </select>
                </div>
            </div>
        </div>
    </nav>

    <!-- Hero Section -->
    <div class="relative min-h-screen flex items-center pt-16">
      <video id="bgVideo" autoplay loop muted playsinline class="absolute top-0 left-0 w-full h-full object-cover">
        <source src="https://github.com/nextap-lab/nextap.github.io/raw/refs/heads/main/site_temp.mp4" type="video/mp4">
      </video>
      <div class="absolute inset-0 bg-black opacity-70"></div>
      <div class="relative max-w-7xl mx-auto px-4 text-center z-10">
        <h1 class="text-4xl md:text-7xl font-bold mb-6 leading-tight">
          Your Success.<br>Our Commitment.
        </h1>
        <p class="text-xl md:text-2xl mb-8 text-gray-300">100% Learning on You. Full Work on Us.</p>
        <div class="flex flex-col md:flex-row justify-center gap-4">
          <button onclick="scrollToCalculator()" class="bg-blue-600 text-white px-8 py-4 rounded-full text-lg font-semibold hover:bg-blue-700 transition-all">
            Get Custom Quote
          </button>
          <button onclick="navigateToSchedule()" class="bg-transparent border border-white text-white px-8 py-4 rounded-full text-lg font-semibold hover:bg-white hover:text-black transition-all">
            Schedule Consultation  
          </button>
        </div>
      </div>
    </div>
    
    <style>
      #bgVideo {
        filter: brightness(70%);
      }
    </style>

    <!-- Services Section with Enhanced Business-Focused Design -->
    <section id="services" class="py-24 bg-gradient-to-b from-gray-900 via-black to-gray-900 relative overflow-hidden">
        <!-- Animated Background Elements -->
        <div class="absolute inset-0">
            <div class="absolute inset-0 bg-[radial-gradient(circle_at_30%_50%,rgba(59,130,246,0.04),transparent_50%)]"></div>
            <div class="absolute inset-0 bg-[radial-gradient(circle_at_70%_80%,rgba(59,130,246,0.03),transparent_40%)]"></div>
        </div>

        <!-- Floating Particles -->
        <div class="particles absolute inset-0 opacity-20"></div>
        
        <div class="max-w-7xl mx-auto px-4 relative z-10">
            <!-- Section Header -->
            <div class="text-center mb-20">
                <h2 class="text-4xl md:text-5xl font-bold mb-6 bg-gradient-to-r from-white via-blue-100 to-white bg-clip-text text-transparent">
                    Enterprise Solutions
                </h2>
                <p class="text-gray-400 text-lg max-w-2xl mx-auto">
                    Tailored academic services designed for professional excellence
                </p>
            </div>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-8 relative">
                <!-- Report Writing Card -->
                <div class="group relative">
                    <!-- Highlight Effect -->
                    <div class="absolute -inset-1 bg-gradient-to-r from-blue-600 to-blue-400 rounded-2xl opacity-0 group-hover:opacity-20 blur transition duration-500"></div>
                    
                    <div class="relative p-8 rounded-2xl bg-gradient-to-b from-gray-800/80 to-gray-900/90 border border-gray-700/50 backdrop-blur-xl transition-all duration-300">
                        <!-- Service Icon -->
                        <div class="service-icon-wrapper mb-8">
                            <div class="w-16 h-16 bg-blue-500/10 rounded-2xl p-4 relative">
                                <div class="absolute inset-0 bg-blue-500/10 rounded-2xl animate-pulse"></div>
                                <svg class="w-full h-full text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path>
                                </svg>
                            </div>
                        </div>
                        
                        <!-- Content -->
                        <div class="space-y-6">
                            <h3 class="text-2xl font-bold text-white tracking-tight">Report Writing</h3>
                            <p class="text-gray-400 leading-relaxed min-h-[80px]">
                                Professional documentation with comprehensive research and meticulous attention to industry standards.
                            </p>
                            
                            <!-- Pricing Tier -->
                            <div class="pricing-tier space-y-2">
                                <div class="flex items-baseline gap-2">
                                    <span class="text-3xl font-bold text-white price" data-price-usd="0.035">₹0.035</span>
                                    <span class="text-gray-400">-</span>
                                    <span class="text-3xl font-bold text-white price" data-price-usd="0.092">₹0.092</span>
                                    <span class="text-gray-400">/word</span>
                                </div>
                                <p class="text-sm text-gray-500">Enterprise-grade quality</p>
                            </div>
                            
                            <!-- Features List -->
                            <ul class="space-y-3">
                                <li class="flex items-center text-sm text-gray-400">
                                    <svg class="w-5 h-5 mr-2 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                                    </svg>
                                    Professional Research
                                </li>
                                <li class="flex items-center text-sm text-gray-400">
                                    <svg class="w-5 h-5 mr-2 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                                    </svg>
                                    Industry Standard Format
                                </li>
                                <li class="flex items-center text-sm text-gray-400">
                                    <svg class="w-5 h-5 mr-2 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                                    </svg>
                                    Premium Support
                                </li>
                            </ul>
                        </div>
                        
                        <!-- CTA Button -->
                        <button onclick="scrollToCalculator('report')" class="mt-8 w-full inline-flex items-center justify-center px-6 py-3 border border-transparent text-base font-medium rounded-xl text-white bg-gradient-to-r from-blue-600 to-blue-700 hover:from-blue-700 hover:to-blue-800 transition-all duration-300 transform hover:-translate-y-0.5">
                            Calculate Investment
                            <svg class="w-5 h-5 ml-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7l5 5m0 0l-5 5m5-5H6"></path>
                            </svg>
                        </button>
                    </div>
                </div>

                <!-- Coding Projects Card -->
                <div class="group relative">
                    <!-- Highlight Effect -->
                    <div class="absolute -inset-1 bg-gradient-to-r from-blue-600 to-blue-400 rounded-2xl opacity-0 group-hover:opacity-20 blur transition duration-500"></div>
                    
                    <div class="relative p-8 rounded-2xl bg-gradient-to-b from-gray-800/80 to-gray-900/90 border border-gray-700/50 backdrop-blur-xl transition-all duration-300">
                        <!-- Service Icon -->
                        <div class="service-icon-wrapper mb-8">
                            <div class="w-16 h-16 bg-blue-500/10 rounded-2xl p-4 relative">
                                <div class="absolute inset-0 bg-blue-500/10 rounded-2xl animate-pulse"></div>
                                <svg class="w-full h-full text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M10 20l4-16m4 4l4 4-4 4M6 16l-4-4 4-4"></path>
                                </svg>
                            </div>
                        </div>
                        
                        <!-- Content -->
                        <div class="space-y-6">
                            <h3 class="text-2xl font-bold text-white tracking-tight">Coding Projects</h3>
                            <p class="text-gray-400 leading-relaxed min-h-[80px]">
                                Enterprise-level development solutions with scalable architecture and comprehensive documentation.
                            </p>
                            
                            <!-- Pricing Tier -->
                            <div class="pricing-tier space-y-2">
                                <div class="flex items-baseline gap-2">
                                    <span class="text-3xl font-bold text-white">Custom</span>
                                    <span class="text-gray-400">Quote</span>
                                </div>
                                <p class="text-sm text-gray-500">Project-based pricing</p>
                            </div>
                            
                            <!-- Features List -->
                            <ul class="space-y-3">
                                <li class="flex items-center text-sm text-gray-400">
                                    <svg class="w-5 h-5 mr-2 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                                    </svg>
                                    Enterprise Architecture
                                </li>
                                <li class="flex items-center text-sm text-gray-400">
                                    <svg class="w-5 h-5 mr-2 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                                    </svg>
                                    Code Documentation
                                </li>
                                <li class="flex items-center text-sm text-gray-400">
                                    <svg class="w-5 h-5 mr-2 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                                    </svg>
                                    Technical Support
                                </li>
                            </ul>
                        </div>
                        
                        <!-- CTA Button -->
                        <button onclick="showQuoteForm()" class="mt-8 w-full inline-flex items-center justify-center px-6 py-3 border border-transparent text-base font-medium rounded-xl text-white bg-gradient-to-r from-blue-600 to-blue-700 hover:from-blue-700 hover:to-blue-800 transition-all duration-300 transform hover:-translate-y-0.5">
                            Request Quote
                            <svg class="w-5 h-5 ml-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7l5 5m0 0l-5 5m5-5H6"></path>
                            </svg>
                        </button>
                    </div>
                </div>

                <!-- Presentations Card -->
                <div class="group relative">
                    <!-- Highlight Effect -->
                    <div class="absolute -inset-1 bg-gradient-to-r from-blue-600 to-blue-400 rounded-2xl opacity-0 group-hover:opacity-20 blur transition duration-500"></div>
                    
                    <div class="relative p-8 rounded-2xl bg-gradient-to-b from-gray-800/80 to-gray-900/90 border border-gray-700/50 backdrop-blur-xl transition-all duration-300">
                        <!-- Service Icon -->
                        <div class="service-icon-wrapper mb-8">
                            <div class="w-16 h-16 bg-blue-500/10 rounded-2xl p-4 relative">
                                <div class="absolute inset-0 bg-blue-500/10 rounded-2xl animate-pulse"></div>
                                <svg class="w-full h-full text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M7 12l3-3 3 3 4-4M8 21l4-4 4 4M3 4h18M4 4h16v12a1 1 0 01-1 1H5a1 1 0 01-1-1V4z"></path>
                                </svg>
                            </div>
                        </div>
                        
                        <!-- Content -->
                        <div class="space-y-6">
                            <h3 class="text-2xl font-bold text-white tracking-tight">Presentations</h3>
                            <p class="text-gray-400 leading-relaxed min-h-[80px]">
                                Strategic presentation design focused on impact and professional delivery.
                            </p>
                            
                            <!-- Pricing Tier -->
                            <div class="pricing-tier space-y-2">
                                <div class="flex items-baseline gap-2">
                                    <span class="text-3xl font-bold text-white price" data-price-usd="5.78">₹5.78</span>
                                    <span class="text-gray-400">-</span>
                                    <span class="text-3xl font-bold text-white price" data-price-usd="11.55">₹11.55</span>
                                    <span class="text-gray-400">/slide</span>
                                </div>
                                <p class="text-sm text-gray-500">Professional design</p>
                            </div>
                            
                            <!-- Features List -->
                            <ul class="space-y-3">
                                <li class="flex items-center text-sm text-gray-400">
                                    <svg class="w-5 h-5 mr-2 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                                    </svg>
                                    Custom Design
                                </li>
                                <li class="flex items-center text-sm text-gray-400">
                                    <svg class="w-5 h-5 mr-2 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                                    </svg>
                                    Engaging Visuals
                                </li>
                                <li class="flex items-center text-sm text-gray-400">
                                    <svg class="w-5 h-5 mr-2 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                                    </svg>
                                    Revision Support
                                </li>
                            </ul>
                        </div>
                        
                        <!-- CTA Button -->
                        <button onclick="scrollToCalculator('presentation')" class="mt-8 w-full inline-flex items-center justify-center px-6 py-3 border border-transparent text-base font-medium rounded-xl text-white bg-gradient-to-r from-blue-600 to-blue-700 hover:from-blue-700 hover:to-blue-800 transition-all duration-300 transform hover:-translate-y-0.5">
                            Calculate Investment
                            <svg class="w-5 h-5 ml-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7l5 5m0 0l-5 5m5-5H6"></path>
                            </svg>
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Animated Background Style -->
        <style>
            .particles {
                background: linear-gradient(to bottom, transparent, transparent),
                            radial-gradient(white, rgba(255,255,255,.2) 2px, transparent 40px),
                            radial-gradient(white, rgba(255,255,255,.15) 1px, transparent 30px),
                            radial-gradient(white, rgba(255,255,255,.1) 2px, transparent 40px);
                background-size: 100% 100%, 550px 550px, 350px 350px, 250px 250px;
                background-position: 0 0, 40px 60px, 130px 270px, 70px 100px;
                animation: particlesAnimation 15s linear infinite;
            }

            @keyframes particlesAnimation {
                0% {
                    background-position: 0 0, 40px 60px, 130px 270px, 70px 100px;
                }
                100% {
                    background-position: 0 0, -510px -490px, -220px -80px, -180px -150px;
                }
            }

            /* Card Hover Effects */
            .service-icon-wrapper {
                position: relative;
                isolation: isolate;
            }

            .service-icon-wrapper::after {
                content: '';
                position: absolute;
                inset: 0;
                border-radius: 1rem;
                background: radial-gradient(
                    500px circle at var(--mouse-x) var(--mouse-y),
                    rgba(59, 130, 246, 0.1),
                    transparent 40%
                );
                opacity: 0;
                transition: opacity 0.3s;
                z-index: -1;
            }

            .group:hover .service-icon-wrapper::after {
                opacity: 1;
            }
        </style>

        <!-- Mouse tracking for lighting effect -->
        <script>
            document.addEventListener('DOMContentLoaded', () => {
                const cards = document.querySelectorAll('.group');
                
                cards.forEach(card => {
                    card.addEventListener('mousemove', (e) => {
                        const rect = card.getBoundingClientRect();
                        const x = e.clientX - rect.left;
                        const y = e.clientY - rect.top;
                        
                        card.style.setProperty('--mouse-x', `${x}px`);
                        card.style.setProperty('--mouse-y', `${y}px`);
                    });
                });
            });

            // Intersection Observer for fade-in animation
            document.addEventListener('DOMContentLoaded', () => {
                const observer = new IntersectionObserver((entries) => {
                    entries.forEach(entry => {
                        if (entry.isIntersecting) {
                            entry.target.classList.add('fade-in');
                            observer.unobserve(entry.target);
                        }
                    });
                }, {
                    threshold: 0.1
                });

                document.querySelectorAll('.group').forEach(card => {
                    observer.observe(card);
                });
            });

            // Function to toggle the mobile menu
            function toggleMobileMenu() {
                const mobileMenu = document.querySelector('.mobile-menu');
                mobileMenu.classList.toggle('active');
            }
        </script>

        <!-- Additional Animations -->
        <style>
            /* Fade in animation for cards */
            .group {
                opacity: 0;
                transform: translateY(20px);
                transition: opacity 0.6s ease-out, transform 0.6s ease-out;
            }

            .group.fade-in {
                opacity: 1;
                transform: translateY(0);
            }

            /* Staggered animation delay for cards */
            .group:nth-child(1) {
                transition-delay: 0.1s;
            }

            .group:nth-child(2) {
                transition-delay: 0.2s;
            }

            .group:nth-child(3) {
                transition-delay: 0.3s;
            }

            /* Smooth hover state transitions */
            .group > div {
                transform: translateY(0);
                transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            }

            .group:hover > div {
                transform: translateY(-4px);
            }

            /* Pricing animation */
            .pricing-tier {
                position: relative;
                overflow: hidden;
            }

            .pricing-tier::after {
                content: '';
                position: absolute;
                top: -50%;
                left: -50%;
                width: 200%;
                height: 200%;
                background: linear-gradient(
                    45deg,
                    transparent,
                    rgba(59, 130, 246, 0.1),
                    transparent
                );
                transform: translateX(-100%);
                transition: transform 0.6s;
            }

            .group:hover .pricing-tier::after {
                transform: translateX(100%);
            }
        </style>
    </section>

    <!-- Project Roadmap -->
    <section class="py-20 bg-black">
        <h2 class="text-4xl font-bold text-center mb-16">Project Journey</h2>
        <div class="roadmap">
            <div class="roadmap-line"></div>
            <div class="roadmap-item">
                <div class="roadmap-bead"></div>
                <div class="roadmap-content">
                    <h3 class="font-semibold text-lg mb-2">1. Get Quote</h3>
                    <p class="text-gray-400">Calculate your investment based on project requirements.</p>
                </div>
            </div>
            <div class="roadmap-item">
                <div class="roadmap-bead"></div>
                <div class="roadmap-content">
                    <h3 class="font-semibold text-lg mb-2">2. One-to-One Consultation</h3>
                    <p class="text-gray-400">Detailed discussion of your project needs and expectations.</p>
                </div>
            </div>
            <div class="roadmap-item">
                <div class="roadmap-bead"></div>
                <div class="roadmap-content">
                    <h3 class="font-semibold text-lg mb-2">3. Project Work Live</h3>
                    <p class="text-gray-400">Begin working on your project with regular updates.</p>
                </div>
            </div>
            <div class="roadmap-item">
                <div class="roadmap-bead"></div>
                <div class="roadmap-content">
                    <h3 class="font-semibold text-lg mb-2">4. Live Monitoring Status</h3>
                    <p class="text-gray-400">Track progress in real-time through our dashboard.</p>
                </div>
            </div>
            <div class="roadmap-item">
                <div class="roadmap-bead"></div>
                <div class="roadmap-content">
                    <h3 class="font-semibold text-lg mb-2">5. Project Delivery</h3>
                    <p class="text-gray-400">Receive your completed project on time.</p>
                </div>
            </div>
            <div class="roadmap-item">
                <div class="roadmap-bead"></div>
                <div class="roadmap-content">
                    <h3 class="font-semibold text-lg mb-2">6. Changes</h3>
                    <p class="text-gray-400">Up to 5 minor revisions included.</p>
                </div>
            </div>
        </div>
    </section>

    <!-- Calculator Section -->
    <section id="calculator" class="py-20 bg-black">
        <div class="max-w-3xl mx-auto px-4">
            <h2 class="text-4xl font-bold text-center mb-16">Get Your Custom Quote</h2>
            <!-- Report & Presentation Calculator -->
            <div class="glass-card p-8 rounded-xl mb-8">
                <div class="mb-6">
                    <label class="block mb-2 text-gray-300">Service Type</label>
                    <select id="serviceType" class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white">
                        <option value="report">Report Writing</option>
                        <option value="presentation">Presentation Design</option>
                        <option value="coding">Coding Project</option>
                    </select>
                </div>
                <div id="wordCountDiv" class="mb-6">
                    <label class="block mb-2 text-gray-300">Number of Words</label>
                    <input type="number" id="wordCount" placeholder="Enter number of words" class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white" min="1">
                </div>
                <div id="slideCountDiv" class="mb-6 hidden">
                    <label class="block mb-2 text-gray-300">Number of Slides</label>
                    <input type="number" id="slideCount" placeholder="Enter number of slides" class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white" min="1">
                </div>
                <div id="qualityLevelDiv" class="mb-6">
                    <label class="block mb-2 text-gray-300">Quality Level</label>
                    <select id="qualityLevel" class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white">
                        <option value="standard">Standard Quality</option>
                        <option value="premium">Premium Quality</option>
                        <option value="enterprise">Enterprise Quality</option>
                    </select>
                </div>
                <div class="calculator-result mb-6">
                    Estimated Investment: <span id="estimatedCost" class="text-blue-500">₹0</span>
                </div>
                <button onclick="navigateToSchedule()" class="w-full bg-blue-600 text-white py-4 rounded-full text-lg font-semibold hover:bg-blue-700 transition-all">
                    Proceed with Order
                </button>
            </div>

            <!-- Coding Project Request Quote Form -->
            <div id="quoteForm" class="glass-card p-8 rounded-xl quote-form">
                <h3 class="text-2xl font-bold text-white mb-6 text-center">Request a Custom Quote</h3>
                <form id="quoteRequestForm">
                    <div class="mb-4">
                        <label for="projectName" class="block mb-2 text-gray-300">Project Name</label>
                        <input type="text" id="projectName" name="projectName" required class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white" placeholder="Enter your project name">
                    </div>
                    <div class="mb-4">
                        <label for="projectDetails" class="block mb-2 text-gray-300">Project Details</label>
                        <textarea id="projectDetails" name="projectDetails" required class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white" rows="5" placeholder="Describe your project"></textarea>
                    </div>
                    <div class="mb-4">
                        <label for="deadline" class="block mb-2 text-gray-300">Deadline</label>
                        <input type="date" id="deadline" name="deadline" required class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white">
                    </div>
                    <div class="mb-4">
                        <label for="contactEmail" class="block mb-2 text-gray-300">Contact Email</label>
                        <input type="email" id="contactEmail" name="contactEmail" required class="w-full bg-gray-800 border border-gray-700 rounded-lg p-3 text-white" placeholder="Enter your email">
                    </div>
                    <div>
                        <button type="submit" class="w-full bg-blue-600 text-white py-4 rounded-full text-lg font-semibold hover:bg-blue-700 transition-all">
                            Submit Request
                        </button>
                    </div>
                </form>
            </div>
        </div>
    </section>

    <!-- Trust Badges Section -->
    <section class="py-16 bg-gray-900">
        <div class="max-w-7xl mx-auto px-4 text-center">
            <div class="grid grid-cols-2 md:grid-cols-4 gap-8">
                <div class="flex flex-col items-center">
                    <div class="text-4xl font-bold text-blue-500 mb-2">5000+</div>
                    <div class="text-gray-400">Projects Completed</div>
                </div>
                <div class="flex flex-col items-center">
                    <div class="text-4xl font-bold text-blue-500 mb-2">98%</div>
                    <div class="text-gray-400">On-Time Delivery</div>
                </div>
                <div class="flex flex-col items-center">
                    <div class="text-4xl font-bold text-blue-500 mb-2">24/7</div>
                    <div class="text-gray-400">Support Available</div>
                </div>
                <div class="flex flex-col items-center">
                    <div class="text-4xl font-bold text-blue-500 mb-2">100%</div>
                    <div class="text-gray-400">Satisfaction Rate</div>
                </div>
            </div>
        </div>
    </section>
    
    <!-- Chatbot Widget -->
    <div class="chatbot-widget">
        <div class="chatbot-button" onclick="toggleChat()">
            <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6" fill="none" viewBox="0 0 24 24"
                stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                    d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z" />
            </svg>
        </div>
        
        <div class="chatbot-window">
            <div class="chatbot-header">
                <span>AI Assistant</span>
                <span class="chatbot-close" onclick="toggleChat()">×</span>
            </div>
            <div class="chatbot-messages"></div>
            <div class="typing-indicator">AI is thinking...</div>
            <div class="chatbot-input">
                <input type="text" placeholder="Type your message..." onkeypress="handleKeyPress(event)">
                <button onclick="sendMessage()">
                    <span>Send</span>
                    <svg xmlns="http://www.w3.org/2000/svg" class="w-4 h-4" fill="none"
                        viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                            d="M22 2L11 13M22 2l-9 9M22 2v7l-9 9V11L22 2z" />
                    </svg>
                </button>
            </div>
        </div>
    </div>

    <!-- Footer Section -->
    <footer class="footer">
        <div class="max-w-7xl mx-auto px-4">
            <div class="footer-links">
                <a href="Privacy Policy.html">Privacy Policy</a>
                <a href="Terms of Service.html">Terms of Service</a>
                <a href="Contact Us.html">Contact Us</a>
                <a href="blogs.html">Blogs</a>
                <a href="testimonials.html">Testimonials</a>
            </div>
            <div class="footer-bottom">
                &copy; 2023 Nextap AI. All rights reserved.
            </div>
        </div>
    </footer>

    <script>
// Global object to store live exchange rates
let exchangeRates = {};
let baseCurrency = 'USD'; // Default value, can be changed

// Function to change base currency
function changeBaseCurrency(newBaseCurrency) {
    baseCurrency = newBaseCurrency;
    loadExchangeRates().then(() => {
        updateAllPrices();
    });
}

// Initialize on page load
window.onload = function() {
    // Load exchange rates first, then detect location
    loadExchangeRates().then(() => {
        fetch('https://ipapi.co/json/')
            .then(response => response.json())
            .then(data => {
                const countryCode = data.country;
                const currency = countryToCurrency(countryCode);
                if (currency) {
                    if (document.getElementById('currencySelect')) {
                        document.getElementById('currencySelect').value = currency;
                    }
                    if (document.getElementById('mobileCurrencySelect')) {
                        document.getElementById('mobileCurrencySelect').value = currency;
                    }
                    updateAllPrices();
                }
            })
            .catch((error) => {
                console.error('Error fetching GeoIP data:', error);
                updateAllPrices(); // Update with default currency
            });
    });
};

// Function to load live exchange rates with fixed Gulf rates
async function loadExchangeRates() {
    try {
        const response = await fetch(`https://api.frankfurter.app/latest?from=${baseCurrency}`);
        const data = await response.json();
        if (data && data.rates) {
            exchangeRates = data.rates;
            
            // Add fixed rates for Gulf currencies relative to USD
            if (baseCurrency !== 'USD') {
                // First get USD rate relative to base currency
                const usdRate = exchangeRates.USD || (baseCurrency === 'USD' ? 1 : 0.01154);
                // Then calculate Gulf currencies based on their USD pegs
                exchangeRates.AED = usdRate * 3.67;
                exchangeRates.SAR = usdRate * 3.75;
            } else {
                // If base is USD, use direct peg values
                exchangeRates.AED = 3.67;
                exchangeRates.SAR = 3.75;
            }
            
            console.log('Exchange rates loaded with Gulf currencies:', exchangeRates);
        } else {
            throw new Error('Invalid exchange rate data');
        }
    } catch (error) {
        console.error('Error loading exchange rates:', error);
        // Fallback rates will be relative to current base currency
        exchangeRates = {
            'USD': baseCurrency === 'USD' ? 1 : 0.01154,
            'AED': baseCurrency === 'USD' ? 3.67 : 0.042,
            'SAR': baseCurrency === 'USD' ? 3.75 : 0.042,
            'EUR': baseCurrency === 'USD' ? 0.95 : 0.011,
            'GBP': baseCurrency === 'USD' ? 0.82 : 0.0095,
            'JPY': baseCurrency === 'USD' ? 155.65 : 1.7963,
            'KWD': baseCurrency === 'USD' ? 0.31 : 0.0036,
        };
    }
}

// Update the conversion function to handle dynamic base currency
function convertCurrency(amount, targetCurrency) {
    if (targetCurrency === baseCurrency) {
        return amount;
    }
    
    let rate = exchangeRates[targetCurrency];
    
    // Special handling for Gulf currencies to ensure exact USD equivalence
    if ((targetCurrency === 'AED' || targetCurrency === 'SAR') && baseCurrency === 'USD') {
        rate = targetCurrency === 'AED' ? 3.67 : 3.75;
    }
    
    if (!rate) {
        console.error(`Exchange rate not found for ${targetCurrency}`);
        return amount;
    }
    
    return amount * rate;
}

// Map country codes to currency codes
function countryToCurrency(countryCode) {
    const mapping = {
        'US': 'USD',
        'GB': 'GBP',
        'AU': 'AUD',
        'JP': 'JPY',
        'AE': 'AED',
        'MY': 'MYR',
        'IN': 'INR',
        'CA': 'CAD',
        'QA': 'QAR',
        'SA': 'SAR',
        'KW': 'KWD',
    };
    return mapping[countryCode] || baseCurrency;
}

// Select HTML elements
const currencySelect = document.getElementById('currencySelect');
const mobileCurrencySelect = document.getElementById('mobileCurrencySelect');
const serviceType = document.getElementById('serviceType');
const wordCount = document.getElementById('wordCount');
const slideCount = document.getElementById('slideCount');
const qualityLevel = document.getElementById('qualityLevel');
const wordCountDiv = document.getElementById('wordCountDiv');
const slideCountDiv = document.getElementById('slideCountDiv');
const qualityLevelDiv = document.getElementById('qualityLevelDiv');
const estimatedCost = document.getElementById('estimatedCost');
const languageSelect = document.getElementById('languageSelect');
const mobileLanguageSelect = document.getElementById('mobileLanguageSelect');
const quoteForm = document.getElementById('quoteForm');
const quoteRequestForm = document.getElementById('quoteRequestForm');

// Update calculator function with live rates
function updateCalculator(service = 'report') {
    const currency = currencySelect.value;
    let cost = 0;
    const type = serviceType.value;

    const pricingData = {
        report: {
            standard: 0.035,
            premium: 0.058,
            enterprise: 0.092
        },
        presentation: {
            standard: 5.78,
            premium: 8.66,
            enterprise: 11.55
        }
    };

    if (type === 'report') {
        const rates = pricingData.report;
        const unitPrice = rates[qualityLevel.value];
        const wordCountValue = parseInt(wordCount.value) || 0;
        cost = wordCountValue * unitPrice;
    } else if (type === 'presentation') {
        const rates = pricingData.presentation;
        const unitPrice = rates[qualityLevel.value] || 0.058;
        const slideCountValue = parseInt(slideCount.value) || 0;
        cost = slideCountValue * unitPrice;
    } else if (type === 'coding') {
        estimatedCost.textContent = 'Custom Quote';
        return;
    }

    if (type !== 'coding') {
        if (currency === baseCurrency) {
            estimatedCost.textContent = getCurrencySymbol(currency) + cost.toFixed(2);
        } else {
            const convertedCost = convertCurrency(cost, currency);
            estimatedCost.textContent = getCurrencySymbol(currency) + convertedCost.toFixed(2);
        }
    }
}

// Function to get currency symbol
function getCurrencySymbol(currencyCode) {
    const symbols = {
        'USD': '$',
        'EUR': '€',
        'GBP': '£',
        'AUD': 'A$',
        'JPY': '¥',
        'AED': 'د.إ',
        'MYR': 'RM',
        'INR': '₹',
        'CAD': 'C$',
        'QAR': 'ر.ق',
        'SAR': '﷼',
        'KWD': 'د.ك',
    };
    return symbols[currencyCode] || currencyCode + ' ';
}

// Update all price displays
function updateAllPrices() {
    const currency = currencySelect.value;
    const priceElements = document.querySelectorAll('.price');
    
    priceElements.forEach(el => {
        const basePriceUSD = parseFloat(el.getAttribute('data-price-usd'));
        if (!isNaN(basePriceUSD)) {
            if (currency === baseCurrency) {
                el.textContent = getCurrencySymbol(currency) + basePriceUSD.toFixed(2);
            } else {
                const convertedPrice = convertCurrency(basePriceUSD, currency);
                el.textContent = getCurrencySymbol(currency) + convertedPrice.toFixed(2);
            }
        }
    });
    
    updateCalculator();
}

// Event listeners for currency selection
[currencySelect, mobileCurrencySelect].forEach((element) => {
    if (element) {
        element.addEventListener('change', (e) => {
            const selectedCurrency = e.target.value;
            loadExchangeRates().then(() => updateAllPrices());
        });
    }
});

// Event listeners for calculator inputs
[serviceType, wordCount, slideCount, qualityLevel].forEach((element) => {
    if (element) {
        element.addEventListener('input', () => {
            const service = serviceType.value;
            updateCalculator(service);
        });
    }
});

// Language selection function
function changeLanguage(lang) {
    const translateElement = document.querySelector('.goog-te-combo');
    if (translateElement) {
        translateElement.value = lang;
        translateElement.dispatchEvent(new Event('change'));
    }
}

// Language selection event listeners
[languageSelect, mobileLanguageSelect].forEach((element) => {
    if (element) {
        element.addEventListener('change', (e) => {
            changeLanguage(e.target.value);
        });
    }
});

// Service type change event handler
serviceType.addEventListener('change', () => {
    const type = serviceType.value;
    if (type === 'report') {
        wordCountDiv.style.display = 'block';
        qualityLevelDiv.style.display = 'block';
        slideCountDiv.style.display = 'none';
    } else if (type === 'presentation') {
        wordCountDiv.style.display = 'none';
        qualityLevelDiv.style.display = 'block';
        slideCountDiv.style.display = 'block';
    } else if (type === 'coding') {
        wordCountDiv.style.display = 'none';
        qualityLevelDiv.style.display = 'none';
        slideCountDiv.style.display = 'none';
        estimatedCost.textContent = 'Custom Quote';
    }
    updateCalculator();
});

// Scroll to calculator function
function scrollToCalculator(service = 'report') {
    document.getElementById('calculator').scrollIntoView({ behavior: 'smooth' });
    const calculatorSection = document.getElementById('calculator');
    calculatorSection.classList.add('highlight-section');
    setTimeout(() => calculatorSection.classList.remove('highlight-section'), 2000);
}

// Navigation to schedule page
function navigateToSchedule() {
    window.location.href = 'schedule.html';
}

// Show quote form function
function showQuoteForm() {
    quoteForm.classList.add('active');
    quoteForm.scrollIntoView({ behavior: 'smooth' });
}

// Handle mobile menu
document.querySelectorAll('.mobile-menu a').forEach(menuItem => {
    menuItem.addEventListener('click', toggleMobileMenu);
});

// Quote form submission handler
quoteRequestForm.addEventListener('submit', (e) => {
    e.preventDefault();
    const projectName = document.getElementById('projectName').value.trim();
    const projectDetails = document.getElementById('projectDetails').value.trim();
    const deadline = document.getElementById('deadline').value;
    const contactEmail = document.getElementById('contactEmail').value.trim();

    if (!projectName || !projectDetails || !deadline || !contactEmail) {
        alert('Please fill in all fields.');
        return;
    }

    alert('Your request has been submitted. We will contact you shortly.');
    quoteRequestForm.reset();
    quoteForm.classList.remove('active');
});

// Window unload handler
window.addEventListener('beforeunload', () => {
    const chatMessages = document.querySelector('.chatbot-messages');
    if (chatMessages.children.length > 0) {
        sessionStorage.setItem('chatHistory', chatMessages.innerHTML);
    }
});

// Refresh exchange rates periodically
setInterval(loadExchangeRates, 3600000); // Refresh every hour
</script>

<!-- Calculator Logic -->
<script>
function changeCurrency(selectedCurrency) {
    if (currencySelect) {
        currencySelect.value = selectedCurrency;
        currencySelect.dispatchEvent(new Event('change'));
    }
}
</script>

<!-- Chatbot Script -->
<script>
const API_KEY = "AIzaSyCE444N0GxkkQhfD5Hzws4jyvTV-bEuVJA";

// Initialize key variables
let chatWindow = document.querySelector('.chatbot-window');
let chatMessages = document.querySelector('.chatbot-messages');
let chatInputField = document.querySelector('.chatbot-input input');
let typingIndicator = document.querySelector('.typing-indicator');
let isProcessing = false;
const MAX_RETRIES = 3;
const RETRY_DELAY = 1000;

// System prompt
const SYSTEM_PROMPT = `You are an AI assistant for our academic services website. Please help users with:
- Questions about our services (report writing, coding projects, presentations)
- Pricing inquiries and custom quotes
- Project consultations and scheduling
- Technical support and requirements
- General academic assistance inquiries

Guidelines:
1. Keep responses professional, clear, and concise
2. Suggest scheduling consultations for complex projects using #schedule_consultation#
3. Direct to pricing calculator for cost discussions using #show_calculator#
4. Focus on academic services and project-related topics
5. Provide clear next steps and actionable guidance
6. Maintain ethical academic standards in responses`;

// Initialize chatbot
document.addEventListener('DOMContentLoaded', () => {
    if (sessionStorage.getItem('chatHistory')) {
        chatMessages.innerHTML = sessionStorage.getItem('chatHistory');
        chatMessages.scrollTo({ top: chatMessages.scrollHeight, behavior: 'smooth' });
    } else {
        addMessage("Hello! I'm your AI assistant. How can I help you with your academic project today?", 'bot');
    }
});

// Toggle chat window
function toggleChat() {
    const display = chatWindow.style.display;
    chatWindow.style.display = display === 'none' || display === '' ? 'flex' : 'none';
    if (chatWindow.style.display === 'flex') {
        chatInputField.focus();
        if (chatMessages.children.length === 0) {
            addMessage("Hello! How can I assist you with your academic needs today?", 'bot');
        }
    }
}

// Handle key press
function handleKeyPress(event) {
    if (event.key === 'Enter' && !event.shiftKey && !isProcessing) {
        event.preventDefault();
        sendMessage();
    }
}

// Add message to chat
function addMessage(text, sender, isError = false) {
    const messageDiv = document.createElement('div');
    messageDiv.classList.add('message', `${sender}-message`);
    if (isError) messageDiv.classList.add('error-message');

    const formattedText = text
        .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
        .replace(/\*(.*?)\*/g, '<em>$1</em>')
        .replace(/`(.*?)`/g, '<code>$1</code>')
        .replace(/\n/g, '<br>');

    messageDiv.innerHTML = formattedText;
    chatMessages.appendChild(messageDiv);
    chatMessages.scrollTo({ top: chatMessages.scrollHeight, behavior: 'smooth' });
}

// Add action buttons
function addActionButton(actionType) {
    const buttonDiv = document.createElement('div');
    buttonDiv.classList.add('chat-action-button');
    
    const buttonConfig = {
        schedule: {
            text: 'Schedule Consultation',
            onClick: 'scheduleConsultation()',
            icon: '<svg class="w-4 h-4 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m0 10v-3m-3 3H6"></path></svg>'
        }
    };

    const config = buttonConfig[actionType];
    if (config) {
        buttonDiv.innerHTML = `
            <button onclick="${config.onClick}" class="flex items-center justify-center bg-gray-800 hover:bg-gray-700 text-white px-4 py-2 rounded-lg">
                ${config.icon}
                ${config.text}
            </button>
        `;
    }
    chatMessages.appendChild(buttonDiv);
    chatMessages.scrollTop = chatMessages.scrollHeight;
}

// Send message function
async function sendMessage() {
    if (isProcessing) return;

    const message = chatInputField.value.trim();
    if (!message) return;

    isProcessing = true;
    addMessage(message, 'user');
    chatInputField.value = '';
    typingIndicator.style.display = 'block';

    let retryCount = 0;
    while (retryCount < MAX_RETRIES) {
        try {
            const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key=${API_KEY}`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    contents: [{
                        parts: [{
                            text: `${SYSTEM_PROMPT}\n\nUser: ${message}`
                        }]
                    }],
                    generationConfig: {
                        temperature: 0.7,
                        topK: 40,
                        topP: 0.95,
                        maxOutputTokens: 1024,
                        stopSequences: ["User:", "Assistant:"]
                    },
                    safetySettings: [
                        {
                            category: "HARM_CATEGORY_HARASSMENT",
                            threshold: "BLOCK_MEDIUM_AND_ABOVE"
                        },
                        {
                            category: "HARM_CATEGORY_HATE_SPEECH",
                            threshold: "BLOCK_MEDIUM_AND_ABOVE"
                        }
                    ]
                })
            });

            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }

            const data = await response.json();

            if (data.candidates && data.candidates[0].content.parts[0].text) {
                const botResponse = data.candidates[0].content.parts[0].text;
                
                const cleanResponse = botResponse.replace(/#schedule_consultation#|#show_calculator#/g, '').trim();
                addMessage(cleanResponse, 'bot');

                if (botResponse.includes('#schedule_consultation#')) {
                    addActionButton('schedule');
                }
                if (botResponse.includes('#show_calculator#')) {
                    addActionButton('calculator');
                }
                break;
            } else {
                throw new Error('Invalid API response format');
            }
        } catch (error) {
            console.error('Error:', error);
            retryCount++;
            if (retryCount === MAX_RETRIES) {
                addMessage("I apologize, but I'm having trouble connecting. Please try again later.", 'bot', true);
            } else {
                await new Promise(resolve => setTimeout(resolve, RETRY_DELAY));
            }
        }
    }

    typingIndicator.style.display = 'none';
    isProcessing = false;
}

// Navigation functions
function scheduleConsultation() {
    navigateToSchedule();
}

function showCalculator() {
    scrollToCalculator();
}

// Function to handle testimonial navigation
function navigateToTestimonials() {
    window.location.href = 'testimonials.html';
}
</script>
</body>
</html>

```

# schedule.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Schedule Consultation - Nextap AI</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.css" rel="stylesheet">
    <style>
        :root {
            --primary-color: #171717;
            --secondary-color: #404040;
            --accent-color: #3b82f6;
            --text-color: #f3f4f6;
        }

        body {
            background-color: var(--primary-color);
            color: var(--text-color);
            scroll-behavior: smooth;
        }

        .form-container {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.5s ease-in-out;
            background: rgba(255, 255, 255, 0.05);
            border-radius: 1rem;
        }

        .form-container.visible {
            max-height: 1000px;
        }

        .form-input {
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.1);
            color: var(--text-color);
            transition: all 0.3s ease;
        }

        .form-input:focus {
            border-color: var(--accent-color);
            outline: none;
            box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.2);
        }

        .calendar {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(10px);
        }

        .calendar-day {
            transition: all 0.3s ease;
            cursor: pointer;
            position: relative;
        }

        .calendar-day:hover:not(.disabled) {
            transform: translateY(-2px);
            background: rgba(59, 130, 246, 0.2);
        }

        .calendar-day.selected {
            background: var(--accent-color);
            color: white;
        }

        .calendar-day.disabled {
            opacity: 0.5;
            cursor: not-allowed;
            background: rgba(239, 68, 68, 0.1);
        }

        .calendar-day.today {
            border: 2px solid var(--accent-color);
        }

        .calendar-day .slot-count {
            position: absolute;
            bottom: 2px;
            right: 2px;
            font-size: 0.6rem;
            color: var(--text-color);
        }

        .time-slots-wrapper {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
            gap: 0.75rem;
            padding: 1rem;
            width: 100%;
        }

        .time-slot {
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.1);
            padding: 0.75rem;
            text-align: center;
            border-radius: 0.5rem;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            min-width: 120px;
        }

        .time-slot:hover:not(.disabled) {
            background: rgba(59, 130, 246, 0.2);
            transform: translateY(-2px);
        }

        .time-slot.selected {
            background: var(--accent-color);
            color: white;
        }

        .progress-steps {
            display: flex;
            justify-content: space-between;
            margin-bottom: 2rem;
            position: relative;
            padding: 0 1rem;
        }

        .progress-step {
            flex: 1;
            text-align: center;
            padding: 1rem;
            position: relative;
            transition: all 0.3s ease;
        }

        .progress-step.active {
            color: var(--accent-color);
        }

        .progress-step .step-number {
            display: inline-block;
            width: 30px;
            height: 30px;
            line-height: 30px;
            border-radius: 50%;
            background: var(--secondary-color);
            margin-bottom: 0.5rem;
        }

        .progress-step.active .step-number {
            background: var(--accent-color);
        }

        .progress-step:not(:last-child)::after {
            content: '';
            position: absolute;
            top: 25%;
            right: -50%;
            width: 100%;
            height: 2px;
            background: var(--secondary-color);
        }

        .progress-step.completed:not(:last-child)::after {
            background: var(--accent-color);
        }

        .progress-step.completed .step-number {
            background: var(--accent-color);
        }

        .error-message {
            background-color: rgba(239, 68, 68, 0.1);
            border: 1px solid rgba(239, 68, 68, 0.2);
            color: #ef4444;
            padding: 1rem;
            border-radius: 0.5rem;
            margin: 1rem 0;
        }

        #loadingSpinner {
            background: rgba(0, 0, 0, 0.7);
            backdrop-filter: blur(5px);
        }
    </style>
</head>
<body class="min-h-screen">
    <!-- Navigation -->
    <nav class="fixed w-full z-40 bg-black bg-opacity-90 backdrop-blur-md">
        <div class="max-w-7xl mx-auto px-4">
            <div class="flex justify-between h-16 items-center">
                <a href="index.html" class="text-2xl font-bold bg-gradient-to-r from-blue-400 to-blue-600 text-transparent bg-clip-text">
                    Nextap AI
                </a>
            </div>
        </div>
    </nav>

    <!-- Main Content -->
    <div class="pt-20 max-w-6xl mx-auto px-4">
        <h1 class="text-center text-4xl font-bold mb-12 bg-gradient-to-r from-blue-400 to-blue-600 text-transparent bg-clip-text">
            Schedule One-to-one Consultation
        </h1>

        <!-- Progress Steps -->
        <div class="progress-steps mb-12">
            <div class="progress-step active" data-step="1">
                <span class="step-number">1</span>
                <div class="step-title">Select Date</div>
            </div>
            <div class="progress-step" data-step="2">
                <span class="step-number">2</span>
                <div class="step-title">Choose Time</div>
            </div>
            <div class="progress-step" data-step="3">
                <span class="step-number">3</span>
                <div class="step-title">Confirm Details</div>
            </div>
        </div>

        <!-- Form Container -->
        <div id="appointmentFormContainer" class="form-container mb-8">
            <div class="p-6">
                <form id="appointmentForm" class="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <div class="space-y-6">
                        <div>
                            <label for="name" class="block text-sm font-medium mb-2">Name</label>
                            <input type="text" id="name" name="name" required
                                class="form-input w-full px-4 py-3 rounded-lg text-lg">
                        </div>
                        <div>
                            <label for="email" class="block text-sm font-medium mb-2">Email</label>
                            <input type="email" id="email" name="email" required
                                class="form-input w-full px-4 py-3 rounded-lg text-lg">
                        </div>
                        <div>
                            <label for="message" class="block text-sm font-medium mb-2">Message (Optional)</label>
                            <textarea id="message" name="message" rows="4"
                                class="form-input w-full px-4 py-3 rounded-lg text-lg"></textarea>
                        </div>
                    </div>
                    <div class="space-y-6">
                        <div class="bg-opacity-10 bg-white p-6 rounded-lg backdrop-blur-sm">
                            <h3 class="font-semibold text-xl mb-4">Selected Time Slot</h3>
                            <p class="mb-2">Date: <span id="selectedDateDisplay" class="font-medium"></span></p>
                            <p>Time: <span id="selectedTimeDisplay" class="font-medium"></span></p>
                        </div>
                        <button type="submit" 
                            class="w-full bg-blue-600 text-white py-4 rounded-lg hover:bg-blue-700 transition-all text-lg font-medium">
                            Schedule Appointment
                        </button>
                    </div>
                </form>
            </div>
        </div>

        <!-- Calendar Section -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
            <div class="calendar p-6 rounded-xl">
                <div class="flex justify-between items-center mb-6">
                    <button id="prevMonth" class="text-gray-400 hover:text-white p-2 rounded-full hover:bg-gray-800 transition-all">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
                        </svg>
                    </button>
                    <h2 id="currentMonth" class="text-xl font-semibold"></h2>
                    <button id="nextMonth" class="text-gray-400 hover:text-white p-2 rounded-full hover:bg-gray-800 transition-all">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
                        </svg>
                    </button>
                </div>
                <div class="grid grid-cols-7 gap-2 text-center mb-4">
                    <div class="text-sm text-gray-400">Sun</div>
                    <div class="text-sm text-gray-400">Mon</div>
                    <div class="text-sm text-gray-400">Tue</div>
                    <div class="text-sm text-gray-400">Wed</div>
                    <div class="text-sm text-gray-400">Thu</div>
                    <div class="text-sm text-gray-400">Fri</div>
                    <div class="text-sm text-gray-400">Sat</div>
                </div>
                <div id="calendarDays" class="grid grid-cols-7 gap-2">
                    <!-- Calendar days will be inserted here -->
                </div>
            </div>

            <!-- Time Slots -->
            <div id="timeSlots" class="space-y-4">
                <!-- Time slots will be inserted here -->
            </div>
        </div>
    </div>

    <!-- Loading Spinner -->
    <div id="loadingSpinner" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="animate-spin rounded-full h-16 w-16 border-t-2 border-b-2 border-blue-500"></div>
    </div>

    <script>
        // Replace with your Apps Script URL
        const SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbxVKY1qgWoDfvRsVIR7StMobrfbJ4YTkbFJWjZlGhiHl2RAPf48d1CCza9zMnHwTeF8Wg/exec';

        let selectedDate = null;
        let selectedTime = null;
        let bookedSlots = [];
        let currentStep = 1;
        let availableTimeSlots = [];

        // Initialize calendar
        async function initCalendar() {
            showSpinner();
            try {
                await fetchBookedSlots();
                updateCalendar();
                updateSteps(1);
                
                if (selectedDate) {
                    updateTimeSlots();
                }
            } catch (error) {
                console.error('Error initializing calendar:', error);
                alert('Error loading appointment data. Please refresh the page.');
            } finally {
                hideSpinner();
            }
        }

        function updateSteps(step) {
            currentStep = step;
            document.querySelectorAll('.progress-step').forEach((el, index) => {
                if (index + 1 < step) {
                    el.classList.add('completed');
                } else {
                    el.classList.remove('completed');
                }
                
                if (index + 1 === step) {
                    el.classList.add('active');
                } else {
                    el.classList.remove('active');
                }
            });
        }

        async function fetchBookedSlots() {
            showSpinner();
            try {
                const response = await fetch(SCRIPT_URL);
                const data = await response.json();
                bookedSlots = data.bookedSlots || [];
                availableTimeSlots = data.availableTimeSlots || generateDefaultTimeSlots();
            } catch (error) {
                console.error('Error fetching slots:', error);
                availableTimeSlots = generateDefaultTimeSlots();
            }
            hideSpinner();
        }

        function generateDefaultTimeSlots() {
            const slots = [];
            for (let hour = 9; hour <= 17; hour++) {
                slots.push(`${hour.toString().padStart(2, '0')}:00`);
                if (hour !== 17) slots.push(`${hour.toString().padStart(2, '0')}:30`);
            }
            return slots;
        }

        function updateCalendar() {
            const firstDay = new Date(currentDate.getFullYear(), currentDate.getMonth(), 1);
            const lastDay = new Date(currentDate.getFullYear(), currentDate.getMonth() + 1, 0);
            
            document.getElementById('currentMonth').textContent = 
                firstDay.toLocaleDateString('en-US', { month: 'long', year: 'numeric' });
            
            const calendarDays = document.getElementById('calendarDays');
            calendarDays.innerHTML = '';
            
            // Add empty cells for days before the first day of the month
            for (let i = 0; i < firstDay.getDay(); i++) {
                calendarDays.appendChild(createDayElement(''));
            }
            
            // Add days of the month
            for (let day = 1; day <= lastDay.getDate(); day++) {
                const date = new Date(currentDate.getFullYear(), currentDate.getMonth(), day);
                calendarDays.appendChild(createDayElement(day, date));
            }
        }

        function createDayElement(day, date) {
            const div = document.createElement('div');
            div.textContent = day;
            div.className = 'calendar-day p-3 rounded-lg text-center relative hover:bg-opacity-20';
            
            if (!day) return div;
            
            const today = new Date();
            const isToday = date?.toDateString() === today.toDateString();
            const isPast = date < new Date(today.setHours(0,0,0,0));
            
            if (isToday) {
                div.classList.add('today');
                div.setAttribute('title', 'Today');
            }

            if (isPast) {
                div.classList.add('disabled');
                div.setAttribute('title', 'Past date');
            } else {
                div.onclick = () => selectDate(date);
                
                // Add available slots count
                const availableCount = getAvailableSlotsCount(date);
                if (availableCount > 0) {
                    const countSpan = document.createElement('span');
                    countSpan.className = 'slot-count text-xs bg-blue-500 text-white px-1 rounded absolute bottom-1 right-1';
                    countSpan.textContent = availableCount;
                    div.appendChild(countSpan);
                } else {
                    div.classList.add('disabled');
                    div.setAttribute('title', 'No available slots');
                }
            }
            
            return div;
        }

        function getAvailableSlotsCount(date) {
            if (!date) return 0;
            const dateStr = date.toISOString().split('T')[0];
            const bookedSlotsForDate = bookedSlots.filter(slot => slot.date === dateStr);
            const availableSlots = getAvailableTimeSlotsForDate(date);
            return availableSlots.length;
        }

        function getAvailableTimeSlotsForDate(date) {
            const dateStr = date.toISOString().split('T')[0];
            const bookedSlotsForDate = bookedSlots.filter(slot => slot.date === dateStr);
            const bookedTimes = new Set(bookedSlotsForDate.map(slot => slot.time.trim()));
            
            const now = new Date();
            const isToday = dateStr === now.toISOString().split('T')[0];
            const currentHour = now.getHours();
            const currentMinute = now.getMinutes();
            
            return availableTimeSlots.filter(time => {
                const standardizedTime = time.trim();
                
                if (bookedTimes.has(standardizedTime)) return false;
                
                if (isToday) {
                    const [hour, minute] = standardizedTime.split(':').map(Number);
                    if (hour < currentHour || (hour === currentHour && minute <= currentMinute)) {
                        return false;
                    }
                }
                
                return true;
            });
        }

        function selectDate(date) {
            selectedDate = date;
            document.querySelectorAll('.calendar-day').forEach(day => {
                day.classList.remove('selected');
            });
            event.target.closest('.calendar-day').classList.add('selected');
            
            updateTimeSlots();
            updateSteps(2);
            
            // Smooth scroll to time slots
            setTimeout(() => {
                document.getElementById('timeSlots').scrollIntoView({ 
                    behavior: 'smooth',
                    block: 'start' 
                });
            }, 300);
        }

        function updateTimeSlots() {
            const timeSlotsDiv = document.getElementById('timeSlots');
            timeSlotsDiv.innerHTML = `
                <h3 class="text-xl font-semibold mb-6">Available Time Slots</h3>
                <p class="text-gray-400 mb-4">Selected date: ${selectedDate.toLocaleDateString('en-US', { 
                    weekday: 'long',
                    year: 'numeric',
                    month: 'long',
                    day: 'numeric'
                })}</p>
            `;
            
            if (!selectedDate) return;
            
            const availableSlots = getAvailableTimeSlotsForDate(selectedDate);
            
            if (availableSlots.length === 0) {
                const message = document.createElement('div');
                message.className = 'error-message';
                message.innerHTML = `
                    <p class="font-semibold mb-2">No available time slots for this date.</p>
                    <p>Please select another date from the calendar.</p>
                `;
                timeSlotsDiv.appendChild(message);
                return;
            }
        
            const gridWrapper = document.createElement('div');
            gridWrapper.className = 'time-slots-wrapper';
        
            availableSlots.forEach(time => {
                const slot = document.createElement('div');
                slot.className = 'time-slot hover:bg-blue-600';
                slot.textContent = time;
                slot.onclick = () => selectTime(time);
                
                if (selectedTime === time) {
                    slot.classList.add('selected');
                }
                
                gridWrapper.appendChild(slot);
            });
        
            timeSlotsDiv.appendChild(gridWrapper);
        }

        function selectTime(time) {
            selectedTime = time;
            document.querySelectorAll('.time-slot').forEach(slot => {
                slot.classList.remove('selected');
            });
            event.target.classList.add('selected');
            
            showAppointmentForm();
            updateSteps(3);
        }

        function showAppointmentForm() {
            const formContainer = document.getElementById('appointmentFormContainer');
            formContainer.classList.add('visible');
            
            document.getElementById('selectedDateDisplay').textContent = 
                selectedDate.toLocaleDateString('en-US', { 
                    weekday: 'long',
                    year: 'numeric',
                    month: 'long',
                    day: 'numeric'
                });
            document.getElementById('selectedTimeDisplay').textContent = selectedTime;

            // Smooth scroll to form
            setTimeout(() => {
                formContainer.scrollIntoView({ 
                    behavior: 'smooth',
                    block: 'center'
                });
            }, 300);
        }

        function showSpinner() {
            document.getElementById('loadingSpinner').classList.remove('hidden');
        }

        function hideSpinner() {
            document.getElementById('loadingSpinner').classList.add('hidden');
        }

        // Form submission
        document.getElementById('appointmentForm').onsubmit = async function(e) {
            e.preventDefault();
            showSpinner();
            
            // Double-check if slot is still available
            await fetchBookedSlots();
            const availableSlots = getAvailableTimeSlotsForDate(selectedDate);
            if (!availableSlots.includes(selectedTime)) {
                hideSpinner();
                alert('Sorry, this time slot was just booked. Please select another time.');
                updateTimeSlots();
                return;
            }
            
            const formData = new FormData(e.target);
            const params = new URLSearchParams({
                name: formData.get('name'),
                email: formData.get('email'),
                date: selectedDate.toISOString().split('T')[0],
                time: selectedTime,
                message: formData.get('message') || ''
            });

            try {
                const response = await fetch(`${SCRIPT_URL}?${params.toString()}`, {
                    method: 'POST'
                });
                const result = await response.json();
                
                if (result.success) {
                    alert('Appointment scheduled successfully!');
                    window.location.reload();
                } else {
                    alert(result.message || 'Failed to schedule appointment. Please try again.');
                    updateTimeSlots();
                }
            } catch (error) {
                console.error('Error scheduling appointment:', error);
                alert('Failed to schedule appointment. Please try again.');
            } finally {
                hideSpinner();
            }
        };

        // Event listeners for month navigation
        document.getElementById('prevMonth').onclick = () => {
            currentDate.setMonth(currentDate.getMonth() - 1);
            updateCalendar();
        };

        document.getElementById('nextMonth').onclick = () => {
            currentDate.setMonth(currentDate.getMonth() + 1);
            updateCalendar();
        };

        // Initialize variables
        let currentDate = new Date();

        // Initialize the calendar when the page loads
        window.onload = initCalendar;
    </script>
</body>
</html>

```

# site_temp.mp4

This is a binary file of the type: Binary

# site_temp2.mp4

This is a binary file of the type: Binary

# testimonial.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nextap AI - Solutions Portfolio</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.css" rel="stylesheet">
    <style>
        :root {
            --primary-color: #171717;
            --secondary-color: #404041;
            --accent-color: #3b82f6;
            --text-color: #f3f4f6;
        }

        body {
            background-color: #000;
            color: var(--text-color);
            scroll-behavior: smooth;
        }

        .nav-blur {
            background: rgba(0, 0, 0, 0.8);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
        }

        .nav-link {
            position: relative;
            transition: color 0.3s ease;
        }

        .nav-link::after {
            content: '';
            position: absolute;
            width: 0;
            height: 2px;
            bottom: -4px;
            left: 0;
            background: var(--accent-color);
            transition: width 0.3s ease;
        }

        .nav-link:hover::after {
            width: 100%;
        }

        .solution-card {
            background: rgba(23, 23, 23, 0.95);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 12px;
            padding: 24px;
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            overflow: hidden;
            cursor: pointer;
        }

        .solution-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 2px;
            background: linear-gradient(90deg, transparent, var(--accent-color), transparent);
            transform: scaleX(0);
            transition: transform 0.6s ease;
        }

        .solution-card:hover {
            transform: translateY(-4px);
            box-shadow: 0 8px 30px rgba(59, 130, 246, 0.15);
        }

        .solution-card:hover::before {
            transform: scaleX(1);
        }

        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            backdrop-filter: blur(5px);
            z-index: 1000;
            opacity: 0;
            transition: opacity 0.3s ease;
        }

        .modal-content {
            background: var(--primary-color);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 16px;
            max-width: 900px;
            width: 90%;
            max-height: 90vh;
            margin: 5vh auto;
            padding: 32px;
            position: relative;
            transform: translateY(-20px);
            transition: all 0.3s ease;
            overflow-y: auto;
        }

        .chart-container {
            background: rgba(255, 255, 255, 0.02);
            border-radius: 12px;
            padding: 20px;
            margin: 20px 0;
        }

        .filter-chip {
            background: rgba(59, 130, 246, 0.1);
            border: 1px solid rgba(59, 130, 246, 0.2);
            border-radius: 20px;
            padding: 6px 16px;
            color: #93c5fd;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .filter-chip:hover {
            background: rgba(59, 130, 246, 0.2);
            transform: translateY(-1px);
        }

        .filter-chip.active {
            background: var(--accent-color);
            color: white;
        }

        .stats-card {
            background: linear-gradient(145deg, rgba(30, 41, 59, 0.7), rgba(15, 23, 42, 0.7));
            backdrop-filter: blur(10px);
            border: 1px solid rgba(59, 130, 246, 0.2);
            border-radius: 16px;
            padding: 1.5rem;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .stats-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(45deg, transparent, rgba(59, 130, 246, 0.1), transparent);
            transform: translateX(-100%);
            transition: transform 0.6s ease;
        }

        .stats-card:hover::before {
            transform: translateX(100%);
        }

        .stats-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 30px rgba(59, 130, 246, 0.2);
        }

        .world-map-section {
            position: relative;
            margin-top: 2rem;
            border-radius: 24px;
            overflow: hidden;
            background: linear-gradient(145deg, rgba(15, 23, 42, 0.8), rgba(30, 41, 59, 0.8));
        }

        .map-container {
            position: relative;
            width: 100%;
            padding-top: 56.25%; /* 16:9 Aspect Ratio */
            overflow: hidden;
        }

        .map-image {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        .hero-section {
            display: flex;
            align-items: center;
            min-height: calc(100vh - 64px); /* Adjust this value based on your nav height */
            position: relative;
            overflow: hidden;
            max-width: 1280px; /* Set a max-width to limit the size */
            margin: 0 auto; /* Center the hero section */
            padding: 2rem;
        }
    
        .hero-content {
            flex: 1;
            padding-right: 2rem;
            z-index: 1;
        }
    
        .hero-content h1 {
            color: #3b82f6;
        }
    
        .hero-content p {
            color: #6b7280;
        }
    
        .video-container {
            flex: 1;
            position: relative;
            width: 100%;
            padding-top: 56.25%; /* 16:9 aspect ratio */
        }
    
        #heroVideo {
            position: absolute;
            top: 0;
            right: 0;
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .animate-fade-in {
            animation: fadeIn 0.5s ease forwards;
        }
    </style>
</head>
<body class="min-h-screen">
    <!-- Navigation -->
    <nav class="fixed w-full z-50 nav-blur">
        <div class="max-w-7xl mx-auto px-4">
            <div class="flex justify-between h-16 items-center">
                <a href="index.html" class="text-2xl font-bold bg-gradient-to-r from-blue-400 to-blue-600 text-transparent bg-clip-text">
                    Nextap AI
                </a>
                <div class="hidden md:flex items-center space-x-8">
                    <a href="index.html" class="nav-link text-gray-300 hover:text-white">Services</a>
                    <a href="schedule.html" class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-2 rounded-lg transition-all duration-300 transform hover:scale-105">
                        Book Demo
                    </a>
                </div>
                <button class="md:hidden text-white">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7" />
                    </svg>
                </button>
            </div>
        </div>
    </nav>

    <!-- Hero Section -->
    <div class="hero-section">
        <div class="hero-content">
            <h1 class="text-4xl md:text-6xl font-bold mb-6">Our Solutions Portfolio</h1>
            <p class="text-xl mb-8">Discover how we're transforming industries</p>
        </div>
        <div class="video-container">
            <video id="heroVideo" autoplay loop muted playsinline>
                <source src="https://github.com/nextap-lab/nextap.github.io/raw/refs/heads/main/site_temp2.mp4" type="video/mp4">
            </video>
        </div>
    </div>

    <!-- Global Presence Section -->
    <div class="bg-black py-16">
        <div class="max-w-7xl mx-auto px-4">
            <h2 class="text-3xl font-bold mb-8">Global Customer Base</h2>
            
            <!-- Stats Cards -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-12">
                <div class="stats-card">
                    <div class="flex items-center justify-between">
                        <div>
                            <div class="text-4xl font-bold text-blue-400 mb-2">1280+</div>
                            <div class="text-gray-400">Active Users</div>
                        </div>
                        <div class="text-blue-400">
                            <svg class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z" />
                            </svg>
                        </div>
                    </div>
                </div>

                <div class="stats-card">
                    <div class="flex items-center justify-between">
                        <div>
                            <div class="text-4xl font-bold text-blue-400 mb-2">15+</div>
                            <div class="text-gray-400">Countries Served</div>
                        </div>
                        <div class="text-blue-400">
                            <svg class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M3.055 11H5a2 2 0 012 2v1a2 2 0 002 2 2 2 0 012 2v2.945M8 3.935V5.5A2.5 2.5 0 0010.5 8h.5a2 2 0 012 2 2 2 0 104 0 2 2 0 012-2h1.064M15 20.488V18a2 2 0 012-2h3.064M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                            </svg>
                        </div>
                    </div>
                </div>

                <div class="stats-card">
                    <div class="flex items-center justify-between">
                        <div>
                            <div class="text-4xl font-bold text-blue-400 mb-2">98%</div>
                            <div class="text-gray-400">Client Satisfaction</div>
                        </div>
                        <div class="text-blue-400">
                            <svg class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M14 10h4.764a2 2 0 011.789 2.894l-3.5 7A2 2 0 0115.263 21h-4.017c-.163 0-.326-.02-.485-.06L7 20m7-10V5a2 2 0 00-2-2h-.095c-.5 0-.905.405-.905.905 0 .714-.211 1.412-.608 2.006L7 11v9m7-10h-2M7 20H5a2 2 0 01-2-2v-6a2 2 0 012-2h2.5" />
                            </svg>
                        </div>
                    </div>
                </div>
            </div>

            <!-- World Map Section -->
            <div class="world-map-section">
                <div class="map-container" id="mapContainer">
                    <img src="https://github.com/nextap-lab/nextap.github.io/blob/main/world-map-nextap.jpg?raw=true" alt="Global Presence Map" class="map-image">
                    <!-- Active region dots will be added via JavaScript -->
                </div>
            </div>
        </div>
    </div>

    <!-- Solutions Grid Section -->
    <div class="max-w-7xl mx-auto px-4 py-12">
        <!-- Filters -->
        <div class="flex flex-wrap gap-3 mb-8">
            <button class="filter-chip active" data-filter="all">All</button>
            <button class="filter-chip" data-filter="education">Education</button>
            <button class="filter-chip" data-filter="technology">Technology</button>
            <button class="filter-chip" data-filter="research">Research</button>
        </div>

        <!-- Solutions Grid -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6" id="solutionsGrid">
            <!-- Cards will be dynamically inserted here -->
        </div>
    </div>

    <!-- Modal -->
    <div id="solutionModal" class="modal">
        <div class="modal-content">
            <button onclick="closeModal()" class="absolute top-4 right-4 text-gray-400 hover:text-white">
                <svg width="24" height="24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M6 18L18 6M6 6l12 12" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
            </button>
            <div id="modalContent"></div>
        </div>
    </div>

    <script>
        // Helper function to generate dot patterns
        function generateDotPattern(width, height, spacing, color) {
            let dots = '';
            for (let x = 0; x < width; x += spacing) {
                for (let y = 0; y < height; y += spacing) {
                    dots += `<circle cx="${x}" cy="${y}" r="1" fill="${color}" opacity="0.3" />`;
                }
            }
            return dots;
        }

        // Helper function to generate clusters
        function generateCluster(centerX, centerY, count, color, density) {
            let cluster = '';
            for (let i = 0; i < count; i++) {
                const angle = (Math.PI * 2 * i) / count;
                const distance = Math.random() * 15;
                const x = centerX + Math.cos(angle) * distance;
                const y = centerY + Math.sin(angle) * distance;
                const opacity = Math.random() * 0.5 + 0.5;
                cluster += `<circle cx="${x}" cy="${y}" r="1.5" fill="${color}" opacity="${opacity * density}" />`;
            }
            return cluster;
        }
        // Mobile menu toggle function
        function toggleMobileMenu() {
            const mobileMenu = document.querySelector('.mobile-menu');
            mobileMenu.classList.toggle('active');
        }
        
        // Solution data
        const solutions = [
            {
                id: 1,
                title: "Advanced Report Generation System",
                description: "Transform raw research data into comprehensive academic reports with AI-powered analysis and formatting.",
                metrics: {
                    accuracy: 98.7,
                    timeReduction: 75,
                    satisfaction: 4.9
                },
                tags: ["Research", "Education", "Quality"],
                industry: "education",
                features: [
                    "Advanced plagiarism detection",
                    "Automated citation management",
                    "Smart formatting templates",
                    "Real-time collaboration tools"
                ],
                caseStudy: {
                    title: "University Research Department",
                    results: "Improved report generation efficiency by 75% while maintaining 98.7% accuracy"
                }
            },
            {
                id: 2,
                title: "Intelligent Code Analysis Platform",
                description: "Advanced code review and optimization system powered by machine learning algorithms.",
                metrics: {
                    efficiency: 95.5,
                    bugReduction: 82,
                    satisfaction: 4.8
                },
                tags: ["Technology", "Quality", "Efficiency"],
                industry: "technology",
                features: [
                    "Real-time code analysis",
                    "Automated bug detection",
                    "Performance optimization",
                    "Security vulnerability scanning"
                ],
                caseStudy: {
                    title: "Tech Startup Success Story",
                    results: "Reduced bug rate by 82% and improved code quality scores by 95.5%"
                }
            },
            {
                id: 3,
                title: "Research Data Analytics Suite",
                description: "Comprehensive research data analysis and visualization platform for academic institutions.",
                metrics: {
                    accuracy: 97.2,
                    timeReduction: 68,
                    satisfaction: 4.7
                },
                tags: ["Research", "Analytics", "Education"],
                industry: "research",
                features: [
                    "Statistical analysis tools",
                    "Data visualization",
                    "Machine learning models",
                    "Collaborative research tools"
                ],
                caseStudy: {
                    title: "Research Institute Implementation",
                    results: "Accelerated research analysis by 68% with 97.2% accuracy rate"
                }
            }
        ];

        // Render solutions
        function renderSolutions(filteredSolutions = solutions) {
            const grid = document.getElementById('solutionsGrid');
            grid.innerHTML = '';

            filteredSolutions.forEach((solution, index) => {
                const card = document.createElement('div');
                card.className = 'solution-card animate-fade-in';
                card.style.animationDelay = `${index * 0.1}s`;
                card.onclick = () => showSolutionDetail(solution.id);

                card.innerHTML = `
                    <h3 class="text-xl font-bold mb-4">${solution.title}</h3>
                    <p class="text-gray-400 mb-6">${solution.description}</p>
                    <div class="flex flex-wrap gap-2 mb-4">
                        ${solution.tags.map(tag => `
                            <span class="filter-chip">${tag}</span>
                        `).join('')}
                    </div>
                    <div class="grid grid-cols-3 gap-4 mb-6">
                        ${Object.entries(solution.metrics).map(([key, value]) => `
                            <div class="text-center p-3 bg-opacity-10 bg-white rounded-lg">
                                <div class="text-xl font-bold text-blue-400">${typeof value === 'number' ? value.toFixed(1) : value}${typeof value === 'number' ? '%' : ''}</div>
                                <div class="text-sm text-gray-400">${key.replace(/([A-Z])/g, ' $1').trim()}</div>
                            </div>
                        `).join('')}
                    </div>
                `;

                grid.appendChild(card);
            });
        }

        // Filter functionality
        function filterSolutions(filter = 'all') {
            const filtered = solutions.filter(solution => 
                filter === 'all' || solution.industry === filter
            );
            renderSolutions(filtered);
        }

        // Modal functionality
        function showSolutionDetail(id) {
            const solution = solutions.find(s => s.id === id);
            const modal = document.getElementById('solutionModal');
            const content = document.getElementById('modalContent');

            content.innerHTML = `
                <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <div>
                        <img src="/api/placeholder/400/300" alt="${solution.title}" class="rounded-lg w-full mb-6">
                        <h2 class="text-2xl font-bold mb-4">${solution.title}</h2>
                        <p class="text-gray-400 mb-6">${solution.description}</p>
                        
                        <h3 class="text-xl font-bold mb-4">Key Features</h3>
                        <ul class="space-y-2 mb-6">
                            ${solution.features.map(feature => `
                                <li class="flex items-center">
                                    <svg class="w-5 h-5 text-blue-500 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/>
                                    </svg>
                                    ${feature}
                                </li>
                            `).join('')}
                        </ul>
                    </div>
                    
                    <div>
                        <div class="chart-container mb-6">
                            <h3 class="text-xl font-bold mb-4">Performance Metrics</h3>
                            ${Object.entries(solution.metrics).map(([key, value]) => `
                                <div class="mb-4">
                                    <div class="flex justify-between mb-2">
                                        <span>${key.replace(/([A-Z])/g, ' $1').trim()}</span>
                                        <span class="text-blue-400">${value}${typeof value === 'number' ? '%' : ''}</span>
                                    </div>
                                    <div class="w-full bg-gray-700 rounded-full h-2">
                                        <div class="bg-blue-500 rounded-full h-2" style="width: ${typeof value === 'number' ? value : 0}%"></div>
                                    </div>
                                </div>
                            `).join('')}
                        </div>

                        <div class="bg-gray-900 rounded-lg p-6 mb-6">
                            <h3 class="text-xl font-bold mb-4">${solution.caseStudy.title}</h3>
                            <div class="grid grid-cols-1 gap-4">
                                <div class="text-center">
                                    <div class="text-xl font-bold text-blue-400">${solution.caseStudy.results}</div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            `;

            modal.style.display = 'block';
            setTimeout(() => {
                modal.style.opacity = '1';
                modal.querySelector('.modal-content').style.transform = 'translateY(0)';
            }, 10);
        }

        function closeModal() {
            const modal = document.getElementById('solutionModal');
            modal.style.opacity = '0';
            modal.querySelector('.modal-content').style.transform = 'translateY(-20px)';
            setTimeout(() => {
                modal.style.display = 'none';
            }, 300);
        }

        // Event Listeners
        document.querySelectorAll('.filter-chip').forEach(chip => {
            chip.addEventListener('click', () => {
                document.querySelectorAll('.filter-chip').forEach(c => c.classList.remove('active'));
                chip.classList.add('active');
                filterSolutions(chip.dataset.filter);
            });
        });

        // Close modal when clicking outside
        window.onclick = function(event) {
            const modal = document.getElementById('solutionModal');
            if (event.target == modal) {
                closeModal();
            }
        }

        // Close modal with escape key
        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape') closeModal();
        });

        // Initialize page with all solutions
        document.addEventListener('DOMContentLoaded', () => {
            renderSolutions();
        });
    </script>
</body>
</html>

```

# world-map-nextap.jpg

This is a binary file of the type: Image

