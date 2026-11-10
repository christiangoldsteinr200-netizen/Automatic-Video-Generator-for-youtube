# Dos Mundos: experiencia cinematográfica 3D

Una película interactiva en 13 escenas. El scroll la controla de principio a fin.
Todo está en un solo archivo, `index.html`, con Three.js, GSAP ScrollTrigger y WebGL.
Los dos objetos son cilindros abstractos de visualización, sin etiquetas, marcas ni claims.

## Cómo verla

Lo mejor es servir la carpeta con un servidor local:

```bash
cd cinematic-experience
python3 -m http.server 8000
# abrir http://localhost:8000
```

También se puede abrir `index.html` con doble clic (`file://`). En ese caso, los navegadores
no dejan que WebGL lea imágenes locales. La página lo detecta y entra en **modo híbrido**:
los 8 fondos se pintan con CSS y los objetos 3D, las partículas y la postproducción se
dibujan encima en WebGL. No aparecen errores en la consola.

## Estructura

```
cinematic-experience/
├── index.html                  # experiencia completa (HTML + CSS + JS)
└── visual_assets_neutral/      # los 8 PNG del ZIP, con sus nombres exactos
    ├── 01_hero_black_red.png
    ├── 02_metallic_macro_texture.png
    ├── 03_pink_abstract_flavor_world.png
    ├── 04_ice_crystal_world.png
    ├── 05_white_flash_transition.png
    ├── 06_green_abstract_flavor_world.png
    ├── 07_silver_black_product_stage.png
    └── 08_final_black_red.png
```

Las rutas son relativas (`visual_assets_neutral/…`). La carpeta se configura en
`CINE.ASSET_PATH`, dentro del primer `<script>`.

Dependencias por CDN, con versiones fijas:

- `three@0.160.0` (jsDelivr, módulos ES con `importmap`): renderer, PMREM, `EffectComposer`,
  `UnrealBloomPass`, `OutputPass`, `ShaderPass` y `Reflector`.
- `gsap@3.12.5` y `ScrollTrigger` (cdnjs).
- Google Fonts: Anton, Inter y JetBrains Mono.

## Secuencia

| # | Escena | Fondo | Cámara / efecto |
|---|--------|-------|-----------------|
| 01 | Dark Entrance | `01_hero_black_red` | Plano lejano y *dolly-in*. El cilindro aparece en silueta con contraluz rojo |
| 02 | Macro Detail | `02_metallic_macro_texture` | *Macro zoom* con objetivo sobre la superficie, gotas, reflejo de barrido y profundidad de campo |
| 03 | Product Reveal | `02_…` | *Dolly-out* con órbita. Entra el segundo objeto y una línea de luz revela los dos materiales |
| 04 | Pink Universe | `03_pink_abstract_flavor_world` | Órbita con *roll*. Esferas y cristales iridiscentes en suspensión |
| 05 | Ice World | `04_ice_crystal_world` | *Tracking* a través de un corredor de cristales |
| 06 | White Flash | `05_white_flash_transition` | Sobreexposición, *motion blur* y **corte de cámara oculto** en el pico del destello |
| 07 | Green Universe | `06_green_abstract_flavor_world` | Órbita y grúa ascendente. Los elementos suben |
| 08 | Metallic World | `07_silver_black_product_stage` | *Tracking* bajo sobre un suelo reflectante, haces volumétricos y objetos cromados |
| 09 | Camera Journey | `01_…` (vacío rojo) | Túnel infinito de anillos, fragmentos, estelas y partículas, con *roll* controlado |
| 10 | Abstract Transformation | rosa → verde → plata | Órbita de 360°. Los cilindros se transforman en metal líquido y cambian de escala y luz |
| 11 | Dual Composition | `07_…` | Simetría, contraluz rojo y haces de luz |
| 12 | Final Approach | `07_…` → `08_final_black_red` | *Dolly-out* lento. Las partículas convergen en un anillo |
| 13 | Final Frame | `08_final_black_red` | Composición mínima y tarjeta final |

## Arquitectura

- **Director**, en el `<script>` clásico. Define la configuración, el estado (`CINE.S`) y la
  **línea de tiempo maestra** de GSAP, con *scrub* y la sección `#film` fijada (*pinned*)
  por ScrollTrigger. También se ocupa del HUD, la navegación por escenas, el loader y los
  fallbacks. No depende de Three.js.
- **Motor**, en el `<script type="module">`. Solo lee `CINE.S` en cada frame y lo convierte en
  escena 3D. La cámara funciona como un *rig* esférico: objetivo, radio, azimut, elevación,
  fov, *roll* y *film offset*. Gracias a eso, todos los movimientos de una escena a la
  siguiente son continuos.

Cada fondo tiene su propio "look": color de niebla, partículas, luces, exposición, bloom y
mapa de entorno. Entre un mundo y el siguiente, el look se interpola y el fondo se funde con
una disolución por ruido y distorsión.

### Efectos

- Fondos integrados en 3D: plano de cámara con parallax por orientación, capa de bruma más
  cercana, niebla fbm, disolución con distorsión y mapas de entorno PMREM generados a partir
  de cada PNG con *lightformers* de estudio. Así, los reflejos del metal cambian con cada mundo.
- Partículas con bokeh físico (tamaño según la distancia al foco), flujo, ascenso y convergencia.
- Gotas de condensación (normal map procedural en el *clearcoat* y gotas 3D instanciadas) y
  gotas suspendidas.
- Bloom moderado con prefiltro Karis antiartefactos, *motion blur* radial calculado a partir
  de la velocidad real de la cámara, profundidad de campo, aberración cromática, viñeta y grano.
- *Morphing* del cilindro a metal líquido mediante el vertex shader.
- Suelo reflectante, conos de luz volumétrica y sombras de contacto.

## Responsive y rendimiento

- En móvil se reducen las partículas, los objetos y las texturas. No hay reflector ni MSAA, y
  la resolución máxima baja. El encuadre se compensa en vertical, salvo en los primeros planos.
- La resolución es adaptativa: el DPR baja si los FPS caen por debajo de 45 y sube si se
  mantienen estables.
- Los shaders se precompilan durante la carga para evitar tirones a mitad del scroll.
- Con `prefers-reduced-motion` se suavizan el destello, la distorsión, la cámara en mano y el
  parallax.

## Fallbacks

| Situación | Comportamiento |
|-----------|----------------|
| Sin WebGL, CDN de Three.js caído o pérdida de contexto | **Modo CSS**: mismos fondos, transiciones, timeline y cilindros CSS animados |
| `file://` (imágenes bloqueadas para WebGL) | **Modo híbrido**: fondo CSS y 3D WebGL transparente encima |
| Sin GSAP | **Versión estática**: 13 secciones con los fondos y los textos |
| Falta algún PNG | Fondo procedural con la paleta de esa escena |
