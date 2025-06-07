# Workflow Builder PoC

This repository contains a proof of concept workflow editor built with **Vue 3** and **Vue Flow**. The goal is to provide a simple foundation for building and editing complex workflows that can be extended with custom nodes (including future AI agent nodes).

## Features

- Drag-and-drop node based editor using [Vue Flow](https://vueflow.dev)
- Connect nodes to build flows
- Import and export flows as JSON
- Easily extendable with custom node types

The initial PoC runs a list of simple function nodes, but the architecture is designed so more advanced features can be added.

## Getting Started

```bash
npm install
npm run dev
```

Then open the dev server URL shown in the terminal.

## Folder Structure

- `src/` – Source files for the Vue workflow editor
- `package.json` – Project dependencies and scripts

## Next Steps

- Add support for more node types
- Persistence and templates
- Nested workflows and subflows
