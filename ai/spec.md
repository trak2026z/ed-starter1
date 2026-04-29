# ed-starter1 — spec / handoff

## Cel bieżącej pracy

Dodać sortowanie po kolumnach w publicznym widoku FIDS `FlightBoard` dla czasu odlotu, terminala i statusu. Sortowanie ma działać po stronie klienta, po zastosowaniu istniejących filtrów, bez zmian w API i bez modyfikowania danych lotów.

## Aktualny stan

Sortowanie zostało zaimplementowane. Nagłówki `Time`, `Term.` i `Status` są klikalne i przełączają stan w cyklu `asc -> desc -> off`. Domyślny widok pozostaje niesortowany, czyli zachowuje kolejność danych otrzymanych z `initialFlights`. Worktree zawiera też zmiany config/docs niezwiązane z tą implementacją.

## Decyzje techniczne

- Stan sortowania trzymany jest w Zustand razem z filtrami i listą lotów.
- Widoczna lista lotów jest wyliczana w selektorze `selectVisibleFlights`: najpierw filtrowanie, potem stabilne sortowanie kopii wyników.
- Kliknięcie innej kolumny zaczyna sortowanie od kierunku rosnącego.
- Kolejność statusów bazuje na `ALL_STATUSES`: `On Time`, `Boarding`, `Departed`, `Delayed`, `Cancelled`; kierunek malejący odwraca tę kolejność.
- Kolejność terminali bazuje na `ALL_TERMINALS`.
- Sortowanie po czasie używa wartości `departureTime` w formacie `HH:MM`.
- Dla kompatybilności pozostawiono alias `selectFilteredFlights = selectVisibleFlights`.

## Zmienione pliki

- `store/flightsStore.ts` — dodany typ i stan sortowania, akcja `toggleSort`, porównywanie lotów i selektor `selectVisibleFlights`.
- `components/fids/FlightBoard.tsx` — nagłówki `Time`, `Term.` i `Status` zamienione na przyciski sortowania z wizualnym wskaźnikiem kierunku i `aria-sort`.
- `ai/spec.md` — aktualny handoff/spec tej pracy.
- Poza zakresem sortowania w worktree są też: `.codex/config.toml`, `AGENTS.md`, `next-env.d.ts`, `.codex/config-kopia.toml`, `.codex/hooks/`, `app/api/flights/stats/`.

## Testy / quality gates

- `npm run typecheck` — passed.
- `npm run lint` — passed z jednym istniejącym, niezwiązanym ostrzeżeniem w `app/admin/page.tsx` dotyczącym użycia `<a>` zamiast `next/link`.
- `npm run dev` — serwer uruchomiony na `http://localhost:3000`.
- `curl -I http://localhost:3000` — returned `200 OK`.

## Ryzyka

- Nie wykonano pełnego testu przeglądarkowego kliknięć w UI; potwierdzono kompilację, lint i odpowiedź serwera.
- `aria-sort` jest ustawione na elementach nagłówków opakowujących przyciski, żeby uniknąć ostrzeżenia lint dla roli `button`.
- `next-env.d.ts` zmienił ścieżkę importu po komendach Next.js; to wygląda na zmianę wygenerowaną przez środowisko, nie część funkcjonalna sortowania.
- Zmiany config/docs w worktree mogą wymagać osobnej decyzji przed commitem; nie były częścią implementacji sortowania.

## Następny krok

Manualnie sprawdzić w przeglądarce `http://localhost:3000`, że kliknięcia `Time`, `Term.` i `Status` przechodzą przez `asc -> desc -> off` oraz że sortowanie działa poprawnie razem z filtrami. Przed commitem rozdzielić zmiany sortowania od niezwiązanych zmian config/docs.
