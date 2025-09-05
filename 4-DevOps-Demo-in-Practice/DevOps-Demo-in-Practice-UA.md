# 🚀 Шпаргалка: деплой Quarkus *getting-started* на DigitalOcean (Ubuntu) з Linux Mint (VirtualBox)

> **Що робимо:** з Linux Mint заходимо по SSH на новий Ubuntu‑дроплет у DigitalOcean, ставимо Java та інші пакети, тягнемо `quarkus-quickstarts/getting-started`, збираємо й запускаємо на порту **8080**, перевіряємо, а потім **видаляємо дроплет**, щоб не платити.

---

## 🏗️ Архітектура демо

### Текстова діаграма налаштування
```
┌─────────────────────────────────────────────────────────────────┐
│                    Локальна машина (Host)                       │
│  ┌─────────────────┐    ┌─────────────────┐                     │
│  │   Windows/Mac   │    │   VirtualBox    │                     │
│  │                 │    │                 │                     │
│  │  ┌─────────────┐│    │  ┌─────────────┐│                     │
│  │  │ Linux Mint  ││    │  │ Linux Mint  ││                     │
│  │  │ (Dev Env)   ││    │  │ (Dev Env)   ││                     │
│  │  └─────────────┘│    │  └─────────────┘│                     │
│  └─────────────────┘    └─────────────────┘                     │
└─────────────────────────────────────────────────────────────────┘
                                │
                                │ SSH Connection
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    DigitalOcean Cloud                           │
│  ┌─────────────────┐    ┌─────────────────┐                     │
│  │   Ubuntu 24.04  │    │   Quarkus App   │                     │
│  │   Droplet       │    │   Port 8080     │                     │
│  │                 │    │                 │                     │
│  │  ┌─────────────┐│    │  ┌─────────────┐│                     │
│  │  │ Java 21     ││    │  │ HTTP Server ││                     │
│  │  │ Maven       ││    │  │ 0.0.0.0:8080││                     │
│  │  │ Git         ││    │  │             ││                     │
│  │  └─────────────┘│    │  └─────────────┘│                     │
│  └─────────────────┘    └─────────────────┘                     │
└─────────────────────────────────────────────────────────────────┘
                                │
                                │ HTTP Request
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Користувач (Browser)                         │
│  ┌─────────────────┐                                            │
│  │   Web Browser   │                                            │
│  │ http://IP:8080  │                                            │
│  └─────────────────┘                                            │
└─────────────────────────────────────────────────────────────────┘
```

### 🔄 Потік виконання
1. **Підготовка** — створення SSH ключа та дроплета
2. **Підключення** — SSH з Linux Mint на Ubuntu дроплет
3. **Налаштування** — встановлення Java, Maven, Git
4. **Код** — клонування Quarkus quickstart репозиторію
5. **Збірка** — компіляція та запуск додатку
6. **Тестування** — перевірка роботи через браузер
7. **Очищення** — видалення дроплета для економії

---

## 🔑 0) Передумови (локально в Linux Mint)
- **SSH‑ключ** (бажано): 
  ```bash
  # якщо ще нема ключа
  ssh-keygen -t ed25519 -C "you@example.com"
  # публічний ключ покажеться так
  cat ~/.ssh/id_ed25519.pub
  ```
  Додайте публічний ключ у DigitalOcean: **Settings → Security → SSH Keys**.

- **Створіть дроплет** у DO (рекомендовано: Ubuntu 24.04 LTS, smallest plan). Запишіть його публічну IP‑адресу як `DROPLET_IP`.

---

## 🔌 1) SSH вхід на дроплет
```bash
# (варіант із ключем)
ssh -o StrictHostKeyChecking=accept-new root@DROPLET_IP

# (якщо парольний вхід — DO скине пароль на email; змініть його при першому вході)
ssh root@DROPLET_IP
```

> Далі всі команди виконуємо **на дроплеті** (як root або через sudo).

---

## ⚙️ 2) Базова підготовка Ubuntu
```bash
apt update && apt -y upgrade

# корисні пакети
apt -y install git curl unzip

# Java (оберіть один варіант)
apt -y install openjdk-21-jdk   # сучасна LTS
# або
# apt -y install openjdk-17-jdk  # також підійде
java -version

# Maven (потрібен для збірки Quarkus quickstart)
apt -y install maven
mvn -version
```

### (Опційно) UFW‑фаєрвол
> У DO порти зазвичай відкриті, але краще обмежити вручну.
```bash
apt -y install ufw
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp           # SSH
ufw allow 8080/tcp         # наш застосунок
ufw --force enable
ufw status verbose
```

