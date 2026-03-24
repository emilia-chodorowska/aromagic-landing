# PRD — Baza Wiedzy Aromagic

## 1. Cel produktu

Baza Wiedzy to moduł Aromagic służący do organizacji i udostępniania materiałów edukacyjnych doTERRA (grafiki, filmy YT, linki, notatki rich text) członkom zespołu. Interfejs inspirowany MyMind — wizualny masonry layout z AI-powered tagowaniem i opisami.

## 2. Użytkownicy i role

| Rola | Opis |
|------|------|
| **Admin** | Lider zespołu doTERRA. Pełny CRUD: dodaje, edytuje, usuwa materiały. Zarządza kategoriami i folderami. Może działać przez UI lub Claude Code CLI (MCP). |
| **Czytelnik** | Członek zespołu. Przegląda, wyszukuje, kopiuje i udostępnia materiały. Nie może dodawać/edytować/usuwać. |

### Matryca uprawnień

| Funkcja | Admin | Czytelnik |
|---------|-------|-----------|
| Przeglądanie materiałów | tak | tak |
| Wyszukiwarka | tak | tak |
| Filtrowanie po typie | tak | tak |
| TLDR, Mind Tags (odczyt) | tak | tak |
| Kopiuj / Udostępnij / Pobierz | tak | tak |
| Dodawanie materiałów (FAB) | tak | nie |
| Edytuj / Usuń | tak | nie |
| Dodawanie tagów | tak | nie |
| Tworzenie/edycja notatek | tak | nie |
| Multi-select + udostępnij zbiorczo | tak | tak |
| Multi-select + usuń zbiorczo | tak | nie |
| Zarządzanie kategoriami/folderami | tak | nie |

## 3. Architektura nawigacji

### Struktura folderów
- **Nieograniczone zagnieżdżanie** — admin tworzy dowolną hierarchię folderów (kategorie → tematy → podtematy → ...)
- Nie ma sztywnego limitu poziomów
- Admin decyduje ile kategorii i folderów istnieje, jakie mają nazwy, emoji i kolory

### Ekran startowy — Kategorie
- Grid kategorii (dynamiczny — tyle kart ile admin stworzył)
- Każda kategoria: pastelowy kolor tła, emoji, nazwa, liczba tematów i materiałów
- Admin przypisuje kolor i emoji przy tworzeniu kategorii

### Ekran folderu — Tematy (podfoldery)
- Lista podfolderów z ikonami
- Każdy temat: ikona + pastelowe tło, nazwa, meta z licznikami (np. "4 grafiki · 1 link · 1 notatka")
- Kliknięcie → wejście głębiej (kolejny folder lub galeria materiałów)

### Ekran materiałów — Galeria (masonry grid)
- Widok masonry 2-kolumnowy (styl MyMind)
- Karty o różnych wysokościach (aspect-ratio: 1:1, 3:4, 4:3 — zależnie od typu i treści)
- AI-generated podpis pod kartą (1 wiersz, text-overflow: ellipsis)
- Filtrowanie po typie materiału (taby: Wszystko / Grafiki / Linki / Filmy / Notatki)
- Nawigacja back z animacjami

## 4. Typy materiałów

### 4.1 Grafika
- **Źródło**: upload ręczny (UI), Claude Code CLI (MCP), lub sync z Discorda
- **Thumbnail w galerii**: obrazek z border-radius 8px, różne proporcje
- **Widok szczegółowy**: pełny obrazek + TLDR + Mind Tags + akcje
- **Akcje czytelnika**: Kopiuj, Udostępnij, Pobierz
- **Akcje admina**: + Usuń

### 4.2 Film YouTube
- **Źródło**: wklejenie linku YT (ręcznie, UI lub CLI)
- **Thumbnail w galerii**: automatyczny z `img.youtube.com/vi/{id}/mqdefault.jpg`
- Badge typu (ikona filmu) w rogu karty
- Kliknięcie → osadzony player YT lub redirect

### 4.3 Link zewnętrzny
- **Źródło**: wklejenie URL (ręcznie, UI lub CLI)
- Typy: Instagram, Google Photos, strony www, NotebookLM, doTERRA.com itp.
- **Thumbnail w galerii**: pastelowy placeholder z ikoną + badge linku
- Kliknięcie → otwarcie URL w nowej karcie

### 4.4 Notatka (rich text)
- **Źródło**: tworzenie przez admina w edytorze (UI) lub przez Claude Code CLI (MCP)
- **Wygląd w galerii** (styl MyMind):
  - Biała karta z delikatnym obramowaniem i cieniem
  - Tytuł (bold) + treść rich text bezpośrednio na karcie
  - Maksymalna wysokość karty: proporcje ok. 3:4 (tekst obcinany overflow hidden)
  - AI-generated podpis pod kartą (1 wiersz, jak inne typy)
