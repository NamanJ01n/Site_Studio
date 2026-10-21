# Site Studio
### Turn any area on a map into an editable 3D digital twin

Draw a shape on a map. Site Studio downloads free OpenStreetMap data for that shape and builds the buildings, roads, parking, green areas, water and even airport runways in 3D. You can then change buildings, add new structures, check for conflicts and export the result.

- **Hackathon:** InnoTech26, KIET
- **Project category:** Smart Solutions, Smarter Society
- **Theme:** AR/VR, Digital Twins & Industry 4.0
- **Team:** [add team name and member names]

---

## 1. The problem

Planning changes to a campus, market or public space is harder than it should be:

- **3D planning tools are expensive** and need trained users. Most colleges, small organisations and students cannot access them.
- **Online maps only show what exists.** You cannot edit them to ask "what if we add a cafe here?"
- **Open map data is incomplete.** Building heights are often missing, and some buildings are not mapped at all.
- **Conflicts are found late.** Overlaps and crowding between new and existing structures are usually discovered after work begins.

## 2. The solution

Site Studio gives anyone a free, fast way to see a place in 3D and test changes before anything is built.

1. Search for a place and draw the exact area you care about, as a freeform shape.
2. The app builds the 3D site automatically from open data.
3. Select any building and change its height, colour and orientation, or move it.
4. Add new buildings, cafes, seating areas, benches and trees.
5. Overlap and clearance checks flag problems straight away.
6. Compare the original site with your proposal, then export it as a 3D model.

## 3. Features

| Feature | What it does |
|---|---|
| Place search | Finds any place by name and jumps the map to it |
| Freeform area selection | Left-click each corner, right-click to close the shape; up to about 10 km across |
| Automatic 3D site | Buildings, roads and paths, parking, green areas, water, airport runways and taxiways |
| Building names | Names from the map appear above buildings (first 200 shown) |
| Height handling | Uses the map's `height` tag, then `building:levels` x 3.2 m, then a 9 m estimate; the panel says which one applies |
| Manual footprint tracing | Click corners on the ground to create a building where the map has none |
| Editable objects | Select any object to change height, length, width and colour (hex or picker), rotate, move or delete it |
| Placeable objects | Building, cafe, seating area, bench, tree, each with a real-looking 3D model |
| Overlap and clearance checks | Added objects turn red if they overlap or sit closer than the set clearance (1.5 m by default) |
| Before / after | One button switches between the original data and your proposal |
| Zoom to object | Fly the camera to the selected structure |
| GLB export | Download the 3D scene for use in other tools |

## 4. How to use it

**Map screen**
1. Search for a place or pan the map to it.
2. Press **Draw area**. Left-click each corner, then right-click to close the shape.
3. Press **Generate 3D site** (or **Try sample** to test without internet).

**3D screen**

| Action | How |
|---|---|
| Rotate / pan / zoom | Left-drag / right-drag / scroll |
| Select an object | Click it with the **Select** tool (it gets a yellow outline) |
| Move an object | Select it, then drag it along the ground |
| Rotate an object | Q / E keys, or the rotate buttons (step is adjustable) |
| Place an object | Pick a tool, then click the ground; the tool returns to Select afterwards |
| Trace a missing building | **Trace** tool: click corners, then **Finish traced shape** |
| Zoom to object | **Zoom to** button, or double-click a selected object |
| Esc | Back to the Select tool |

## 5. How it works

```
 Map screen (Leaflet + OSM tiles)
   freeform polygon  -->  Nominatim search jumps the map
        |
        v
 Overpass API query using the polygon
   buildings, roads, parking, green, water, airport features
        |
        v
 Parse + convert lat/lon to local metres
   height rules, building names, point-in-polygon filter
        |
        v
 Three.js 3D scene
   extruded buildings, merged road meshes, flat green/water layers
        |
        v
 Editing layer: select, move, rotate, recolour, place, trace
   overlap / clearance checks, before / after
        |
        v
 GLB export
```

**Key ideas**
- **Local metres.** Every object is a polygon in metres around the centre of the chosen area, so editing, moving and distance checks are simple geometry.
- **Extrusion.** A building footprint polygon is pushed up by its height to make a 3D shape, and each building is its own object with its own outline.
- **Honest data.** Each height is labelled as verified (from map tags), estimated, or set by the user. Estimates are never presented as facts.
- **Freeform selection.** The polygon is sent to the map server as a filter, then buildings are kept only if their centre is inside the shape.
- **Overlap check.** Bounding boxes, expanded by the clearance distance, are compared for every added object.

## 6. Technology

| Part | Tool | Why |
|---|---|---|
| 3D graphics | Three.js (r160) | Runs in any browser; extrusion, raycasting selection, GLB exporter |
| Map picker | Leaflet 1.9 | Lightweight interactive map |
| Build tool | Vite 5 | Fast development server and static production build |
| Language | Plain JavaScript (ES modules) | Easy to read and extend |
| Map data | OpenStreetMap via the Overpass API | Free, open, worldwide |
| Map tiles | OpenStreetMap standard tiles | Free base map for area selection |
| Place search | Nominatim (OpenStreetMap) | Free place name lookup |

There is **no backend, database or API key**. Everything runs in the browser, so the finished site is just static files.

