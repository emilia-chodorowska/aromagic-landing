# Decyzje architektoniczne — Aromagic

Krótki log kluczowych decyzji. Format: `data: decyzja — dlaczego`.

---

## Architektura ogólna
- Mikroserwisy (backend, stats, pricing, team, share) zamiast monolitu — niezależne skalowanie i deploy, każdy serwis ma jasno określoną odpowiedzialność
- Keycloak SSO zamiast własnego auth — nie wymyślamy koła na nowo, gotowe JWT, RBAC, integracja z OAuth
- PostgreSQL + Liquibase — relacyjna baza z wersjonowanymi migracjami, elastyczność w schematach
- Redis (distributed cache) + Caffeine (local cache) — dwupoziomowy cache: Redis dla danych wspólnych, Caffeine dla hot path w ramach jednego serwisu
- Qdrant + Spring AI do RAG — wektorowa baza do inteligentnego wyszukiwania, integracja z OpenAI i Anthropic

## Frontend
- React 18 + Vite + MUI — szybki dev, bogaty ekosystem komponentów, bez potrzeby pisania UI od zera
- Context API zamiast Redux/Zustand — wystarczająca złożoność stanu, mniej boilerplate'u
- Recharts do wykresów — lekka biblioteka, dobra integracja z React
- Fioletowy motyw (#7E57C2) — spójny branding Aromagic

## Backend
- Wzorzec Controller → Service → Repository — czysta separacja warstw, łatwe testowanie
- Facade pattern z package-private klasami — publiczne API przez *Facade.java, reszta ukryta
- DTO zamiast encji na granicach — zero wycieków modelu bazodanowego do API

## Browser plugin
- Manifest v3 (Chrome/Edge/Brave), v2 (Firefox) — v3 wymagany przez Chrome, Firefox jeszcze nie wspiera w pełni
- DOM scraping portalu doTERRA — brak oficjalnego API, jedyny sposób na pobieranie danych
- TypeScript strict, zero `any` — plugin operuje na danych finansowych, bezpieczeństwo typów krytyczne

## Infrastruktura
- Porty: backend 8080, frontend 3000, stats 8085, team 8084, pricing 8082, share 8083, Keycloak 8090
- Resilience4j circuit breakers w komunikacji między serwisami — odporne na awarie jednego serwisu

---

## Bieżące decyzje

<!-- Nowe wpisy dodawaj tutaj, najnowsze na górze -->

- 2026-02-14 [Emilia]: Zamówienia internetowe (nie-LRP) wyróżnione kolorem niebieskim — szybkie odróżnienie od LRP na liście zamówień (ARO-15)
- 2026-02-14 [Emilia]: Ikonka drzewa struktury przy WA — szybki dostęp do drzewa bez opuszczania widoku (ARO-16)
