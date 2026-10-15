# Anonimizator

**Jednoplikowa aplikacja webowa do anonimizacji polskich dokumentów — w całości w przeglądarce.**
*Single-file, browser-only anonymizer for Polish documents (PESEL, NIP, IBAN, names, addresses…).*

Anonimizator wykrywa w dokumentach polskie dane wrażliwe i zastępuje je etykietami,
danymi fikcyjnymi albo zaczernieniem — z zachowaniem struktury i formatowania pliku.
Ma też funkcję odwrotną: przywrócenie oryginalnych danych na podstawie klucza sesji,
przydatną po przepuszczeniu zanonimizowanego tekstu przez model AI.

Dokument nigdy nie opuszcza urządzenia: nie ma backendu ani zapytań sieciowych z treścią.

## Uruchomienie

Brak instalacji, serwera i kroku budowania.

1. Pobierz `anonimizator.html`.
2. Otwórz go w przeglądarce (dwuklik) albo wystaw na dowolnym serwerze statycznym (np. GitHub Pages).

Wymagania: nowoczesna przeglądarka (Chrome, Edge, Firefox; ES2017+, Blob, DOMParser, TreeWalker).

**Sieć:** przetwarzanie danych jest lokalne i offline. Połączenie jest potrzebne tylko do pobrania
kodu bibliotek z cdnjs i fontów z Google Fonts przy otwarciu strony (nigdy danych):

| Biblioteka | Wersja | Do czego |
|---|---|---|
| JSZip | 3.10.1 | odczyt/zapis ZIP (DOCX/XLSX/PPTX/ODF) |
| SheetJS (xlsx) | 0.18.5 | konwersja starych plików .xls |
| pdf.js | 3.11.174 | ekstrakcja tekstu z PDF |

Do pracy w pełni offline te pliki i fonty można hostować lokalnie zamiast z CDN.

## Jak używać

**Ustawienia** (wspólne dla wszystkich zakładek):
- **Tryb:** Etykiety (`[OSOBA_1]` — spójne, odwracalne) / Dane fikcyjne (realistyczne zamienniki
  z poprawnymi sumami kontrolnymi) / Zaczernianie (`██████` — trwałe, nieodwracalne).
- **Kategorie danych** — klikalne chipsy; kolor kropki = kolor podświetlenia w podglądzie.
- **Własne frazy** — dodatkowe ciągi do maskowania (np. nazwa kodowa projektu).

**Zakładki:**
- **Pliki** — przeciągnij pliki; każdy dostaje kartę z licznikiem wykrytych danych, podglądem
  i przyciskiem pobrania; „Pobierz wszystko” pakuje wyniki do ZIP.
- **Tekst** — wklej tekst i obserwuj podświetlenia na żywo; przycisk przykładowego tekstu demo
  (dane w demo są wartościami przykładowymi, nie danymi realnych osób).
- **Przywróć dane** — wklej tekst z etykietami lub danymi fikcyjnymi z tej samej sesji;
  aplikacja podstawi oryginały na podstawie klucza sesji.

**Klucz sesji** — tabela par oryginał → zamiennik z eksportem do CSV (na wypadek zamknięcia karty).

## Obsługiwane formaty

| Format | Rozszerzenia | Obsługa |
|---|---|---|
| Word | .docx | pełna — edycja XML z zachowaniem formatowania |
| Excel | .xlsx, .xlsm, .xltx | pełna, w tym komórki liczbowe z numerami wrażliwymi; makra są usuwane |
| Excel (stary) | .xls | konwersja do .xlsx (SheetJS); formuły w zmienionych komórkach są usuwane |
| PowerPoint | .pptx | pełna — edycja XML |
| OpenDocument | .odt, .ods, .odp | pełna — edycja XML |
| PDF | .pdf | tylko warstwa tekstowa → wynik .txt; skany bez OCR sygnalizowane ostrzeżeniem |
| Tekstowe | .txt .csv .json .xml .md .rtf .log .vcf .ics | bezpośrednio; detekcja kodowania (UTF-8 → windows-1250) |
| .doc, .ppt, pliki Google | — | nieobsługiwane — komunikat z instrukcją konwersji |

## Wykrywane dane

17 kategorii z priorytetem rozstrzygającym nakładające się dopasowania: rachunek (IBAN/NRB, mod 97),
karta płatnicza (Luhn), PESEL (cyfra kontrolna + data), NIP, REGON (9 i 14 cyfr), KRS, dowód osobisty,
paszport, e-mail, telefon, adres, firma (forma prawna), osoba (słownik imion + odmiana), kod pocztowy,
data i kwota (domyślnie wyłączone), własne frazy.

Dane fikcyjne pochodzą z deterministycznego generatora (mulberry32) zasianego wartością źródłową:
ta sama wartość dostaje w sesji zawsze ten sam zamiennik, a fikcyjne numery mają poprawne sumy kontrolne.

## Bezpieczeństwo i RODO

- Cały proces (parsowanie → detekcja → podmiana → zapis) działa w pamięci karty przeglądarki.
- Zapis na dysk tylko po jawnej akcji użytkownika.
- Metadane autora w plikach Office/ODF są nadpisywane wartością „Anonim”.
- Klucz pseudonimizacji istnieje tylko w pamięci karty; zamknięcie strony go kasuje.
- **Wyeksportowany CSV z kluczem jest dokumentem wrażliwym** — pozwala odwrócić anonimizację
  i wymaga takiej samej ochrony jak dane osobowe.

## Ograniczenia

- PDF: tylko tekst, bez układu graficznego; skany wymagają wcześniejszego OCR.
- Detekcja imion i nazwisk opiera się na słowniku i heurystykach — możliwe pominięcia
  (imiona spoza słownika, obcokrajowcy) i nadmiarowe dopasowania.
- Narzędzie wspiera anonimizację, ale nie gwarantuje jej kompletności — sprawdź wynik przed udostępnieniem.
- Brak trwałego zapisu ustawień i klucza między sesjami (świadomy wybór).

## Licencja

MIT © Bartosz Radziszewski — zob. [LICENSE](LICENSE).
