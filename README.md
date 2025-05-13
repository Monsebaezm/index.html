<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Sugeridor de Tipografías</title>
<link href="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.css" rel="stylesheet">
<style>
.font-card {
transition: all 0.3s ease;
}
.font-card:hover {
transform: translateY(-5px);
}
.highlight {
background-color: #fff3cd;
padding: 2px;
border-radius: 4px;
}
</style>
</head>
<body class="bg-gray-100 min-h-screen p-8">
<div class="max-w-4xl mx-auto">
<h1 class="text-4xl font-bold text-center mb-8 text-gray-800">Sugeridor de Tipografías</h1>
<div class="bg-white rounded-lg shadow-lg p-6 mb-8">
        <form id="searchForm" class="space-y-4">
            <div>
                <label class="block text-gray-700 text-sm font-bold mb-2" for="projectTheme">
                    Tema del Proyecto:
                </label>
                <input
                    type="text"
                    id="projectTheme"
                    class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="Ej: Tecnología, Moda, Comida..."
                    required
                >
            </div>
            <button
                type="submit"
                class="w-full bg-blue-500 text-white font-bold py-2 px-4 rounded-md hover:bg-blue-600 transition duration-300"
            >
                Buscar Fuentes
            </button>
        </form>
    </div>

    <div id="results" class="grid grid-cols-1 md:grid-cols-2 gap-6"></div>
</div>

<script>
    // ⚠️ CAMBIA ESTA LÍNEA CON TU CLAVE API DE GOOGLE FONTS ⚠️
    const API_KEY = 'AIzaSyB_2OWeSDHVAoVwAD5syNX1Jc_5hAEMeAo'; // REEMPLAZA ESTA CLAVE CON LA TUYA

    // Categorías de fuentes según temas comunes
    const themeCategories = {
        tecnologia: ['display', 'sans-serif'],
        moda: ['serif', 'handwriting'],
        comida: ['handwriting', 'display'],
        deporte: ['sans-serif', 'display'],
        naturaleza: ['handwriting', 'serif'],
        arte: ['handwriting', 'display'],
        negocios: ['sans-serif', 'serif'],
        educacion: ['sans-serif', 'serif'],
        default: ['sans-serif', 'serif', 'display', 'handwriting']
    };

    document.getElementById('searchForm').addEventListener('submit', async (e) => {
        e.preventDefault();
        const theme = document.getElementById('projectTheme').value.toLowerCase();
        await getFontSuggestions(theme);
    });

    async function getFontSuggestions(theme) {
        const results = document.getElementById('results');
        results.innerHTML = '<div class="col-span-2 text-center">Buscando fuentes...</div>';

        let categories = themeCategories.default;
        for (const [key, value] of Object.entries(themeCategories)) {
            if (theme.includes(key)) {
                categories = value;
                break;
            }
        }

        try {
            const category = categories[Math.floor(Math.random() * categories.length)];
            const response = await fetch(`https://www.googleapis.com/webfonts/v1/webfonts?key=${API_KEY}&sort=popularity`);
            const data = await response.json();

            const filteredFonts = data.items
                .filter(font => font.category === category)
                .sort(() => 0.5 - Math.random())
                .slice(0, 6);

            results.innerHTML = '';
            filteredFonts.forEach(font => {
                const card = document.createElement('div');
                card.className = 'font-card bg-white p-6 rounded-lg shadow-lg';
                card.innerHTML = `
                    <h3 class="text-xl font-bold mb-2">${font.family}</h3>
                    <p class="text-gray-600 mb-4">Categoría: ${font.category}</p>
                    <p class="text-gray-800 text-lg">
                        <link href="<https://fonts.googleapis.com/css2?family=$>{font.family.replace(' ', '+')}&display=swap" rel="stylesheet">
                        <span style="font-family: '${font.family}'">
                            Ejemplo de texto con esta tipografía
                        </span>
                    </p>
                    <div class="mt-4">
                        <a href="<https://fonts.google.com/specimen/$>{font.family.replace(' ', '+')}"
                           target="_blank"
                           class="text-blue-500 hover:text-blue-700">
                            Ver en Google Fonts
                        </a>
                    </div>
                `;
                results.appendChild(card);
            });
        } catch (error) {
            results.innerHTML = `
                <div class="col-span-2 text-center text-red-500">
                    Error al cargar las fuentes. Por favor, intenta de nuevo.
                </div>
            `;
        }
    }
</script>
</body>
</html>
