# Бие даалт 14 — Integration & API Testing

ШУТИС — F.CSM311 Программ хангамжийн бүтээлт

Postman collection + Newman CLI + GitHub Actions ашиглан **DummyJSON**
public API дээр integration / API testing хийнэ.

[![API tests](https://github.com/Ulmj7/ButeeltLab14/actions/workflows/api-tests.yml/badge.svg)](https://github.com/Ulmj7/ButeeltLab14/actions/workflows/api-tests.yml)

## Сонгосон API

**DummyJSON** — https://dummyjson.com
Дэлгэрэнгүй: [partA/SETUP.md](partA/SETUP.md)

## Бүтэц

```
bie-daalt-14/
├── .github/workflows/api-tests.yml   # GitHub Actions CI
├── partA/
│   ├── SETUP.md                      # API сонголт, brief, auth, base URL
│   └── screenshot.png                # Эхний амжилттай request-ийн зураг
├── postman/
│   ├── collection.json               # 8 request, 30 assertion
│   ├── env.dev.json                  # Dev environment
│   └── env.ci.json                   # CI environment (secret placeholder)
├── reports/
│   └── api.html                      # Newman htmlextra тайлан
├── README.md
└── REFLECTION.md                     # 5 асуултын хариулт
```

## Collection-ы агуулга

3 folder, 8 request:

| Folder | Request | Method | Зорилго |
|---|---|---|---|
| Auth | Login | POST `/auth/login` | accessToken + userId-г env-д хадгална (chain эх) |
| Auth | Get my info | GET `/auth/me` | Login-ы token ашиглана (chained) |
| Products | List products | GET `/products?limit=10` | Happy GET |
| Products | Get product by id | GET `/products/1` | GET by id |
| Products | Add product | POST `/products/add` | Pre-request → random title, POST create |
| Products | Update product | PUT `/products/1` | PUT/PATCH |
| Products | Delete product | DELETE `/products/1` | DELETE |
| Errors | Get non-existent product | GET `/products/999999` | Negative test → 404 |

### Assertion-ы хамрах хүрээ

| Төрөл | Тоо |
|---|---|
| Status code | 8 |
| Response time | 2 |
| JSON schema / property | 7 |
| Утгын тип | 5 |
| Бизнес дүрэм (regex, eql, comparison) | 7 |
| Header | 1 |
| **Нийт** | **30** |

## Local-д хэрхэн ажиллуулах

### Шаардлага
- Node.js ≥ 18 ([nodejs.org](https://nodejs.org))
- Newman + htmlextra reporter

```bash
npm install -g newman newman-reporter-htmlextra
```

### Repo татах

```bash
git clone https://github.com/Ulmj7/ButeeltLab14.git
cd ButeeltLab14
```

### Тест ажиллуулах

```bash
# Энгийн CLI report
newman run postman/collection.json -e postman/env.dev.json

# HTML report үүсгэх (reports/api.html-д хадгалагдана)
newman run postman/collection.json \
  -e postman/env.dev.json \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export reports/api.html
```

Амжилттай run-ы үр дүн:
```
requests:     8 executed, 0 failed
test-scripts: 8 executed, 0 failed
assertions:   30 executed, 0 failed
```

## Postman-аас collection-ыг import хийх

1. Postman Desktop нээх
2. `Import` → `postman/collection.json`-ыг сонгох
3. Environment-уудыг import: `postman/env.dev.json`, `postman/env.ci.json`
4. Дээд буланд environment-аа `dev` болгох → `Run collection` → `Run`

## CI / GitHub Actions

`push` болон `pull_request` бүрд `.github/workflows/api-tests.yml` ажиллана:

1. Node.js 20 суулгана
2. `newman` + `newman-reporter-htmlextra` суулгана
3. `env.ci.json` дотор `REPLACE_WITH_GITHUB_SECRET` placeholder-ыг GitHub Secret
   эсвэл fallback default-аар орлуулна
4. Newman ажиллаж бүх 30 assertion-ыг шалгана
5. HTML + JUnit XML report-ыг `api-test-report` нэртэй artifact болгож upload хийнэ

Run-ы үр дүнг харах: [Actions tab](https://github.com/Ulmj7/ButeeltLab14/actions).
Artifact-ыг татаж `api.html`-ыг нээж тоймыг үзнэ үү.

## Secret-уудыг хэрхэн зохицуулсан

- `env.dev.json` — DummyJSON-ы public test credentials (`emilys`/`emilyspass`).
  Бодит secret биш, тийм болохоор repo-д commit хийсэн.
- `env.ci.json` — password нь `REPLACE_WITH_GITHUB_SECRET` placeholder.
  CI ажиллах үед workflow `sed`-ээр `${{ secrets.DUMMYJSON_PASSWORD }}`-ыг
  орлуулна. Secret хоосон бол fallback ажиллана.

GitHub дээр secret тохируулах:
`Settings → Secrets and variables → Actions → New repository secret` →
нэр `DUMMYJSON_PASSWORD`, утга `emilyspass`.

## Эргэцүүлэл

5 асуултын хариулт: [REFLECTION.md](REFLECTION.md)
