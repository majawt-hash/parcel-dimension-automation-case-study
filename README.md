# Process Automation Case Study
**Role:** Process Automation Designer  
**Project:** Automatyzacja obsługi wyjątku przekroczenia gabarytu przesyłki (Over-dimension Exception Handling)  

---

## 1. Problem Statement & Context
W procesie logistycznym zdarzają się przypadki, gdy przesyłka zaadresowana do automatu paczkowego przekracza maksymalne dopuszczalne wymiary (tj. największą skrytkę). Przyczyną mogą być błędne dane wprowadzone przez nadawcę, zmiana opakowania zbiorczego lub błąd integracji API po stronie e-commerce.

**Stan obecny (As-Is):**
* Paczka z błędnymi gabarytami przechodzi przez sortownię i trafia do auta kuriera.
* Kurier na miejscu pod automatem paczkowym odkrywa brak możliwości umieszczenia przesyłki w skrytce.
* Kurier traci czas na bezpośredni kontakt telefoniczny z klientem lub zwraca paczkę do oddziału.
* **Marnotrawstwo:** Koszt paliwa, strata czasu pracy kuriera, opóźnienia w doręczeniach innych paczek, niski wskaźnik Customer Experience (NPS).

---

## 2. Proposed Solution (To-Be Workflow)
Wdrożenie automatycznego przepływu danych (**Dimension Exception Workflow**) z wykorzystaniem platformy Low-Code (np. n8n) połączonej z API operatora oraz systemami powiadomień.

```mermaid
graph TD
    A[Skaner gabarytów w sortowni / API Carrier] -->|Wysyłka danych o wymiarach| B(n8n Webhook Trigger)
    B --> C{Czy wymiary > Max Gabaryt?}
    C -->|Nie| D[Standardowa ścieżka doręczenia]
    C -->|Tak| E[Wstrzymanie wydania kurierowi]
    E --> F[Wysyłka automatycznego powiadomienia Push/SMS/WhatsApp z linkiem]
    F --> G{Decyzja klienta w aplikacji}
    G -->|Option A| H[Przekierowanie: Doręczenie kurierem pod adres]
    G -->|Option B| I[Przekierowanie: Odbiór w Punkcie POP]
    H --> J[Aktualizacja statusu w systemie i przekierowanie paczki]
    I --> J
```
### Analiza SWOT rozwiązania

| Mocne strony (Strengths) | Słabe strony (Weaknesses) |
| :--- | :--- |
| • Eliminacja niepotrzebnych kursów kuriera<br>• Automatyczna komunikacja w czasie rzeczywistym<br>• Szybkie wdrożenie dzięki architekturze Low-Code (n8n) | • Zależność od dokładności skanowania na sortowni<br>• Konieczność reakcji klienta na powiadomienie |
| **Szanse (Opportunities)** | **Zagrożenia (Threats)** |
| • Wzrost wskaźnika NPS (klient sam decyduje o paczce)<br>• Odciążenie infolinii i doręczycieli<br>• Możliwość ponownego wykorzystania workflow dla innych wyjątków | • Opóźnienie decyzji klienta wydłużające czas magazynowania |

---

## 3. Business Case & Return on Investment (ROI)

*Uwaga: Poniższe wyliczenia opierają się na szacunkowych założeniach operacyjnych na potrzeby zadania.*

### 1. Obecnie ponoszone koszty (Stan As-Is)
* **Skala i założenia:** Przyjęto ultraostrożne, minimalne założenie **1 000 błędów gabarytowych miesięcznie** w skali całego kraju. 
  > *Rzeczywisty kontekst:* Przy wolumenie ok. 63 mln paczek obsługiwanych przez wiodącego operatora logistycznego w Polsce miesięcznie, odpowiada to zaledwie **~0,0016% wszystkich przesyłek** (1 przypadek błędu na 63 000 paczek).
* **Koszt pojedynczego wyjątku:** ~15 PLN (nieefektywny czas pracy kuriera pod automatem paczkowym, próba załadunku, obsługa telefoniczna z klientem oraz logistyka zwrotna przesyłki na magazyn).
* **Miesięczny koszt błędów:** ~15 000 PLN / miesiąc (1 000 przypadków × 15 PLN).

### 2. Koszty wdrożenia rozwiązania (Stan To-Be)
* **Jednorazowy koszt wdrożenia (CAPEX):** ~6 000 PLN (zaprojektowanie, konfiguracja i testy workflow w n8n; ~40h pracy Process Automation Designera).
* **Koszt utrzymania miesięczny (OPEX):** ~300 PLN / miesiąc 
  *(Obejmuje koszt wysyłki powiadomień SMS/Push dla 1 000 wyselekcjonowanych wyjątków oraz krańcowy koszt zasobów instancji n8n Self-hosted, przetwarzającej zdarzenia i weryfikującej warunki gabarytowe dla przepływu).*

**3. Oszczędność i Wynik Finansowy**
* **Miesięczna oszczędność operacyjna netto:** **~14 700 PLN / miesiąc** *(15 000 PLN oszczędności brutto − 300 PLN OPEX)*.
* **Oszczędność netto w 1. roku (z uwzględnieniem CAPEX):** **~170 400 PLN** *(12 × 14 700 PLN oszczędności operacyjnej − 6 000 PLN jednorazowego CAPEX)*.

### 4. Zwrot z inwestycji (Payback Period)
* **Czas zwrotu:** ~13 dni od momentu wdrożenia rozwiązania na produkcję (6 000 PLN CAPEX / 14 700 PLN oszczędności netto miesięcznie = ~0,4 miesiąca).

---

## 4. Implementation Roadmap

1. **Tydzień 1: Analiza i mapowanie API**
   * Zmapowanie punktów styku (punkt weryfikacji wymiarów na sortowni).
   * Określenie endpointów API do zmiany statusu paczki i wysyłki powiadomień.
2. **Tydzień 2: Budowa prototypu w n8n**
   * Stworzenie workflow logicznego (Webhooks, warunki `IF`, integracja z bramką SMS/Push).
   * Przygotowanie dedykowanego mikroformularza dla klienta (wybór: Kurier / POP).
3. **Tydzień 3: Testy i obsługa błędów (Error Handling)**
   * Testy wydajnościowe oraz weryfikacja scenariusza, w którym klient nie podejmie decyzji w ciągu 12h (fallback do POP).
4. **Tydzień 4: Produkcja i monitoring**
   * Wdrożenie produkcyjne na wybranym oddziale pilotażowym.
   * Ustawienie monitoringu błędów i wskaźników wykonania workflow.