- **Widok szczegółowy**:
  - Header z przyciskiem back + tytuł "Notatka"
  - Pełna treść rich text: nagłówki, listy, pogrubienia, kursywa, highlight bloki
  - TLDR (AI-generated podsumowanie)
  - Mind Tags (AI-generated + ręczne admina)
  - Akcje czytelnika: Kopiuj, Udostępnij
  - Akcje admina: + Edytuj, Usuń
- **Edytor** (tylko admin):
  - Pole tytułu + pole treści
  - Toolbar formatowania: B, I, U, lista, nagłówek H2, link
  - Przyciski: Anuluj / Zapisz notatkę
- **Edycja**: notatki mogą być edytowane po stworzeniu (w przeciwieństwie do grafik/linków)

### 4.5 PDF
- **Źródło**: upload ręczny (UI lub CLI)
- Podgląd w galerii: ikona PDF + nazwa pliku
- Kliknięcie → podgląd/pobieranie

## 5. AI Features

### 5.1 AI-generated podpisy (Card Labels)
Każdy materiał otrzymuje automatyczny podpis generowany przez AI:
- **Grafiki**: opis tego co jest na obrazku (np. "Infografika przedstawiająca 5 podstawowych olejków")
- **Notatki**: skrócona wersja tytułu/treści
- **Linki**: tytuł strony lub opis zawartości
- **Filmy YT**: tytuł filmu

Widoczne pod kartą w galerii (1 wiersz, ellipsis). Nie wymagają ręcznego wpisywania.

### 5.2 AI-generated tagi (Mind Tags)
Automatyczne tagowanie materiałów na podstawie treści:
- **Grafiki**: obiekt, kolor, temat (np. "lawenda", "sen", "apteczka")
- **Notatki**: słowa kluczowe z treści
- **Linki**: kategoria źródła + temat
- **Filmy**: tematy z tytułu/opisu

Widoczne w widoku szczegółowym (sekcja "Mind Tags"). Tagi AI-suggested wyświetlane jaśniejszym kolorem. Admin może dodawać ręczne tagi (przycisk "+ Dodaj tag").

### 5.3 TLDR (AI Summary)
Krótkie podsumowanie materiału generowane przez AI:
- Widoczne w widoku szczegółowym (sekcja "TLDR")
- Dla grafik: opis zawartości obrazka
- Dla notatek: streszczenie treści
- Dla linków: podsumowanie strony

## 6. Wyszukiwarka
- Globalna wyszukiwarka na ekranie startowym
- Przeszukuje: tytuły, podpisy AI, tagi, treść notatek
- Wyniki: lista z thumbnail, nazwa, ścieżka (np. "Aromaterapia > Biofilm i bakterie")
- Kliknięcie wyniku → nawigacja do galerii lub materiału

## 7. Widok szczegółowy materiału (styl MyMind)
Ujednolicony dla wszystkich typów:
- Header z przyciskiem back + tytuł
- Treść (grafika / embed YT / treść notatki / link preview)
- Sekcja **TLDR** — AI-generated opis
- Sekcja **Mind Tags** — tagi (AI + ręczne)
- **Akcje na dole** (pills):
  - Czytelnik: Kopiuj, Udostępnij, Pobierz
  - Admin: + Edytuj (tylko notatki), Usuń
- "Dodano X dni temu"

## 8. Multi-select i udostępnianie zbiorcze
- W galerii materiałów użytkownik może **zaznaczyć wiele elementów** (long press lub tryb zaznaczania)
- Po zaznaczeniu pojawia się pasek akcji z liczbą zaznaczonych + opcje:
  - **Udostępnij** — wysyłka zaznaczonych materiałów (WhatsApp, link, kopiuj)
  - **Pobierz** — pobranie zaznaczonych grafik/PDF jako paczka
  - **Usuń** (tylko admin) — usunięcie zaznaczonych
- Działa na wszystkich typach materiałów (grafiki, filmy, linki, notatki)
- Umożliwia np. wybranie 3 grafik + 1 notatki z tematu "Kadzidłowiec" i wysłanie jednym udostępnieniem

## 9. Tryb admin

### Przez UI
- W produkcji: rola z Keycloak (nie toggle UI, toggle jest tylko w mockupie)
- Admin widzi:
  - **FAB** (przycisk +) z opcjami: Nowy folder, Dodaj grafikę/PDF, Dodaj link, Dodaj film YT, Nowa notatka
  - Przyciski **Edytuj/Usuń** w widoku szczegółowym
  - Przycisk **"+ Dodaj tag"** w Mind Tags
  - Zarządzanie folderami (tworzenie, zmiana nazwy, usuwanie, zmiana kolejności)

