<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Asistente Futurista Offline</title>
<style>
body {
    margin: 0;
    font-family: 'Orbitron', sans-serif;
    background: #0b0c10;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    color: #00ffea;
}

@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');

.chat-container {
    width: 500px;
    max-width: 95%;
    background: #1f2833;
    border-radius: 15px;
    box-shadow: 0 0 40px #00ffea;
    display: flex;
    flex-direction: column;
    overflow: hidden;
}

.chat-box {
    flex: 1;
    padding: 20px;
    overflow-y: auto;
    max-height: 600px;
    display: flex;
    flex-direction: column;
}

.message {
    margin-bottom: 15px;
    padding: 12px 18px;
    border-radius: 20px;
    max-width: 75%;
    word-wrap: break-word;
    font-size: 14px;
    line-height: 1.4;
    animation: fadeIn 0.3s ease-in-out;
}

.user {
    background: #00ffea;
    color: #0b0c10;
    align-self: flex-end;
    border-bottom-right-radius: 0;
}

.assistant {
    background: #0b0c10;
    border: 1px solid #00ffea;
    color: #00ffea;
    align-self: flex-start;
    border-bottom-left-radius: 0;
}

.input-box {
    display: flex;
    border-top: 1px solid #00ffea;
}

input {
    flex: 1;
    padding: 15px;
    border: none;
    outline: none;
    font-size: 16px;
    background: #1f2833;
    color: #00ffea;
}

button {
    padding: 15px 20px;
    background: #00ffea;
    border: none;
    color: #0b0c10;
    cursor: pointer;
    font-size: 16px;
    transition: 0.2s;
}

button:hover {
    background: #00d1b2;
}

@keyframes fadeIn {
    from { opacity: 0; transform: translateY(10px);}
    to { opacity: 1; transform: translateY(0);}
}
</style>
</head>
<body>

<div class="chat-container">
    <div class="chat-box" id="chatBox"></div>
    <div class="input-box">
        <input type="text" id="userInput" placeholder="Escribe tu pregunta...">
        <button onclick="sendMessage()">Enviar</button>
    </div>
</div>

<script>
const chatBox = document.getElementById('chatBox');
const userInput = document.getElementById('userInput');

