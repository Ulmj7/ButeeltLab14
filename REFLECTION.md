# Эргэцүүлэл — Бие даалт 14

## 1. Аль assertion хамгийн их үнэ цэнэтэй санагдсан вэ?

Намайг хамгийн их сэтгэгдэл төрүүлсэн assertion бол `Add product` request-д
байгаа **"title matches the random title we sent"** —
`pm.expect(data.title).to.eql(pm.environment.get('randomTitle'))`. Энэ нь
status code болон schema-аас илүү гүн —  яг **end-to-end contract**-ыг
шалгана: pre-request script-д үүсгэсэн утга шууд request body-руу очиж,
сервер хүлээж аваад, response-руу буцааж байгаа эсэхийг нотолно. Status 200
буцсан ч сервер title-ыг авахгүй өнгөрвөл (тажуулагдах эсвэл өөр field-руу
оруулчихвал) энэ assertion л барих юм. "Хариу амжилттай" гэдгээс илүү
"хариу зөв" гэдгийг харж байгаа нь үнэ цэнэтэй.

## 2. Negative test-ийн жишээ

`Errors / Get non-existent product (404)` request нь `GET
{{baseUrl}}/products/999999`-руу очиж 404 буцахыг шалгана. Энэ нь хоёр зүйлийг
барина: **(1)** id олдохгүй үед сервер 200-ийг хоосон object-той хамт
буцаахгүй байх (silent failure), **(2)** `message` field бүхий тогтсон
формат буцаах. Хэрэв API-г refactor хийсэн хүн алдааны response-ыг
`{ "error": "..." }`-руу хувиргавал манай assertion (`have.property('message')`)
шууд барина. 404-ийг шалгахгүй бол client тал object байхгүй атал unmount
хийгээгүй data render-лэх зам үүсч болзошгүй.

## 3. Postman-д pass, Newman-д fail болсон уу?

**Тийм.** Анх collection-ы test script бүрд `const data = pm.response.json();`
гэж бичсэн. Postman-ы Tests tab-д run хийхэд асуудалгүй, учир нь request бүр
шинэ scope-той ажилладаг. Гэтэл Newman нэг VM дотор бүх script-ыг дараалан
ажиллуулдаг тул хоёр дахь request-ийн `const data`-аас `SyntaxError:
Identifier 'data' has already been declared` гэж унаа. `var data`-руу
сольсноор шийдэгдсэн. Сургамж: Newman-ы isolated scope биш гэдэг
давтагдашгүй гадуурх (top-level) const/let-тэй ялгаатай. Production CI-д
push хийхээс өмнө `newman run` заавал ажиллуулж шалгах нь чухал.

## 4. Token / secret-ийн зохицуулалт

DummyJSON-ы public test credential (`emilys` / `emilyspass`) нь бодит
secret биш ч secret-ийн соёлыг харуулах зорилгоор хоёр env файл хуваасан.
`env.dev.json` нь local хөгжүүлэлтэд хэрэглэгдэх ба үнэн утгуудтай.
`env.ci.json` нь `password`-ыг `REPLACE_WITH_GITHUB_SECRET` placeholder-аар
орлуулсан. GitHub Actions workflow ажиллах үед `sed`-ээр
`${{ secrets.DUMMYJSON_PASSWORD }}`-аар орлуулна. Secret тохируулагдаагүй
бол fallback ажиллана. Real API key (жишээ нь OpenWeatherMap)-ийг ашиглах
тохиолдолд fallback-гүй, Settings → Secrets-д заавал тохируулах ёстой.

## 5. API өөрчлөгдвөл хамгийн их эвдрэх хэсэг

Хамгийн эмзэг хэсэг бол **schema/property assertion** (`have.property('id')`,
`data.products`) болон **bizness rule** (`eql(1)`, regex matches). API-ийн зохиогч
field-ийн нэрийг `productId`, `data` → `items` гэх мэт өөрчилбөл олон тест
шууд унана. Login response-д `token` нь `accessToken` болж өөрчлөгдсөн нь
үүний бодит жишээ. Эвдрэх эрсдэлийг бууруулах гурван арга: **(1)** field
нэрүүдийг collection variable болгож хувьсагч-аар лавлах, **(2)** OpenAPI/JSON
Schema файл байгаа бол `ajv`-аар schema validation хийх, **(3)** API
version-ыг URL-д хатуу зааж (`/api/v1`) хариуцлагатай deprecation policy-той
provider сонгох. Тогтвортой contract бичих нь тестийн пирамидын дунд
давхаргад API testing-ийг тогтвортой барина.
