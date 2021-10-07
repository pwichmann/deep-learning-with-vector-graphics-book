# Challenges of Vector Graphics

This section aims to summarise why vector graphics, and SVG specifically, are challenging to work with in the context of Deep Learning.

## Why Vector Graphics (e.g. SVG) are challenging

1. Deep Learning models expect a numeric tensor. SVG files, however, are XML text.
2. SVG files are hierarchical / a graph of nodes and attributes.
3. There are *many* ways to design an SVG that results in the same rastered PNG.
4. SVG allows for simple paths as well as pre-defined SVG primitives (such as rectangles).
5. SVGs have an x,y coordinate system of a certain size; in addition only a certain part of it may be defined to be shown to the user (viewport)
6. SVGs can contain animations and all sorts of other Web technology (scripts, ...) that need to be ignored.