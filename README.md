Henock Muteta
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Convertisseur JPG en PDF - Gratuit & Sécurisé</title>
    <!-- Tailwind CSS pour un design moderne et responsive -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome pour les icônes -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Librairie jsPDF pour la génération du PDF côté client -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        /* Styles personnalisés pour le comportement du glisser-déposer */
        .drag-over {
            border-color: #3b82f6 !important;
            background-color: #eff6ff !important;
        }
        .dark .drag-over {
            background-color: #1e293b !important;
        }
        /* Style pour l'élément en cours de déplacement */
        .dragging {
            opacity: 0.5;
            transform: scale(0.95);
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col font-sans transition-colors duration-200 dark:bg-slate-950 dark:text-slate-100">

    <!-- Header / Barre de navigation -->
    <header class="bg-white border-b border-slate-200 sticky top-0 z-30 transition-colors duration-200 dark:bg-slate-900 dark:border-slate-800">
        <div class="max-w-6xl mx-auto px-4 py-4 flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <div class="bg-red-500 text-white p-2.5 rounded-xl shadow-md shadow-red-500/20">
                    <i class="fa-solid fa-file-pdf text-xl"></i>
                </div>
                <div>
                    <h1 class="font-extrabold text-xl tracking-tight text-slate-900 dark:text-white">JPG<span class="text-red-500">2</span>PDF</h1>
                    <p class="text-xs text-slate-500 dark:text-slate-400">Convertisseur 100% local & sécurisé</p>
                </div>
            </div>
            
            <div class="flex items-center space-x-2">
                <!-- Bouton Bascule Mode Sombre -->
                <button id="theme-toggle" class="p-2.5 rounded-lg text-slate-500 hover:bg-slate-100 dark:text-slate-400 dark:hover:bg-slate-800 transition-colors" title="Changer de thème">
                    <i id="theme-icon" class="fa-solid fa-moon text-lg"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- Corps principal -->
    <main class="flex-grow max-w-5xl w-full mx-auto px-4 py-8">
        
        <!-- Section d'accroche descriptive -->
        <div class="text-center max-w-2xl mx-auto mb-10">
            <h2 class="text-3xl font-extrabold text-slate-900 md:text-4xl dark:text-white leading-tight">
                Convertissez vos images en PDF gratuitement
            </h2>
            <p class="mt-3 text-slate-600 dark:text-slate-300">
                Aucune limite de fichiers. Aucune inscription requise. Vos images ne quittent jamais votre appareil pour une confidentialité absolue.
            </p>
        </div>

        <!-- Zone de Notification Dynamique -->
        <div id="toast-container" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2 pointer-events-none"></div>

        <!-- Zone d'action de conversion -->
        <div class="bg-white rounded-2xl shadow-xl border border-slate-100 p-6 md:p-8 transition-colors duration-200 dark:bg-slate-900 dark:border-slate-800">
            
            <!-- Zone de dépôt des fichiers (Uploader) -->
            <div id="drop-zone" class="border-2 border-dashed border-slate-300 rounded-xl p-8 md:p-12 text-center cursor-pointer hover:border-blue-500 dark:border-slate-700 transition-all flex flex-col items-center justify-center group">
                <input type="file" id="file-input" class="hidden" accept="image/jpeg, image/png, image/webp" multiple>
                
                <div class="bg-blue-50 text-blue-500 dark:bg-blue-950/50 dark:text-blue-400 p-5 rounded-full mb-4 group-hover:scale-110 transition-transform duration-200">
                    <i class="fa-solid fa-cloud-arrow-up text-3xl md:text-4xl"></i>
                </div>
                
                <h3 class="font-bold text-lg md:text-xl text-slate-900 dark:text-white mb-2">
                    Glissez-déposez vos images ici
                </h3>
                <p class="text-sm text-slate-500 dark:text-slate-400 mb-6">
                    Supporte les formats JPG, JPEG, PNG, WEBP
                </p>
                <button class="bg-blue-600 hover:bg-blue-700 text-white font-semibold px-6 py-2.5 rounded-xl shadow-md shadow-blue-600/10 hover:shadow-blue-600/20 active:scale-95 transition-all text-sm md:text-base">
                    Sélectionner des images
                </button>
            </div>

            <!-- Paramètres de conversion et liste d'aperçus (caché si aucun fichier) -->
            <div id="editor-section" class="hidden mt-8 space-y-8">
                
                <!-- Barre d'outils et Options du PDF -->
                <div class="bg-slate-50 p-4 md:p-6 rounded-xl border border-slate-200/60 dark:bg-slate-800/50 dark:border-slate-800 flex flex-col md:flex-row gap-6 justify-between items-start md:items-center">
                    
                    <div class="grid grid-cols-2 sm:grid-cols-3 gap-4 w-full md:w-auto">
                        <!-- Option : Format de page -->
                        <div class="flex flex-col gap-1.5">
                            <label class="text-xs font-bold uppercase tracking-wider text-slate-500 dark:text-slate-400">Taille de Page</label>
                            <select id="pdf-size" class="bg-white dark:bg-slate-800 border border-slate-300 dark:border-slate-700 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                                <option value="a4" selected>A4 (Par défaut)</option>
                                <option value="letter">Lettre (US)</option>
                                <option value="fit">Ajuster à l'image</option>
                            </select>
                        </div>

                        <!-- Option : Orientation -->
                        <div id="orientation-container" class="flex flex-col gap-1.5">
                            <label class="text-xs font-bold uppercase tracking-wider text-slate-500 dark:text-slate-400">Orientation</label>
                            <select id="pdf-orientation" class="bg-white dark:bg-slate-800 border border-slate-300 dark:border-slate-700 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                                <option value="p" selected>Portrait</option>
                                <option value="l">Paysage</option>
                            </select>
                        </div>

                        <!-- Option : Marges -->
                        <div class="flex flex-col gap-1.5 col-span-2 sm:col-span-1">
                            <label class="text-xs font-bold uppercase tracking-wider text-slate-500 dark:text-slate-400">Marges</label>
                            <select id="pdf-margin" class="bg-white dark:bg-slate-800 border border-slate-300 dark:border-slate-700 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                                <option value="none" selected>Aucune (Plein écran)</option>
                                <option value="small">Petite (5mm)</option>
                                <option value="medium">Moyenne (12mm)</option>
                            </select>
                        </div>
                    </div>

                    <!-- Actions globales -->
                    <div class="flex gap-3 w-full md:w-auto border-t border-slate-200 md:border-none pt-4 md:pt-0">
                        <button id="clear-btn" class="flex-1 md:flex-initial flex items-center justify-center gap-2 bg-slate-200 hover:bg-slate-300 text-slate-700 dark:bg-slate-800 dark:hover:bg-slate-700 dark:text-slate-200 px-4 py-2.5 rounded-xl text-sm font-semibold transition-all">
                            <i class="fa-solid fa-trash-can"></i> Tout vider
                        </button>
                        <button id="add-more-btn" class="flex-1 md:flex-initial flex items-center justify-center gap-2 bg-blue-50 text-blue-600 hover:bg-blue-100 dark:bg-blue-950/40 dark:text-blue-400 px-4 py-2.5 rounded-xl text-sm font-semibold transition-all">
                            <i class="fa-solid fa-plus"></i> Ajouter
                        </button>
                    </div>
                </div>

                <!-- Liste dynamique des images importées -->
                <div>
                    <div class="flex items-center justify-between mb-4">
                        <h4 class="font-bold text-lg text-slate-900 dark:text-white flex items-center gap-2">
                            <span>Images importées</span>
                            <span id="items-count" class="bg-blue-100 text-blue-800 dark:bg-blue-900/50 dark:text-blue-300 text-xs px-2.5 py-0.5 rounded-full font-semibold">0</span>
                        </h4>
                        <p class="text-xs text-slate-500 dark:text-slate-400 hidden md:block">
                            💡 Astuce : Glissez les cartes pour réorganiser l'ordre des pages dans votre PDF.
                        </p>
                    </div>

                    <!-- Conteneur grille des images -->
                    <div id="preview-grid" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-4">
                        <!-- Généré dynamiquement en JS -->
                    </div>
                </div>

                <!-- Bouton de téléchargement final -->
                <div class="flex flex-col items-center pt-6 border-t border-slate-100 dark:border-slate-800">
                    <div class="w-full max-w-md">
                        <label class="block text-sm font-semibold mb-2 text-slate-700 dark:text-slate-300">Nom du fichier PDF de sortie</label>
                        <div class="flex rounded-xl shadow-sm mb-4">
                            <input type="text" id="pdf-name" value="mes-images-converties" class="block w-full rounded-l-xl border border-slate-300 dark:border-slate-700 bg-white dark:bg-slate-800 px-4 py-3 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500 focus:z-10">
                            <span class="inline-flex items-center rounded-r-xl border border-l-0 border-slate-300 dark:border-slate-700 bg-slate-50 dark:bg-slate-800 px-4 text-sm text-slate-500 font-medium">.pdf</span>
                        </div>
                    </div>

                    <button id="generate-btn" class="w-full max-w-md flex items-center justify-center gap-3 bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-4 px-6 rounded-2xl shadow-lg shadow-emerald-600/20 active:scale-[0.98] transition-all text-base md:text-lg">
                        <i class="fa-solid fa-file-pdf text-xl"></i>
                        <span>Générer et Télécharger mon PDF</span>
                    </button>
                </div>

            </div>

        </div>

        <!-- Section FAQ explicative -->
        <section class="mt-16 max-w-4xl mx-auto space-y-8">
            <h3 class="text-2xl font-bold text-center text-slate-900 dark:text-white">Pourquoi utiliser notre convertisseur local ?</h3>
            <div class="grid md:grid-cols-3 gap-6">
                <div class="bg-white p-6 rounded-xl border border-slate-100 shadow-sm dark:bg-slate-900 dark:border-slate-800">
                    <div class="text-blue-500 mb-3"><i class="fa-solid fa-shield-halved text-2xl"></i></div>
                    <h4 class="font-bold mb-2">Sécurité et RGPD totale</h4>
                    <p class="text-sm text-slate-600 dark:text-slate-400">Vos fichiers ne transitent sur aucun serveur. La conversion est effectuée localement dans votre propre navigateur.</p>
                </div>
                <div class="bg-white p-6 rounded-xl border border-slate-100 shadow-sm dark:bg-slate-900 dark:border-slate-800">
                    <div class="text-amber-500 mb-3"><i class="fa-solid fa-bolt text-2xl"></i></div>
                    <h4 class="font-bold mb-2">Vitesse Instantanée</h4>
                    <p class="text-sm text-slate-600 dark:text-slate-400">Pas de temps de téléversement ou de téléchargement depuis un serveur distant. C'est instantané, même hors ligne.</p>
                </div>
                <div class="bg-white p-6 rounded-xl border border-slate-100 shadow-sm dark:bg-slate-900 dark:border-slate-800">
                    <div class="text-red-500 mb-3"><i class="fa-solid fa-infinity text-2xl"></i></div>
                    <h4 class="font-bold mb-2">Entièrement Illimité</h4>
                    <p class="text-sm text-slate-600 dark:text-slate-400">Convertissez autant de photos et d'images que vous le souhaitez, sans aucune restriction de taille ni de quantité.</p>
                </div>
            </div>
        </section>

    </main>

    <!-- Pied de page -->
    <footer class="bg-white border-t border-slate-200 mt-20 transition-colors duration-200 dark:bg-slate-900 dark:border-slate-800">
        <div class="max-w-6xl mx-auto px-4 py-8 text-center text-sm text-slate-500 dark:text-slate-400">
            <p>© 2026 JPG2PDF. Application web gratuite exécutée localement. Aucun fichier stocké.</p>
        </div>
    </footer>

    <!-- Scripts logiques de l'application -->
    <script>
        // Initialisation de la bibliothèque jsPDF
        const { jsPDF } = window.jspdf;

        // Éléments du DOM
        const themeToggle = document.getElementById('theme-toggle');
        const themeIcon = document.getElementById('theme-icon');
        const dropZone = document.getElementById('drop-zone');
        const fileInput = document.getElementById('file-input');
        const editorSection = document.getElementById('editor-section');
        const previewGrid = document.getElementById('preview-grid');
        const itemsCount = document.getElementById('items-count');
        const clearBtn = document.getElementById('clear-btn');
        const addMoreBtn = document.getElementById('add-more-btn');
        const generateBtn = document.getElementById('generate-btn');
        const pdfNameInput = document.getElementById('pdf-name');
        
        // Paramètres PDF
        const pdfSizeSelect = document.getElementById('pdf-size');
        const pdfOrientationSelect = document.getElementById('pdf-orientation');
        const pdfMarginSelect = document.getElementById('pdf-margin');
        const orientationContainer = document.getElementById('orientation-container');

        // Tableau d'état stockant les images importées
        let filesList = [];

        // --- GESTION DU MODE SOMBRE / CLAIR ---
        function initTheme() {
            if (localStorage.getItem('theme') === 'dark' || (!('theme' in localStorage) && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
                document.documentElement.classList.add('dark');
                themeIcon.className = 'fa-solid fa-sun text-lg';
            } else {
                document.documentElement.classList.remove('dark');
                themeIcon.className = 'fa-solid fa-moon text-lg';
            }
        }

        themeToggle.addEventListener('click', () => {
            if (document.documentElement.classList.contains('dark')) {
                document.documentElement.classList.remove('dark');
                localStorage.setItem('theme', 'light');
                themeIcon.className = 'fa-solid fa-moon text-lg';
                showToast('Mode clair activé', 'info');
            } else {
                document.documentElement.classList.add('dark');
                localStorage.setItem('theme', 'dark');
                themeIcon.className = 'fa-solid fa-sun text-lg';
                showToast('Mode sombre activé', 'info');
            }
        });

        // --- SYSTÈME DE NOTIFICATIONS (TOASTS) ---
        function showToast(message, type = 'success') {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            
            let bgClass = 'bg-slate-900 dark:bg-white text-white dark:text-slate-950';
            let icon = '<i class="fa-solid fa-info-circle text-blue-400"></i>';
            
            if (type === 'success') {
                bgClass = 'bg-emerald-600 text-white';
                icon = '<i class="fa-solid fa-circle-check"></i>';
            } else if (type === 'error') {
                bgClass = 'bg-red-600 text-white';
                icon = '<i class="fa-solid fa-triangle-exclamation"></i>';
            }

            toast.className = `${bgClass} flex items-center gap-3 px-4 py-3 rounded-xl shadow-lg text-sm font-semibold transform translate-y-2 opacity-0 transition-all duration-300 pointer-events-auto`;
            toast.innerHTML = `${icon} <span>${message}</span>`;
            
            container.appendChild(toast);
            
            // Animation d'entrée
            setTimeout(() => {
                toast.classList.remove('translate-y-2', 'opacity-0');
            }, 50);

            // Suppression automatique
            setTimeout(() => {
                toast.classList.add('opacity-0', 'translate-y-2');
                setTimeout(() => {
                    toast.remove();
                }, 300);
            }, 3000);
        }

        // --- GESTION DE L'UPLOADER & DES GLISSER-DÉPOSER ---
        
        // Clic sur la zone de dépôt pour déclencher l'input file
        dropZone.addEventListener('click', () => fileInput.click());

        // Bouton d'ajout additionnel de fichiers
        addMoreBtn.addEventListener('click', () => fileInput.click());

        // Comportement de dragover et dragleave
        ['dragenter', 'dragover'].forEach(eventName => {
            dropZone.addEventListener(eventName, (e) => {
                e.preventDefault();
                e.stopPropagation();
                dropZone.classList.add('drag-over');
            }, false);
        });

        ['dragleave', 'drop'].forEach(eventName => {
            dropZone.addEventListener(eventName, (e) => {
                e.preventDefault();
                e.stopPropagation();
                dropZone.classList.remove('drag-over');
            }, false);
        });

        // Gestion du drop physique
        dropZone.addEventListener('drop', (e) => {
            const dt = e.dataTransfer;
            const files = dt.files;
            handleFiles(files);
        });

        // Gestion de l'input de sélection classique
        fileInput.addEventListener('change', (e) => {
            handleFiles(e.target.files);
            fileInput.value = ''; // Réinitialisation de l'input pour ré-uploader le même fichier si besoin
        });

        // Lecture et stockage des images importées
        function handleFiles(files) {
            const validImageTypes = ['image/jpeg', 'image/png', 'image/webp'];
            let addedCount = 0;

            Array.from(files).forEach(file => {
                if (validImageTypes.includes(file.type)) {
                    const reader = new FileReader();
                    reader.readAsDataURL(file);
                    reader.onload = () => {
                        // Stockage de l'image (base64) et du nom dans notre tableau
                        filesList.push({
                            id: crypto.randomUUID(),
                            name: file.name,
                            data: reader.result
                        });
                        
                        renderPreviews();
                        addedCount++;
                    };
                } else {
                    showToast(`Format non supporté pour : ${file.name}`, 'error');
                }
            });

            // Une petite pause asynchrone pour faire la notification globale après chargement des readers
            setTimeout(() => {
                if (addedCount > 0) {
                    showToast(`${addedCount} image(s) ajoutée(s) avec succès !`);
                }
            }, 100);
        }

        // Ajuster l'interface selon le format choisi
        pdfSizeSelect.addEventListener('change', () => {
            if (pdfSizeSelect.value === 'fit') {
                // Si "Ajuster", l'orientation n'a plus de sens (chaque page s'ajuste individuellement à l'image)
                orientationContainer.classList.add('opacity-40', 'pointer-events-none');
            } else {
                orientationContainer.classList.remove('opacity-40', 'pointer-events-none');
            }
        });

        // --- GÉNÉRATION DU RENDU ET APERÇU ---
        function renderPreviews() {
            previewGrid.innerHTML = '';
            
            if (filesList.length === 0) {
                editorSection.classList.add('hidden');
                dropZone.classList.remove('hidden');
                return;
            }

            // Afficher l'éditeur et cacher le dropzone central initial si souhaité (on peut ajouter par la barre d'outils)
            editorSection.classList.remove('hidden');
            dropZone.classList.add('hidden');
            
            itemsCount.innerText = filesList.length;

            filesList.forEach((file, index) => {
                const card = document.createElement('div');
                card.className = 'bg-slate-100 dark:bg-slate-800 rounded-xl p-3 border border-slate-200 dark:border-slate-700 relative group flex flex-col justify-between cursor-move transition-transform shadow-sm hover:shadow-md';
                card.setAttribute('draggable', 'true');
                card.dataset.id = file.id;

                card.innerHTML = `
                    <!-- Numéro de page -->
                    <span class="absolute top-2 left-2 bg-slate-900/70 text-white text-xs font-bold px-2 py-0.5 rounded-md z-10">
                        P. ${index + 1}
                    </span>
                    
                    <!-- Bouton Supprimer -->
                    <button class="delete-btn absolute top-2 right-2 bg-red-500/90 text-white rounded-lg p-1.5 opacity-0 group-hover:opacity-100 transition-opacity z-10 hover:bg-red-600 focus:opacity-100" title="Supprimer cette page">
                        <i class="fa-solid fa-trash text-xs"></i>
                    </button>

                    <!-- Zone Aperçu Image -->
                    <div class="aspect-[3/4] rounded-lg overflow-hidden bg-white dark:bg-slate-950 flex items-center justify-center mb-3">
                        <img src="${file.data}" alt="${file.name}" class="object-contain max-h-full max-w-full">
                    </div>

                    <!-- Infos Image -->
                    <div class="flex items-center justify-between gap-1">
                        <p class="text-xs truncate font-medium text-slate-700 dark:text-slate-300 w-full" title="${file.name}">
                            ${file.name}
                        </p>
                    </div>

                    <!-- Boutons de réordonnancement manuel (pour l'accessibilité sur tactile/mobile) -->
                    <div class="flex justify-between mt-2.5 pt-2 border-t border-slate-200 dark:border-slate-700/60 z-10">
                        <button class="move-prev-btn text-slate-400 hover:text-blue-500 p-1 rounded hover:bg-slate-200 dark:hover:bg-slate-700 ${index === 0 ? 'opacity-30 cursor-not-allowed' : ''}" ${index === 0 ? 'disabled' : ''}>
                            <i class="fa-solid fa-arrow-left"></i>
                        </button>
                        <span class="text-[10px] text-slate-400 font-bold self-center">Bouger</span>
                        <button class="move-next-btn text-slate-400 hover:text-blue-500 p-1 rounded hover:bg-slate-200 dark:hover:bg-slate-700 ${index === filesList.length - 1 ? 'opacity-30 cursor-not-allowed' : ''}" ${index === filesList.length - 1 ? 'disabled' : ''}>
                            <i class="fa-solid fa-arrow-right"></i>
                        </button>
                    </div>
                `;

                // Attachement des gestionnaires d'événements pour cette carte
                
                // Supprimer individuellement
                card.querySelector('.delete-btn').addEventListener('click', (e) => {
                    e.stopPropagation();
                    filesList = filesList.filter(f => f.id !== file.id);
                    renderPreviews();
                    showToast('Page supprimée');
                });

                // Reculer la page
                card.querySelector('.move-prev-btn').addEventListener('click', (e) => {
                    e.stopPropagation();
                    if (index > 0) {
                        const temp = filesList[index];
                        filesList[index] = filesList[index - 1];
                        filesList[index - 1] = temp;
                        renderPreviews();
                    }
                });

                // Avancer la page
                card.querySelector('.move-next-btn').addEventListener('click', (e) => {
                    e.stopPropagation();
                    if (index < filesList.length - 1) {
                        const temp = filesList[index];
                        filesList[index] = filesList[index + 1];
                        filesList[index + 1] = temp;
                        renderPreviews();
                    }
                });

                // Drag & Drop événementiel de positionnement
                card.addEventListener('dragstart', handleDragStart);
                card.addEventListener('dragover', handleDragOver);
                card.addEventListener('drop', handleDrop);
                card.addEventListener('dragend', handleDragEnd);

                previewGrid.appendChild(card);
            });
        }

        // --- GESTION DU RE-ORDONNANCEMENT D'IMAGES PAR GLISSER-DÉPOSER ---
        let dragSourceEl = null;

        function handleDragStart(e) {
            this.classList.add('dragging');
            dragSourceEl = this;
            e.dataTransfer.effectAllowed = 'move';
            e.dataTransfer.setData('text/plain', this.dataset.id);
        }

        function handleDragOver(e) {
            if (e.preventDefault) {
                e.preventDefault();
            }
            e.dataTransfer.dropEffect = 'move';
            return false;
        }

        function handleDrop(e) {
            e.stopPropagation();
            e.preventDefault();

            if (dragSourceEl !== this) {
                const draggedId = e.dataTransfer.getData('text/plain');
                const targetId = this.dataset.id;

                const draggedIndex = filesList.findIndex(f => f.id === draggedId);
                const targetIndex = filesList.findIndex(f => f.id === targetId);

                if (draggedIndex !== -1 && targetIndex !== -1) {
                    // Supprime l'élément et l'insère à la nouvelle position
                    const [removed] = filesList.splice(draggedIndex, 1);
                    filesList.splice(targetIndex, 0, removed);
                    renderPreviews();
                }
            }
            return false;
        }

        function handleDragEnd() {
            this.classList.remove('dragging');
            const cols = previewGrid.querySelectorAll('[draggable]');
            cols.forEach(col => col.classList.remove('dragging'));
        }

        // --- VIDER LA LISTE ---
        clearBtn.addEventListener('click', () => {
            filesList = [];
            renderPreviews();
            showToast('Toutes les images ont été vidées', 'info');
        });

        // --- ALGORITHME DE CONVERSION VERS PDF ---
        generateBtn.addEventListener('click', async () => {
            if (filesList.length === 0) {
                showToast('Veuillez ajouter au moins une image', 'error');
                return;
            }

            // Changement d'état visuel du bouton
            generateBtn.disabled = true;
            generateBtn.innerHTML = `<i class="fa-solid fa-circle-notch animate-spin text-xl"></i> Génération en cours...`;

            try {
                // Récupération des choix de l'utilisateur
                const pageSize = pdfSizeSelect.value;
                const orientation = pdfOrientationSelect.value;
                const marginSetting = pdfMarginSelect.value;
                const pdfName = (pdfNameInput.value.trim() || 'mes-images-converties') + '.pdf';

                let doc;

                // Marges en mm
                let margin = 0;
                if (marginSetting === 'small') margin = 5;
                if (marginSetting === 'medium') margin = 12;

                // Création et assemblage des pages du PDF
                for (let i = 0; i < filesList.length; i++) {
                    const imgData = filesList[i].data;
                    
                    // Chargement temporaire pour récupérer les dimensions de l'image
                    const img = await loadImage(imgData);
                    const imgWidth = img.width;
                    const imgHeight = img.height;

                    let finalPageFormat;
                    let finalPageOrientation = orientation;

                    if (pageSize === 'fit') {
                        // Adapter le format du PDF exactement aux proportions de l'image
                        // La conversion se fait de px à mm approximativement (1 px ~ 0.264583 mm)
                        const wMm = imgWidth * 0.264583 + (margin * 2);
                        const hMm = imgHeight * 0.264583 + (margin * 2);
                        finalPageFormat = [wMm, hMm];
                        finalPageOrientation = wMm > hMm ? 'l' : 'p';
                    } else {
                        finalPageFormat = pageSize; // 'a4' ou 'letter'
                    }

                    // Initialisation du document au premier passage, sinon ajout d'une nouvelle page
                    if (i === 0) {
                        doc = new jsPDF({
                            orientation: finalPageOrientation,
                            unit: 'mm',
                            format: finalPageFormat,
                            compress: true
                        });
                    } else {
                        doc.addPage(finalPageFormat, finalPageOrientation);
                    }

                    // Dimensions effectives de la page de destination
                    const pageWidth = doc.internal.pageSize.getWidth();
                    const pageHeight = doc.internal.pageSize.getHeight();

                    // Calcul de la zone imprimable (page - marges)
                    const printableWidth = pageWidth - (margin * 2);
                    const printableHeight = pageHeight - (margin * 2);

                    // Calcul du ratio d'affichage pour conserver l'aspect original de la photo
                    const imgRatio = imgWidth / imgHeight;
                    const pageRatio = printableWidth / printableHeight;

                    let renderWidth, renderHeight;

                    if (imgRatio > pageRatio) {
                        // L'image est plus large proportionnellement
                        renderWidth = printableWidth;
                        renderHeight = printableWidth / imgRatio;
                    } else {
                        // L'image est plus haute proportionnellement
                        renderHeight = printableHeight;
                        renderWidth = printableHeight * imgRatio;
                    }

                    // Centrage de l'image sur la page
                    const x = margin + (printableWidth - renderWidth) / 2;
                    const y = margin + (printableHeight - renderHeight) / 2;

                    // Ajout du calque d'image au PDF (Auto-détection du format)
                    const format = imgData.startsWith('data:image/png') ? 'PNG' : 'JPEG';
                    doc.addImage(imgData, format, x, y, renderWidth, renderHeight, undefined, 'FAST');
                }

                // Enregistrement final côté client
                doc.save(pdfName);
                showToast('Votre PDF a été généré et téléchargé avec succès !', 'success');

            } catch (error) {
                console.error(error);
                showToast('Une erreur est survenue lors de la génération.', 'error');
            } finally {
                // Rétablissement du bouton
                generateBtn.disabled = false;
                generateBtn.innerHTML = `
                    <i class="fa-solid fa-file-pdf text-xl"></i>
                    <span>Générer et Télécharger mon PDF</span>
                `;
            }
        });

        // Utilitaire pour transformer l'URL Base64 en élément Image asynchrone
        function loadImage(src) {
            return new Promise((resolve, reject) => {
                const img = new Image();
                img.src = src;
                img.onload = () => resolve(img);
                img.onerror = (err) => reject(err);
            });
        }

        // Lancement au chargement de la page
        window.onload = function() {
            initTheme();
        };
    </script>
</body>
</html>