---

## 📥 3) Клонування репозиторію Quarkus quickstarts (тільки приклад getting-started)
```bash
cd /opt
git clone https://github.com/quarkusio/quarkus-quickstarts.git
cd quarkus-quickstarts/getting-started
```

> Структура прикладу — Maven‑проєкт. Далі зберемо і запустимо.

---

## 🚀 4) Збірка та запуск на 0.0.0.0:8080
> За замовчуванням Quarkus може біндитись на localhost. Для доступу ззовні вкажемо хост **0.0.0.0**.

### Варіант A: **швидкий dev‑режим** (для демонстрації)
```bash
# dev-режим (гаряче перезавантаження, зручно для швидкого демо)
./mvnw quarkus:dev -Dquarkus.http.host=0.0.0.0 -Dquarkus.http.port=8080
# якщо ./mvnw ще не executable: chmod +x mvnw
```

### Варіант B: **продібний запуск з JAR**
```bash
# збірка
mvn -DskipTests package

# запуск fast-jar (з біндом на всі інтерфейси)
java -Dquarkus.http.host=0.0.0.0 -Dquarkus.http.port=8080 \
     -jar target/quarkus-app/quarkus-run.jar
```

> Переконайтесь, що порт 8080 не зайнятий:
```bash
ss -tulpn | grep 8080 || true
```

---

## ✅ 5) Перевірка роботи
- На **локальній машині** (Linux Mint) відкрийте в браузері:  
  `http://DROPLET_IP:8080/`
- Або з дроплета/локально:
  ```bash
  curl -i http://127.0.0.1:8080/
  curl -i http://DROPLET_IP:8080/
  ```

Очікуєте відповідь типу `200 OK` і JSON або HTML залежно від ендпойнта.

---

## ⏹️ 6) Зупинка застосунку
- Якщо запускали у **dev‑режимі** чи як **jar** — натисніть `Ctrl + C` у тій консолі.
- Перевірте, що порт 8080 вільний:
  ```bash
  ss -tulpn | grep 8080 || echo "8080 вільний"
  ```

---

## 🗑️ 7) Прибирання і **видалення дроплета**, щоб не платити
> **Важливо:** Просто вимкнути сервер — **недостатньо**. Потрібно **Destroy** в DO.

### Через веб‑інтерфейс DigitalOcean
1. Увійдіть у **DigitalOcean → Droplets**.
2. Оберіть ваш дроплет → **Destroy** → підтвердьте.

### (Опційно) через `doctl` з локальної машини
```bash
# встановити doctl (якщо треба): https://docs.digitalocean.com/reference/doctl/how-to/install/
# авторизація
doctl auth init
# перевірка списку дроплетів
doctl compute droplet list
# видалення за ID
doctl compute droplet delete <DROPLET_ID> --force
```

---

## Часті збої та рішення
- **Порт 8080 недоступний зовні**  
  – Переконайтесь у `-Dquarkus.http.host=0.0.0.0`.  
  – Відкрийте порт у UFW/DO Firewall.  
  – Перевірте IP (інколи доводиться чекати 10–30 сек після старту застосунку).

- **Java/Maven не знайдені**  
  – Перевірте `java -version` та `mvn -version`; якщо нема — встановіть пакети.  
  – Інколи достатньо `apt update` і повторної спроби `apt install`.

- **`permission denied` для `mvnw`**  
  – Виконайте `chmod +x mvnw` у корені Maven‑проєкту.

- **Зайнятий 8080**  
  – Дізнайтесь PID: `ss -tulpn | grep 8080` → `kill -9 <PID>` або змініть порт: `-Dquarkus.http.port=9090` та відкрийте його у фаєрволі.

---

## Швидкий план демо (під запис екрану)
1. Вхід на дроплет по SSH.  
2. `apt update && apt upgrade`, встановити Java/Maven/Git.  
3. Клонувати репо `quarkus-quickstarts`, перейти в `getting-started`.  
4. **Запуск:** `./mvnw quarkus:dev -Dquarkus.http.host=0.0.0.0`  
5. Відкрити `http://DROPLET_IP:8080/` у браузері → показати відповідь.  
6. Зупинити застосунок (`Ctrl+C`).  
7. Видалити дроплет у DO (**Destroy**).

---

### Готово!
Це мінімальна, але реалістична демонстрація DevOps‑флоу: підняли інфру (дроплет), підготували середовище, зібрали та запустили застосунок, відкрили мережу, перевірили результат і вимкнули ресурси.
