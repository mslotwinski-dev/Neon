# ⚙️ Neon

<p align="center">
  <img src="public/readme_icon.png" alt="Neon project icon" width="160" />
</p>

**Neon** to projekt platformy do rozproszonego uruchamiania obliczeń naukowych i zadań wsadowych.
Celem jest dostarczenie lekkiego, niezawodnego i obserwowalnego środowiska do orkiestracji pracy wielu węzłów obliczeniowych.

---

## Spis treści

- [O projekcie](#o-projekcie)
- [Kluczowe założenia](#kluczowe-założenia)
- [Architektura wysokiego poziomu](#architektura-wysokiego-poziomu)
- [Status repozytorium](#status-repozytorium)
- [Kierunek rozwoju](#kierunek-rozwoju)
- [Zastosowania](#zastosowania)
- [Jak śledzić postęp](#jak-śledzić-postęp)
- [Licencja](#licencja)

---

## O projekcie

Neon jest projektowany jako warstwa orkiestracji dla zadań obliczeniowych, w której:

- zadania są przyjmowane centralnie,
- planowane i rozdzielane na węzły robocze,
- monitorowane w czasie rzeczywistym,
- odporne na awarie pojedynczych komponentów.

To repozytorium przedstawia kierunek projektu i jego założenia architektoniczne.

---

## Kluczowe założenia

- **Niezawodność** — brak „cichej” utraty zadań, jawne raportowanie błędów.
- **Skalowalność** — możliwość rozszerzania klastra o kolejne węzły.
- **Obserwowalność** — metryki, logi i czytelny wgląd w stan systemu.
- **Bezpieczeństwo** — uwierzytelnianie, autoryzacja i kontrola komunikacji między usługami.
- **Przewidywalność wykonania** — limity zasobów, timeouty, retry i spójny cykl życia jobów.

---

## Architektura wysokiego poziomu

Neon zakłada dwa główne komponenty:

1. **Control Plane (Master)**
   - przyjmowanie i kolejkowanie zadań,
   - planowanie wykonania,
   - śledzenie stanu klastra i jobów.

2. **Worker Nodes**
   - wykonywanie zadań,
   - raportowanie wykorzystania zasobów,
   - przekazywanie logów i wyników.

Komunikacja między komponentami ma być zoptymalizowana pod wydajność i niezawodność.

---

## Status repozytorium

Aktualnie repozytorium znajduje się na etapie **wczesnego fundamentu projektu**
i zawiera głównie dokumentację koncepcji oraz zasoby identyfikacji wizualnej.

Nie jest to jeszcze gotowy produkt ani pełna implementacja runtime.

---

## Kierunek rozwoju

Najbliższe priorytety:

- uruchomienie podstawowych serwisów `master` i `worker`,
- wdrożenie minimalnego systemu kolejkowania i schedulera,
- dodanie pierwszego referencyjnego typu joba obliczeniowego,
- rozszerzenie telemetrii (metryki + logi + status wykonania).

---

## Zastosowania

Neon jest projektowany z myślą o zadaniach takich jak:

- symulacje Monte Carlo,
- wsadowe obliczenia numeryczne,
- równoległe przetwarzanie danych pomiarowych,
- pipeline’y analityczne wymagające kontroli wykonania i odporności na błędy.

---

## Jak śledzić postęp

Najlepszym źródłem informacji o postępie prac są:

- historia commitów,
- opisy pull requestów,
- kolejne aktualizacje README.

---

## Licencja

Brak zdefiniowanej licencji w repozytorium na ten moment.
W przypadku publikacji produkcyjnej zostanie dodana jawna licencja projektu.
