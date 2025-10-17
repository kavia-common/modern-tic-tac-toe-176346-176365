<script lang="ts">
  import Board from '$lib/Board.svelte';
  import { game } from '$lib/stores/game';
  import type { Move, GameState } from '$lib/types';

  // Subscribe to the state readable to get actual GameState during SSR and CSR
  let state = $state<GameState | undefined>(undefined);
  $effect(() => {
    const unsub = game.state.subscribe((s) => {
      // ensure s is defined
      if (s) state = s as GameState;
    });
    return () => unsub();
  });

  const current = $derived(state?.current);
  const winnerInfo = $derived(state?.winner);
  const statusText = $derived(() => {
    if (winnerInfo?.winner) return `Winner: ${winnerInfo.winner}`;
    if (winnerInfo?.isDraw) return 'Draw game';
    return `Current player: ${current?.next === 'X' ? 'X' : 'O'}`;
  });

  function onReset() {
    game.reset();
  }

  function jumpTo(index: number) {
    game.jumpTo(index);
  }

  function formatMove(m: Move, i: number) {
    if (i === 0) return 'Game start';
    const row = Math.floor(m.pos / 3) + 1;
    const col = (m.pos % 3) + 1;
    return `#${i}: ${m.player} → r${row}c${col}`;
  }

  const isOver = $derived(!!winnerInfo?.winner || !!winnerInfo?.isDraw);
</script>

<svelte:head>
  <title>Modern Tic-Tac-Toe</title>
  <meta name="description" content="A modern, accessible two-player Tic-Tac-Toe built with Svelte." />
</svelte:head>

<div class="page">
  <section class="card" aria-label="Tic-Tac-Toe game">
    <header class="header">
      <h1>Tic‑Tac‑Toe</h1>
      <p class="status" role="status" aria-live="polite">{statusText}</p>
    </header>

    <Board
      board={current?.squares ?? Array(9).fill(null)}
      winningLine={winnerInfo?.line ?? []}
      disabled={isOver}
      onplay={(e) => game.play(e.detail.index)}
    />

    <div class="actions">
      <button
        class="reset"
        type="button"
        onclick={onReset}
        aria-label="Reset game"
      >
        Reset
      </button>
    </div>
  </section>

  <aside class="history card" aria-label="Move history">
    <div class="history-header">
      <h2>History</h2>
      <small>{(state?.history?.length ?? 1) - 1} moves</small>
    </div>
    <ol>
      {#each (state?.history ?? []) as move, i (i)}
        <li>
          <button
            type="button"
            class:selected={i === state?.step}
            onclick={() => jumpTo(i)}
            aria-current={i === state?.step ? 'step' : undefined}
            aria-label={`Go to move ${i}`}
          >
            {formatMove(move, i)}
          </button>
        </li>
      {/each}
    </ol>
  </aside>
</div>

<style>
  :global(:root) {
    --primary: #2563EB;
    --secondary: #F59E0B;
    --success: #F59E0B;
    --error: #EF4444;
    --bg: #f9fafb;
    --surface: #ffffff;
    --text: #111827;
    --muted: #6b7280;
    --ring: rgba(37, 99, 235, 0.5);
    --shadow: 0 10px 15px -3px rgba(17, 24, 39, 0.08), 0 4px 6px -4px rgba(17, 24, 39, 0.06);
    --radius: 14px;
  }

  .page {
    display: grid;
    grid-template-columns: 1fr;
    gap: 24px;
    width: 100%;
    max-width: 1100px;
    padding: 24px;
    margin-inline: auto;
  }

  @media (min-width: 960px) {
    .page {
      grid-template-columns: 3fr 2fr;
      align-items: start;
    }
  }

  .card {
    background: var(--surface);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    padding: 20px;
    border: 1px solid rgba(17, 24, 39, 0.06);
  }

  .header {
    margin-bottom: 16px;
  }

  h1 {
    margin: 0 0 6px 0;
    font-size: 1.6rem;
    color: var(--text);
    letter-spacing: 0.2px;
  }

  .status {
    margin: 0;
    color: var(--muted);
    font-weight: 500;
  }

  .actions {
    display: flex;
    justify-content: center;
    margin-top: 16px;
  }

  .reset {
    background: linear-gradient(180deg, #ffffff, #f3f4f6);
    border: 1px solid #e5e7eb;
    color: var(--text);
    padding: 10px 16px;
    border-radius: 10px;
    cursor: pointer;
    transition: transform 120ms ease, box-shadow 160ms ease, background 160ms ease, border-color 160ms ease;
    box-shadow: 0 1px 0 rgba(17,24,39,0.04);
  }
  .reset:hover {
    transform: translateY(-1px);
    box-shadow: 0 8px 14px -6px rgba(17,24,39,0.15);
    border-color: #d1d5db;
  }
  .reset:focus-visible {
    outline: 2px solid transparent;
    box-shadow: 0 0 0 4px var(--ring);
  }
  .reset:active {
    transform: translateY(0);
  }

  .history {
    overflow: hidden;
  }

  .history-header {
    display: flex;
    align-items: baseline;
    justify-content: space-between;
    margin-bottom: 8px;
  }

  .history h2 {
    font-size: 1.1rem;
    margin: 0;
    color: var(--text);
  }

  .history small {
    color: var(--muted);
  }

  .history ol {
    list-style: none;
    padding: 0;
    margin: 0;
    max-height: 420px;
    overflow: auto;
  }

  .history li + li {
    margin-top: 8px;
  }

  .history button {
    width: 100%;
    text-align: left;
    padding: 10px 12px;
    border-radius: 10px;
    border: 1px solid #e5e7eb;
    background: #fff;
    color: var(--text);
    cursor: pointer;
    transition: background 150ms ease, transform 120ms ease, box-shadow 160ms ease, border-color 160ms ease;
  }

  .history button:hover {
    background: #f9fafb;
    transform: translateY(-1px);
    box-shadow: 0 8px 14px -6px rgba(17,24,39,0.12);
  }

  .history button.selected {
    border-color: var(--primary);
    background: linear-gradient(180deg, rgba(37,99,235,0.06), rgba(255,255,255,1));
  }

  .history button:focus-visible {
    outline: 2px solid transparent;
    box-shadow: 0 0 0 4px var(--ring);
  }
</style>
