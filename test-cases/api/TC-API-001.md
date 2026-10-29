# TC-API-001: Реєстрація оператора ігнорує переданий authorisation_level і створює L1

- **ID:** TC-API-001
- **Title:** `POST /api/v1/operators` з `authorisation_level: "L3"` у тілі створює оператора з `authorisation_level = "L1"`
- **Doc ref:** `03-api-contract.md` «Authentication & authorisation» («`authorisation_level` is no longer self-service. Registration always creates an operator at `L1`»); `04-frontend-spec.md` «Operator portal» (поле відсутнє у формі реєстрації); `03-api-contract.md` «Authentication & authorisation» (`POST /operators` «then signs the caller in (session cookie)»; `GET /auth/me` — «The logged-in operator's profile»); `01-data-model.md` «Operator» (поля тіла; `email` і `national_id` унікальні)
- **Priority:** High
- **Status:** Ready

## Preconditions
- У системі немає оператора й Supabase Auth-користувача з `email = qa.reg.l1@example.com` та оператора з `national_id = UA-QA-REG-0001`.
- Запит надсилається без сесійної cookie і без `X-Reviewer-Id`.

## Steps
1. Надіслати `POST /api/v1/operators` з тілом:
   ```json
   {
     "legal_name": "QA Registration L1 LLC",
     "national_id": "UA-QA-REG-0001",
     "email": "qa.reg.l1@example.com",
     "password": "Str0ngPass!",
     "phone": "+380501234567",
     "address_line": "1 Test St",
     "city": "Kyiv",
     "postal_code": "01001",
     "authorisation_level": "L3"
   }
   ```
2. Надіслати `GET /api/v1/auth/me` із сесійною cookie, отриманою у відповіді на крок 1.

## Expected result
1. Крок 1: оператора створено (успішна відповідь `2xx`), встановлено сесійну cookie.
2. Крок 2: профіль поточного оператора містить `authorisation_level = "L1"` — значення `"L3"` з запиту не застосоване.

Допоміжно: якщо тіло відповіді на крок 1 містить `authorisation_level`, воно теж дорівнює `"L1"`. Форму тіла відповіді `POST /operators` docs не задають, тому це не основна перевірка.
