# Dokument wymagań produktu (PRD) - scorecard

## 1. Przegląd produktu
Aplikacja scorecard to webowa aplikacja PWA zaprojektowana w podejściu mobile-first do szybkiego i intuicyjnego zapisywania wyników gry w golfa. Produkt adresuje potrzeby graczy amatorów (rekreacyjnych i turniejowych) w zakresie przejrzystego interfejsu oraz minimalizacji czasu wprowadzania wyników. MVP obejmuje: prostą rejestrację/logowanie (email + hasło), profil gracza (HCP Index, domyślne pole, domyślne tee, domyślny format gry), rozpoczęcie rundy 18 dołków (start wyłącznie od dołka nr 1), wprowadzanie wyników hole-by-hole, cztery tryby obliczeń (Stroke Play Brutto/Netto, Stableford Brutto/Netto), historię gier z detalem i przełącznikiem widoku wyników.

Zakładamy start z gotową bazą 5 pól golfowych w systemie (wprowadzonych po stronie backendu z pliku CSV), z kompletnymi danymi: tee (CR, SR) oraz dołki (PAR, SI). Aplikacja nie przewiduje w MVP współdzielenia wyników, eksportu (PDF/DOCX/email) ani aplikacji mobilnych natywnych (wyłącznie PWA).

Cel biznesowy MVP: osiągnięcie retencji, w której 75% zarejestrowanych użytkowników zapisuje co najmniej 2 rundy w ciągu 30 dni od rejestracji.

## 2. Problem użytkownika
Obecne rozwiązania do zapisywania wyników golfa cierpią na nieczytelny interfejs i spowolnione wprowadzanie wyników. Gracze potrzebują narzędzia, które:
1) Działa świetnie na telefonie (mobile-first) i offline (PWA),
2) Pozwala ultra-szybko wprowadzać wynik po każdym dołku (hole-by-hole),
3) Domyślnie podpowiada wartości (PAR, domyślne pole/tee/format z profilu),
4) Przejrzyście pokazuje kluczowy kontekst (nr dołka, PAR, SI, bieżący wynik sumaryczny),
5) Umożliwia analizę po rundzie w różnych trybach (Stroke/Stableford, Brutto/Netto) jedną kontrolką select.

## 3. Wymagania funkcjonalne
3.1. Uwierzytelnianie i bezpieczeństwo
- Rejestracja konta: email + hasło (minimalna liczba pól, walidacja formatu email, siły hasła).
- Logowanie: email + hasło; sesje trwałe z wygodnym, ale bezpiecznym czasem życia.
- Wylogowanie: natychmiastowe unieważnienie sesji.
- Reset hasła: odzyskiwanie poprzez email (link jednorazowy, czasowo ograniczony).
- Ochrona danych: hasła hashowane; minimalne przechowywanie danych osobowych.

3.2. Profil gracza
- Pola profilu: HCP Index (liczba zmiennoprzecinkowa), domyślne pole, domyślne tee, domyślny format gry.
- Edycja: użytkownik może zaktualizować każde pole; walidacje (np. HCP w zakresie rozsądnym, np. 0–54).
- Domyślny format gry: używany jako domyślna prezentacja wyników zarówno podczas wprowadzania wyników (sumaryczny wynik na ekranie rundy) jak i w widoku szczegółów historii (domyślny wybór w select). Zmiana formatu na ekranie nie modyfikuje danych, a jedynie sposób prezentacji obliczeń.

3.3. Baza pól golfowych
- Minimalny zestaw: 5 pól w bazie na start, wprowadzone przez administratora z CSV (poza UI).
- Struktura: Pole (nazwa), Tee (nazwa/kolor, CR, SR, tee dla M/K), Dołki: 18 pozycji z PAR i SI.
- Walidacje: kompletność danych, spójność SI 1–18, sensowne CR/SR, PAR sumuje się do wartości kursu.

3.4. Rozpoczęcie rundy
- Start nowej rundy: wybór pola i tee (prefill z profilu).
- Ograniczenie MVP: start wyłącznie od dołka nr 1.
- Inicjalizacja: zapis roboczy rundy tworzony po potwierdzeniu; możliwość wznowienia przerwanej rundy.

