# 🗄️ Oracle HR Sxemi üzrə SQL Tapşırıqları və Həlləri

Bu qovluqda Oracle database **HR (Human Resources)** sxemi üzərində yerinə yetirilmiş fərqli çətinlik dərəcələrinə malik tapşırıq variantları, mürəkkəb cədvəl birləşmələri (**JOIN**), analitik funksiyalar və irəli səviyyə alt sorğular (**Subqueries**) yer alır.

---

## 📂 Qovluğun Strukturu və Tapşırıq İndeksi

Bütün sənədlər mövzularına və təqdim olunan imtahan/laboratoriya variantlarına əsasən aşağıdakı şəkildə indekslənmişdir:

### 1. 📌 [WHERE Mövzusu üzrə Tapşırıqlar](./02.Where_01_HR.LEYLA.docx)
*Məlumatların şərtlər əsasında filtrlənməsi və əsas SQL operatorlarının tətbiqi.*
- `WHERE` şərt operatorunun idarə olunması
- `LIKE`, `IN`, `IS NOT NULL`, `AND / OR` məntiqi filtrləmələri
- Maaş hədləri və müəyyən departamentlər üzrə işçi siyahılarının çıxarılması (10 Tapşırıq)

### 2. 📑 [Exam 1 - Mürəkkəb SQL, JOIN və Tarix Funksiyaları](./Exam1.txt)
*Cədvəllərin əlaqələndirilməsi, şərtli məntiqlər və sətir funksiyaları.*
- **`JOIN` strukturları:** `EMPLOYEES`, `DEPARTMENTS` və digər HR strukturlarının əlaqələndirilməsi
- `SUBSTR` və `INSTR` funksiyaları vasitəsilə mətn tipli dataların (məsələn, Email və Adlar) parçalanması və manipulyasiyası
- `CASE WHEN` ilə fərqli vəzifələr və maaş hədləri üçün dinamik bonusların hesablanması
- `LAST_DAY` və `ADD_MONTHS` funksiyaları ilə işçilərin maaş artım tarixlərinin analizi (8 Tapşırıq)

### 📑 3. [Exam 2 - İrəli Səviyyə JOIN, Alt Sorğular (Subquery) və LISTAGG](./Exam2.txt)
*Çətinlik dərəcəsi yüksək olan sorğuların subquery və analitik metodlarla həlli.*
- **Advanced Subqueries (Alt sorğular):** `JOIN` əməliyyatları ilə yanaşı, daxili şərtlərin alt sorğular (Subquery) vasitəsilə idarə olunması və verilənlərin optimallaşdırılması
- `EXISTS` operatoru ilə digər cədvəllərdəki mövcudluq şərtinə görə əsas sorğunun filtrlənməsi
- `LISTAGG() WITHIN GROUP` funksiyası ilə regionlar üzrə şəhər adlarının sətirdə vergüllə qruplaşdırılaraq əlifba sırası ilə çıxarılması
- Mətn uzunluqlarının `LENGTH` ilə müqayisəsi və `GROUP BY / HAVING` strukturları (8 Tapşırıq)

### 📊 4. [Final - Analitik Funksiyalar, Subquery və Self-JOIN](./Final.txt)
*Window funksiyaları, daxili verilənlər çoxluğu və idarəetmə zəncirinin analizi.*
- **Subquery ilə Filtrləmə:** Ümumi işçilərin orta maaşının yarısını (`SELECT AVG(salary)/2 FROM...`) alt sorğu kimi şərt daxilində işlədərək hesablama aparılması
- **`SELF JOIN` məntiqi:** Eyni cədvəli (`EMPLOYEES`) özü-özünə bağlayaraq həm işçi, həm də departament meneceri olan şəxslərin və onların yerləşdiyi ünvanların (`LOCATIONS`) tapılması
- `DENSE_RANK() OVER (PARTITION BY ... ORDER BY ...)` analitik funksiyası ilə vəzifə və departament üzrə maaş reytinqlərinin (Top 3) təyin edilməsi (10 Tapşırıq)

### 🧬 5. [SET Operatorları ilə İş](./SetOperators-Tasks.Leyla.txt)
*Çoxlu sorğuların nəticələrinin riyazi çoxluqlar kimi birləşdirilməsi.*
- `UNION ALL` operatoru ilə fərqli qruplaşdırılmış (departament və vəzifə üzrə) maaş cəmlərinin eyni cədvəldə alt-alta göstərilməsi
- `MINUS` operatoru vasitəsilə `DEPARTMENTS` cədvəlində olub `EMPLOYEES` cədvəlində heç bir işçisi olmayan departament ID-lərinin analizi (4 Tapşırıq)

---

## 🛠️ İstifadə Olunan Əsas SQL Alətləri və Konseptlər

Bu layihədə verilənlər bazasının idarə olunması üçün aşağıdakı qabaqcıl SQL konseptlərindən istifadə edilmişdir:
* **Alt Sorğular & Operatorlar:** `Subqueries (Inline Views)`, `EXISTS`, `IN`
* **Cədvəl Birləşmələri (JOINs):** `INNER JOIN`, `LEFT OUTER JOIN`, `SELF JOIN`
* **Analitik (Window) Funksiyalar:** `DENSE_RANK() OVER()`, `MAX() OVER()`
* **Çoxluq Operatorları:** `UNION ALL`, `MINUS`
* **Aqreqasiya & Qruplaşdırma:** `SUM()`, `COUNT()`, `AVG()`, `GROUP BY`, `HAVING`
* **String & Data Funksiyaları:** `SUBSTR()`, `INSTR()`, `LENGTH()`, `LISTAGG()`, `CASE WHEN`, `ADD_MONTHS()`

---
*Hazırladı: Leyla R.*
