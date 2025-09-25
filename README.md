# The-Gathering
Pathfinder 1e tracking for The Gathering campaign
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Embedded Content Viewer</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/feather-icons"></script>
    <script src="https://cdn.jsdelivr.net/npm/feather-icons/dist/feather.min.js"></script>
    <link href="https://unpkg.com/aos@2.3.1/dist/aos.css" rel="stylesheet">
    <script src="https://unpkg.com/aos@2.3.1/dist/aos.js"></script>
    <style>
        .tab-content {
            display: none;
        }
        .tab-content.active {
            display: block;
        }
        .tab-button.active {
            background-color: #3b82f6;
            color: white;
        }
        iframe {
            width: 100%;
            height: calc(100vh - 150px);
            border: none;
            border-radius: 0.5rem;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
        }
        .add-tab-btn {
            transition: all 0.2s ease;
        }
        .add-tab-btn:hover {
            transform: scale(1.05);
        }
        .tab-close {
            transition: all 0.2s ease;
        }
        .tab-close:hover {
            color: #ef4444;
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen">
    <div class="container mx-auto px-4 py-8">
        <header class="mb-8 text-center">
            <h1 class="text-4xl font-bold text-gray-800 mb-2">Embedded Content Viewer</h1>
            <p class="text-gray-600">View multiple websites in one place with easy tab navigation</p>
        </header>

        <div class="bg-white rounded-xl shadow-lg overflow-hidden">
            <!-- Tab Navigation -->
            <div class="flex items-center bg-gray-50 border-b border-gray-200 overflow-x-auto">
                <div class="flex flex-nowrap">
                    <!-- Tabs will be added here dynamically -->
                </div>
                <button id="addTabBtn" class="add-tab-btn flex items-center justify-center px-4 py-2 text-blue-500 hover:text-blue-700">
                    <i data-feather="plus" class="mr-1"></i> Add Tab
                </button>
            </div>

            <!-- Tab Content -->
            <div class="p-4">
                <div id="tabContent" class="tab-content active">
                    <div class="flex flex-col items-center justify-center h-64 bg-gray-50 rounded-lg">
                        <i data-feather="globe" class="w-16 h-16 text-gray-400 mb-4"></i>
                        <p class="text-gray-500 mb-4">Add a new tab to start browsing</p>
                        <button id="firstTabBtn" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition">
                            Add First Tab
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Add URL Modal -->
        <div id="urlModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center hidden">
            <div class="bg-white rounded-lg p-6 w-full max-w-md">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="text-xl font-semibold">Add Website URL</h3>
                    <button id="closeModalBtn" class="text-gray-500 hover:text-gray-700">
                        <i data-feather="x"></i>
                    </button>
                </div>
                <div class="mb-4">
                    <label for="urlInput" class="block text-gray-700 mb-2">Website URL</label>
                    <input type="text" id="urlInput" placeholder="https://example.com" 
                           class="w-full px-3 py-2 border border-gray-300 rounded focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <div class="mb-4">
                    <label for="tabNameInput" class="block text-gray-700 mb-2">Tab Name (optional)</label>
                    <input type="text" id="tabNameInput" placeholder="My Website" 
                           class="w-full px-3 py-2 border border-gray-300 rounded focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <div class="flex justify-end">
                    <button id="cancelModalBtn" class="px-4 py-2 mr-2 text-gray-700 hover:text-gray-900">
                        Cancel
                    </button>
                    <button id="saveUrlBtn" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition">
                        Add Website
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            feather.replace();
            AOS.init();

            let tabCount = 0;
            const tabsContainer = document.querySelector('.flex.flex-nowrap');
            const tabContentContainer = document.getElementById('tabContent');
            const addTabBtn = document.getElementById('addTabBtn');
            const firstTabBtn = document.getElementById('firstTabBtn');
            const urlModal = document.getElementById('urlModal');
            const closeModalBtn = document.getElementById('closeModalBtn');
            const cancelModalBtn = document.getElementById('cancelModalBtn');
            const saveUrlBtn = document.getElementById('saveUrlBtn');
            const urlInput = document.getElementById('urlInput');
            const tabNameInput = document.getElementById('tabNameInput');

            // Show modal when add tab button is clicked
            addTabBtn.addEventListener('click', showModal);
            firstTabBtn.addEventListener('click', showModal);

            // Close modal
            closeModalBtn.addEventListener('click', hideModal);
            cancelModalBtn.addEventListener('click', hideModal);

            // Save URL and create new tab
            saveUrlBtn.addEventListener('click', function() {
                const url = urlInput.value.trim();
                const tabName = tabNameInput.value.trim() || `Tab ${tabCount + 1}`;
                
                if (url) {
                    if (!url.startsWith('http://') && !url.startsWith('https://')) {
                        urlInput.value = 'https://' + url;
                    }
                    createNewTab(url, tabName);
                    hideModal();
                } else {
                    alert('Please enter a valid URL');
                }
            });

            function showModal() {
                urlModal.classList.remove('hidden');
                urlInput.focus();
            }

            function hideModal() {
                urlModal.classList.add('hidden');
                urlInput.value = '';
                tabNameInput.value = '';
            }

            function createNewTab(url, tabName) {
                tabCount++;
                
                // Create tab button
                const tabId = `tab-${tabCount}`;
                const tabButton = document.createElement('div');
                tabButton.className = 'flex items-center px-4 py-2 border-r border-gray-200 cursor-pointer tab-button';
                tabButton.id = `${tabId}-btn`;
                tabButton.setAttribute('data-tab', tabId);
                
                tabButton.innerHTML = `
                    <span class="mr-2">${tabName}</span>
                    <i data-feather="x" class="tab-close w-4 h-4" data-tab="${tabId}"></i>
                `;
                
                // Add tab button to container
                tabsContainer.appendChild(tabButton);
                
                // Create tab content
                const tabContent = document.createElement('div');
                tabContent.className = 'tab-content';
                tabContent.id = tabId;
                tabContent.innerHTML = `
                    <iframe src="${url}" frameborder="0" sandbox="allow-same-origin allow-scripts allow-popups allow-forms"></iframe>
                `;
                
                // Add tab content to container
                tabContentContainer.parentNode.insertBefore(tabContent, tabContentContainer.nextSibling);
                
                // Set first tab as active if it's the first one
                if (tabCount === 1) {
                    document.querySelector('.tab-content.active').classList.remove('active');
                    tabButton.classList.add('active');
                    tabContent.classList.add('active');
                }
                
                // Add click event to tab button
                tabButton.addEventListener('click', function() {
                    document.querySelectorAll('.tab-button').forEach(btn => btn.classList.remove('active'));
                    document.querySelectorAll('.tab-content').forEach(content => content.classList.remove('active'));
                    
                    this.classList.add('active');
                    document.getElementById(tabId).classList.add('active');
                });
                
                // Add click event to close button
                const closeBtn = tabButton.querySelector('.tab-close');
                closeBtn.addEventListener('click', function(e) {
                    e.stopPropagation();
                    const tabToClose = this.getAttribute('data-tab');
                    
                    // Remove tab content
                    document.getElementById(tabToClose).remove();
                    
                    // Remove tab button
                    this.parentNode.remove();
                    
                    // If no tabs left, show empty state
                    if (document.querySelectorAll('.tab-button').length === 0) {
                        tabContentContainer.classList.add('active');
                    } else {
                        // Activate the first remaining tab
                        const firstTab = document.querySelector('.tab-button');
                        firstTab.classList.add('active');
                        document.getElementById(firstTab.getAttribute('data-tab')).classList.add('active');
                    }
                });
                
                // Activate the new tab
                document.querySelectorAll('.tab-button').forEach(btn => btn.classList.remove('active'));
                document.querySelectorAll('.tab-content').forEach(content => content.classList.remove('active'));
                tabButton.classList.add('active');
                tabContent.classList.add('active');
                
                // Update feather icons
                feather.replace();
            }
        });
    </script>
</body>
</html>
