# А хэсэг — Setup

## Сонгосон API

**DummyJSON** — https://dummyjson.com

DummyJSON нь чөлөөтэй ашиглах боломжтой REST API бөгөөд CRUD үйлдлүүд, JWT
аутентификаци (login → token → protected endpoint), pagination, search зэрэг
бодит API-д тааралддаг бүх онцлогуудыг агуулдаг.

## Brief

| Зүйл | Утга |
|---|---|
| Base URL | `https://dummyjson.com` |
| Authentication | JWT (POST `/auth/login` → `accessToken`) |
| Format | JSON |
| Rate limit | Албан ёсоор зарлаагүй, тестэд бараг хязгааргүй |
| CORS | Нээлттэй |
| Hosting | Cloud (хэрэглэгчийн зүгээс юу ч суулгах шаардлагагүй) |

## Endpoint-ууд (тус лабораторт ашиглах)

| Method | Path | Тайлбар |
|---|---|---|
| POST | `/auth/login` | Хэрэглэгч нэвтэрч `accessToken` авах |
| GET | `/auth/me` | Token-той хэрэглэгчийн мэдээллийг авах (chain) |
| GET | `/products` | Бүтээгдэхүүний жагсаалт (pagination-той) |
| GET | `/products/:id` | Нэг бүтээгдэхүүн |
| POST | `/products/add` | Шинэ бүтээгдэхүүн үүсгэх |
| PUT | `/products/:id` | Бүтээгдэхүүн засах |
| DELETE | `/products/:id` | Бүтээгдэхүүн устгах |
| GET | `/products/999999` | Алдааны кейс (404) |

## Туршилтын credentials

DummyJSON-ы заавал бүртгэлтэй хэрэглэгчийн жишээ:

- username: `emilys`
- password: `emilyspass`

(Албан ёсны документ: https://dummyjson.com/docs/auth)

## Workspace тохиргоо

- Postman Workspace: **F.CSM311 — Lab14**
- Collection нэр: **Ulmj — DummyJSON**
- Environment-ууд:
  - `dev` — `baseUrl = https://dummyjson.com`
  - `staging` — хоосон (зөвхөн бүтэц харуулах зорилгоор)
  - `prod` — хоосон

## Эхний амжилттай request

```
GET https://dummyjson.com/products/1
Status: 200 OK
```

Хариу JSON-ы эхлэл:
```json
{
  "id": 1,
  "title": "Essence Mascara Lash Princess",
  "price": 9.99,
  ...
}
```

Screenshot: `partA/screenshot.png`-ийг үзнэ үү.
