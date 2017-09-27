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

Default components and classes (style.css)
==========================================

###Default classes:
Changes value of 'display' option to 'flex'
```css
.flex-container{}
```
Aligns flex items center 
```css
.align-center {}
```
Justifies flex items with equal distance between them
```css
.justify-sp-between {}
```
Changes value of color option to 'orange' color from the psd-template
```css
.orange-text {}
```
Changes value of background-color to 'red' color from the psd-template
```css
.red-bg {}
```
Changes value of background-color to 'black' color from the psd-template
```css
.black-bg {}
```
Changes value of background-color to 'blue' color from the psd-template
```css
.blue-bg {}
```
Changes value of background-color to 'green' color from the psd-template
```css
.green-bg {}
```
Changes value of background-color to white color from the psd-template
```css
.white-bg {}
```
Changes value of background-color to 'light orange' color from the psd-template
```css
.light-orange-bg {}
```
Changes value of background-color to 'orange' color from the psd-template
```css
.orange-bg {}
```
Creates button default styles:
* resets line-height to 'normal' value;
* sets orange background color;
* removes borders;
* removes border-radius;
* sets white fonts color
* sets Harmonia_semibold font family

```css
.btn-default {}
```
Changes background color on hover state to lighter orange and white font color 
```css
.btn-default:hover {}
```
Sets font size to 14px,  padding top/bottom to 16px and left/right to 30px
```css
.btn-rectangle {}
```
Sets font size to 18px, padding top/bottom to 16px and left/right 23px
```css
.btn-radius {}
```
Creates a grey side-border and aligns center vertically
```css
.border {}
```
###Defalts components:
The wrap block of header. It has position relative option, consists of .top-header and .bottom-header
```css
.header {}
```
Section that contains email, phone and social links
```css
header .top-header {}
```
Section contains logo, navigation links and user-controls buttons
```css
.bottom-header{}
```
Default section on the page with padding top/bottom 60px and left/right 0px
```css
.default-section {}
```
Displays custom icons in line
```css
.custom-icon {}
```
Default title class on the page, with 'Harmonia_black' font, in uppercase state, font size is 36px and margin bottom is 25px 
```css
.title {}
```
Title with 52px font size 
```css
.title.big {}
```
Default subtitle on the page. Displays block, 16px font size and margin bottom is 80px
```css
.subtitle {
    display: block;
    font-size: 16px;
    color: #495662;
    margin-bottom: 80px;
}
```
Block contains photo of the product, title and price.
In hover state .more-info-on-hover{} appears. It's and additional info with available colors, share, like, and add to cart buttons
```css
.product-item {}
```

```css
.product-item:hover {}
```
Button for showing more product items
```css
.show-more-dots {}
```
Section with input field for email and button 'Subscribe'
```css
.subscribe-form {}
```
Common footer section consists of logo, copy rights text, navigation links and section with payment methods 
```css
footer {}
```

