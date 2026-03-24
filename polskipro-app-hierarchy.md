# PolskiPro App — Screen Hierarchy

*Mapped from app screenshots (v1.2.5)*

---

## Bottom Tab Bar

| Tab | Icon | Screen |
|-----|------|--------|
| **Główna** | 🏠 | Home dashboard |
| **Ćwiczenia** | ✏️ | Exercise mode (= Tryb ćwiczeń) |
| **Postęp** | 📊 | Progress tracking |
| **Profil** | 👤 | User profile & settings |

---

## Tab 1: Główna (Home)

```
Główna
├── Tryb ćwiczeń          → opens Ćwiczenia tab
├── Pomoc i FAQ           → FAQ screen
│   ├── O aplikacji PolskiPro (expandable)
│   ├── Polityka Prywatności (expandable)
│   └── [Zadaj pytanie] button
├── Harmonogram egzaminów → Exam schedule screen
│   ├── 15-16.11.2025 — B1, C1 i C2
│   ├── 14-15.02.2026 — B1 i B2 (dorośli + młodzież)
│   ├── 25-26.04.2026 — B1 i C1
│   ├── 27-28.06.2026 — B1 i B2
│   ├── 17-18.11.2026 — B1, C1 i C2
│   └── [Sprawdź lokalizacje] button
└── Przewodnik gramatyczny → Grammar guide
    └── Alfabet → Grid of 32 Polish letters with example words
        Aa(auto), Ąą(wąż), Bb(balon), Cc(cukier), Ćć(ćma),
        Dd(dom), Ee(elf), Ęę(gęś), Ff(flaga), Gg(gwiazda),
        Hh(harfa), Ii(igła), Jj(jabłko), Kk(kot), Ll(liść), ...
```

---

## Tab 2: Ćwiczenia (Exercises)

```
Ćwiczenia (= Tryb ćwiczeń)
│
├── 1. Rozumienie ze słuchu (Listening)
│   └── Wybór wielokrotny
│       • Audio play buttons (▶)
│       • Multiple choice (3 options per question)
│       • Example with correct answer marked green
│
├── 2. Rozumienie tekstu (Reading) — 8 exercise types
│   ├── Dopasuj odpowiedzi do fragmentu
│   │   └── Read passage → select correct answer from 3 options
│   ├── Dopasuj odpowiedzi do sytuacji
│   │   └── Match signs/notices to locations (3 options each)
│   ├── Prawda / Fałsz / Brak informacji
│   │   └── Read passage → mark statements P (true) or F (false)
│   ├── Ułóż fragmenty w logiczną całość
│   │   └── Drag-and-drop text fragments into correct order
│   ├── Uzupełnij tekst fragmentami
│   │   └── Fill gaps in text with provided sentence fragments
│   ├── Uzupełnij tekst słowami
│   │   └── Fill gaps with individual words from a word bank
│   ├── Połącz fragmenty
│   │   └── Match text passages to correct titles/headings
│   └── Uzupełnij tekst, wybierając słowa
│       └── Choose best word for each gap from options in parentheses
│
├── 3. Gramatyka (Grammar)
│   ├── Ćwiczenia na podstawie błędów (Error-based)
│   │   └── AI-generated exercises based on user's past mistakes
│   └── Ćwiczenia według tematów (By topic) — 16 topics
│       ├── Odmiana rzeczownika i przymiotnika
│       │   └── Fill-in: noun/adjective declension in context
│       ├── Stopniowanie przymiotników i przysłówków
│       │   └── Fill-in: comparative/superlative forms
│       ├── Czas teraźniejszy i przyszły
│       │   └── Fill-in: conjugate verbs in present/future tense
│       ├── Czas przeszły
│       │   └── Fill-in: conjugate verbs in past tense
│       ├── Czas teraźniejszy i przeszły
│       │   └── Fill-in: mixed present and past tense forms
│       ├── Aspekt czasownika
│       │   └── Choose perfective vs imperfective verb form
│       ├── Aspekt i tryby czasownika
│       │   └── Fill gaps in dialogue with correct aspect/mood
│       ├── Tryb rozkazujący
│       │   └── Fill-in: imperative mood verb forms
│       ├── Tryb przypuszczający
│       │   └── Fill-in: conditional mood verb forms
│       ├── Zaimki osobowe
│       │   └── Choose correct pronoun form for each sentence
│       ├── Przyimki
│       │   └── Fill gaps with prepositions from word bank
│       ├── Spójniki
│       │   └── Fill gaps with conjunctions from word bank
│       │       Bank: jednak, czy, bo, ale, i, a, albo, chociaż, żeby, że, więc
│       ├── Pełna odmiana
│       │   └── Full declension of nouns/adjectives/pronouns in context
│       ├── Liczebniki
│       │   └── Choose correct numeral form in context
│       ├── Zadawanie pytań
│       │   └── Form questions about underlined parts of sentences
│       └── Transformacje zdań
│           └── Rewrite sentence using given word, preserving meaning
│
├── 4. Pisanie (Writing)
│   └── Wybór zestawu — choose 1 of 3 sets
│       ├── Zestaw 1
│       │   ├── Zadanie A: Ogłoszenie o zaginionym kocie (30 słów)
│       │   └── Zadanie B: Opowiadanie z fotografią (170 słów)
│       ├── Zestaw 2
│       │   ├── Zadanie A: SMS z przeprosinami do szefa (25 słów)
│       │   └── Zadanie B: Esej o środowisku naturalnym (175 słów)
│       └── Zestaw 3
│           ├── Zadanie A: Ogłoszenie o wynajmie pokoju (30 słów)
│           └── Zadanie B: Opis dnia pracy + refleksje (170 słów)
│
└── 5. Mówienie (Speaking)
    └── Timed session (countdown from 20:00)
        ├── Zadanie A: Opisz fotografię i sytuację (photo description)
        ├── Zadanie B: Opowiedz o wymarzonym domu/mieszkaniu (monologue)
        └── Zadanie C: Rozmowa ze sprzedawcą w sklepie (dialogue role-play)
```