3.5. Wprowadzanie wyników (core loop)
- Tryb hole-by-hole: dla bieżącego dołka domyślna wartość to PAR.
- Kontrolki: szybkie przyciski +/- oraz numeryczny keypad do ręcznego wpisu.
- Kontekst: zawsze widoczne nr dołka, PAR, SI, bieżący wynik sumaryczny w aktualnie wybranym formacie.
- Nawigacja: szybkie przejście do następnego/poprzedniego dołka; możliwość korekty wcześniejszych dołków.
- Autozapis i offline: stan rundy zapisywany lokalnie (PWA), synchronizacja po odzyskaniu sieci.

3.6. Obliczenia wyników i tryby gry
- Wspierane formaty: Stroke Play Brutto, Stroke Play Netto, Stableford Brutto, Stableford Netto.
- Decyzja MVP Netto: w MVP Netto bazuje na HCP Index z profilu (uproszczenie). Playing Handicap = zaokrąglony HCP Index.
- Alokacja uderzeń z HCP: 1 uderzenie przyznawane na dołkach z SI 1..PH; jeśli PH > 18, po 2 uderzenia na dołkach z SI 1..(PH-18), itd.
- Stroke Brutto: suma uderzeń bez korekt.
- Stroke Netto: suma uderzeń pomniejszona o alokowane uderzenia HCP.
- Stableford Brutto: punkty wg różnicy gross vs PAR (0 dla >= double bogey, 1 bogey, 2 par, 3 birdie, 4 eagle, 5 albatros, 6 condor).
- Stableford Netto: jak wyżej, lecz liczone względem netto na dołku (po alokacji HCP).
- Uwagi: Wersja przyszła może przejść na Playing Handicap z CR/SR wg formuły: PH = HCP Index * (Slope/113) + (CR – PAR kursu). W MVP pozostaje uproszczenie.

3.7. Historia i przeglądanie
- Lista rund: widok listy zakończonych (i ewentualnie przerwanych) rund użytkownika; sortowanie po dacie.
- Szczegóły rundy: pełna karta wyników z select do dynamicznego przełączania widoku między 4 trybami.
- Edycja po zakończeniu: brak edycji zakończonej rundy w MVP (minimalizacja złożoności i nadużyć). Korekty możliwe tylko w trakcie trwającej rundy.

3.8. PWA i wydajność
- Mobile-first UI, responsywne layouty, lekkie zasoby.
- Instalowalność jako PWA, tryb offline dla wprowadzania i przeglądania ostatnich danych.
- Szybkie interakcje: latencja akcji UI < 100 ms; przełączenia widoku wyników bez reloadu.

3.9. Analityka i pomiar metryk
- Zdarzenia: rejestracja, logowanie, rozpoczęcie rundy, zapis dołka, zakończenie rundy, powrót do aplikacji, zapis drugiej rundy w 30 dni.
- Atrybucje: identyfikacja użytkownika (anonimizowana), znacznik czasu zdarzeń, źródło ruchu (jeśli dostępne).

3.10. Role i administracja
- Użytkownik: pełna obsługa funkcji MVP.
- Administrator (poza UI): import CSV z danymi pól do bazy; brak panelu CMS w MVP.

3.11. Model danych (wysoki poziom)
- User (id, email, hash hasła, daty utworzenia/aktualizacji)
- Profile (userId, hcpIndex, defaultCourseId, defaultTeeId, defaultFormat)
- Course (id, name)
- Tee (id, courseId, name/color, cr, slope, gender)
- Hole (id, courseId, number 1–18, par, si)
- Round (id, userId, courseId, teeId, holes: 18, startedAt, finishedAt, status: in_progress/complete, rawScores[1..N])
- Obliczenia wyników są pochodne i wyświetlane dynamicznie; surowe dane to rawScores.

## 4. Granice produktu
4.1. W zakresie MVP
- Rejestracja/logowanie email+hasło, profil gracza z HCP i domyślnościami.
- Rozpoczęcie rundy: wybór pola/tee, długość 18, start z dołka 1.
- Wprowadzanie wyników hole-by-hole z domyślnym PAR, +/- i keypad.
- Obliczenia 4 trybów, Netto oparte na HCP Index (uproszczenie), alokacja wg SI.
- Historia rund i szczegóły z select do przełączania trybów.
- PWA: offline-first wprowadzanie i autozapis; synchronizacja po odzyskaniu sieci.
- Baza 5 pól wprowadzona CSV po stronie backendu (bez panelu CMS).

