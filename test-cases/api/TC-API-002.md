# TC-API-002: Реєстрація оператора одразу встановлює сесію

- **ID:** TC-API-002
- **Title:** Після успішного `POST /api/v1/operators` сесійна cookie з відповіді автентифікує `GET /api/v1/auth/me` як щойно зареєстрованого оператора
- **Doc ref:** `03-api-contract.md` «Authentication & authorisation» (`POST /operators`: «creates the Supabase Auth user *and* the Operator profile in one request, then signs the caller in (session cookie)»; `GET /auth/me`: «The logged-in operator's profile, or `401`»); `04-frontend-spec.md` «Operator portal» («logging the operator in immediately»)
- **Priority:** High
- **Status:** Ready

## Preconditions
- У системі немає оператора й Supabase Auth-користувача з `email = qa.reg.session@example.com` та оператора з `national_id = UA-QA-REG-0002`.
- HTTP-клієнт без жодних cookie, запити без `X-Reviewer-Id`.
- Виконано `POST /api/v1/operators` з тілом:
  ```json
  {
    "legal_name": "QA Registration Session LLC",
    "national_id": "UA-QA-REG-0002",
    "email": "qa.reg.session@example.com",
    "password": "Str0ngPass!",
    "phone": "+380501234568",
    "address_line": "2 Test St",
    "city": "Kyiv",
    "postal_code": "01001"
  }
  ```
  Відповідь `2xx`; збережено `id` з тіла відповіді та cookie з `Set-Cookie`.

## Steps
1. Надіслати `GET /api/v1/auth/me` лише з cookie, отриманими у відповіді на реєстрацію (без `X-Reviewer-Id`, без окремого `POST /auth/login`).

## Expected result
Відповідь `200` з профілем оператора, у якому `id` дорівнює `id` з відповіді на реєстрацію (`email = qa.reg.session@example.com`), а не `401`.
