# Практическое задание №3
Журавлева Юлия БИСО-01-20

# Анализ данных сетевого трафика при помощи библиотеки Arrow

## Цель работы

1.  Изучить возможности технологии Apache Arrow для обработки и анализ
    больших данных
2.  Получить навыки применения Arrow совместно с языком программирования
    R
3.  Получить навыки анализа метаинфомации о сетевом трафике
4.  Получить навыки применения облачных технологий хранения, подготовки
    и анализа данных: Yandex Object Storage, Rstudio Server.

## Исходные данные

1.  Ноутбук с ОС Windows 10
2.  Apache Arrow
3.  Yandex Object Storage
4.  RStudio Server

## Задание

Используя язык программирования R, библиотеку arrow и облачную
`IDE Rstudio Server`, развернутую в `Yandex Cloud`, выполнить задания и
составить отчет.

## Ход работы

### 1. Настройка подключения к IDE Rstudio Server через ssh-туннель

-   Подключимся к удалённому серверу через ssh-туннель как пользователь
    user18.

<!-- -->

    PS C:\Users\Юлия> ssh -i "C:\Users\\Downloads\Telegram Desktop\rstudio.key" -L 8787:127.0.0.1:8787 user18@62.84.123.211

![](screen/1.png)

-   Поменяем пароль с дефолтного на персональный. Скриншот предоставлен
    с системы Kali Linux, так как первоначальная настройка была именно
    на нём, но из-за возникших проблем на ВМ, пришлось перейти на
    основную систему Windows.

![](screen/2.png)

-   Перейдём по адресу `http://127.0.0.1:8787` и зайдём под созданным
    пользователем `user18`.

![](screen/3.png)

-   Настроим Git на RStudio Server с помощью SSH ключа. Так как
    изначально установка была на ОС Kali Linux, то повторной
    аутентификации на ОС Windows не потребовалось.

![](screen/4.png)

Однако этого оказалось недостаточно для пуша в репозиторий, поэтому
после танцев с бубном, спасение нашлось в индивидуальном токене
пользователя на замену аутентификации по паролю.

![](screen/5.png)

### 2. Настройка рабочего пространства

-   Первым делом установим необходимые библиотеки

``` r
library(arrow)
```

    Some features are not enabled in this build of Arrow. Run `arrow_info()` for more information.


    Attaching package: 'arrow'

    The following object is masked from 'package:utils':

        timestamp

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ✔ purrr     1.0.2     

    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ lubridate::duration() masks arrow::duration()
    ✖ dplyr::filter()       masks stats::filter()
    ✖ dplyr::lag()          masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(dplyr)
```

-   Далее создаем директорию dataset и загружаем в нее dataframe
    arrow-datasets/tm_data.pqt из Yandex Object Storage.

``` r
dir.create("dataset")
```

    Warning in dir.create("dataset"): 'dataset' already exists

``` r
curl::multi_download("https://storage.yandexcloud.net/arrow-datasets/tm_data.pqt", "dataset/ya_dt.pqt",
  resume = TRUE
)
```

    # A tibble: 1 × 10
      success status_code resumefrom url    destfile error type  modified
      <lgl>         <int>      <dbl> <chr>  <chr>    <chr> <chr> <dttm>  
    1 TRUE            416          0 https… /home/u… <NA>  appl… NA      
    # ℹ 2 more variables: time <dbl>, headers <list>

-   Посмотрим содержимое нашего датасета, чтобы убедиться, что он
    работает.

``` r
full_df <- arrow::open_dataset(sources = "dataset/ya_dt.pqt", format  = "parquet")
full_df %>% glimpse()
```

    FileSystemDataset with 1 Parquet file
    105,747,730 rows x 5 columns
    $ timestamp <double> 1.578326e+12, 1.578326e+12, 1.578326e+12, 1.578326e+12, 1.5…
    $ src       <string> "13.43.52.51", "16.79.101.100", "18.43.118.103", "15.71.108…
    $ dst       <string> "18.70.112.62", "12.48.65.39", "14.51.30.86", "14.50.119.33…
    $ port       <int32> 40, 92, 27, 57, 115, 92, 65, 123, 79, 72, 123, 123, 22, 118…
    $ bytes      <int32> 57354, 11895, 898, 7496, 20979, 8620, 46033, 1500, 979, 103…
    Call `print()` for full schema details

-   Заметим, что с полем `timestamp` что-то не так, а именно указан
    неверный тип данных. Приведём его в более понятный вид.

``` r
full_df <- full_df %>% mutate(timestamp = as_datetime(timestamp / 1000, origin = "1970-01-01", tz = "UTC"))
full_df %>% glimpse()
```

    FileSystemDataset with 1 Parquet file (query)
    105,747,730 rows x 5 columns
    $ timestamp <timestamp[ns, tz=UTC]> 2020-01-06 16:41:53, 2020-01-06 16:41:53, 20…
    $ src                      <string> "18.53.88.102", "12.53.121.87", "19.67.28.67…
    $ dst                      <string> "14.51.30.86", "15.93.39.125", "13.41.79.125…
    $ port                      <int32> 118, 50, 118, 94, 29, 90, 50, 22, 102, 56, 7…
    $ bytes                     <int32> 67081, 867, 36669, 1130, 4406, 42, 1113, 645…
    Call `print()` for query details

## Обработка данных

### Задание 1: Найдите утечку данных из Вашей сети

***Поставленная задача***

Важнейшие документы с результатами нашей исследовательской деятельности
в области создания вакцин скачиваются в виде больших заархивированных
дампов. Один из хостов в нашей сети используется для пересылки этой
информации – он пересылает гораздо больше информации на внешние ресурсы
в Интернете, чем остальные компьютеры нашей сети. Определите его
IP-адрес.

-   Для начала определим IP адреса внутренней сети, которые начинаются с
    12-14 октетов.

``` r
df_inside <- full_df %>%
  filter(str_detect(src, "^1[2-4].")) %>%
  filter(!str_detect(dst, "^1[2-4]."))
```

-   Используя отфильтрованные данные внутреннего трафика, сгрупируем по
    IP-адресу источника и просуммируем его общее количество байтов.
    Выведем на экран итоговые значения источика с наибольшим количеством
    байтов.

``` r
sus_host <- df_inside %>% group_by(src) %>% 
  summarise(ins_sum = sum(bytes)) %>% arrange(desc(ins_sum)) %>% collect()

sus_host_1 <- sus_host %>% slice(1)

cat("IP-адрес подозрительного хоста:", sus_host_1$src, "\n", 
    "Сумма затраченного трафика (байт)", format(sus_host_1$ins_sum, scientific = FALSE))
```

    IP-адрес подозрительного хоста: 13.37.84.125 
     Сумма затраченного трафика (байт) 10625497574

### Задание 2: Найдите утечку данных 2

***Поставленная задача***

Другой атакующий установил автоматическую задачу в системном
планировщике `cron` для экспорта содержимого внутренней wiki системы.
Эта система генерирует большое количество трафика в нерабочие часы,
больше чем остальные хосты. Определите IP этой системы. Известно, что ее
IP адрес отличается от нарушителя из предыдущей задачи.

### Задание 3: Найдите утечку данных 3

***Поставленная задача***

Еще один нарушитель собирает содержимое электронной почты и отправляет в
Интернет используя порт, который обычно используется для другого типа
трафика. Атакующий пересылает большое количество информации используя
этот порт, которое нехарактерно для других хостов, использующих этот
номер порта. Определите IP этой системы. Известно, что ее IP адрес
отличается от нарушителей из предыдущих задач.

## Вывод

текст