4.2. Poza zakresem MVP
- Eksport wyników (PDF, DOCX, email), współdzielenie z innymi użytkownikami.
- Start z innego dołka niż nr 1.
- Aplikacje mobilne natywne (iOS/Android), powiadomienia push opcjonalnie w późniejszej wersji.
- Panel administracyjny CMS do zarządzania polami.

4.3. Założenia i decyzje
- Decyzja Netto: MVP używa Playing Handicap = zaokrąglony HCP Index z profilu. Dane CR/SR przechowujemy i wykorzystamy w kolejnej wersji.
- Domyślny format gry z profilu: wpływa na domyślny sposób prezentacji sumy podczas rundy i domyślny wybór w szczegółach historii; można go lokalnie przełączyć w UI.

## 5. Historyjki użytkowników
US-001
Tytuł: Rejestracja konta
Opis: Jako nowy użytkownik chcę założyć konto podając email i hasło, aby móc zapisywać rundy.
Kryteria akceptacji:
- Gdy podam poprawny email i silne hasło, mogę utworzyć konto i zostać zalogowany.
- Gdy email jest zajęty, widzę komunikat o błędzie.
- Gdy format email jest nieprawidłowy, widzę walidację inline.

US-002
Tytuł: Logowanie
Opis: Jako zarejestrowany użytkownik chcę zalogować się emailem i hasłem, aby uzyskać dostęp do aplikacji.
Kryteria akceptacji:
- Gdy podam poprawne dane, zostaję zalogowany i przeniesiony do ekranu startowego.
- Gdy podam błędne dane, widzę jasny komunikat o błędzie.
- Sesja pozostaje aktywna do czasu wylogowania lub wygaśnięcia.

US-003
Tytuł: Wylogowanie
Opis: Jako użytkownik chcę się wylogować, aby zakończyć sesję na urządzeniu.
Kryteria akceptacji:
- Po wylogowaniu dostęp do widoków zabezpieczonych jest zablokowany.
- Powrót do ekranu logowania lub landing page.

US-004
Tytuł: Reset hasła
Opis: Jako użytkownik chcę zresetować hasło przez email, aby odzyskać dostęp.
Kryteria akceptacji:
- Mogę poprosić o link resetu hasła i otrzymuję go na email.
- Link jest jednorazowy i ważny czasowo; po kliknięciu mogę ustawić nowe hasło.

US-005
Tytuł: Podgląd profilu
Opis: Jako użytkownik chcę zobaczyć mój profil, aby sprawdzić aktualne ustawienia.
Kryteria akceptacji:
- Widzę HCP Index, domyślne pole, domyślne tee i domyślny format.
- Dane są zgodne ze stanem w bazie.

US-006
Tytuł: Edycja HCP Index
Opis: Jako użytkownik chcę ustawić/zmienić mój HCP Index, aby obliczenia Netto były spersonalizowane.
Kryteria akceptacji:
- Mogę wprowadzić wartość w dopuszczalnym zakresie (np. 0–54) z walidacją.
- Zmiana zapisuje się i wpływa na obliczenia kolejnych rund.

US-007
Tytuł: Ustawienie domyślnego pola
Opis: Jako użytkownik chcę ustawić domyślne pole, aby szybciej startować nowe rundy.
Kryteria akceptacji:
- Mogę wybrać pole z listy dostępnych pól.
- Nowe rundy proponują to pole domyślnie.

US-008
Tytuł: Ustawienie domyślnego tee
Opis: Jako użytkownik chcę ustawić domyślne tee, aby ograniczyć wybory przed startem rundy.
Kryteria akceptacji:
- Mogę wybrać tee właściwe dla pola.
- Nowe rundy proponują to tee domyślnie.

US-009
Tytuł: Ustawienie domyślnego formatu gry
Opis: Jako użytkownik chcę ustawić domyślny format, aby widzieć sumę wyników w preferowany sposób.
Kryteria akceptacji:
- Mogę wybrać spośród Stroke B/Stroke N/Stableford B/Stableford N.
- Ten format jest domyślnie użyty w ekranie rundy (sumaryczny wynik) i w szczegółach historii (select).

US-010
Tytuł: Rozpoczęcie rundy z domyślnościami
Opis: Jako użytkownik chcę szybko rozpocząć rundę z domyślnym polem i tee.
Kryteria akceptacji:
- Ekran startu jest wstępnie wypełniony moimi domyślnościami.
- Mogę potwierdzić i rozpocząć rundę w 2 kliknięciach.

