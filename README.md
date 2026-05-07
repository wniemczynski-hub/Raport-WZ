# Dashboard magazynowy M1

Wewnętrzne narzędzie do analizy raportów dokumentów magazynowych z systemu WMS (eksport `QWH_DOCUMENTS_F*.xlsx`).

## Jak to działa

Aplikacja jest zwykłą stroną HTML — cała logika wykonuje się w przeglądarce użytkownika. **Plik Excel z danymi nigdy nie jest wysyłany na żaden serwer** — jest parsowany lokalnie. Hosting (GitHub Pages) udostępnia tylko sam kod aplikacji, bez danych.

## Korzystanie

1. Otwórz URL aplikacji w przeglądarce.
2. Kliknij strefę „Wgraj nowy plik raportu" lub przeciągnij na nią plik `QWH_DOCUMENTS_F*.xlsx` wyeksportowany z systemu WMS.
3. Dashboard automatycznie:
   - wykryje nagłówki w arkuszu „Lista dokumentów magazynowych",
   - zastosuje reguły wykluczeń (patrz niżej),
   - zagreguje dane per dzień, typ operacji, opis,
   - przeliczy kafelki, wykresy i breakdown.

### Funkcje

- **Kafelki podsumowujące** — łączna liczba dokumentów, pozycji, wysyłek WZ, średnia dzienna.
- **Wykres dzienny** — stosowany słupkowy z podziałem na typ operacji (Wysyłka / Dostawa / Inwentaryzacja). Suma dnia widoczna na dole każdego słupka.
- **Rozbicie wg opisu** — paski poziome z udziałem każdego typu operacji (Wydanie zewnętrzne, Zwrot ECOM, Dostawa przychód zewnętrzny itd.), posortowane od największej wartości.
- **Średnia dzienna wg dnia tygodnia** — uśrednienie aktywności dla pn/wt/śr/cz/pt/sb/nd. Sensowne dopiero przy raporcie z dłuższego okresu (np. miesiąca).
- **Filtrowanie** — przełącznik metryki (dokumenty/pozycje) oraz typu operacji wpływa na wszystkie wizualizacje jednocześnie.
- **Eksport do Excel** — przycisk „↓ Excel" generuje plik `.xlsx` z trzema arkuszami (liczba dokumentów, suma pozycji, lista wykluczeń) odzwierciedlający aktualnie załadowane dane.

## Reguły wykluczeń

Następujące rekordy są pomijane przy agregacji (uznane za techniczne / nieinformacyjne):

| Typ operacji | Opis |
|---|---|
| Inwentaryzacja | Status jakości QS+ |
| Inwentaryzacja | Status jakości QS- |
| Przesunięcie mmag | (cała kategoria) |
| Wysyłka | Montaż |
| Dostawa | Dostawa wewnętrzna |
| Dostawa | Montaż |

Reguły są zaszyte w kodzie (sekcja `isExcluded` w pliku HTML). W razie potrzeby zmiany — edytuj funkcję `isExcluded` w `index.html`.

## Wymagania pliku wejściowego

Plik wyeksportowany z systemu WMS jako spread/xlsx, arkusz **„Lista dokumentów magazynowych"** (pierwszy arkusz jest brany jako fallback). Wymagane kolumny w wierszu nagłówków:

- `Nr dokumentu`
- `Typ operacji`
- `Opis`
- `Data wystawienia`
- `Ilość pozycji`

Dashboard sam znajdzie wiersz nagłówków (skanuje pierwsze 20 wierszy w poszukiwaniu „Typ operacji" i „Opis"), więc układ z metadanymi nad tabelą (jak w standardowym eksporcie WMS) działa od razu.

## Stack

- HTML / CSS / JS — bez frameworka, jeden plik
- [Chart.js 4.4.1](https://www.chartjs.org/) — wykresy
- [SheetJS 0.18.5](https://sheetjs.com/) — parsowanie i zapis plików `.xlsx`
- Oba załadowane z CDN (cdnjs / jsdelivr) — do pierwszego otwarcia wymagany internet, potem cache.

## Bezpieczeństwo i prywatność

- **Repo jest publiczne** — kod HTML jest widoczny dla każdego, kto trafi na repozytorium. Kod nie zawiera żadnych danych firmowych ani konfiguracji wrażliwej.
- **Dane firmowe (plik Excel) nigdy nie opuszczają komputera użytkownika.** Aplikacja działa w 100% po stronie klienta — żaden serwer, łącznie z GitHub Pages, nie widzi zawartości wgrywanego pliku.
- **URL aplikacji nie jest jawny** — udostępniany tylko zaufanym osobom prywatnym kanałem. Nie wpisuj go nigdzie publicznie i nie indeksuj.
- W razie podejrzenia, że URL wyciekł — zmień nazwę repo na inną losową w Settings → General → Rename. Stary URL przestanie działać.

## Aktualizacje

Aby zaktualizować dashboard:

1. Wejdź do repo na GitHub.
2. Kliknij plik `index.html` → ikonę ołówka (Edit).
3. Wklej nową zawartość lub edytuj bezpośrednio.
4. **Commit changes** na dole strony.
5. Po 1-2 minutach nowa wersja jest dostępna pod tym samym URL-em.

Alternatywnie: usuń plik (Delete file) i wgraj nową wersję jako nowy upload.
