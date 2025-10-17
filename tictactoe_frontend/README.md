# Modern Tic‑Tac‑Toe (Svelte)

A modern, accessible, two‑player Tic‑Tac‑Toe built with Svelte and SvelteKit. Features:
- Ocean Professional theme (primary #2563EB, secondary/success #F59E0B, error #EF4444, bg #f9fafb, surface #ffffff, text #111827)
- Centered 3x3 grid with rounded corners, subtle shadows, and smooth transitions
- Live status: current player, winner, or draw
- Winning line highlight and move blocking after game end
- Reset button
- Move history with time‑travel to any step
- Responsive and keyboard accessible (Enter/Space to play, focus ring)
- LocalStorage persistence of last game

## Getting Started

Install dependencies:
```bash
npm install
```

Run the app (port 3000, strict):
```bash
npm run dev
```

Build for production:
```bash
npm run build
npm run preview
```

## Keyboard Accessibility

- Use Tab/Shift+Tab to focus cells.
- Press Enter or Space to place a mark.
- Cells are disabled once occupied or when the game is over.

## Project Structure

- src/routes/+page.svelte: App shell, status, reset, and history.
- src/lib/Board.svelte: 3x3 grid and input handling.
- src/lib/Square.svelte: Single cell with visuals and ARIA.
- src/lib/stores/game.ts: Game state, history, winner/draw detection, persistence.
- src/lib/types.ts: Type definitions.
- src/app.css: Global theme and base styles.

No backend or external services are required.