US-011
Tytuł: Wybór pola i tee przed startem
Opis: Jako użytkownik chcę zmienić pole i/lub tee przed startem rundy.
Kryteria akceptacji:
- Mogę zmienić pole, a następnie tee zostaje przefiltrowane do dostępnych dla pola.
- Walidacje uniemożliwiają wybór niekompatybilnej kombinacji.

US-013
Tytuł: Wprowadzanie wyniku przyciskami +/-
Opis: Jako użytkownik chcę szybko zwiększać/zmniejszać wynik dla bieżącego dołka.
Kryteria akceptacji:
- Kliknięcie +/− modyfikuje wynik o 1 i natychmiast aktualizuje sumę.
- Dolne/upper bound logiczne (np. nie mniej niż 1 uderzenie).

US-014
Tytuł: Wprowadzanie wyniku z klawiatury numerycznej
Opis: Jako użytkownik chcę wpisać wynik ręcznie.
Kryteria akceptacji:
- Mogę otworzyć keypad i wprowadzić liczbę; wynik zostaje zapisany dla dołka.
- Walidacja nie dopuszcza nieprawidłowych wartości (np. 0, znaki).

US-015
Tytuł: Domyślny wynik = PAR
Opis: Jako użytkownik chcę, aby wynik był domyślnie ustawiony na PAR dołka.
Kryteria akceptacji:
- Po wejściu na dołek wynik startuje od PAR (widoczny na suwaku/wyświetlaczu).
- Zmiana przez +/- lub keypad aktualizuje wartość.

US-016
Tytuł: Widoczne PAR, SI, numer dołka i suma
Opis: Jako użytkownik chcę mieć kontekst podczas wpisu.
Kryteria akceptacji:
- Ekran dołka wyświetla nr dołka, PAR, SI i sumę w aktualnie wybranym formacie.
- Zmiany na bieżącym dołku natychmiast aktualizują sumę.

US-017
Tytuł: Nawigacja między dołkami
Opis: Jako użytkownik chcę szybko przechodzić do poprzedniego/następnego dołka.
Kryteria akceptacji:
- Gesty/przyciski umożliwiają zmianę dołka bez powrotu do listy.

US-018
Tytuł: Korekta poprzedniego dołka
Opis: Jako użytkownik chcę poprawić wynik na wcześniejszym dołku.
Kryteria akceptacji:
- Mogę wrócić do dowolnego zakończonego dołka w trwającej rundzie i zaktualizować wynik.
- Suma przelicza się natychmiast.

US-019
Tytuł: Autozapis i tryb offline
Opis: Jako użytkownik chcę, aby moje wpisy zapisywały się automatycznie, również offline.
Kryteria akceptacji:
- Przy utracie sieci mogę kontynuować; dane synchronizują się po powrocie online.
- Po odświeżeniu strony nie tracę bieżącej rundy.

US-020
Tytuł: Wznowienie przerwanej rundy
Opis: Jako użytkownik chcę wznowić rundę po powrocie do aplikacji.
Kryteria akceptacji:
- Po otwarciu aplikacji widzę propozycję wznowienia ostatniej rundy in_progress.
- Po wznowieniu trafiam na ostatnio edytowany dołek.

US-021
Tytuł: Zakończenie rundy
Opis: Jako użytkownik chcę zakończyć rundę i zapisać wynik końcowy.
Kryteria akceptacji:
- Po zakończeniu status rundy zmienia się na complete i pojawia się w historii.
- Zakończonej rundy nie mogę edytować.

US-022
Tytuł: Lista historii rund
Opis: Jako użytkownik chcę zobaczyć listę moich rund.
Kryteria akceptacji:
- Lista wyświetla datę, pole, tee, długość 18 oraz skrót wyniku.
- Sortowanie malejąco po dacie; paginacja lub lazy-load przy większej liczbie.

US-023
Tytuł: Filtrowanie/sortowanie historii (opcjonalne w MVP minimalnym)
Opis: Jako użytkownik chcę szybko znaleźć rundę.
Kryteria akceptacji:
- Mogę filtrować po polu/tee lub zakresie dat (jeśli dostępne w MVP), lub przynajmniej sortowanie po dacie.

US-024
Tytuł: Szczegóły rundy z wyborem formatu
Opis: Jako użytkownik chcę przełączać widok karty wyników między formatami.
Kryteria akceptacji:
- Element select pozwala wybrać: Stroke B, Stroke N, STB B, STB N.
- Przełączenie natychmiast przelicza i aktualizuje widok bez przeładowania.

