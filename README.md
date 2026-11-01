# Memory Cards

A flashcard study app: one card is visible at a time, you click it to flip between question and answer, and Prev/Next step through the stack. Cards persist in `localStorage`, and an overlay form lets you add your own.

## Overview

The app ships with a small starter deck of JavaScript basics questions so it's usable the moment you open it. Clicking the active card triggers a 3D flip (via CSS `transform`/`perspective`) to reveal the answer underneath. The nav bar shows "N / total" and steps between cards with a left/right slide transition. The "Add New Card" button slides in a form overlay for question/answer pairs, and "Clear Cards" wipes everything from storage and starts over.

## Why I Built It

This project was about combining `localStorage` persistence with DOM state that has to survive across page reloads. It's also a CSS-3D exercise (`preserve-3d`, `backface-visibility`, `rotateX`) driven entirely by a JS-toggled class, and a chance to practice defensive coding: the original navigation logic assumed at least one card always existed, which breaks the moment `localStorage` is genuinely empty.

## Features

- Displays a starter set of sample question/answer cards on first visit
- Click the active card to flip it and reveal the answer
- Prev/Next buttons cycle through the deck, clamped at both ends
- "Add New Card" overlay form saves new question/answer pairs to `localStorage`
- "Clear Cards" removes all saved cards and reloads to an empty state
- Safe navigation when there are no cards to show (buttons no-op instead of throwing)

## JavaScript Concepts

- **`localStorage` read/write with `JSON.stringify`/`JSON.parse`** to persist an array of card objects between sessions
- **DOM class toggling for state** — flipping (`show-answer`), positioning (`left`/`active`/`right`), and overlay visibility (`show`) are all driven by adding/removing classes rather than inline styles
- **Guard clauses** — the Prev/Next handlers now bail out early when the card list is empty, instead of indexing into an array that has nothing in it
- **Array methods** (`forEach`, `push`) for building the deck from stored data and appending new cards
- **Event listeners on dynamically created elements**, attached at creation time in `createCard` rather than delegated

## How It Works

On load, `getCardsData()` checks `localStorage` for a `cards` key; if nothing is there yet (a fresh browser), it seeds storage with a small built-in sample deck before anything renders, so the app never starts on a blank screen. `createCards()` then builds one `.card` div per entry and tracks them in the `cardsEl` array, marking the first as `.active`. Clicking a card toggles `.show-answer`, which CSS turns into a 3D flip. The Prev/Next buttons swap the outgoing card's class for a slide-out position, move `currentActiveCard` within bounds, and mark the new card `.active`; both handlers now check `cardsEl.length` first so clicking them with zero cards does nothing instead of throwing. Adding a card via the overlay form pushes the new entry into `cardsData`, re-renders, and saves to `localStorage` (which also triggers a reload to keep the DOM and storage in sync). Clearing wipes `localStorage` and reloads to an empty deck.

## How to Run

Open `index.html` in a browser, or serve the folder with a static file server. No build tools or external JS dependencies (Font Awesome is loaded from a CDN for icons).

## Technologies

- HTML5, CSS3 (3D transforms, transitions)
- Vanilla JavaScript
- Browser `localStorage` API
- Font Awesome (icons via CDN)

## What I Learned

The empty-state crash was the useful lesson here: the code worked fine in every manual test I ran because I always had leftover cards in storage from previous testing, so the bug only surfaces for someone opening the app for the very first time with a clean `localStorage`. It's a good reminder to explicitly test the "zero items" case for anything backed by persisted state, not just the cases you happen to hit during development.

## Future Improvements

- Editing or deleting a single existing card, rather than only adding new ones or clearing everything
- Keyboard navigation (arrow keys for Prev/Next, space/enter to flip)
- Shuffling the deck or tracking which cards you've already reviewed