// Array de respuestas (puedes poner el mega array completo de 500+ aquí)
const respuestas = [
  {keywords:["hola","buenos días","buenas"], respuesta:"¡Hola! ¿Cómo estás?" },
  {keywords:["cómo estás","qué tal"], respuesta:"Estoy bien, gracias por preguntar. ¿Y tú?" },
  {keywords:["adiós","hasta luego","nos vemos"], respuesta:"¡Hasta luego! Que tengas un buen día." },
  {keywords:["programación","java","python","c++","javascript"], respuesta:"Puedo ayudarte con dudas de programación." },
  {keywords:["informática","ordenador","pc","computadora","hardware"], respuesta:"Soy un asistente offline de informática." },
  {keywords:["internet","wifi","conexión"], respuesta:"Asegúrate de que tu conexión sea estable." },
  {keywords:["windows","linux","mac"], respuesta:"Puedo darte información sobre diferentes sistemas operativos." },
  {keywords:["instalar","setup","configurar"], respuesta:"Necesitas seguir los pasos de instalación del software que deseas." },
  {keywords:["error","problema","fallo"], respuesta:"Intenta reiniciar el programa o buscar ayuda en foros especializados." },
  {keywords:["correo","email","gmail"], respuesta:"Recuerda siempre revisar la carpeta de spam si no llega tu correo." },
  {keywords:["seguridad","contraseña","hack"], respuesta:"Usa contraseñas seguras y no compartas tus datos personales." },
  {keywords:["juego","gaming","videojuego"], respuesta:"Puedes disfrutar de muchos juegos dependiendo de tu plataforma." },
  {keywords:["película","cine","series"], respuesta:"Recomiendo ver películas según tu género favorito." },
  {keywords:["música","canción","spotify"], respuesta:"Puedes escuchar música en plataformas como Spotify o YouTube." },
  {keywords:["viajar","vacaciones","destino"], respuesta:"Planea tu viaje con anticipación para aprovecharlo al máximo." },
  {keywords:["comida","receta","cocinar"], respuesta:"Prueba recetas nuevas y fáciles para divertirte cocinando." },
  {keywords:["deporte","fútbol","baloncesto","tenis"], respuesta:"Hacer deporte es bueno para la salud física y mental." },
  {keywords:["salud","medicina","doctor"], respuesta:"Consulta siempre a un profesional de la salud ante cualquier duda." },
  {keywords:["clima","tiempo","temperatura"], respuesta:"Revisa la app de clima de tu ciudad para estar preparado." },
  {keywords:["viaje espacial","nasa","planeta"], respuesta:"El espacio es fascinante, la NASA tiene mucha información interesante." },
  {keywords:["tecnología","gadgets","smartphone"], respuesta:"Hay muchos dispositivos nuevos en el mercado cada año." },
  {keywords:["robot","inteligencia artificial","ia"], respuesta:"La IA está revolucionando la tecnología moderna." },
  {keywords:["matemáticas","suma","resta","multiplicación"], respuesta:"Puedo ayudarte a resolver problemas matemáticos sencillos." },
  {keywords:["historia","guerra","antiguo"], respuesta:"La historia es importante para entender el presente." },
  {keywords:["ciencia","experimento","laboratorio"], respuesta:"La ciencia nos ayuda a entender cómo funciona el mundo." },
  {keywords:["filosofía","pensamiento","vida"], respuesta:"Reflexionar sobre la vida puede ser muy enriquecedor." },
  {keywords:["arte","pintura","dibujo"], respuesta:"El arte es una forma de expresar ideas y emociones." },
  {keywords:["literatura","libro","novela"], respuesta:"Leer libros amplía tu conocimiento y creatividad." },
  {keywords:["matemática avanzada","álgebra","cálculo"], respuesta:"Puedes estudiar álgebra y cálculo paso a paso para mejorar." },
  {keywords:["programar web","html","css","javascript"], respuesta:"Puedo enseñarte lo básico de desarrollo web." },
  {keywords:["base de datos","mysql","sqlite","postgresql"], respuesta:"Las bases de datos son fundamentales para almacenar información." },
  {keywords:["redes","router","ip","subred"], respuesta:"Conocer redes te ayuda a configurar y solucionar problemas de conexión." },
  {keywords:["ciberseguridad","firewall","antivirus"], respuesta:"Proteger tus dispositivos es clave para mantener tu información segura." },
  {keywords:["economía","dinero","finanzas"], respuesta:"Planificar tu dinero te ayuda a evitar problemas financieros." },
  {keywords:["programa educativo","curso","aprendizaje"], respuesta:"Aprender cosas nuevas siempre es útil y entretenido." },
  {keywords:["video","youtube","tutorial"], respuesta:"Los tutoriales de YouTube son una buena forma de aprender visualmente." },
  {keywords:["motivación","ánimo","frustración"], respuesta:"Mantén una actitud positiva y no te rindas ante los obstáculos." },
  {keywords:["fruta","verdura","alimentación"], respuesta:"Una buena alimentación es clave para la salud." },
  {keywords:["perro","gato","mascota"], respuesta:"Cuidar una mascota requiere tiempo y responsabilidad." },
  {keywords:["viaje al pasado","historia antigua","civilización"], respuesta:"Las civilizaciones antiguas son fascinantes de estudiar." },
  {keywords:["astronomía","estrella","galaxia"], respuesta:"El universo es inmenso y está lleno de misterios." },
  {keywords:["lenguaje","idioma","traducción"], respuesta:"Aprender idiomas abre muchas oportunidades." },
  {keywords:["programar","codigo","debug"], respuesta:"Depurar código es parte del proceso de programación." },
  {keywords:["ejercicio","fitness","gimnasio"], respuesta:"El ejercicio diario ayuda a mantener cuerpo y mente activos." },
  {keywords:["viajar en tren","autobús","avión"], respuesta:"Cada medio de transporte tiene ventajas y desventajas." },
  {keywords:["cocina","postre","receta fácil"], respuesta:"Puedes probar recetas sencillas y deliciosas en casa." },
  {keywords:["series","Netflix","HBO"], respuesta:"Existen muchas series populares que puedes ver según tu gusto." },
  {keywords:["curiosidad","dato curioso","hecho"], respuesta:"¡Aquí tienes un dato curioso interesante!" },
  {keywords:["mundo","planeta tierra","naturaleza"], respuesta:"Cuidar el medio ambiente es responsabilidad de todos." },
  {keywords:["fotografía","cámara","foto"], respuesta:"Tomar fotos puede ser un hobby creativo muy entretenido." },
  {keywords:["programar apps","android","ios"], respuesta:"Puedes aprender desarrollo móvil con recursos en línea." },
  {keywords:["robótica","arduino","raspberry"], respuesta:"La robótica es divertida y educativa para aprender tecnología." },
  {keywords:["inteligencia artificial","machine learning","deep learning"], respuesta:"La IA está cambiando muchas industrias." },
  {keywords:["cine","acción","drama"], respuesta:"El cine ofrece diferentes géneros para todos los gustos." },
  {keywords:["juegos de mesa","ajedrez","cartas"], respuesta:"Los juegos de mesa son ideales para socializar y entrenar la mente." },
  {keywords:["economía mundial","inversión","mercado"], respuesta:"Es importante informarse antes de invertir dinero." },
  {keywords:["salud mental","estrés","relajación"], respuesta:"Tomarse un descanso y respirar ayuda a reducir el estrés." },
  {keywords:["viaje virtual","google maps","street view"], respuesta:"Puedes explorar el mundo virtualmente desde tu ordenador." },
  {keywords:["educación","escuela","clase"], respuesta:"Aprender cosas nuevas es siempre positivo." },
  {keywords:["pintura","óleo","acuarela"], respuesta:"El arte desarrolla la creatividad y la concentración." },
  {keywords:["música clásica","rock","pop"], respuesta:"La música influye en nuestro ánimo y emociones." },
  {keywords:["literatura infantil","cuento","poesía"], respuesta:"Leer cuentos ayuda a desarrollar la imaginación." },
  {keywords:["programación avanzada","estructuras de datos","algoritmos"], respuesta:"Puedes practicar algoritmos para mejorar como programador." },
  {keywords:["hardware","memoria ram","disco duro"], respuesta:"Conocer los componentes de tu PC ayuda a optimizar su rendimiento." },
  {keywords:["software","aplicación","programa"], respuesta:"Instalar y actualizar software mantiene tu equipo seguro y funcional." },
  {keywords:["redes sociales","facebook","instagram","twitter"], respuesta:"Usa las redes sociales de forma responsable." },
  {keywords:["blog","escribir","contenido"], respuesta:"Escribir blogs es una buena forma de expresar ideas." },
  {keywords:["podcast","audio","entrevista"], respuesta:"Los podcasts son ideales para aprender mientras te desplazas." },
  {keywords:["datos","estadística","información"], respuesta:"Analizar datos ayuda a tomar mejores decisiones." },
  {keywords:["fotografía","edición","photoshop"], respuesta:"Aprender edición de fotos puede mejorar tu trabajo creativo." },
  {keywords:["tecnología del futuro","innovación","gadgets"], respuesta:"La tecnología avanza rápidamente, mantente informado." },
  {keywords:["medio ambiente","reciclaje","sostenible"], respuesta:"Reciclar y reducir residuos ayuda a proteger el planeta." },
  {keywords:["salud física","alimentación","ejercicio"], respuesta:"Mantener hábitos saludables mejora tu calidad de vida." },
  {keywords:["viajar solo","aventura","turismo"], respuesta:"Viajar solo puede ser una experiencia enriquecedora y segura." },
  {keywords:["videojuego retro","arcade","NES"], respuesta:"Los videojuegos retro tienen un encanto especial y nostálgico." },
  {keywords:["inteligencia emocional","empatía","social"], respuesta:"Desarrollar la inteligencia emocional ayuda en la vida diaria." },
  {keywords:["programación web","react","vue","angular"], respuesta:"Aprender frameworks modernos de web te da ventaja como desarrollador." },
  {keywords:["servidor","hosting","cloud"], respuesta:"Configurar servidores requiere conocimientos de red y seguridad." },
  {keywords:["aprender inglés","idioma","vocabulario"], respuesta:"Estudiar idiomas abre muchas oportunidades en el mundo laboral." },
  {keywords:["matemáticas básicas","suma","resta"], respuesta:"Practicar operaciones básicas mejora la agilidad mental." },
  {keywords:["química","experimento","laboratorio"], respuesta:"La química nos ayuda a entender cómo funcionan las sustancias." },
  {keywords:["física","energía","movimiento"], respuesta:"La física explica las leyes del universo que nos rodea." },
  {keywords:["historia moderna","guerra mundial","revolución"], respuesta:"Estudiar historia nos ayuda a aprender del pasado." },
  {keywords:["geografía","mapa","países"], respuesta:"Aprender geografía te ayuda a entender el mundo actual." },
  {keywords:["literatura","poesía","novela"], respuesta:"Leer literatura amplia tu conocimiento y sensibilidad cultural." },
  {keywords:["inteligencia artificial","chatbot","robot"], respuesta:"Los chatbots usan IA para simular conversaciones humanas." },
  {keywords:["videojuegos","PC","consola"], respuesta:"Los videojuegos ofrecen entretenimiento y desafíos estratégicos." },
  {keywords:["educación online","curso","tutorial"], respuesta:"Aprender en línea es flexible y accesible para todos." },
  {keywords:["mecánica","coche","motor"], respuesta:"Entender mecánica básica ayuda a mantener tu vehículo en buen estado." },
  {keywords:["cultura general","curiosidades","hechos"], respuesta:"Aprender curiosidades es divertido y educativo." },
  {keywords:["programación móvil","android","ios"], respuesta:"Crear apps móviles es una habilidad muy demandada hoy en día." },
  {keywords:["robótica educativa","arduino","lego"], respuesta:"La robótica es divertida y fomenta la creatividad técnica." },
  {keywords:["salud mental","estrés","relajación"], respuesta:"Dedicar tiempo a relajarse mejora tu bienestar general." },
  {keywords:["fotografía móvil","selfie","cámara"], respuesta:"Experimenta con distintos ángulos y luz para mejorar tus fotos." },
  {keywords:["viajar barato","ahorro","budget"], respuesta:"Planifica tu viaje con antelación para ahorrar dinero." },
  {keywords:["cine clásico","Hollywood","película antigua"], respuesta:"El cine clásico tiene películas muy icónicas para disfrutar." },
  {keywords:["juegos educativos","aprender","niños"], respuesta:"Los juegos educativos ayudan a desarrollar habilidades cognitivas." },
  {keywords:["programación python","automatización","script"], respuesta:"Python es ideal para automatizar tareas y crear scripts." },
  {keywords:["software libre","opensource","licencia"], respuesta:"El software libre permite modificar y compartir programas legalmente." },
  {keywords:["salud física","rutina","ejercicios"], respuesta:"Mantener una rutina de ejercicio mejora la salud física y mental." },
  {keywords:["tecnología del mañana","IA","innovación"], respuesta:"La innovación tecnológica transforma nuestra forma de vivir." },
  {keywords:["juegos de cartas","poker","blackjack"], respuesta:"Los juegos de cartas son ideales para socializar y pensar estratégicamente." },
  {keywords:["curiosidades del mundo","dato","hecho curioso"], respuesta:"¡Aquí tienes un dato curioso para aprender algo nuevo!" },
  {keywords:["astronomía","planeta","sol"], respuesta:"El sistema solar está compuesto por 8 planetas y muchos cuerpos menores."},
  {keywords:["biología","animal","planta"], respuesta:"La biología estudia la vida y los seres vivos en todas sus formas."},
  {keywords:["química orgánica","molécula","reacción"], respuesta:"La química orgánica estudia los compuestos basados en carbono."},
  {keywords:["física cuántica","átomo","partícula"], respuesta:"La física cuántica estudia el comportamiento de partículas subatómicas."},
  {keywords:["geografía","montaña","río"], respuesta:"La geografía nos ayuda a conocer la distribución de los elementos de la Tierra."},
  {keywords:["historia antigua","egipto","romanos"], respuesta:"Las civilizaciones antiguas dejaron un legado histórico impresionante."},
  {keywords:["literatura clásica","Shakespeare","Miguel de Cervantes"], respuesta:"Leer clásicos de la literatura enriquece tu vocabulario y cultura."},
  {keywords:["música","instrumento","orquesta"], respuesta:"Aprender a tocar un instrumento desarrolla habilidades cognitivas."},
  {keywords:["tecnología","smartwatch","dron"], respuesta:"Los gadgets modernos facilitan muchas tareas diarias."},
  {keywords:["programación","algoritmo","función"], respuesta:"Los algoritmos son pasos claros para resolver problemas."},
  {keywords:["matemáticas","geometría","triángulo"], respuesta:"La geometría estudia las formas, tamaños y propiedades de los espacios."},
  {keywords:["deporte","natación","ciclismo"], respuesta:"Hacer deporte regularmente mejora la salud física y mental."},
  {keywords:["salud","vacuna","prevención"], respuesta:"Vacunarse protege frente a enfermedades contagiosas."},
  {keywords:["cocina","sopa","ensalada"], respuesta:"Preparar alimentos saludables es importante para tu bienestar."},
  {keywords:["viaje","ciudad","país"], respuesta:"Viajar te permite conocer nuevas culturas y perspectivas."},
  {keywords:["idiomas","francés","alemán"], respuesta:"Aprender un nuevo idioma abre muchas oportunidades laborales y personales."},
  {keywords:["cine","documental","thriller"], respuesta:"Ver documentales es una excelente forma de aprender mientras te entretienes."},
  {keywords:["arte","escultura","museo"], respuesta:"Visitar museos y galerías enriquece tu cultura visual."},
  {keywords:["fotografía","luz","perspectiva"], respuesta:"Jugar con la luz y la perspectiva mejora tus fotos."},
  {keywords:["literatura infantil","cuento","fantasía"], respuesta:"Leer cuentos de fantasía estimula la imaginación de los niños."},
  {keywords:["música electrónica","dj","festival"], respuesta:"La música electrónica tiene un impacto fuerte en festivales y cultura moderna."},
  {keywords:["medicina","síntoma","diagnóstico"], respuesta:"Consultar con un profesional es clave ante cualquier síntoma inusual."},
  {keywords:["ingeniería","puente","edificio"], respuesta:"La ingeniería permite construir infraestructuras seguras y eficientes."},
  {keywords:["ecología","bosque","animales"], respuesta:"Proteger los ecosistemas asegura la supervivencia de muchas especies."},
  {keywords:["historia moderna","revolución","siglo XX"], respuesta:"Estudiar historia moderna ayuda a entender la actualidad."},
  {keywords:["programación web","backend","frontend"], respuesta:"El desarrollo web combina frontend (interfaz) y backend (lógica y datos)."},
  {keywords:["robótica","sensor","motor"], respuesta:"Los robots usan sensores y motores para interactuar con el entorno."},
  {keywords:["videojuegos","estrategia","aventura"], respuesta:"Cada tipo de videojuego desarrolla diferentes habilidades cognitivas."},
  {keywords:["cine","animación","pixar"], respuesta:"Las películas de animación combinan arte y tecnología."},
  {keywords:["literatura contemporánea","novela","ensayo"], respuesta:"La literatura contemporánea refleja la sociedad actual."},
  {keywords:["historia mundial","imperio","colonialismo"], respuesta:"Conocer la historia mundial permite aprender de los errores y aciertos del pasado."},
  {keywords:["matemáticas avanzadas","integral","derivada"], respuesta:"Calcular derivadas e integrales es fundamental en cálculo avanzado."},
  {keywords:["física","gravedad","energía"], respuesta:"La gravedad afecta todos los objetos con masa en el universo."},
  {keywords:["astronomía","estrella","constelación"], respuesta:"Las constelaciones han servido de guía durante siglos."},
  {keywords:["ciencia","experimento","laboratorio"], respuesta:"Los experimentos son la base del método científico."},
  {keywords:["ingeniería informática","servidor","red"], respuesta:"Conocer redes y servidores es esencial para el mundo digital."},
  {keywords:["fotografía","paisaje","retrato"], respuesta:"Cada tipo de fotografía requiere técnicas diferentes."},
  {keywords:["viaje","turismo","hotel"], respuesta:"Planifica tu alojamiento antes de viajar para evitar problemas."},
  {keywords:["deporte","correr","maratón"], respuesta:"Correr es un ejercicio cardiovascular excelente para la salud."},
  {keywords:["arte digital","dibujo","tablet"], respuesta:"El arte digital permite combinar creatividad y tecnología."},
  {keywords:["programación","python","data"], respuesta:"Python es muy usado para análisis de datos y automatización."},
  {keywords:["tecnología","smartphone","app"], respuesta:"Las aplicaciones móviles simplifican muchas tareas diarias."},
  {keywords:["historia","revolución industrial","siglo XIX"], respuesta:"La revolución industrial transformó la economía y sociedad modernas."},
  {keywords:["literatura","cuento corto","novela gráfica"], respuesta:"Explorar distintos formatos literarios enriquece la lectura."},
  {keywords:["medicina","hospital","urgencias"], respuesta:"Los hospitales son esenciales para la atención médica inmediata."},
  {keywords:["ecología","agua","contaminación"], respuesta:"Cuidar el agua es vital para la vida en la Tierra."},
  {keywords:["astronomía","luna","eclipse"], respuesta:"Los eclipses lunares son fenómenos naturales espectaculares."},
  {keywords:["historia","antigua Grecia","Roma"], respuesta:"La antigua Grecia y Roma dejaron un legado cultural enorme."},
  {keywords:["música","instrumento","cantar"], respuesta:"Cantar y tocar instrumentos desarrolla habilidades musicales y coordinación."},
  {keywords:["matemáticas","probabilidad","estadística"], respuesta:"La probabilidad y estadística son útiles en la vida diaria y ciencia."},
  {keywords:["programación","javascript","html","css"], respuesta:"El trío HTML, CSS y JS es fundamental para crear páginas web interactivas."},
  {keywords:["viaje","crucero","aventura"], respuesta:"Un crucero ofrece experiencias únicas de viaje y ocio."},
  {keywords:["cine","suspense","thriller"], respuesta:"Las películas de suspense mantienen al espectador atento y entretenido."},
  {keywords:["literatura","poema","verso"], respuesta:"Escribir poesía desarrolla la creatividad y sensibilidad."},
  {keywords:["salud","ejercicio físico","bienestar"], respuesta:"Mantener hábitos de ejercicio mejora la salud física y mental."},
  {keywords:["deporte","yoga","meditación"], respuesta:"El yoga combina ejercicio físico y mental para mejorar la salud."},
  {keywords:["educación","curso online","aprendizaje"], respuesta:"Aprender online es flexible y accesible para todos los niveles."},
  {keywords:["tecnología","IA","aprendizaje automático"], respuesta:"La inteligencia artificial aprende patrones para ayudar en tareas específicas."},
  {keywords:["videojuegos","simulación","estrategia"], respuesta:"Los juegos de simulación enseñan planificación y toma de decisiones."},
  {keywords:["historia","edad media","castillo"], respuesta:"La Edad Media tuvo grandes cambios políticos, sociales y culturales."},
  {keywords:["astronomía","cometa","asteroide"], respuesta:"Los cometas y asteroides son cuerpos fascinantes del espacio."},
  {keywords:["programación","algoritmo","optimización"], respuesta:"Optimizar algoritmos mejora el rendimiento de programas y software."},
  {keywords:["matemáticas","ecuación","problema"], respuesta:"Resolver ecuaciones desarrolla el pensamiento lógico."},
  {keywords:["cine","documental","historia"], respuesta:"Los documentales son una excelente forma de aprender sobre hechos reales."},
  {keywords:["tecnología","hardware","componentes"], respuesta:"Conocer los componentes de un PC permite mantenerlo y mejorarlo."},
  {keywords:["arte","pintura moderna","surrealismo"], respuesta:"El arte moderno y el surrealismo desafían nuestra percepción y creatividad."},
  {keywords:["cómo puedo mejorar mi concentración","tips concentración","consejos estudiar"], respuesta:"Establece un horario fijo, elimina distracciones y toma descansos cortos cada cierto tiempo."},
  {keywords:["ideas para proyectos de programación","proyecto software","programa interesante"], respuesta:"Puedes crear un juego, una aplicación web o un bot de tareas automatizadas."},
  {keywords:["formas de aprender un idioma rápido","aprendizaje idiomas","mejorar vocabulario"], respuesta:"Escucha podcasts, mira series subtituladas y practica con hablantes nativos."},
  {keywords:["qué hacer si mi computadora no enciende","PC no arranca","problemas encendido ordenador"], respuesta:"Verifica la corriente, revisa cables y prueba encender en modo seguro si es posible."},
  {keywords:["cómo cocinar arroz perfecto","receta arroz","consejos cocina"], respuesta:"Lava el arroz, usa la proporción correcta de agua, y cocina a fuego lento hasta que absorba todo el líquido."},
  {keywords:["cómo organizar mi tiempo","gestión del tiempo","planificación diaria"], respuesta:"Usa agendas o apps, prioriza tareas importantes y establece límites de tiempo para cada actividad."},
  {keywords:["qué hacer para relajarse después del trabajo","estrés trabajo","relajación"], respuesta:"Puedes hacer ejercicio, meditación, escuchar música o leer un libro que disfrutes."},
  {keywords:["formas de ahorrar dinero","finanzas personales","ahorrar"], respuesta:"Crea un presupuesto, evita gastos innecesarios y revisa tus suscripciones regularmente."},
  {keywords:["cómo mejorar mis habilidades de escritura","escribir mejor","gramática"], respuesta:"Lee mucho, escribe regularmente y revisa tus textos en busca de errores o mejoras."},
  {keywords:["ideas para viajes económicos","viajar barato","destinos low cost"], respuesta:"Busca vuelos con antelación, usa alojamiento económico y el transporte público para moverte."},
  {keywords:["cómo hacer una página web","crear web","html css javascript"], respuesta:"Aprende HTML y CSS básicos, luego JavaScript para interacción, y prueba tus páginas localmente."},
  {keywords:["qué hacer si pierdo mi contraseña","recuperar contraseña","clave perdida"], respuesta:"Usa la opción de 'olvidé mi contraseña', verifica tu email y restablece la contraseña de manera segura."},
  {keywords:["cómo mejorar mi memoria","ejercicios memoria","memorizar mejor"], respuesta:"Practica con juegos de memoria, repite información en voz alta y mantén hábitos saludables."},
  {keywords:["consejos para estudiar matemáticas","aprender matemáticas","resolución problemas"], respuesta:"Entiende la teoría primero, practica muchos ejercicios y revisa tus errores para aprender de ellos."},
  {keywords:["cómo crear un currículum profesional","CV profesional","curriculum"], respuesta:"Incluye experiencia relevante, habilidades clave, usa un formato limpio y revisa ortografía y coherencia."},
  {keywords:["ideas para hobbies creativos","manualidades","actividad creativa"], respuesta:"Dibujo, pintura, fotografía, escribir historias o crear pequeños proyectos DIY son excelentes opciones."},
  {keywords:["cómo hacer que mi casa sea más ecológica","hogar sostenible","ahorrar energía"], respuesta:"Usa bombillas LED, recicla, reduce el consumo de plástico y ahorra agua con prácticas responsables."},
  {keywords:["formas de mejorar la productividad","productividad","trabajo eficiente"], respuesta:"Organiza tu espacio, planifica tus tareas, evita multitasking y toma descansos regulares."},
  {keywords:["qué leer para aprender sobre historia","libros historia","historia interesante"], respuesta:"Puedes leer biografías, libros sobre grandes civilizaciones o historia mundial reciente."},
  {keywords:["cómo aprender a tocar un instrumento","música","aprender guitarra piano"], respuesta:"Practica diariamente, sigue tutoriales y toma clases si es posible para mejorar técnica y ritmo."},
  {keywords:["formas de mejorar mi salud física","ejercicio diario","fitness"], respuesta:"Combina ejercicio cardiovascular, fuerza y flexibilidad, además de una alimentación equilibrada."},
  {keywords:["cómo estudiar para un examen","preparación examen","consejos estudio"], respuesta:"Haz resúmenes, repasa ejercicios prácticos, usa tarjetas de memoria y evita dejarlo todo para último momento."},
  {keywords:["ideas para aprender sobre ciencia","experimentos caseros","ciencia divertida"], respuesta:"Puedes probar experimentos con agua, química sencilla y observar la naturaleza para aprender de manera práctica."},
  {keywords:["cómo organizar un viaje con amigos","viaje grupo","planificación vacaciones"], respuesta:"Establezcan presupuesto, destino, fechas, alojamiento y actividades antes de salir para evitar problemas."},
  {keywords:["cómo empezar con la fotografía","fotografía para principiantes","cámara"], respuesta:"Aprende sobre luz, composición y enfoque; empieza con tu smartphone antes de invertir en equipo avanzado."},
  {keywords:["formas de mejorar mi inglés","aprender inglés","inglés fluido"], respuesta:"Escucha música, mira series en inglés, haz intercambios y practica conversación regularmente."},
  {keywords:["qué hacer si tengo ansiedad","ansiedad","relajación mental"], respuesta:"Respira profundamente, haz ejercicio, habla con alguien de confianza o consulta un profesional si es necesario."},
  {keywords:["cómo preparar una presentación eficaz","presentación profesional","hablar en público"], respuesta:"Estructura tu contenido, usa apoyos visuales, practica y controla tu lenguaje corporal."},
  {keywords:["ideas para emprendimiento","emprender negocio","negocio propio"], respuesta:"Identifica un problema, busca soluciones, valida tu idea y empieza con algo pequeño y escalable."},
  {keywords:["cómo mejorar en deportes","entrenamiento físico","deporte"], respuesta:"Practica regularmente, cuida tu alimentación y realiza ejercicios específicos para tu disciplina."},
  {keywords:["cómo organizar mi escritorio","ordenar espacio","productividad"], respuesta:"Mantén solo lo necesario a la mano, clasifica documentos y usa contenedores para objetos pequeños."},
  {keywords:["cómo aprender a programar desde cero","principiante programación","aprender código"], respuesta:"Empieza con un lenguaje sencillo como Python, sigue tutoriales online y practica con proyectos simples."},
  {keywords:["qué hacer en caso de emergencia","emergencia","primeros auxilios"], respuesta:"Mantén la calma, llama a los servicios de emergencia y presta primeros auxilios básicos si es seguro hacerlo."},
  {keywords:["cómo reducir el estrés","estrés","relajación"], respuesta:"Haz ejercicio, medita, duerme bien y establece prioridades en tus tareas diarias."},
  {keywords:["cómo mejorar la creatividad","ser creativo","ideas nuevas"], respuesta:"Inspírate con libros, música, arte, cambia de entorno y practica brainstorming regularmente."},
  {keywords:["qué hacer si mi internet va lento","internet lento","wifi problemas"], respuesta:"Reinicia el router, verifica la velocidad y limita la cantidad de dispositivos conectados."},
  {keywords:["cómo hacer un presupuesto personal","gestión dinero","ahorrar"], respuesta:"Anota ingresos y gastos, establece límites de gasto y revisa tu presupuesto semanalmente."},
  {keywords:["formas de aprender historia de manera divertida","historia entretenida","aprender historia"], respuesta:"Mira documentales, visita museos, lee novelas históricas y usa juegos educativos."},
  {keywords:["cómo mantenerme motivado","motivación","lograr objetivos"], respuesta:"Establece metas claras, celebra pequeños logros y rodéate de personas que te inspiren."},
  {keywords:["cómo mejorar mi escritura creativa","escribir mejor","historia"], respuesta:"Lee autores variados, escribe diariamente, revisa y edita tus textos y recibe retroalimentación."},
  {keywords:["cómo empezar con meditación","meditar","relajación mental"], respuesta:"Encuentra un lugar tranquilo, dedica 5-10 minutos al día y concéntrate en tu respiración."},
  {keywords:["formas de aprender sobre astronomía","planetas","universo"], respuesta:"Observa el cielo, usa apps de astronomía y lee libros o artículos sobre el universo."},
  {keywords:["cómo aprender sobre programación web","html css javascript","crear página web"], respuesta:"Empieza con tutoriales básicos, practica con ejemplos y luego crea proyectos propios pequeños."},
  {keywords:["qué hacer para dormir mejor","insomnio","dormir bien"], respuesta:"Mantén horarios regulares, evita pantallas antes de dormir y crea un ambiente relajante."},
  {keywords:["cómo mantener la motivación en el estudio","estudiar motivado","productividad estudio"], respuesta:"Establece metas diarias, usa recompensas y toma descansos regulares para evitar agotamiento."},
  {keywords:["formas de mejorar la memoria visual","memorizar imágenes","recordar mejor"], respuesta:"Asocia imágenes con conceptos, haz mapas mentales y practica observación."},
  {keywords:["cómo empezar a dibujar","dibujar desde cero","arte"], respuesta:"Practica líneas simples, estudia formas básicas y observa objetos a tu alrededor para inspiración."},
  {keywords:["cómo aprender a programar videojuegos","crear juego","desarrollo juegos"], respuesta:"Empieza con motores como Unity o Godot y sigue tutoriales para principiantes."},
  {keywords:["formas de aprender economía","economía básica","finanzas"], respuesta:"Lee libros introductorios, sigue cursos online y analiza noticias económicas."},
  {keywords:["qué hacer si me siento aburrido","aburrimiento","entretenimiento"], respuesta:"Prueba un nuevo hobby, aprende algo nuevo o sal a caminar y despeja la mente."},
  {keywords:["cómo mejorar la concentración en clase","estudiar concentrado","clase atención"], respuesta:"Elimina distracciones, toma notas activamente y haz preguntas para mantener la atención."},
  {keywords:["formas de aprender programación de manera divertida","programación gamificada","coding"], respuesta:"Crea pequeños juegos, resuelve retos online y participa en hackathons."},
  {keywords:["qué hacer para mejorar mi postura","espalda recta","ejercicios postura"], respuesta:"Haz estiramientos, fortalece tu core y ajusta la altura de sillas y mesas al sentarte."}
];

function sendMessage() {
    const text = userInput.value.trim();
    if(!text) return;
    
    addMessage(text, 'user');
    
    const respuesta = getRespuesta(text);
    addMessage(respuesta, 'assistant');
    
    userInput.value = '';
    chatBox.scrollTop = chatBox.scrollHeight;
}

function addMessage(text, type) {
    const div = document.createElement('div');
    div.className = `message ${type}`;
    div.textContent = text;
    chatBox.appendChild(div);
}

function getRespuesta(text) {
    const lowerText = text.toLowerCase();
    for(const item of respuestas) {
        for(const key of item.keywords) {
            if(lowerText.includes(key)) {
                return item.respuesta;
            }
        }
    }
    return "Lo siento, no tengo una respuesta para eso todavía.";
}

// Permite enviar con Enter
userInput.addEventListener('keydown', function(e){
    if(e.key === 'Enter') sendMessage();
});
</script>

</body>
</html>