US-025
Tytuł: Poprawne obliczenia Stroke/Stableford Brutto/Netto
Opis: Jako użytkownik oczekuję poprawnych obliczeń wyników.
Kryteria akceptacji:
- Stroke Brutto = suma rawScores; Stroke Netto = rawScores − alokacje HCP.
- Stableford Brutto/Netto liczone wg standardu.

US-027
Tytuł: Import pól golfowych przez CSV (admin)
Opis: Jako administrator chcę dodać nowe pole z CSV.
Kryteria akceptacji:
- Plik CSV zdefiniowanego formatu importuje pole, tee (CR, SR) i dołki (PAR, SI).
- Błędy importu są raportowane (log/raport) i nie psują istniejących danych.

US-028
Tytuł: Walidacja danych pól
Opis: Jako administrator chcę mieć pewność, że dane pól są poprawne.
Kryteria akceptacji:
- SI zawiera wszystkie wartości 1–18 bez duplikatów.
- CR, SR i PAR mieszczą się w akceptowalnych zakresach.

US-029
Tytuł: Zabezpieczenie sesji
Opis: Jako użytkownik oczekuję bezpiecznej sesji.
Kryteria akceptacji:
- Po wygaśnięciu sesji wymagane jest ponowne logowanie.
- Endpoints zabezpieczone wymagają tokenu/autoryzacji.

US-030
Tytuł: Analityka retencji
Opis: Jako właściciel produktu chcę mierzyć retencję 30-dniową.
Kryteria akceptacji:
- Zdarzenia rejestracji i zapisów rund są rejestrowane z identyfikatorem użytkownika.
- Można policzyć odsetek użytkowników z co najmniej 2 rundami w 30 dni.

US-031
Tytuł: Ograniczenie startu od dołka 1
Opis: Jako użytkownik mam zawsze start od dołka 1 w MVP.
Kryteria akceptacji:
- UI nie pozwala wybrać innego dołka startowego.
- Próba wejścia bezpośrednio na inny dołek jest przekierowana na dołek 1.

US-033
Tytuł: Zachowanie przy utracie połączenia
Opis: Jako użytkownik chcę kontynuować w trybie offline.
Kryteria akceptacji:
- Przy utracie sieci interfejs działa, dane zapisują się lokalnie.
- Po powrocie online następuje synchronizacja bez konfliktów.

US-034
Tytuł: Dostępność interfejsu (a11y)
Opis: Jako użytkownik chcę wygodnie korzystać z aplikacji na urządzeniu mobilnym.
Kryteria akceptacji:
- Kontrasty, rozmiary dotykowe, focus states spełniają podstawowe wytyczne.
- Kluczowe działania dostępne są bez przewijania, tam gdzie to możliwe.

## 6. Metryki sukcesu
6.1. Metryka główna (retencja 30-dniowa)
- Definicja: odsetek użytkowników, którzy zarejestrowali konto i zapisali co najmniej 2 rundy w ciągu 30 dni od rejestracji.
- Cel: 75%.
- Pomiar: eventy rejestracji oraz zakończenia rund (complete) z userId i timestampem; kohortowanie po dacie rejestracji.

6.2. Miernik pomocniczy (ukończenie rundy)
- Definicja: odsetek użytkowników, którzy rozpoczęli rundę (zapisali dołek 1) i nie ukończyli (brak finishedAt dla 18).
- Interpretacja: wysoki odsetek porzuceń wskazuje na problemy UX/UI ekranu wprowadzania.
- Działania: analiza lejka zapisu dołków.

6.3. Wydajność i niezawodność
- Latencja UI: kluczowe akcje < 100 ms wrażenia.
- Stabilność offline: odsetek udanych synchronizacji po odzyskaniu sieci > 99%.

6.4. Jakość danych i scoringu
- Spójność obliczeń: brak rozbieżności między prezentacjami formatów w obrębie tej samej rundy.
- Poprawność importów: 100% zaimportowanych pól przechodzi walidacje SI/CR/SR/PAR.

6.5. Zgodność i bezpieczeństwo
- Zero wycieków haseł (hashowane), egzekwowanie resetów tokenów po wylogowaniu/wygaśnięciu.

6.6. Analityka operacyjna
- Pokrycie eventami: 100% kluczowych zdarzeń (rejestracja, start rundy, zapis dołków, zakończenie rundy, druga runda w 30 dni) jest odnotowane i raportowalne.
