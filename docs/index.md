---
layout: home
title: DriftJS — Register-Based Reactivity for the Web
titleTemplate: DriftJS

hero:
  name: DriftJS
  text: Register-Based Reactivity for the Web
  tagline: "Ultra-fast in-browser bytecode VM execution with zero Virtual DOM overhead. Compile .drift templates to compact bytecode, execute through a 256-register VM, and surgically update the DOM."
  image:
    src: /logo.svg
    alt: DriftJS Logo
  actions:
    - theme: brand
      text: Get Started →
      link: /introduction/getting-started
    - theme: alt
      text: Documentation
      link: /introduction/overview
    - theme: alt
      text: GitHub
      link: https://github.com/hrutavmodha/driftjs

features:
  - icon: ⚡
    title: Register-Based Virtual Machine
    details: "256 fast virtual registers (r0–r255) store DOM elements, text nodes, fragments, and evaluated values. Direct bytecode execution against the DOM."
  - icon: 🔧
    title: Bytecode Compilation
    details: ".drift templates compile to compact 15-opcode bytecode streams with constant pools and reactive bindings. Binary-serializable CompiledModule output."
  - icon: 🎯
    title: Reactive Regions
    details: "HTML comment anchors (<!--if-->, <!--for-->) bound @if and @for blocks for surgical re-rendering of targeted regions."
  - icon: 🔄
    title: Keyed LIS Reconciliation
    details: "Longest Increasing Subsequence algorithm minimizes DOM node movements, insertions, and deletions in @for loops."
  - icon: 📐
    title: Expression Engine
    details: "High-performance JS expression evaluation in scope with compiled functions for optimal runtime execution."
  - icon: ⚙️
    title: Vite Integration
    details: "First-class Vite plugin for instant .drift template compilation and full-reload HMR on file save."
---

<div class="arch-diagram" style="margin-top: 32px">
  <div style="font-family: var(--drift-font-display); font-size: 13px; font-weight: 700; letter-spacing: 1.5px; text-transform: uppercase; color: var(--drift-text-muted); margin-bottom: 20px;">Compilation Pipeline</div>
  <div class="arch-step">
    <div class="arch-node">
      <span class="arch-icon">📄</span> .drift Template
      <span class="arch-desc">Single-file component with script + markup</span>
    </div>
  </div>
  <div class="arch-arrow"></div>
  <div class="arch-step">
    <div class="arch-node">
      <span class="arch-icon">🔤</span> DriftLexer
      <span class="arch-desc">On-demand parser-driven tokenization</span>
    </div>
  </div>
  <div class="arch-arrow"></div>
  <div class="arch-step">
    <div class="arch-node">
      <span class="arch-icon">🌳</span> DriftParser
      <span class="arch-desc">AST construction (ProgramNode, ElementNode, IfNode, ForNode)</span>
    </div>
  </div>
  <div class="arch-arrow"></div>
  <div class="arch-step">
    <div class="arch-node">
      <span class="arch-icon">✨</span> DriftTransformer
      <span class="arch-desc">Whitespace stripping & JS expression enrichment</span>
    </div>
  </div>
  <div class="arch-arrow"></div>
  <div class="arch-step">
    <div class="arch-node">
      <span class="arch-icon">⚙️</span> DriftGenerator
      <span class="arch-desc">Emits 15-opcode bytecode, constant pool & reactive bindings</span>
    </div>
  </div>
  <div class="arch-arrow"></div>
  <div class="arch-step">
    <div class="arch-node" style="border-color: var(--drift-accent); box-shadow: 0 0 20px rgba(59, 130, 246, 0.12);">
      <span class="arch-icon">⚡</span> Register VM → DOM
      <span class="arch-desc">256-register VM executes bytecode with keyed LIS reconciliation</span>
    </div>
  </div>
</div>

<div style="text-align: center; margin: 40px 0 16px;">
  <div style="font-family: var(--drift-font-display); font-size: 13px; font-weight: 700; letter-spacing: 1.5px; text-transform: uppercase; color: var(--drift-text-muted);">Quick Start</div>
</div>

```bash
pnpm create drift my-app
# or
npm create drift my-app
```

<div style="text-align: center; margin: 24px 0;">
  <span class="status-badge experimental">⚠ Experimental Prototype</span>
</div>

<div style="text-align: center; max-width: 600px; margin: 0 auto 48px; font-size: 14px; color: var(--drift-text-secondary); line-height: 1.6;">
  DriftJS is currently an experimental prototype exploring register-based VM execution for web UI.
  Single-template compilation, expression evaluation, and keyed LIS reconciliation are implemented and passing all tests.
  <a href="/project/limitations">View current limitations →</a>
</div>
