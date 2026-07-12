# Notemark — Viewport Annotation Tool for Blender

[![Blender Version](https://img.shields.io/badge/Blender-3.0_--_5.1+-orange.svg)](https://docs.blender.org/)
[![License](https://img.shields.io/badge/License-GPL_3.0-blue.svg)](https://www.gnu.org/licenses/gpl-3.0.html)
[![Version](https://img.shields.io/badge/Version-1.1.0-green.svg)](./CHANGELOG.md)

Notemark is a lightweight, minimal viewport annotation add-on designed for solo artists and small team scene planning. Unlike complex studio production tracking systems, Notemark acts as a frictionless "sticky note" utility inside the 3D viewport—allowing you to drop, write, and manage spatial notes in real time without heavy pipeline overhead.

---

## Key Features

* **Direct Viewport Typing:** `Click to Place` drops a clean note card right onto mesh surfaces or empty 3D space, entering an immediate typing modal with live rendering and text cursor feedback.
* **Frictionless Viewport Interaction:** Hold `Ctrl + Click` directly on a note card in the 3D viewport to instantly edit text inline, or `Ctrl + Drag` the note's anchor dot to shift its spatial positioning.
* **Contextual Color Tagging:** Categorize visual markers instantly into standard operational states: `TODO`, `PROBLEM`, `DONE`, or `REFERENCE`.
* **Adaptive Depth & Resolution Management:** Intelligently tracks viewport screen projected boundaries, computing pixel-accurate margins and line-wrapping rules using font metrics via `blf.dimensions()`.
* **Centralized Control Panel:** Clean sidebar layout in the 3D Viewport ($N$-panel) featuring full list aggregations, quick-jump zoom functions, and individual edit/delete triggers.
* **Native Undo Engine Integration:** Built entirely with native structural tracking to support default Blender `Ctrl + Z` undo histories cleanly.

---

## Installation

1. Download the repository source file packaged as `notemark.zip`.
2. Open Blender and navigate to **Edit > Preferences > Add-ons** (or **Extensions** for Blender 4.2+).
3. Click **Install...** (or **Install from Disk...**) and select the `notemark.zip` file.
4. Enable the checkbox next to **Interface: Notemark**.

---

## Architecture & Interaction Matrix

The add-on registers persistent 2D drawing callbacks using the `bl_op_region_2d` space via Blender's `bpy.types.SpaceView3D.draw_handler_add` registry running on the `POST_PIXEL` execution pass. 

To bypass stale context errors typical of long-running viewport rendering callbacks, the draw routine executes as a no-argument wrapper function, querying the active window region, 3D perspective matrix (`RegionView3D`), and underlying model arrays fresh on every screen redraw:

$$\mathbf{X}_{\text{screen}}, \mathbf{Y}_{\text{screen}} = \text{view3d\_utils.location\_3d\_to\_region\_2d}(\text{region}, \text{rv3d}, \mathbf{P}_{\text{world}})$$

A continuous, modal background event controller captures global system inputs via a `Ctrl + Click` keymap layer to capture selection bounding boxes around the computed 2D screen coordinates of drawing dots without blocking standard geometry selection.

---

## Repository Structure

```text
notemark/
├── .gitignore
├── LICENSE
├── README.md
├── CHANGELOG.md
└── notemark/
    ├── __init__.py
    └── blender_manifest.toml
