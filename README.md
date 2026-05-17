# 🏍️ Simulador de Conducción para Motociclistas en VR

Entorno urbano 3D inspirado en una manzana latina de Bogotá, Colombia, desarrollado en Blender con datos reales de OpenStreetMap y exportado para Unity. Proyecto académico orientado a gafas de realidad virtual Meta Quest.

---

## 📸 Vista previa

<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/afd41085-5142-4243-a675-6bc355772d9e" />
<img width="900" height="1600" alt="image" src="https://github.com/user-attachments/assets/ec7c72c2-6b08-4420-b337-29b5d566c73d" />
<img width="1195" height="896" alt="image" src="https://github.com/user-attachments/assets/b17bdb8f-a961-4864-bb04-8ab3ee1abf9d" />
<img width="1035" height="1024" alt="image" src="https://github.com/user-attachments/assets/f5ebe6ec-3499-495a-9de8-2fd13aac1577" />
<img width="992" height="1060" alt="image" src="https://github.com/user-attachments/assets/93396be5-142d-444e-ba1c-5294c29058e0" />


---

## 🛠️ Tecnologías utilizadas

| Herramienta | Uso |
|---|---|
| **Blender 4.4** | Modelado, UV mapping y preparación de la escena |
| **BlenderGIS** | Generación del entorno urbano desde datos OSM |
| **OpenStreetMap (OSM)** | Datos geográficos reales de la ciudad |
| **Python (bpy)** | Scripts de automatización dentro de Blender |
| **Unity 6 (URP)** | Motor de videojuego para VR |
| **Meta Quest 2/3** | Plataforma objetivo de VR |

---

## 🗺️ Generación del entorno

El entorno urbano fue generado usando el addon **BlenderGIS**, que importa datos reales de OpenStreetMap directamente en Blender.

### Capas generadas por BlenderGIS

- `map.osm` — Capa principal del mapa
- `map_2.osm` — Edificios extruidos automáticamente desde huellas OSM
- `map_3.osm`, `map_4.osm`, `map_7.osm` — Capas adicionales de vías y perfiles urbanos
- `way_profiles` — Perfiles de carreteras (residencial, servicio, terciaria, ferroviaria, peatonal)

### Proceso de limpieza de geometría OSM

Los edificios generados automáticamente presentan problemas comunes que fueron corregidos manualmente y por script:

- **Normales invertidas** → corregidas con `Mesh → Normals → Recalculate Outside` (Shift+N)
- **Geometría corrupta** → edificios con formas no convexas descartados y reemplazados
- **Caras de fondo innecesarias** → eliminadas para reducir el conteo de triángulos

---

## 🏗️ Pipeline de construcción del entorno

```
OpenStreetMap → BlenderGIS → Limpieza de geometría → 
Agrupación aleatoria → UV Mapping → Atlas de texturas → 
Exportación .glb → Unity
```

### 1. Modelos base

Se generó con el add_on OSM de blender, que generó las carreteras y los cubos base para las ciudades de formas variadas

- Angosto y alto (2-3 pisos)
- Ancho y bajo (local comercial)
- Mediano cuadrado
- Forma en L o con patio
- Mediano con desnivel de techo

Cada modelo fue preparado con:
- Origen en la base del objeto (Z = 0)
- Transformaciones aplicadas (`Ctrl+A → All Transforms`)
- Normales recalculadas hacia el exterior

### 2. Agrupación aleatoria con Python

Los **161 edificios OSM** fueron distribuidos aleatoriamente en **2 grupos** mediante un script Python (`agrupador_aleatorio.py`). El sistema usa propiedades personalizadas de Blender (`obj["ya_agrupado"]`) como memoria persistente para evitar reprocesar objetos en ejecuciones posteriores.


### 3. UV Mapping

Se aplicó **Smart UV Project** a los 5 modelos base para generar coordenadas UV automáticas compatibles con el atlas de texturas.

### 4. Atlas de texturas

Se crearon **2 atlas de texturas** con imágenes fotográficas de fachadas latinas bogotanas reales:

- `atlas_ecci.png` — Fachadas estilo republicano, puertas azules, balcones con hierro forjado
- `atlasnuevo.png` / `atlas2.png` — Fachadas comerciales, locales de planta baja, tejas españolas, elementos urbanos (hidrantes, señales, medidores)

Los atlas incluyen elementos auténticos como:
- Café Bogotano / Café Republicano
- Zona de Carga
- Farmacia Central
- Miscelánea El Sol
- Tejas de barro
- Balcones con macetas

---

## 🐍 Scripts de Python

| Script | Descripción |
|---|---|
| `agrupador_aleatorio.py` | Distribuye los 161 edificios OSM en 20 grupos aleatorios con memoria persistente |
| `contador_coleccion.py` | Cuenta objetos por tipo dentro de una colección |
| `selector_n_grupos.py` | Selecciona N grupos aleatorios y todos sus objetos |
| `selector_n_objetos.py` | Selecciona N objetos aleatorios de los grupos |
| `mover_a_coleccion.py` | Mueve objetos seleccionados a una nueva colección |
| `set_origin_bottom.py` | Corrige el origen de los modelos base a la base del objeto |
| `lod_optimizer_quest.py` | Sistema LOD por zonas de distancia para optimización en Meta Quest |

### Uso general de los scripts

1. Abrir Blender → **Text Editor**
2. Cargar el script con el ícono de carpeta
3. Verificar los parámetros en la sección `PARÁMETROS` al inicio de cada script
4. Ejecutar con **Alt+P**
5. Ver resultados en `Window → Toggle System Console`

---

## ⚡ Optimización para Meta Quest

El entorno fue diseñado con las siguientes consideraciones para VR en Meta Quest 2/3:

- **Presupuesto de triángulos:** < 50k tris totales en escena
- **Sistema LOD por zonas:**
  - Zona 1 (0–30m): modelos low poly completos (~800 tris c/u)
  - Zona 2 (30–80m): imposters (planos de 2 triángulos con textura)
  - Zona 3 (> 80m): eliminados de la escena
- **Atlas único** de texturas → 1 draw call para todos los edificios
- **Linked duplicates** → todos los clones comparten la misma malla en memoria

---

## 🚀 Exportación a Unity

Exportar desde Blender con `File → Export → glTF 2.0 (.glb)` con estos ajustes:

```
Transform:
  Forward: -Z Forward
  Up: Y Up
```

Importar en Unity arrastrando el `.glb` al panel **Project → Assets**.

---

## 👤 Autora

Proyecto académico — Ingeniería de Sistemas  
**Universidad ECCI — Bogotá, Colombia**

---

## 📄 Licencia

Datos geográficos © [OpenStreetMap contributors](https://www.openstreetmap.org/copyright) — licencia ODbL.
