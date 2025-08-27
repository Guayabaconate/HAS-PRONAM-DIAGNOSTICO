<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Diagnóstico Interactivo de Hipertensión Arterial Sistémica</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #fdfbf7; color: #3a3a3a; }
        .checklist-item { cursor: pointer; transition: all 0.2s; }
        .checklist-item.completed { text-decoration: line-through; color: #888; }
        .checklist-item.completed .checkbox-icon { background-color: #A0522D; border-color: #A0522D; }
        .checkbox-icon { border: 2px solid #D2B48C; }
        .chart-container { position: relative; width: 100%; max-width: 450px; margin: auto; height: 300px; max-height: 350px; }
        @media (min-width: 768px) { .chart-container { height: 350px; max-height: 400px; } }
    </style>
</head>
<body class="antialiased p-4 sm:p-6 md:p-8">

    <div class="container mx-auto max-w-7xl">
        <header class="text-center mb-8">
            <h1 class="text-3xl md:text-4xl font-bold text-[#8B4513]">Diagnóstico Interactivo de Hipertensión Arterial Sistémica (HAS)</h1>
            <p class="text-md md:text-lg text-gray-600 mt-2">Guía basada en el Protocolo Nacional de Atención Médica de México.</p>
        </header>

        <main>
            <section id="diagnostico" class="content-section active">
                <div class="bg-white/80 backdrop-blur-sm p-6 rounded-2xl shadow-lg border border-gray-200/80">
                    <h2 class="text-2xl font-bold text-[#A0522D] mb-4">Proceso de Diagnóstico</h2>
                    <p class="text-gray-700 mb-6">El diagnóstico correcto de la HAS requiere una medición precisa de la presión arterial y una evaluación clínica completa. Utilice la siguiente lista interactiva para seguir los pasos recomendados y consulte la gráfica para clasificar la presión arterial del paciente.</p>
                    <div class="grid md:grid-cols-2 gap-8">
                        <div>
                            <h3 class="text-xl font-semibold text-[#8B4513] mb-4">Checklist de Medición y Evaluación</h3>
                            <div id="checklist" class="space-y-3">
                                <div class="checklist-item bg-stone-50 p-3 rounded-lg flex items-center" data-step="1">
                                    <div class="checkbox-icon w-6 h-6 rounded-full mr-4 flex-shrink-0 flex items-center justify-center text-white font-bold"></div>
                                    <span>Paciente en reposo por 5 min en ambiente tranquilo.</span>
                                </div>
                                <div class="checklist-item bg-stone-50 p-3 rounded-lg flex items-center" data-step="2">
                                    <div class="checkbox-icon w-6 h-6 rounded-full mr-4 flex-shrink-0 flex items-center justify-center text-white font-bold"></div>
                                    <span>Brazalete de tamaño adecuado en brazo sin ropa.</span>
                                </div>
                                <div class="checklist-item bg-stone-50 p-3 rounded-lg flex items-center" data-step="3">
                                    <div class="checkbox-icon w-6 h-6 rounded-full mr-4 flex-shrink-0 flex items-center justify-center text-white font-bold"></div>
                                    <span>Realizar 3 mediciones (promediar las 2 últimas).</span>
                                </div>
                                <div class="text-gray-500 text-sm mt-1 ml-10">
                                    <span class="inline-block w-4 h-4 mr-2">-</span>
                                    <span>Ejemplo: Si las mediciones son 145/95, 142/92 y 140/90, el promedio es 141/91.</span>
                                </div>
                                <div class="checklist-item bg-stone-50 p-3 rounded-lg flex items-center" data-step="4">
                                    <div class="checkbox-icon w-6 h-6 rounded-full mr-4 flex-shrink-0 flex items-center justify-center text-white font-bold"></div>
                                    <span>Medir PA en ambos brazos en la primera visita.</span>
                                </div>
                                <div class="checklist-item bg-stone-50 p-3 rounded-lg flex items-center" data-step="5">
                                    <div class="checkbox-icon w-6 h-6 rounded-full mr-4 flex-shrink-0 flex items-center justify-center text-white font-bold"></div>
                                    <span>Evaluar hipotensión ortostática.</span>
                                </div>
                                <div class="checklist-item bg-stone-50 p-3 rounded-lg flex items-center" data-step="6">
                                    <div class="checkbox-icon w-6 h-6 rounded-full mr-4 flex-shrink-0 flex items-center justify-center text-white font-bold"></div>
                                    <span>Realizar historia clínica y examen físico completo.</span>
                                </div>
                                <div class="checklist-item bg-stone-50 p-3 rounded-lg flex items-center" data-step="7">
                                    <div class="checkbox-icon w-6 h-6 rounded-full mr-4 flex-shrink-0 flex items-center justify-center text-white font-bold"></div>
                                    <span>Solicitar estudios de laboratorio y gabinete (EGO, QS, Perfil lipídico, ECG).</span>
                                </div>
                            </div>
                        </div>
                        <div>
                            <h3 class="text-xl font-semibold text-center mb-2 text-[#8B4513]">Clasificación de la Presión Arterial (mmHg)</h3>
                            <div class="chart-container">
                                <canvas id="bpChart"></canvas>
                            </div>
                        </div>
                    </div>
                </div>
            </section>
        </main>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function () {
            // Manejador para la lista de verificación interactiva.
            // Cuando un elemento de la lista es clickeado, alterna la clase 'completed'
            // para aplicar el estilo de "tachado" y cambiar el color del ícono.
            const checklistItems = document.querySelectorAll('.checklist-item');
            checklistItems.forEach(item => {
                item.addEventListener('click', () => {
                    item.classList.toggle('completed');
                    const icon = item.querySelector('.checkbox-icon');
                    if (item.classList.contains('completed')) {
                        icon.innerHTML = '✓'; // Muestra la marca de verificación
                    } else {
                        icon.innerHTML = ''; // Quita la marca de verificación
                    }
                });
            });

            // Configuración y renderización del gráfico de barras para la clasificación de la PA.
            // Usa Chart.js para visualizar los rangos de presión sistólica y diastólica.
            const bpCtx = document.getElementById('bpChart').getContext('2d');
            new Chart(bpCtx, {
                type: 'bar',
                data: {
                    labels: ['Normal', 'Limítrofe', 'HAS', 'HAS + Comorbilidades'],
                    datasets: [{
                        label: 'Sistólica (mmHg)',
                        data: [120, 139, 140, 135],
                        backgroundColor: '#A0522D',
                        borderRadius: 6,
                    }, {
                        label: 'Diastólica (mmHg)',
                        data: [79, 89, 90, 85],
                        backgroundColor: '#D2B48C',
                        borderRadius: 6,
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: { 
                            beginAtZero: true, 
                            title: { 
                                display: true, 
                                text: 'Presión (mmHg)' 
                            } 
                        },
                        x: { 
                            title: { 
                                display: true, 
                                text: 'Categoría' 
                            } 
                        }
                    },
                    plugins: {
                        legend: { position: 'bottom' },
                        tooltip: {
                            callbacks: {
                                title: function(context) {
                                    return `Límite para ${context[0].label}`;
                                }
                            }
                        }
                    }
                }
            });
        });
    </script>
</body>
</html>
