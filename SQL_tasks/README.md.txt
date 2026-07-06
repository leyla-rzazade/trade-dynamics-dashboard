# 🗄️ Oracle HR Sxemi üzrə SQL Tapşırıqları və Həlləri

Bu qovluqda Oracle database **HR (Human Resources)** sxemi üzərində yerinə yetirilmiş fərqli mövzuları, analitik funksiyaları və çətinlik dərəcələrinə sahib tapşırıq variantlarının SQL sorğuları yer alır.

---

## 📂 Qovluğun Strukturu və Tapşırıq İndeksi

Bütün tapşırıqlar mövzularına və müəllim tərəfindən təqdim olunan variantlara əsasən aşağıdakı şəkildə qruplaşdırılmışdır:

### 1. 📌 [WHERE Mövzusu üzrə Tapşırıqlar](./02.Where_01_HR.LEYLA.docx)
*Məlumatların sadə və mürəkkəb şərtlər əsasında filtrlənməsi.*
- `WHERE` şərt operatorunun tətbiqi
- `LIKE`, `IN`, `IS NOT NULL`, `AND / OR` məntiqi operatorları
- Maaş hədləri və departament nömrələrinə görə süzgəcləmələr (10 Tapşırıq)

### 2. 📑 [Variant I - Mürəkkəb SQL Sorğuları](./03.Variant_I_HR.LEYLA.sql)
*Mətn funksiyaları, Şərtli məntiqlər (CASE WHEN) və Tarix funksiyaları.*
- `SUBSTR` və `INSTR` ilə sətirlərin manipulyasiyası
- `CASE WHEN` ilə dinamik maaş artımının hesablanması və reytinqlərin çıxarılması
- `LAST_DAY` və `ADD_MONTHS` funksiyaları ilə tarix hesablamaları (10 Tapşırıq)

### 📑 3. [Variant II - Analitik və İrəli Səviyyə Sorğular](./04.Variant_II_HR.LEYLA.sql)
*Analitik funksiyalar (Window Functions), Subquery (Alt sorğular) və JOIN əməliyyatları.*
- `DENSE_RANK() OVER (PARTITION BY ...)` funksiyası ilə departamentdaxili maaş reytinqləri
- `MAX() OVER` analitik funksiyasının şərti məntiqlərlə istifadəsi
- Çoxlu cədvəllərin (`EMPLOYEES`, `DEPARTMENTS`, `LOCATIONS`, `JOBS`) `LEFT JOIN` və `INNER JOIN` əlaqələri (10 Tapşırıq)

### 📊 4. [Əlavə Laboratoriya Tapşırıqları (Sorğu Birləşmələri)](./05.Lab_Tasks_HR.LEYLA.sql)
*`GROUP BY` aqreqat funksiyaları, `EXISTS`, `LISTAGG` və Alt sorğu nümunələri.*
- Ölkələr üzrə departament saylarının analizi (`HAVING COUNT(*) > 1`)
- Şəhərlərin adlarına görə qruplaşdırılaraq `LISTAGG` funksiyası ilə vergüllə sıralanması
- İkinci ən yüksək maaşın analitik funksiya ilə tapılması (10 Tapşırıq)

### 🧬 5. [SET Operatorları ilə İş](./06.Set_Operators_HR.LEYLA.sql)
*Çoxlu sorğuların nəticələrinin birləşdirilməsi və fərqinin tapılması.*
- `UNION ALL` operatoru vasitəsilə fərqli qruplaşmaların tək bir cədvəldə alt-alta birləşdirilməsi
- `MINUS` operatoru ilə `DEPARTMENTS` cədvəlində olub `EMPLOYEES`-də mövcud olmayan departamentlərin tapılması (4 Tapşırıq)

---

## 🛠️ İstifadə Olunan Əsas SQL Alətləri və Konseptlər

Bu tapşırıqlar toplusunda aşağıdakı SQL mexanizmlərindən peşəkar şəkildə istifadə edilmişdir:
*   **Aqreqat Funksiyaları:** `SUM()`, `COUNT()`, `AVG()`, `MAX()`
*   **Analitik (Window) Funksiyalar:** `DENSE_RANK()`, `OVER(PARTITION BY...)`
*   **Mətn və Məntiq Funksiyaları:** `SUBSTR()`, `INSTR()`, `LENGTH()`, `LOWER()`, `REGEXP_LIKE()`
*   **Tarix Manipulyasiyası:** `EXTRACT(MONTH FROM...)`, `TO_CHAR(..., 'DY')`, `ADD_MONTHS()`

---
*Hazırladı: Leyla R.*