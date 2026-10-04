# Приложение «Relab» в сторах — что сделать

Состояние на 04.10.2026. Код приложения — репозиторий `relab-miniapp-test`,
папка `apps/hub-mobile` («Relab», `family.relab.hub`). Здесь — только
документы и анкеты для App Store и Google Play.

> ⚠️ В этот репозиторий **не кладём** пароли, ключи и файлы ключей: `.p8`,
> JSON сервисного аккаунта Firebase, `google-services.json`, keystore, логин и
> пароль демо-аккаунта. Они живут в менеджере паролей, на машине сборки или
> на сервере.

## 1. Документы — заполнить здесь

- [ ] [anketa.md](anketa.md) — 10 ответов: реквизиты, почты, сроки, хостинг.
- [ ] [privacy-policy.md](privacy-policy.md) — политика конфиденциальности:
      места `‹…›` заполняются по анкете, потом текст смотрит юрист.

## 2. Готово, перенести в консоли сторов

- [ ] [store-listing.md](store-listing.md) — название, описание, ключевые
      слова, Review Notes, скриншоты.
- [ ] [data-forms.md](data-forms.md) — App Privacy (Apple), Data safety и
      остальные анкеты (Google).

## 3. Аккаунты и ключи (делает Роман)

| Что | Где | Статус |
|---|---|---|
| Apple Developer | физлицо Романа | есть |
| Google Play Console | юрлицо | есть |
| Вход в Xcode тем же Apple ID и Team ID | Xcode → Settings → Accounts; developer.apple.com → Membership | ☐ |
| Ключ APNs `.p8` и его Key ID (push на iOS) | developer.apple.com → Certificates, IDs & Profiles → Keys → «+» → Apple Push Notifications service. Скачивается один раз | ☐ |
| Firebase: проект и Android-приложение `family.relab.hub` | console.firebase.google.com | ☐ |
| `google-services.json` | Firebase → настройки проекта → Android-приложение. Кладётся на машину сборки в `apps/hub-mobile/`, не в git | ☐ |
| JSON сервисного аккаунта Firebase | Firebase → Project settings → Service accounts → «Generate new private key». Только на сервер, в `/secrets/push` | ☐ |
| Ключ загрузки Google Play (upload keystore) | создаётся командой ниже, копия — в менеджер паролей | ☐ |

Ключ загрузки (пароль спросит сама команда):

```bash
keytool -genkeypair -v -storetype PKCS12 -keystore ~/.gradle/relab-hub-upload.keystore \
  -alias relab-hub-upload -keyalg RSA -keysize 2048 -validity 10000
```

## 4. Демо-аккаунт для ревью (на проде)

- [ ] Должность «Демо-проверяющий»: только право Relab Check, без
      «Управления».
- [ ] Аккаунт с этой должностью, без второго фактора, сложный пароль.
- [ ] Объект «Демо-бар», шаблон на 3–4 пункта (да/нет, фото) и ежедневное
      расписание на этот аккаунт — иначе ревьюер увидит пустой список.
- [ ] Логин и пароль — только в App Store Connect (App Review Information) и
      Play Console (App access). После ревью пароль сменить.

## 5. Выкатка на прод перед отправкой

- [ ] Политика на `https://hub.relab.family/privacy.html` (после юриста).
- [ ] Страница поддержки для App Store (Support URL) — по ответу в анкете.
- [ ] Ключи push на сервере и `CONTROL_PUSH_ENABLED=1`.

## 6. Сборка и отправка

- [ ] Сборка `pnpm release:ios` и `pnpm release:android` в `apps/hub-mobile`
      (нужны почта для удаления, Team ID, `google-services.json`, ключ
      загрузки).
- [ ] App Store: Transporter → `.ipa` → TestFlight → отправка на ревью.
- [ ] Google Play: «Создать выпуск» → `.aab` → отправка на проверку.

## Риски ревью

- **Apple 3.2** — приложение для сотрудников одной сети: Apple может
  предложить Custom Apps (Apple Business Manager) вместо публичного App Store.
- **Apple 5.1.1(v)** — удаление аккаунта запросом на почту, а не кнопкой:
  допустимо, раз приложение аккаунты не создаёт, но ревьюер может придраться.
- **Apple, физлицо** — продавцом будет имя Романа, оператор в политике —
  юрлицо: ревьюер может спросить право на бренд «Relab».
