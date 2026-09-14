# InPost Process Automation Case Study
**Role:** Process Automation Designer  
**Project:** Automatyzacja obsługi wyjątku przekroczenia gabarytu przesyłki (Over-dimension Exception Handling)  

---

## 1. Problem Statement & Context
W procesie logistycznym zdarzają się przypadki, gdy przesyłka zaadresowana do Paczkomatu® przekracza maksymalne dopuszczalne wymiary (np. skrytka Gabaryt C). Przyczyną mogą być błędne dane wprowadzone przez nadawcę, zmiana opakowania zbiorczego lub błąd integracji API po stronie e-commerce.

**Stan obecny (As-Is):**
* Paczka z błędnymi gabarytami przechodzi przez sortownię i trafia do auta kuriera.
* Kurier na miejscu pod Paczkomatem odkrywa brak możliwości umieszczenia przesyłki w skrytce.
* Kurier traci czas na bezpośredni kontakt telefoniczny z klientem lub zwraca paczkę do oddziału.
* **Marnotrawstwo:** Koszt paliwa, strata czasu pracy kuriera, opóźnienia w doręczeniach innych paczek, niski wskaźnik Customer Experience (NPS).

---

## 2. Proposed Solution (To-Be Workflow)
Wdrożenie automatycznego przepływu danych (**Dimension Exception Workflow**) z wykorzystaniem platformy Low-Code (np. n8n) połączonej z API InPost oraz systemami powiadomień.

```mermaid
graph TD
    A[Skaner gabarytów w sortowni / API InPost] -->|Wysyłka danych o wymiarach| B(n8n Webhook Trigger)
    B --> C{Czy wymiary > Gabaryt C?}
    C -->|Nie| D[Standardowa ścieżka doręczenia]
    C -->|Tak| E[Wstrzymanie wydania kurierowi]
    E --> F[Wysyłka automatycznego powiadomienia Push/SMS/WhatsApp z linkiem]
    F --> G{Decyzja klienta w aplikacji}
    G -->|Option A| H[Przekierowanie: Doręczenie kurierem pod adres]
    G -->|Option B| I[Przekierowanie: Odbiór w Punkcie POP]
    H & I --> J[Aktualizacja statusu w systemie i przekierowanie paczki]
