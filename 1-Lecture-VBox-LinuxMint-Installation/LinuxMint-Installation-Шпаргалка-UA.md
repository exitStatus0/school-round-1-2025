# 🐧 Linux Mint у VirtualBox — Шпаргалка

> **⚡ Швидкий гайд для встановлення Linux Mint у VirtualBox**

---

## 📋 1. Підготовка

### Завантаження ISO
- **Офіційний сайт:** [https://linuxmint.com/download.php](https://linuxmint.com/download.php)
- **Рекомендована версія:** Linux Mint Cinnamon 64-bit
- **Розмір файлу:** ~2.5 GB

### Системні вимоги
- ✅ **VirtualBox** (вже встановлений)
- ✅ **Вільне місце:** мін. 20 GB (рекомендовано 40+ GB)
- ✅ **RAM:** мінімум 2 GB (рекомендовано 4+ GB)
- ✅ **CPU:** 2+ ядра

---

## 🖥️ 2. Створення віртуальної машини

### Крок 1: Базова конфігурація
1. Відкрий **VirtualBox** → натисни **New**
2. Заповни поля:
   - **Name:** `LinuxMint-DevOps`
   - **Type:** `Linux`
   - **Version:** `Ubuntu (64-bit)`

### Крок 2: Ресурси
- **RAM:** `4096 MB` (або більше, якщо є)
- **CPU:** мін. `2 cores` (рекомендовано 4)

### Крок 3: Диск
- **Тип:** VDI (VirtualBox Disk Image)
- **Режим:** Dynamically allocated
- **Розмір:** `30–50 GB`

---

## 💿 3. Підключення ISO

### Налаштування Storage
1. Вибери створену машину → **Settings** → **Storage**
2. У розділі **Controller: IDE** додай ISO:
   - Натисни на "Empty" → вибери завантажений ISO Linux Mint
3. Перевір **Boot Order:**
   - 1️⃣ **Optical (ISO)**
   - 2️⃣ **Hard Disk**

---

## 🚀 4. Інсталяція Linux Mint

### Запуск та встановлення
1. **Запусти VM** (кнопка **Start**)
2. **У вікні вибери** `Start Linux Mint`
3. **На робочому столі** клацни **Install Linux Mint**

### Кроки інсталяції
- **Мова:** `English` або `Українська`
- **Keyboard layout:** `English (US)` / `Ukrainian`
- **Install multimedia codecs:** ✅ (рекомендовано)
- **Installation type:** `Erase disk and install Linux Mint`
  > ⚠️ **Безпечно!** Це тільки для VM, не торкнеться основної системи
- **Create user:**
  - **Ім'я:** `devops` або `admin`
  - **Пароль:** `devops123` (або складніший)
  - **Hostname:** `linuxmint-devops`

---

## 🔄 5. Перший запуск

### Після інсталяції
1. **VM перезавантажиться** автоматично
2. **Вийми ISO:**
   - Settings → Storage → видали ISO з Optical Disk
3. **Залогінься** у свою систему

---

## ✅ 6. Верифікація роботи

### Тестування системи
Виконай у терміналі:

```bash
# Перевірка версії системи
lsb_release -a

# Перевірка IP-адреси (мережа працює)
ip a

# Перевірка оновлень
sudo apt update && sudo apt upgrade -y

# Перевірка доступу в інтернет
ping -c 4 google.com

# Перевірка вільнего місця
df -h
```

### Очікуваний результат
- ✅ Система показує версію Linux Mint
- ✅ IP-адреса відображається (наприклад: 192.168.1.100)
- ✅ Оновлення пройшли без помилок
- ✅ Ping до google.com працює
- ✅ Вільне місце на диску показується

---

## 🛠️ 7. Додаткові налаштування

### VirtualBox Guest Additions
```bash
# Встановлення Guest Additions для кращої роботи
sudo apt update
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
# Потім: Devices → Insert Guest Additions CD image
```

### Корисні пакети
```bash
# Встановлення базових інструментів
sudo apt install -y curl wget git vim nano htop tree
```

---

## 🆘 Якщо щось не працює

### Часті проблеми
- **VM не запускається:** перевір чи увімкнена віртуалізація в BIOS
- **Повільна робота:** збільш RAM до 4+ GB
- **Немає інтернету:** перевір налаштування мережі (NAT/Bridged)
- **Не встановлюється:** перевір чи правильно підключений ISO

### Відновлення
- **Створи Snapshot** перед експериментами
- **Віднови з Snapshot** якщо щось зламалося

---

## 🎯 Готово!

**Тепер у тебе є:**
- ✅ Робочий Linux Mint у VirtualBox
- ✅ Безпечне середовище для експериментів
- ✅ Підготовка до DevOps навчання

> 🚀 **Наступний крок:** Встановлення Docker та практика з Linux командами!
