Basic template on GULP
=====================

### Возможности
1. Минификация css
2. Добавление вендорных префиксов в css
3. Автоматическое обновление браузера
4. Минификация и конкатенация JavaScript
5. Оптимизация картинок
6. Создание спрайтов


**Процесс установки.**

1. Клонируем репозиторий
```js
git clone https://github.com/dmgame/template.git
```
2. Перейдите в склонированную папку или откройте е в редакторе кода
```js
cd template
```

3. Разворачивание проекта (установка всех модулей). У вас должен быть установлен nodejs и gulp глобально
```js
 npm up
```
---
**Запуск gulp**

1. Запуск gulp. Запустится таск default
```js
 gulp
```
---
***Установка gulp глобально `(если он не установлен)`***
1. Установите nodejs по ссылке [Nodejs](https://nodejs.org/uk/)

2. Установите gulp глобально
```js
npm i gulp -g
```
---
***Привяжите к своему репозиторию***
1. Создайте новый репозиторий на github

2. Подвяжите текущий template к своему репозиторию
```js
git remote set-url origin "ссылка на ваш репозиторий"
```
---


**Структура папок**

Название папок  | Содержание файла
----------------|----------------------
app             | Директория с готовым проектом
app/css         | Готовые стили к продакшену
app/js          | Готовый js к продакшену
app/img         | Готовые картинки к продакшену
app/fonts       | Шрифты
src             | Директория с исходными файлыми
src/css         | Исходные стили, здесь мы пишем наши стили и они будут конвертироваться в app/css
src/img         | Исходные картинки, они будут минифицироваться и перегоняться в app/img
src/js          | Исходный js будет минифицироваться и переносится в app/js
src/sprite      | Папка для нарезанных картинок под будущие спрайты, после конветрации попадут в app/img

---
**Используемые по модули**

```js
var gulp         = require('gulp'), // Подключаем Gulp
    browserSync  = require('browser-sync'), // Подключаем Browser Sync
    concat       = require('gulp-concat'), // Подключаем gulp-concat (для конкатенации файлов)
    uglify       = require('gulp-uglifyjs'), // Подключаем gulp-uglifyjs (для сжатия JS)
    cssnano      = require('gulp-cssnano'), // Подключаем пакет для минификации CSS
    rename       = require('gulp-rename'), // Подключаем библиотеку для переименования файлов
    imagemin     = require('gulp-imagemin'), // Подключаем библиотеку для работы с изображениями
    pngquant     = require('imagemin-pngquant'), // Подключаем библиотеку для работы с png
    cache        = require('gulp-cache'), // Подключаем библиотеку кеширования
    autoprefixer = require('gulp-autoprefixer'),// Подключаем библиотеку для автоматического добавления префиксов
    spritesmith = require('gulp.spritesmith'), // Подключение библиотеки для создания спрайтов
    merge = require('merge-stream');

```
**Все таски gulp file**


```js
gulp.task('css', function(){ // Создаем таск Sass
    return gulp.src('src/css/**/*.css') // Берем источник
        .pipe(autoprefixer(['last 15 versions', '> 1%', 'ie 8', 'ie 7'], { cascade: true })) // Создаем префиксы
        .pipe(gulp.dest('app/css')) // Выгружаем результата в папку app/css
        .pipe(browserSync.reload({stream: true})) // Обновляем CSS на странице при изменении
});

gulp.task('browser-sync', function() { // Создаем таск browser-sync
    browserSync({ // Выполняем browserSync
        server: { // Определяем параметры сервера
            baseDir: 'app' // Директория для сервера - app
        },
        notify: false // Отключаем уведомления
    });
});

gulp.task('sprite', function () { // Создаем таск sprite
    var spriteData = gulp.src('src/sprite/*.png').pipe(spritesmith({ // Настройка спрайта
        imgName: 'sprite.png',
        cssName: 'sprite.css'
    }));
    // return spriteData.pipe(gulp.dest('app/img/')); // выгружаем спрайты в папку img
    var imgStream = spriteData.img
        .pipe(gulp.dest('app/img/'));

    var cssStream = spriteData.css
        .pipe(gulp.dest('src/css/'));

    return merge(imgStream, cssStream);
});


gulp.task('scripts', function() {
    return gulp.src('src/js/**/*.js')
        .pipe(concat('plugins.min.js')) // Собираем их в кучу в новом файле plugins.min.js
        .pipe(uglify()) // Сжимаем JS файл
        .pipe(gulp.dest('app/js')); // Выгружаем в папку app/js
});

gulp.task('css-libs', ['css'], function() {
    return gulp.src('app/css/libs.css') // Выбираем файл для минификации
        .pipe(cssnano()) // Сжимаем
        .pipe(rename({suffix: '.min'})) // Добавляем суффикс .min
        .pipe(gulp.dest('app/css')); // Выгружаем в папку app/css
});

gulp.task('watch', ['browser-sync', 'css', 'scripts', 'sprite'], function() {
    gulp.watch('src/css/**/*.css', ['css']); // Наблюдение за css файлами в папке css
    gulp.watch('src/sprite/*.png', ['sprite']); // Наблюдение за папкой с картинками для спрайтов  папке sprite
    gulp.watch('app/*.html', browserSync.reload); // Наблюдение за HTML файлами в корне проекта
    gulp.watch('app/js/**/*.js', browserSync.reload);   // Наблюдение за JS файлами в папке js
});

gulp.task('img', function() {
    return gulp.src('src/img/**/*') // Берем все изображения из app
        .pipe(cache(imagemin({  // Сжимаем их с наилучшими настройками с учетом кеширования
            interlaced: true,
            progressive: true,
            svgoPlugins: [{removeViewBox: false}],
            use: [pngquant()]
        })))
        .pipe(gulp.dest('app/img')); // Выгружаем на продакшен
});


gulp.task('clear', function () {
    return cache.clearAll();
});

gulp.task('default', ['watch']);

```


**Используемые шрифты**

*Harmonia_regular // основной шрифт страницы
*Harmonia_black
*Harmonia_semibold
*Harmonia_bold
*Harmonia_italic
*Font-awesome

**Стандартные классы и компоненты**

.flex-container - присваивает дочерним элементам гипкий тип отображения

.align-center - выравнивание по центру перпендикулярной оси

.justify-sp-between - элементы располагаются по главной оси, распределяя свободное место между собой

---

.white-bg - белый цвет фона

.light-orange-bg - светлооранжевый цвет фона

.orange-bg - оранжевый цвет фона

.red-bg - красный цвет фона

.black-bg - черный цвет фона

.blue-bg - голубой цвет фона

.green-bg - зеленый цвет фона

.orange-text - оранжевый цвет текста

---

.header - шапка 

.top-header - верняя часть шапки

.bottom-header - нижняя часть шапки

.phone-list - контакты

.social-nav - ссылки на социальные сети

.default-section - стандартная секция

.load-more - секция для просмотра большего количества товаров

.logo - логотип сайта

.header-nav - навигация сайта

.users-controls - панель управления 

---

.footer-content - контент подвала

.footer-list - список в подвале

---

.subscribe-form - секция для подписки

.subscribe-input-group - поле для ввода с иконкой

.subscribe-input - форма для ввода почты
   
.subscribe-description - описание блока подписки

---

.title - стиль для заголовков

.title.big - заголовок с увеличеным шрифтом

.subtitle - подзаголовок

.checkbox-group - стили для checkbox-элементов

---

.item - секция с товаром

.item-img - изображение в секции с товарами

.item-description - стили для описания товара

.item-title - наименование товара

.item-price - стоимость товара

.more-info-on-hover - информация видна при наведении курсора на секцию с товаром

.size - возможные размеры товара 

.colors - возможные цвета товара

.item-controls - возможные действия с товаром

---

.btn - основные стили кнопки

.btn-default - стандарный цвет текста и фона для кнопок

.btn-radius - кнопка с закругленными углами

---

**Используемые плагины**

Проект разработан с использованием фреймворка Bootstrap. Для компоновки элементов использован CSS flexbox  — модуль макета гибкого контейнера.

Использованы такие плагины JQuery:
*Sticknav - с помощью этого плагина добавили "бургер", который скрывает навигацию на необходимых разрешенияхж
*Flexslider - слайдер на странице с товаром
*Formstyler - для стилизации селектов, чекбоксов, радиокнопок, файловых и числовых полей

Plugin - обеспечивает функционирование кнопки с боковым меню на странице с категориями товаров

$ ( function() {

    var sidebar_btn=$('.mobile-sidebar-btn');
    var main=$('main');
    var overlay=$('.overlay');

    sidebar_btn.on('click', function (e){
        main.toggleClass('show'); // при нажатии на кнопку переключает класс show на блоке main 
    }); 
    overlay.on('click', function (e){
        main.toggleClass('show'); // при нажатии вне зоны меню переключает класс show на блоке main
    });
        
});