---

## Tab 3: Postęp (Progress)

```
Mój postęp
├── Wykorzystanie limitu (Weekly usage limits)
│   ├── Pisanie:    ████░░░░░░ 0/3
│   ├── Mówienie:   ████░░░░░░ 0/3
│   ├── Czytanie:   ████░░░░░░ 0/5
│   ├── Gramatyka:  ████░░░░░░ 0/10
│   ├── Słuchanie:  ████░░░░░░ 0/5
│   └── Limity odnowią się: 28/03/2026
│
└── Wyniki (Results)
    ├── Słuchanie:  80%  ▼ (expandable)
    ├── Czytanie:   100% ▼ (expandable)
    └── Mówienie:   3
```

---

## Tab 4: Profil (Profile)

```
Profil
├── User info
│   ├── Name: Denys Nehometianov
│   └── Email: denis.negometyanov@gmail.com
│
├── Plan i funkcje
│   ├── Premium Miesiąc
│   ├── Subskrypcja wygasa: 21/04/2026
│   ├── Limity odnowią się: 28/03/2026
│   └── Wykupione funkcje (weekly limits):
│       ├── Pisanie:    3 pozostało w tym tygodniu
│       ├── Mówienie:   3 pozostało w tym tygodniu
│       ├── Czytanie:   5 pozostało w tym tygodniu
│       ├── Gramatyka:  10 pozostało w tym tygodniu
│       └── Słuchanie:  5 pozostało w tym tygodniu
│
├── [Kup lub ulepsz plan] button
│
├── Powiadomienia
│   ├── Odnowienie limitu tygodniowego
│   └── Powiadomienia Push: [toggle ON]
│
├── [Usuń konto] button (requires policy acceptance)
│
└── Wersja aplikacji: 1.2.5
```

---

## Navigation Pattern

- **Bottom tab bar** is always visible on all main screens
- **Sub-screens** (exercises, FAQ, schedule, etc.) replace the tab content and show a **← back button** in the top-left
- **Exercise flow**: Section list → Exercise type list → Individual exercise → [Sprawdź] → Results
- **Pisanie**: Set selection → Writing editor (textarea with word count)
- **Mówienie**: All 3 tasks shown on one screen with countdown timer
