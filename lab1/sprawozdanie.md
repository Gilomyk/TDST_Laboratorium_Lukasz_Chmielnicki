# Wojskowa Akademia Techniczna

Teoria Decyzji Statystycznych

Sprawozdanie z laboratorium nr 1

Łukasz Chmielnicki WCY25IV1S4

Prowadzący: Przemysław Czuba

**Narzędzie agenta AI wykorzystane w zadaniu:**

AntiGravity – Gemini 4 Argon - Low

---

## 1. Jaki wynik zlecono, jakie zmiany były dozwolone i o jakie informacje do odbioru poproszono agenta?

Podczas pracy z agentem zlecono mu zapoznanie się z treścią zadania i projektem (głównie plikiem dokumentacji projektu README.md), a następnie uruchomienie skryptu i przeanalizowanie wygenerowanego raportu. W trakcie pracy podczas 1. Części zadania agent mógł czytać pliki zawarte w projekcie ale bez ich modyfikacji na poziomie dokumentacji, kodu, danych lub testów. W 2. Części zadania agent miał zezwolenie na modyfikację jednej linijki wewnątrz zbioru danych w pliku .csv.

Agent podczas wykonywanej pracy miał zwracać informacje o:

- lokalizacji plików danych wejściowych i wyjściowych
- zawartości wygenerowanego raportu
- wytłumaczeniu przeanalizowanego fragmentu kodu

---

## 2. Jak powstał raport? Wskaż wejście, polecenie, narzędzie, środowisko wykonania i wynik procesu. Dodaj link do końcowego raportu i wyjaśnij jego liczebności.

Końcowy raport opisujący dane wejściowe powstał poprzez rozdzielenie zadań między użytkownikiem, modelem agentowym i komputerem. Na początku użytkownik zlecił agentowi zadanie uruchomienia skryptu describe_data.py w głównym folderze projektu, gdzie:

- dane wejściowe pochodziły z pliku data/exercise.csv
- polecenie do uruchomienia było zapisane w README.md i miało postać:

```bash
python3 describe_data.py --data data/demo.csv --out results/demo-summary.json
```

- komenda miałą być uruchomiona w terminalu aplikacji AntiGravity
- środowiskiem wykonawczym miał być lokalny komputer
- wynik procesu miał być zapisany do pliku results/exercise-summary.json

Następnie agent AI wykonał odpowiednie polecenie w terminalu na lokalnej maszynie, a po wykonaniu programu odczytał jego wynik i zawartość raportu.

[Raport końcowy](exercise-summary.json)

W raporcie występują liczebności:

```text
labels: {'ham': 5, 'spam': 3}
```

Oznaczają one ile wiadomości w pliku exercise.csv zostały otykietowane jako spam, a ile jako prawidłowe wiadomości firmy.

---

## 3. Który fragment dokumentacji lub kodu wyjaśniłeś? Podaj plik i miejsce, własne wyjaśnienie oraz mały przypadek: przewidywanie, sposób sprawdzenia i wniosek.

Z pomocą agenta AI wyjaśniłem działanie kodu w pliku describe_data.py w linijkach 15-17:

```python
rows = csv.DictReader(io.StringIO(raw.decode("utf-8"), newline=""))
if rows.fieldnames != ["message_id", "text", "label"]:
    raise ValueError("Expected columns: message_id,text,label.")
```

Pierwsza linijka fragmentu kodu ma za zadanie odczytać surowy ciąg bajtów wczytanych z pliku .csv, a następnie zamienić je na tekst z wykorzystaniem kodowania UTF-8. Io.StringIO() pozwala na traktowanie tekstu jako pliku w pamięci RAM, tymczasem csv.DictReader() przekształca tekst w słownik, w którym pierwszy wiersz jest odczytywany jako nazwy kolumn tabeli, a każdy kolejny wiersz jako nowe rekordy tabeli.

Kolejne dwie linijki służą temu, żeby odczytany tekst spełniał „kontrakt” opisany w kodzie, czyli zawierał poprawne nazwy kolumn w poprawnej kolejności. W przypadku zmiany nazw kolumn, ich liczby lub kolejności program wyrzuca ValueError.

Jako przypadek zmiennego kodu agent wygenerował tekst pliku .csv o małej zawartości:

```csv
message_id,label,text
msg-001,ham,"Subject: Hello

Test message."
```

Moje przewidywanie wyniku wykonania programu na nowym pliku zakładały, że program zwróci błąd, poniewać 2. I 3. Nagłówek kolumn zostały zamienione, przez co dojdzie do konfliktu przy porównaniu. Sprawdzenie odbyło się poprzez wykonanie przez czat komendy w terminalu z zapisem pythona w jednej linijce aby nie modyfikować kodu:

```bash
python -c "import csv, io; rows = csv.DictReader(io.StringIO('message_id,label,text\nmsg-001,ham,Test\n')); print('Odczytane fieldnames:', rows.fieldnames); print('Czy rowne [message_id, text, label]?:', rows.fieldnames == ['message_id', 'text', 'label'])"
```

W kodzie agent umieścił warunek przyrównania nagłówkó nowego tekstu do listy z oryginalnego pliku kodu. Wynik programu wyniósł:

```text
Odczytane fieldnames: ['message_id', 'label', 'text']
Czy rowne [message_id, text, label]?: False
```

Z przeprowadzonego tekstu wnioskuję, że czat poprawnie wytłumaczył mi jak funkcjonuje kod, dzięki czemu poprawnie przewidziałem jego zachowanie przy zmienionych danych wejściowych.

---

## 4. Jak uruchomiono gotowy test, jaki był wynik i czego dotyczyło sprawdzenie?

Gotowy test uruchomiono w ten sam sposób co głowny skrypt projektu. Jako użytkownik zleciłem czatowi zadanie uruchomienia skryptu według komendy testowej zapisanej w README.md:

```bash
python3 -m unittest discover -s tests -v
```

Następnie agent wykonał tą komendę w terminalu zezwalając lokalnej maszynie na przeprowadzenie obliczeń, a następnie przedstawił wynik wypisany w terminalu. Test polegał na wykonaniu pięciu asercji dotyczących:

- Kod zakończenia procesu
- Utworzenie pliku raportu
- Zgodność ścieżki wejściowej
- Łączną liczbę wiadomości
- Liczebności etykiet

Test zakończył się pełnym powodzeniem.

---

## 5. Jaką etykietę zmieniono w etapie 2? Jakiej zmiany liczebności oczekiwałeś, jakie operacje wybrałeś i dlaczego? Porównaj wynik przed zmianą i po nowym wykonaniu.

W etapie 2. Zadania zmieniono etykietę wiadomości o identyfikatorze exercise-001. Oczekiwałem, że liczebność danych zmieni się z ham:5, spam:3 na ham:4, spam:4. W tym celu poprosiłem agenta, żeby po zmianie danych wykonał te same operacje, które zostały opisane w 2. Punkcie sprawozdania, żeby upewnić się, że zmiany w nowym raporcie zależą wyłącznie od zmian w danych wejściowych.

**Wynik generowania raportu przed zmianą:**

```text
Report saved: C:\Users\lukas\Downloads\lab-1-project\project\results\exercise-summary.json
Messages: 8; labels: {'ham': 5, 'spam': 3}
```

**Wynik generowania raportu po zmianie:**

```text
Report saved: C:\Users\lukas\Downloads\lab-1-project\project\results\exercise-summary.json
Messages: 8; labels: {'ham': 4, 'spam': 4}
```
