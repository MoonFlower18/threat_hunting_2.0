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

-   Первым делом установим необходимые билбиотеки

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

-   Далее скачаем необходимый для работы датасет.

## Обработка данных

Задание 1: Найдите утечку данных из Вашей сети Задание 2: Найдите утечку
данных 2 Задание 3: Найдите утечку данных 3

## Вывод

текст