### Przez Claude Code CLI (MCP)
Pełny CRUD na materiałach i folderach:
- **Materiały**: dodawanie (grafiki, linki, filmy YT, notatki, PDF), edycja metadanych/treści, usuwanie
- **Foldery/kategorie**: tworzenie, zmiana nazwy, usuwanie, przenoszenie
- **Tagi**: dodawanie/usuwanie ręcznych tagów
- **Bulk operations**: masowe dodawanie materiałów (np. import z Discorda)
- Sync z Discorda (kanały mirror/) jako źródło treści

## 10. Źródła treści
- **Claude Code CLI / MCP** (główne): pełny CRUD, sync z Discorda, bulk import
- **UI** (pełne): ręczny upload grafik/PDF, wklejanie linków/filmów YT, tworzenie notatek w edytorze, zarządzanie folderami

## 11. Design

### Paleta kolorów
- Pastelowe tła kategorii (przypisywane przez admina): lavender, mint, sky, rose, peach, lemon i inne
- Akcent: purple `#7E57C2`, green `#4CAF50`
- Tekst: `#111827` (primary), `#6B7280` (secondary), `#9CA3AF` (muted)
- Tło: `#ffffff` (bg), `#F9FAFB` (soft)
- Border: `#E5E7EB`

### Typografia
- Font: Inter (Google Fonts)
- Podpisy pod kartami: 0.72rem, kolor secondary
- Notatki w galerii: tytuł 0.6rem bold, body 0.55rem
- Tagi: 0.75rem, pills z border-radius 20px

### Layout
- Border-radius: 8px (karty, thumbnaily)
- Masonry: 2 kolumny, gap 8px
- Content padding: 16px
- Scrollbary ukryte (natywny mobile look)
- Mobile-first: 393x852 (iPhone 17 Pro)

## 12. Wersja desktopowa
> **TODO** — do zaprojektowania. Wstępne założenia:
> - 3-4 kolumny masonry zamiast 2
> - Sidebar z kategoriami (zamiast nawigacji ekranowej)
> - Reszta interfejsu identyczna

## 13. Mockup
- **Live**: https://emilia-chodorowska.github.io/aromagic-landing/baza-wiedzy-mockup/
- **Plik**: `landing/baza-wiedzy-mockup.html`
- Toggle "czytelnik / twórca" u góry po prawej (tylko w mockupie, w produkcji rola z Keycloak)

## 14. Stack techniczny
- **Frontend**: React 18 + Vite + MUI (repo: aromagic-frontend)
- **Backend**: Spring Boot + PostgreSQL (repo: aromagic-backend, port 8080)
- **Auth**: Keycloak (role admin/czytelnik)
- **AI**: Qdrant + Spring AI (RAG) — generowanie TLDR, tagów, podpisów
- **CLI**: Claude Code CLI z MCP — pełny CRUD na materiałach i folderach
- **Sync**: MCP connectors (Discord mirror/ i inne źródła)

## 15. Funkcje do rozbudowy (backlog)
- **Notyfikacje** — powiadomienia dla czytelników o nowych materiałach (push/in-app). Np. "Dodano 3 nowe materiały w Aromaterapia > Kadzidłowiec"
- **Okładki kategorii** — admin może ustawić własną grafikę jako okładkę kategorii/folderu (zamiast pastelowego tła)
- **Toggle widoku podfolderów** — przełącznik lista/grid na ekranach z podfolderami. Lista (jak teraz) lub kwadratowe karty (jak na ekranie startowym)
- **Wersja desktopowa** — 3-4 kolumny masonry, sidebar z kategoriami
- **Ulubione / Zapisane** — czytelnik może oznaczać materiały jako ulubione i mieć do nich szybki dostęp
- **Historia przeglądania** — ostatnio oglądane materiały
- **Komentarze** — czytelnik może dodać komentarz do materiału (pytanie, feedback)
- **Statystyki** (admin) — ile osób otworzyło materiał, najpopularniejsze treści
- **Wersjonowanie notatek** — historia edycji notatek

## 16. Istniejący kod (do przebudowy)
Obecna implementacja w `aromagic-frontend` obsługuje tylko pliki (PDF/DOC/TXT) z folderami:
- `src/pages/KnowledgeBasePage.jsx` — strona główna (desktop, MUI)
- `src/components/document/FolderView.jsx` — widok folderów
- `src/components/document/UploadDialog.jsx` — dialog uploadu
- `src/components/document/CreateFolderDialog.jsx`
- `src/components/document/RenameFolderDialog.jsx`
- `src/components/document/MoveDocumentDialog.jsx`
- `src/services/documentService.js` — serwis REST

Wymaga przebudowy na nowy model z dynamicznymi kategoriami, wieloma typami materiałów, notatkami rich text i AI features.
