<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d7/RPM_logo.svg/1200px-RPM_logo.svg.png" alt="Banner" width="30%">
</p>

## ![Lesson](https://img.shields.io/badge/Lesson-otus__rpm-0A84FF?style=for-the-badge&logo=redhat&logoColor=white&labelColor=111827)![Author](https://img.shields.io/badge/Author-Kamil%20Ibragimov-10B981?style=for-the-badge&logo=github&logoColor=white&labelColor=111827)![Date](https://img.shields.io/badge/Date-14.12.2025-F59E0B?style=for-the-badge&logo=calendar&logoColor=white&labelColor=111827)

### 📌 Задание
1. Создать свой RPM-пакет (приложение или скрипт).
2. Создать свой репозиторий и разместить там собранный RPM.
3. Реализовать раздачу пакета через веб-сервер (Nginx).

### ✅ Результат
- [x] RPM-пакет `otus-package-1.0-1` собран вручную.
- [x] Локальный репозиторий создан
- [x] Пакет доступен для скачивания через Nginx. Результат см. на скриншоте 🖼️ ["repo_check.png"](repo_check.png)

### 🧭 Оглавление
- [🧰 Шаг 1 - Подготовка SPEC](#one)
- [🧰 Шаг 2 - Сборка пакета](#two)
- [🧰 Шаг 3 - Создание репозитория](#three)
- [🧰 Шаг 4 - Настройка Nginx](#four)

---

<a id="one"></a>
## 🧰 Шаг 1 - Подготовка SPEC

Создаем манифест пакета `otus.spec`

```bash
Name:           otus-package
Version:        1.0
Release:        1%{?dist}
Summary:        Otus Homework
License:        GPL
Source0:        otus-script.sh
BuildArch:      noarch
Requires:       bash

%description
Otus homework package.

%install
mkdir -p %{buildroot}/usr/bin/
install -m 755 %{SOURCE0} %{buildroot}/usr/bin/otus-date

%files
/usr/bin/otus-date
Requires:       bash

%description
Otus homework package.

%install
mkdir -p %{buildroot}/usr/bin/
install -m 755 %{SOURCE0} %{buildroot}/usr/bin/otus-date

%files
/usr/bin/otus-date
```

<a id="two"></a>
🧰 Шаг 2 - Сборка пакета

```bash
rpmdev-setuptree
rpmbuild -bb ~/rpmbuild/SPECS/otus.spec
```

<a id="three"></a>
🧰 Шаг 3 - Создание репозитория

```bash
mkdir -p /usr/share/nginx/html/repo
cp ~/rpmbuild/RPMS/noarch/*.rpm /usr/share/nginx/html/repo/
createrepo /usr/share/nginx/html/repo/
```

<a id="four"></a>
🧰 Шаг 4 - Настройка Nginx
Конфигурация /etc/nginx/conf.d/repo.conf для отображения списка файлов:

```bash
Nginx

server {
    listen 80;
    server_name _;
    root /usr/share/nginx/html;
    
    location /repo/ {
        autoindex on;
    }
}

# Проверяем доступность репозитория
curl -I http://localhost/repo/repodata/repomd.xml
