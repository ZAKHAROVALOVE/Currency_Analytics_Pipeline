# Currency Analytics Data Pipeline (Fabric, Frankfurter API)

## 1. Overview

This project is a **currency analytics data pipeline** built on **Microsoft Fabric**.

Мета проєкту:
- щодня завантажувати курси валют з публічного **Frankfurter API**;
- зберігати сирі дані в **OneLake (Raw Layer)**;
- виконувати очищення та агрегування в **Lakehouse (Curated Layer)**;
- завантажувати модель у **Fabric Warehouse**;
- будувати аналітику в **Power BI** (курси, динаміка, волатильність).

Основні сценарії:
- аналітика історичних курсів валют;
- моніторинг середньомісячних значень;
- оцінка волатильності за обраними валютами.

---

## 2. Architecture

Пайплайн побудований за шаровим підходом:

- **Ingestion (Raw Layer)**  
  - Fabric Data Pipeline: `pl_ingest_exchangerates`  
  - Джерело: `https://api.frankfurter.app/`  
  - Результат: JSON-файли в OneLake:
    - `Files/raw/exchange_rates/date=YYYY-MM-DD/base=USD/`

- **Transformation (Curated Layer)**  
  - Notebook: `nb_transform_exchangerates.ipynb`  
  - Логіка:
    - читання сирих JSON з Raw;
    - нормалізація структури;
    - приведення типів і назв колонок;
    - розрахунок базових агрегатів.
  - Результат:
    - таблиця `curated.exchange_rates`
    - опційно: `curated.exchange_rates_stats` (агрегації, статистики).

- **Serving (Warehouse Layer)**  
  - Warehouse: `wh_finsight_currency`  
  - Основні таблиці:
    - `DimCurrency`
    - `FactExchangeRates`

- **Reporting (Analytics Layer)**  
  - Power BI report (Direct Lake / DirectQuery до Warehouse)  
  - Основні показники:
    - середньомісячний курс;
    - % зміна від попереднього періоду;
    - волатильність;
    - тренд курсу в часі.

---

## 3. Prerequisites

Потрібні:

- Доступ до **Microsoft Fabric** (Workspace з роллю `Member` або вище).  
- Створений **Lakehouse**: `lh_finsight_currency`.  
- Створений **Warehouse**: `wh_finsight_currency`.  
- Створений **Fabric Data Pipeline**: `pl_ingest_exchangerates`.  
- Доступ до **Frankfurter API** (анонімний, без API-ключа).

---

## 4. How to run ingestion (Raw Layer)

Ingestion виконується через Fabric Data Pipeline `pl_ingest_exchangerates`.

1. Відкрити Workspace → Data Factory → Pipeline `pl_ingest_exchangerates`.
2. Натиснути **Run**.
3. Заповнити параметри:
   - `pDate` – дата в форматі `YYYY-MM-DD` (наприклад, `2024-01-01`);
   - `pBaseCurrency` – базова валюта, наприклад `USD`.
4. Після успішного запуску перевірити OneLake (Lakehouse Files):

   ```text
   Files/raw/exchange_rates/date=YYYY-MM-DD/base=USD/
       rates_YYYY-MM-DD_HHMMSS.json