## 7. How it was built

1. **Idea.** Inspired by existing 3D site generators; the goal was to go further with editable objects and planning checks.
2. **Core pipeline.** Map data download, conversion to metres, building extrusion with height fallbacks, orbit camera.
3. **Editing.** Raycast selection, height and colour editing, tracing for missing buildings, placing new objects.
4. **Checks.** Overlap and clearance detection, before/after comparison.
5. **Scale.** Freeform area selection, larger areas (airports, campuses), roads, green areas, water and runways.
6. **Polish.** Place search, realistic tree, bench, seating and cafe models, rotation and drag-to-move, GLB export.
7. **Performance.** Reuse of unchanged meshes, freeing memory, drawing only when something changes.
8. **Interface and deployment.** Dark responsive interface and a static build ready for hosting.

The project was developed step by step, with Claude (an AI assistant from Anthropic) used as a coding assistant. The core product itself does not use AI at runtime: it is deterministic geometry built from open data.

## 8. Project structure

```
site-studio/
  index.html        page layout (map screen and 3D screen)
  src/main.js       all application logic (map, data, 3D, editing, checks, export)
  src/style.css     interface styling
  vite.config.js    build settings (relative paths so it works anywhere)
  package.json      dependencies and scripts
```

## 9. Run it locally

```
npm install
npm run dev        # open the localhost address it prints
```

## 10. Deploy it

```
npm run build      # creates the dist/ folder
npm run preview    # check the built site locally
```

Upload `dist/` to any static host. Easiest: drag the `dist` folder onto `app.netlify.com/drop`. Or push the project to GitHub and import it on Vercel (it detects Vite automatically).

## 11. Limitations (be upfront about these)

- Heights without map tags are **estimates**, not surveyed values.
- Overlap checks use **bounding boxes**, so L-shaped buildings can be flagged when only their corners come close.
- Length and width scale the whole footprint evenly, so irregular shapes keep their proportions; individual corners cannot be reshaped yet. Trees resize by height only.
- Buildings stored as complex multipolygons in OpenStreetMap are skipped.
- Visuals are flat-coloured, not photo-realistic, because satellite imagery is licensed only for use inside its provider's products.
- Very large areas depend on free public servers, which can be slow or busy. Large sites with thousands of buildings can be heavy on low-end laptops.
- Map quality varies by place. Where buildings are unmapped, manual tracing is needed.

## 12. Future work

- Exact polygon-based overlap and clearance checks.
- Reshape individual corners of a footprint.
- Complex multipolygon buildings and interiors, so a campus can zoom down to a single room.
- Save and share projects by link, with comments.
- Analysis tools: sunlight and shadows, walking distances, seating capacity.
- Optional AI suggestions for placement, layered on top of the core tool.

## 13. Data, licences and attribution

- Map data is © OpenStreetMap contributors, available under the Open Database Licence (ODbL). The app always shows this credit (a small "©" in the 3D view and the map credit on the map screen).
- The public OpenStreetMap servers (tiles, search, Overpass) are free for light use. For heavy public traffic, use a paid tile provider and your own search and data servers.
- No satellite or proprietary imagery is used.

---

## Presentation guide

### 3-minute demo script
1. **Hook (20 s).** "Want to add a cafe to your campus? Today you need expensive 3D software or you guess. We built Site Studio to do it in minutes, for free."
2. **Select (30 s).** Search a place, draw a freeform shape, press Generate.
3. **Explore (30 s).** Orbit the 3D site. Point out buildings, names, roads, parking, green areas.
4. **Edit (45 s).** Select a building, change height and colour, rotate and drag it. Say that heights are labelled verified or estimated.
5. **Plan (45 s).** Place a cafe, seating and trees. Drop one too close to a building so it turns red and the report explains why.
6. **Compare and export (20 s).** Toggle before/after, then export the GLB.
7. **Close (10 s).** "Free, open data, works anywhere, ready to use today."

Always have **Try sample** ready as a fallback if the internet or map server is slow.

### Likely questions and answers
- **Where does the data come from?** OpenStreetMap, free and open, crowd-sourced worldwide. We credit it as the licence requires.
- **Is it accurate?** Footprints are as accurate as the map. Heights come from tags when available and are clearly marked as estimates otherwise. Missing buildings can be traced by hand.
- **Why a digital twin?** It is a virtual copy of a real place that can be changed and tested before the real world changes. That is exactly what planners need.
- **Is there AI in it?** Not at runtime; it is deterministic geometry. We built it with an AI coding assistant, and AI placement suggestions are a planned extension.
- **How is it different from existing tools?** Every building is individually editable, you can add new structures, run overlap checks, compare before and after, trace missing buildings, and export the model.
- **Can it scale?** Areas up to about 10 km work. Public servers limit heavy use, so a production version would use dedicated data and tile servers.
- **Who is it for?** Colleges, students, local authorities and community groups, and small businesses or architects checking a concept quickly.
- **What if there is no map data?** Use the Trace tool to draw the footprint manually.

### Slide outline
1. Title and one-line pitch
2. The problem
3. The solution (screenshots)
4. Live demo
5. How it works (use the diagram in section 5)
6. Tech stack and open data
7. Impact: who benefits
8. Limitations and roadmap
9. Thank you and the live link
