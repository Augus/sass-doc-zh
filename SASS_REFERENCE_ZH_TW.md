# Sass （Syntactically Awesome StyleSheets 語法超讚樣式表）

* 目錄
{:toc}

Sass 是讓 CSS 基礎語法更加強大、優雅的擴充版本。它允許你使用 [變數](#variables_), [巢狀規則](#nested_rules)、
[混搭(mixins)](#mixins)、[直接匯入(inline import)](#import) 等眾多功能，並且完全相容 CSS 既有語法。Sass 有助於保持大型樣式表的結構嚴謹，同時也讓人能夠快速上手小型樣式表，特別是在搭配 [Compass 函式庫](http://compass-style.org)使用的時候。

## 特色

* 完全相容 CSS3
* 變數、巢狀、混搭(mixins)等語法擴充
* 許多操控色彩與其他數值的{Sass::Script::Functions 好用函式}
* 進階功能，像是提供函式庫使用的[控制指令](#control_directives)
* 符合語法、客制化的輸出
* [Firebug 整合](https://addons.mozilla.org/en-US/firefox/addon/103988)

## 語法

Sass 有兩種語法。
第一種稱為 SCSS (Sassy CSS)，是一個 CSS3 語法的擴充版本，這整份參考資料都使用本語法。
這代表著所有符合 CSS3 語法的樣式表也都是符合語法並且意義相同的 SCSS 文件。
此外，SCSS 瞭解大多數 CSS hacks 以及瀏覽器專屬語法，像是 [IE 的舊版 `filter` 語法](http://msdn.microsoft.com/en-us/library/ms533754%28VS.85%29.aspx)。
這種語法提供的 Sass 強化特色如下面所述。
使用本語法的文件，副檔名是 `.scss` 結尾。

第二種比較舊的語法稱為縮排語法（indented syntax，或是就稱作 "Sass"），提供一種更簡潔的 CSS 撰寫方式。
它不使用括號，透過縮排來表示選擇符(selectors)的巢狀階層，也捨棄分號，用換行來隔開屬性(properties)。
有些人發現，這樣比 SCSS 更易讀好寫。
縮排語法有著所有相同的特色，雖然有些語法上稍有差異。這些差異在{file:INDENTED_SYNTAX.md 縮排語法參考資料}都有描述。
使用本語法的文件，副檔名是 `.sass` 結尾。

任一種語法都可以[匯入](#import)用另一種語法撰寫的文件。
只要使用 `sass-convert` 命令列工具，就可以將文件自動轉換成另一種語法：

    # 把 Sass 轉換成 SCSS
    $ sass-convert style.sass style.scss

    # 把 SCSS 轉換成 Sass
    $ sass-convert style.scss style.sass

## 使用 Sass

Sass 有三種使用方式：
作為命令列工具、獨立的 Ruby 模組，以及 Ruby on Rails 與 Merb 這些支援 Rack 的程式框架(framework)的外掛套件。
所有這些方式的第一步都是安裝 Sass gem：

    gem install sass

如果你使用 Windoes，你大概需要先 [安裝 Ruby](http://rubyinstaller.org/download.html)。

要在命令列執行 Sass，只要輸入

    sass input.scss output.css

你也可以命令 Sass 監看文件，並且在每次 Sass 文件修改後更新 CSS：

    sass --watch input.scss:output.css

如果你的目錄裡有許多 Sass 文件，你也可以命令 Sass 監看整個目錄：

    sass --watch app/sass:public/stylesheets

使用 `sass --help` 列出完整文件。

在 Ruby 程式碼中使用 Sass 非常容易。
安裝完 Sass gem 之後，你可以執行 `require "sass"`，然後像這樣使用 {Sass::Engine}：

    engine = Sass::Engine.new("#main {background-color: #0000ff}", :syntax => :scss)
    engine.render #=> "#main { background-color: #0000ff; }\n"

### Rack/Rails/Merb 外掛套件

要在 Rails 3 之前的版本啟用 Sass，請把這一行加到 `environment.rb`：

    config.gem "sass"

對於 Rails 3，則是把這一行加到 Gemfile：

    gem "sass"

要在 Merb 裡啟用 Sass，請把這一行加到 `config/dependencies.rb`：

    dependency "merb-haml"

要在一個 Rack 應用程式裡啓用 Sass，在 `config.ru` 加上

    require 'sass/plugin/rack'
    use Sass::Plugin::Rack

Sass 樣式表跟視圖(views)的運作方式不同。
它並沒有動態的內容，所以 CSS 只需要在 Sass 文件更新時產生。

默認情況下，`.sass` 跟 `.scss` 文件是放在 public/stylesheets/sass 目錄（這可以用 [`:template_location`](#template_location-option) 選項客制化）。然後在必要時，他們會被編譯成相應的 CSS 文件放到 public/stylesheets。例如，public/stylesheets/sass/main.scss 會被編譯成 public/stylesheets/main.css。

### 快取(Caching)

默認情況下，Sass 會快取編譯過的樣板與 [partials](#partials)。
這會明顯加快大批 Sass 文件的重新編譯速度，並且在 Sass 樣板被切割成分散文件，再透過 [`@import`](#import) 匯入成一個大文件的時候效果最好。

在沒有使用框架的情況，Sass 會把快取樣板放在 `.sass-cache` 目錄。
在 Rails 與 Merb，它們會放在 `tmp/sass-cache`。
這個目錄可以用 [`:cache_location`](#cache_location-option) 選項客制化。

如果你完全不希望 Sass 使用快取，就把 [`:cache`](#cache-option) 選項設為 `false`.

### 選項

Options can be set by setting the {Sass::Plugin::Configuration#options Sass::Plugin#options} hash
in `environment.rb` in Rails or `config.ru` in Rack...

    Sass::Plugin.options[:style] = :compact

...or by setting the `Merb::Plugin.config[:sass]` hash in `init.rb` in Merb...

    Merb::Plugin.config[:sass][:style] = :compact

...or by passing an options hash to {Sass::Engine#initialize}.
All relevant options are also available via flags
to the `sass` and `scss` command-line executables.
Available options are:

{#style-option} `:style`
: Sets the style of the CSS output.
  See [Output Style](#output_style).

{#syntax-option} `:syntax`
: The syntax of the input file, `:sass` for the indented syntax
  and `:scss` for the CSS-extension syntax.
  This is only useful when you're constructing {Sass::Engine} instances yourself;
  it's automatically set properly when using {Sass::Plugin}.
  Defaults to `:sass`.

{#property_syntax-option} `:property_syntax`
: Forces indented-syntax documents to use one syntax for properties.
  If the correct syntax isn't used, an error is thrown.
  `:new` forces the use of a colon or equals sign
  after the property name.
  例如： `color: #0f3`
  or `width: $main_width`.
  `:old` forces the use of a colon
  before the property name.
  例如： `:color #0f3`
  or `:width $main_width`.
  By default, either syntax is valid.
  This has no effect on SCSS documents.

{#cache-option} `:cache`
: Whether parsed Sass files should be cached,
  allowing greater speed. Defaults to true.

{#read_cache-option} `:read_cache`
: If this is set and `:cache` is not,
  only read the Sass cache if it exists,
  don't write to it if it doesn't.

{#cache_store-option} `:cache_store`
: If this is set to an instance of a subclass of {Sass::CacheStores::Base},
  that cache store will be used to store and retrieve
  cached compilation results.
  Defaults to a {Sass::CacheStores::Filesystem} that is
  initialized using the [`:cache_location` option](#cache_location-option).

{#never_update-option} `:never_update`
: Whether the CSS files should never be updated,
  even if the template file changes.
  Setting this to true may give small performance gains.
  It always defaults to false.
  Only has meaning within Rack, Ruby on Rails, or Merb.

{#always_update-option} `:always_update`
: Whether the CSS files should be updated every
  time a controller is accessed,
  as opposed to only when the template has been modified.
  Defaults to false.
  Only has meaning within Rack, Ruby on Rails, or Merb.

{#always_check-option} `:always_check`
: Whether a Sass template should be checked for updates every
  time a controller is accessed,
  as opposed to only when the server starts.
  If a Sass template has been updated,
  it will be recompiled and will overwrite the corresponding CSS file.
  Defaults to false in production mode, true otherwise.
  Only has meaning within Rack, Ruby on Rails, or Merb.

{#poll-option} `:poll`
: When true, always use the polling backend for {Sass::Plugin::Compiler#watch}
  rather than the native filesystem backend.

{#full_exception-option} `:full_exception`
: Whether an error in the Sass code
  should cause Sass to provide a detailed description
  within the generated CSS file.
  If set to true, the error will be displayed
  along with a line number and source snippet
  both as a comment in the CSS file
  and at the top of the page (in supported browsers).
  Otherwise, an exception will be raised in the Ruby code.
  Defaults to false in production mode, true otherwise.
  Only has meaning within Rack, Ruby on Rails, or Merb.

{#template_location-option} `:template_location`
: A path to the root sass template directory for your application.
  If a hash, `:css_location` is ignored and this option designates
  a mapping between input and output directories.
  May also be given a list of 2-element lists, instead of a hash.
  Defaults to `css_location + "/sass"`.
  Only has meaning within Rack, Ruby on Rails, or Merb.
  Note that if multiple template locations are specified, all
  of them are placed in the import path, allowing you to import
  between them.
  **Note that due to the many possible formats it can take,
  this option should only be set directly, not accessed or modified.
  Use the {Sass::Plugin::Configuration#template_location_array Sass::Plugin#template_location_array},
  {Sass::Plugin::Configuration#add_template_location Sass::Plugin#add_template_location},
  and {Sass::Plugin::Configuration#remove_template_location Sass::Plugin#remove_template_location} methods instead**.

{#css_location-option} `:css_location`
: The path where CSS output should be written to.
  This option is ignored when `:template_location` is a Hash.
  Defaults to `"./public/stylesheets"`.
  Only has meaning within Rack, Ruby on Rails, or Merb.

{#cache_location-option} `:cache_location`
: The path where the cached `sassc` files should be written to.
  Defaults to `"./tmp/sass-cache"` in Rails and Merb,
  or `"./.sass-cache"` otherwise.
  If the [`:cache_store` option](#cache_location-option) is set,
  this is ignored.

{#unix_newlines-option} `:unix_newlines`
: If true, use Unix-style newlines when writing files.
  Only has meaning on Windows, and only when Sass is writing the files
  (in Rack, Rails, or Merb, when using {Sass::Plugin} directly,
  or when using the command-line executable).

{#filename-option} `:filename`
: The filename of the file being rendered.
  This is used solely for reporting errors,
  and is automatically set when using Rack, Rails, or Merb.

{#line-option} `:line`
: The number of the first line of the Sass template.
  Used for reporting line numbers for errors.
  This is useful to set if the Sass template is embedded in a Ruby file.

{#load_paths-option} `:load_paths`
: An array of filesystem paths or importers which should be searched
  for Sass templates imported with the [`@import`](#import) directive.
  These may be strings, `Pathname` objects, or subclasses of {Sass::Importers::Base}.
  This defaults to the working directory and, in Rack, Rails, or Merb,
  whatever `:template_location` is.
  The load path is also informed by {Sass.load_paths}
  and the `SASS_PATH` environment variable.

{#filesystem_importer-option} `:filesystem_importer`
: A {Sass::Importers::Base} subclass used to handle plain string load paths.
  This should import files from the filesystem.
  It should be a Class object inheriting from {Sass::Importers::Base}
  with a constructor that takes a single string argument (the load path).
  Defaults to {Sass::Importers::Filesystem}.

{#line_numbers-option} `:line_numbers`
: When set to true, causes the line number and file
  where a selector is defined to be emitted into the compiled CSS
  as a comment. Useful for debugging, especially when using imports
  and mixins.
  This option may also be called `:line_comments`.
  Automatically disabled when using the `:compressed` output style
  or the `:debug_info`/`:trace_selectors` options.

{#trace_selectors-option} `:trace_selectors`
: When set to true, emit a full trace of imports and mixins before
  each selector. This can be helpful for in-browser debugging of
  stylesheet imports and mixin includes. This option supersedes
  the `:line_comments` option and is superseded by the
  `:debug_info` option. Automatically disabled when using the
  `:compressed` output style.

{#debug_info-option} `:debug_info`
: When set to true, causes the line number and file
  where a selector is defined to be emitted into the compiled CSS
  in a format that can be understood by the browser.
  Useful in conjunction with [the FireSass Firebug extension](https://addons.mozilla.org/en-US/firefox/addon/103988)
  for displaying the Sass filename and line number.
  Automatically disabled when using the `:compressed` output style.

{#custom-option} `:custom`
: An option that's available for individual applications to set
  to make data available to {Sass::Script::Functions custom Sass functions}.

{#quiet-option} `:quiet`
: When set to true, causes warnings to be disabled.

### 語法選擇

Sass 命令列工具會根據文件副檔名判斷你使用的語法，但是有時候未必有檔名這回事。`sass` 命令列程式預設採用縮排語法，但是如果你需要輸入的資料用 SCSS 語法解析，可以傳入 `--scss` 選項。
另外，你可以使用 `scss` 命令列程式，它跟 `sass` 程式完全一模一樣，不過預設採用的語法是 SCSS。

### 編碼

When running on Ruby 1.9 and later, Sass is aware of the character encoding of documents
and will handle them the same way that CSS would.
By default, Sass assumes that all stylesheets are encoded
using whatever coding system your operating system defaults to.
For many users this will be `UTF-8`, the de facto standard for the web.
For some users, though, it may be a more local encoding.

If you want to use a different encoding for your stylesheet
than your operating system default,
you can use the `@charset` declaration just like in CSS.
Add `@charset "encoding-name";` at the beginning of the stylesheet
(before any whitespace or comments)
and Sass will interpret it as the given encoding.
Note that whatever encoding you use, it must be convertible to Unicode.

Sass will also respect any Unicode BOMs and non-ASCII-compatible Unicode encodings
[as specified by the CSS spec](http://www.w3.org/TR/CSS2/syndata.html#charset),
although this is *not* the recommended way
to specify the character set for a document.
Note that Sass does not support the obscure `UTF-32-2143`,
`UTF-32-3412`, `EBCDIC`, `IBM1026`, and `GSM 03.38` encodings,
since Ruby does not have support for them
and they're highly unlikely to ever be used in practice.

#### Output Encoding

In general, Sass will try to encode the output stylesheet
using the same encoding as the input stylesheet.
In order for it to do this, though, the input stylesheet must have a `@charset` declaration;
otherwise, Sass will default to encoding the output stylesheet as UTF-8.
In addition, it will add a `@charset` declaration to the output
if it's not plain ASCII.

When other stylesheets with `@charset` declarations are `@import`ed,
Sass will convert them to the same encoding as the main stylesheet.

Note that Ruby 1.8 does not have good support for character encodings,
and so Sass behaves somewhat differently when running under it than under Ruby 1.9 and later.
In Ruby 1.8, Sass simply uses the first `@charset` declaration in the stylesheet
or any of the other stylesheets it `@import`s.

## CSS 擴充版本

### 巢狀規則 Nested Rules

Sass 允許 CSS 規則被嵌套在其他規則裡。
內層規則只適用在外層規則的選擇符上。
例如：

    #main p {
      color: #00ff00;
      width: 97%;

      .redbox {
        background-color: #ff0000;
        color: #000000;
      }
    }

被編譯成：

    #main p {
      color: #00ff00;
      width: 97%; }
      #main p .redbox {
        background-color: #ff0000;
        color: #000000; }

這有助於避免重複的父選擇符，
並且讓有許多巢狀選擇符的複雜 CSS 佈局變得更簡單。
例如：

    #main {
      width: 97%;

      p, div {
        font-size: 2em;
        a { font-weight: bold; }
      }

      pre { font-size: 3em; }
    }

被編譯成：

    #main {
      width: 97%; }
      #main p, #main div {
        font-size: 2em; }
        #main p a, #main div a {
          font-weight: bold; }
      #main pre {
        font-size: 3em; }

### 參考父選擇符：`&`

有時候提供其他途徑來使用巢狀語法的父選擇符是非常有幫助的。
例如，你可能會希望在選擇符被滑鼠停留、或是當 body 元件有特定類別的時候，給予選擇符特定的樣式
在這種情況下，你可以用 `&` 字元來明確指出父選擇符應該放在哪裡。
例如：

    a {
      font-weight: bold;
      text-decoration: none;
      &:hover { text-decoration: underline; }
      body.firefox & { font-weight: normal; }
    }

被編譯成：

    a {
      font-weight: bold;
      text-decoration: none; }
      a:hover {
        text-decoration: underline; }
      body.firefox a {
        font-weight: normal; }

`&` 在編譯成 CSS 時，會被父選擇符置換掉。
這代表說，要是你有一個深層的巢狀規則，父選擇符也會被完整解析，再置換掉 `&`。
例如：

    #main {
      color: black;
      a {
        font-weight: bold;
        &:hover { color: red; }
      }
    }

被編譯成：

    #main {
      color: black; }
      #main a {
        font-weight: bold; }
        #main a:hover {
          color: red; }

### 巢狀屬性

CSS 有相當多屬性都有「命名空間(namespace)」，像是 `font-family`、`font-size`、`font-weight` 都屬於 `font` 命名空間。
在 CSS 裡，如果你要設定屬於相同命名空間的一大堆屬性，你得要每次一個個輸入。
Sass 提供了一個捷徑：只要寫一次命名空間，再用巢狀語法包住每個子屬性。
例如：

    .funky {
      font: {
        family: fantasy;
        size: 30em;
        weight: bold;
      }
    }

被編譯成：

    .funky {
      font-family: fantasy;
      font-size: 30em;
      font-weight: bold; }

命名空間的屬性本身也可以有值。
例如：

    .funky {
      font: 2px/3px {
        family: fantasy;
        size: 30em;
        weight: bold;
      }
    }

被編譯成：

    .funky {
      font: 2px/3px;
        font-family: fantasy;
        font-size: 30em;
        font-weight: bold; }

### 佔位選擇符：`%foo`

Sass 支援一種叫做「佔位選擇符」的特殊選擇符。
它們看起來就像類別與 id 選擇符，只是 `#` 或 `.` 被 `%` 所取代。
它們必須要配合 [`@extend` 指令](#extend) 使用。
更多資訊請見 [`@extend` 獨享的選擇符](#placeholders).

佔位選擇符如果沒有被 `@extend` 用到，它們裡面的規則將不會輸出成 CSS。

## 註解：`/* */` 與 `//` {#comments}

Sass 支援使用 `/* */` 的標準多行 CSS 註解，以及使用 `//` 的單行註解。
多行註解會在 CSS 輸出裡盡可能地保留，單行注解則會被移除。
例如：

    /* This comment is
     * several lines long.
     * since it uses the CSS comment syntax,
     * it will appear in the CSS output. */
    body { color: black; }

    // These comments are only one line long each.
    // They won't appear in the CSS output,
    // since they use the single-line comment syntax.
    a { color: green; }

被編譯成：

    /* This comment is
     * several lines long.
     * since it uses the CSS comment syntax,
     * it will appear in the CSS output. */
    body {
      color: black; }

    a {
      color: green; }

當註解的第一個字元是 `!` 的時候，即使是在壓縮輸出模式(compressed output mode)，這段註解也會被添加到 CSS 輸出結果裡。
當你需要在生成的 CSS 裡添加版權聲明時，這個功能十分有用。

## SassScript {#sassscript}

除了純粹的 CSS 屬性語法之外，Sass 也支援一個名為 SassScript 的小型擴充語法集合。
SassScript 允許屬性使用變數、算術和額外的函式。
SassScript 可以用在任何屬性值。

SasScript 也可用於產生選擇符與屬性名稱，
這在撰寫 [mixins](#mixins) 時很有用。
這是靠 [插補(interpolation)](#interpolation_) 機制完成的。

### 互動式界面

你可以輕易地透過互動式界面來嘗試 SassScript。
要啓動此界面，就在執行 Sass 命令列時使用 `-i` 選項。
當提示符號出現時，輸入任何合法的 SassScript 表示式，就會執行並顯示

當提示出現時，輸入任何合法的 SassScript 表示式，就可以執行它，並且把結果印出來給你：

    $ sass -i
    >> "Hello, Sassy World!"
    "Hello, Sassy World!"
    >> 1px + 1px + 1px
    3px
    >> #777 + #777
    #eeeeee
    >> #777 + #888
    white

### 變數：`$` {#variables_}

SassScript 最簡單的使用方式就是操作變數。
變數是用美元符號開頭，並且設定方式就像 CSS 屬性：

    $width: 5em;

然後你就可以在設定屬性時提到它：

    #main {
      width: $width;
    }

變數只會在定義它們的巢狀選擇符內有效。
如果它們是在巢狀選擇符外被定義，那麼他們在任何地方都有效。

變數曾經使用 `!` 前置字元。這種用法仍然有效，但是已經被廢棄(deprecated)了，並且會印出警告訊息。`$` 是推薦的語法。

變數也曾經使用 `=` 而非 `:` 定義。這種用法也仍然有效，但是已經被廢棄，並且會印出警告訊息。`:` 是推薦的語法。

### Data Types

SassScript supports six main data types:

* numbers (e.g. `1.2`, `13`, `10px`)
* strings of text, with and without quotes (e.g. `"foo"`, `'bar'`, `baz`)
* colors (e.g. `blue`, `#04a3f9`, `rgba(255, 0, 0, 0.5)`)
* booleans (e.g. `true`, `false`)
* nulls (e.g. `null`)
* lists of values, separated by spaces or commas (e.g. `1.5em 1em 0 2em`, `Helvetica, Arial, sans-serif`)

SassScript also supports all other types of CSS property value,
such as Unicode ranges and `!important` declarations.
However, it has no special handling for these types.
They're treated just like unquoted strings.

#### Strings {#sass-script-strings}

CSS specifies two kinds of strings: those with quotes,
such as `"Lucida Grande"` or `'http://sass-lang.com'`,
and those without quotes, such as `sans-serif` or `bold`.
SassScript recognizes both kinds,
and in general if one kind of string is used in the Sass document,
that kind of string will be used in the resulting CSS.

There is one exception to this, though:
when using [`#{}` interpolation](#interpolation_),
quoted strings are unquoted.
This makes it easier to use e.g. selector names in [mixins](#mixins).
例如：

    @mixin firefox-message($selector) {
      body.firefox #{$selector}:before {
        content: "Hi, Firefox users!";
      }
    }

    @include firefox-message(".header");

被編譯成：

    body.firefox .header:before {
      content: "Hi, Firefox users!"; }

It's also worth noting that when using the [deprecated `=` property syntax](#sassscript),
all strings are interpreted as unquoted,
regardless of whether or not they're written with quotes.

#### Lists

Lists are how Sass represents the values of CSS declarations
like `margin: 10px 15px 0 0` or `font-face: Helvetica, Arial, sans-serif`.
Lists are just a series of other values, separated by either spaces or commas.
In fact, individual values count as lists, too: they're just lists with one item.

On their own, lists don't do much,
but the [Sass list functions](Sass/Script/Functions.html#list-functions)
make them useful.
The {Sass::Script::Functions#nth nth function} can access items in a list,
the {Sass::Script::Functions#join join function} can join multiple lists together,
and the {Sass::Script::Functions#append append function} can add items to lists.
The [`@each` rule](#each-directive) can also add styles for each item in a list.

In addition to containing simple values, lists can contain other lists.
For example, `1px 2px, 5px 6px` is a two-item list
containing the list `1px 2px` and the list `5px 6px`.
If the inner lists have the same separator as the outer list,
you'll need to use parentheses to make it clear
where the inner lists start and stop.
For example, `(1px 2px) (5px 6px)` is also a two-item list
containing the list `1px 2px` and the list `5px 6px`.
The difference is that the outer list is space-separated,
where before it was comma-separated.

When lists are turned into plain CSS, Sass doesn't add any parentheses,
since CSS doesn't understand them.
That means that `(1px 2px) (5px 6px)` and `1px 2px 5px 6px`
will look the same when they become CSS.
However, they aren't the same when they're Sass:
the first is a list containing two lists,
while the second is a list containing four numbers.

Lists can also have no items in them at all.
These lists are represented as `()`.
They can't be output directly to CSS;
if you try to do e.g. `font-family: ()`, Sass will raise an error.
If a list contains empty lists or null values,
as in `1px 2px () 3px` or `1px 2px null 3px`,
the empty lists and null values will be removed
before the containing list is turned into CSS.

### Operations

All types support equality operations (`==` and `!=`).
In addition, each type has its own operations
that it has special support for.

#### Number Operations

SassScript supports the standard arithmetic operations on numbers
(`+`, `-`, `*`, `/`, `%`),
and will automatically convert between units if it can:

    p {
      width: 1in + 8pt;
    }

被編譯成：

    p {
      width: 1.111in; }

Relational operators
(`<`, `>`, `<=`, `>=`)
are also supported for numbers,
and equality operators
(`==`, `!=`)
are supported for all types.

##### Division and `/`
{#division-and-slash}

CSS allows `/` to appear in property values
as a way of separating numbers.
Since SassScript is an extension of the CSS property syntax,
it must support this, while also allowing `/` to be used for division.
This means that by default, if two numbers are separated by `/` in SassScript,
then they will appear that way in the resulting CSS.

However, there are three situations where the `/` will be interpreted as division.
These cover the vast majority of cases where division is actually used.
They are:

1. If the value, or any part of it, is stored in a variable.
2. If the value is surrounded by parentheses.
3. If the value is used as part of another arithmetic expression.

例如：

    p {
      font: 10px/8px;             // Plain CSS, no division
      $width: 1000px;
      width: $width/2;            // Uses a variable, does division
      height: (500px/2);          // Uses parentheses, does division
      margin-left: 5px + 8px/2px; // Uses +, does division
    }

被編譯成：

    p {
      font: 10px/8px;
      width: 500px;
      height: 250px;
      margin-left: 9px; }

If you want to use variables along with a plain CSS `/`,
you can use `#{}` to insert them.
例如：

    p {
      $font-size: 12px;
      $line-height: 30px;
      font: #{$font-size}/#{$line-height};
    }

被編譯成：

    p {
      font: 12px/30px; }

#### Color Operations

All arithmetic operations are supported for color values,
where they work piecewise.
This means that the operation is performed
on the red, green, and blue components in turn.
例如：

    p {
      color: #010203 + #040506;
    }

computes `01 + 04 = 05`, `02 + 05 = 07`, and `03 + 06 = 09`,
and 被編譯成：

    p {
      color: #050709; }

Often it's more useful to use {Sass::Script::Functions color functions}
than to try to use color arithmetic to achieve the same effect.

Arithmetic operations also work between numbers and colors,
also piecewise.
例如：

    p {
      color: #010203 * 2;
    }

computes `01 * 2 = 02`, `02 * 2 = 04`, and `03 * 2 = 06`,
and 被編譯成：

    p {
      color: #020406; }

Note that colors with an alpha channel
(those created with the {Sass::Script::Functions#rgba rgba}
or {Sass::Script::Functions#hsla hsla} functions)
must have the same alpha value in order for color arithmetic
to be done with them.
The arithmetic doesn't affect the alpha value.
例如：

    p {
      color: rgba(255, 0, 0, 0.75) + rgba(0, 255, 0, 0.75);
    }

被編譯成：

    p {
      color: rgba(255, 255, 0, 0.75); }

The alpha channel of a color can be adjusted using the
{Sass::Script::Functions#opacify opacify} and
{Sass::Script::Functions#transparentize transparentize} functions.
例如：

    $translucent-red: rgba(255, 0, 0, 0.5);
    p {
      color: opacify($translucent-red, 0.3);
      background-color: transparentize($translucent-red, 0.25);
    }

被編譯成：

    p {
      color: rgba(255, 0, 0, 0.9);
      background-color: rgba(255, 0, 0, 0.25); }

IE filters require all colors include the alpha layer, and be in
the strict format of #AABBCCDD. You can more easily convert the
color using the {Sass::Script::Functions#ie_hex_str ie_hex_str}
function.
例如：

    $translucent-red: rgba(255, 0, 0, 0.5);
    $green: #00ff00;
    div {
      filter: progid:DXImageTransform.Microsoft.gradient(enabled='false', startColorstr='#{ie-hex-str($green)}', endColorstr='#{ie-hex-str($translucent-red)}');
    }

被編譯成：

    div {
      filter: progid:DXImageTransform.Microsoft.gradient(enabled='false', startColorstr=#FF00FF00, endColorstr=#80FF0000);
    }

#### String Operations

The `+` operation can be used to concatenate strings:

    p {
      cursor: e + -resize;
    }

被編譯成：

    p {
      cursor: e-resize; }

Note that if a quoted string is added to an unquoted string
(that is, the quoted string is to the left of the `+`),
the result is a quoted string.
Likewise, if an unquoted string is added to a quoted string
(the unquoted string is to the left of the `+`),
the result is an unquoted string.
例如：

    p:before {
      content: "Foo " + Bar;
      font-family: sans- + "serif";
    }

被編譯成：

    p:before {
      content: "Foo Bar";
      font-family: sans-serif; }

By default, if two values are placed next to one another,
they are concatenated with a space:

    p {
      margin: 3px + 4px auto;
    }

被編譯成：

    p {
      margin: 7px auto; }

Within a string of text, #{} style interpolation can be used to
place dynamic values within the string:

    p:before {
      content: "I ate #{5 + 10} pies!";
    }

被編譯成：

    p:before {
      content: "I ate 15 pies!"; }

Null values are treated as empty strings for string interpolation:

    $value: null;
    p:before {
      content: "I ate #{$value} pies!";
    }

被編譯成：

    p:before {
      content: "I ate  pies!"; }

#### Boolean Operations

SassScript supports `and`, `or`, and `not` operators
for boolean values.

#### List Operations

Lists don't support any special operations.
Instead, they're manipulated using the
[list functions](Sass/Script/Functions.html#list-functions).

### Parentheses

Parentheses can be used to affect the order of operations:

    p {
      width: 1em + (2em * 3);
    }

被編譯成：

    p {
      width: 7em; }

### Functions

SassScript defines some useful functions
that are called using the normal CSS function syntax:

    p {
      color: hsl(0, 100%, 50%);
    }

被編譯成：

    p {
      color: #ff0000; }

#### Keyword Arguments

Sass functions can also be called using explicit keyword arguments.
The above example can also be written as:

    p {
      color: hsl($hue: 0, $saturation: 100%, $lightness: 50%);
    }

While this is less concise, it can make the stylesheet easier to read.
It also allows functions to present more flexible interfaces,
providing many arguments without becoming difficult to call.

Named arguments can be passed in any order, and arguments with default values can be omitted.
Since the named arguments are variable names, underscores and dashes can be used interchangeably.

See {Sass::Script::Functions} for a full listing of Sass functions and their argument names,
as well as instructions on defining your own in Ruby.

### Interpolation: `#{}` {#interpolation_}

You can also use SassScript variables in selectors
and property names using #{} interpolation syntax:

    $name: foo;
    $attr: border;
    p.#{$name} {
      #{$attr}-color: blue;
    }

被編譯成：

    p.foo {
      border-color: blue; }

It's also possible to use `#{}` to put SassScript into property values.
In most cases this isn't any better than using a variable,
but using `#{}` does mean that any operations near it
will be treated as plain CSS.
例如：

    p {
      $font-size: 12px;
      $line-height: 30px;
      font: #{$font-size}/#{$line-height};
    }

被編譯成：

    p {
      font: 12px/30px; }

### Variable Defaults: `!default`

You can assign to variables if they aren't already assigned
by adding the `!default` flag to the end of the value.
This means that if the variable has already been assigned to,
it won't be re-assigned,
but if it doesn't have a value yet, it will be given one.

例如：

    $content: "First content";
    $content: "Second content?" !default;
    $new_content: "First time reference" !default;

    #main {
      content: $content;
      new-content: $new_content;
    }

被編譯成：

    #main {
      content: "First content";
      new-content: "First time reference"; }

Variables with `null` values are treated as unassigned by !default:

    $content: null;
    $content: "Non-null content" !default;

    #main {
      content: $content;
    }

被編譯成：

    #main {
      content: "Non-null content"; }

## `@`-Rules and Directives {#directives}

Sass supports all CSS3 `@`-rules,
as well as some additional Sass-specific ones
known as "directives."
These have various effects in Sass, detailed below.
See also [control directives](#control_directives)
and [mixin directives](#mixins).

### `@import` {#import}

Sass extends the CSS `@import` rule
to allow it to import SCSS and Sass files.
All imported SCSS and Sass files will be merged together
into a single CSS output file.
In addition, any variables or [mixins](#mixins)
defined in imported files can be used in the main file.

Sass looks for other Sass files in the current directory,
and the Sass file directory under Rack, Rails, or Merb.
Additional search directories may be specified
using the [`:load_paths`](#load_paths-option) option,
or the `--load-path` option on the command line.

`@import` takes a filename to import.
By default, it looks for a Sass file to import directly,
but there are a few circumstances under which it will compile to a CSS `@import` rule:

* If the file's extension is `.css`.
* If the filename begins with `http://`.
* If the filename is a `url()`.
* If the `@import` has any media queries.

If none of the above conditions are met
and the extension is `.scss` or `.sass`,
then the named Sass or SCSS file will be imported.
If there is no extension,
Sass will try to find a file with that name and the `.scss` or `.sass` extension
and import it.

For example,

    @import "foo.scss";

or

    @import "foo";

would both import the file `foo.scss`,
whereas

    @import "foo.css";
    @import "foo" screen;
    @import "http://foo.com/bar";
    @import url(foo);

would all compile to

    @import "foo.css";
    @import "foo" screen;
    @import "http://foo.com/bar";
    @import url(foo);

It's also possible to import multiple files in one `@import`. 例如：

    @import "rounded-corners", "text-shadow";

would import both the `rounded-corners` and the `text-shadow` files.

Imports may contain `#{}` interpolation, but only with certain restrictions.
It's not possible to dynamically import a Sass file based on a variable;
interpolation is only for CSS imports.
As such, it only works with `url()` imports.
例如：

    $family: unquote("Droid+Sans");
    @import url("http://fonts.googleapis.com/css?family=\#{$family}");

would compile to

    @import url("http://fonts.googleapis.com/css?family=Droid+Sans");

#### Partials {#partials}

If you have a SCSS or Sass file that you want to import
but don't want to compile to a CSS file,
you can add an underscore to the beginning of the filename.
This will tell Sass not to compile it to a normal CSS file.
You can then import these files without using the underscore.

For example, you might have `_colors.scss`.
Then no `_colors.css` file would be created,
and you can do

    @import "colors";

and `_colors.scss` would be imported.

Note that you may not include a partial and a non-partial with the same name in
the same directory. For example, `_colors.scss` may not exist alongside
`colors.scss`.

#### Nested `@import` {#nested-import}

Although most of the time it's most useful to just have `@import`s
at the top level of the document,
it is possible to include them within CSS rules and `@media` rules.
Like a base-level `@import`, this includes the contents of the `@import`ed file.
However, the imported rules will be nested in the same place as the original `@import`.

For example, if `example.scss` contains

    .example {
      color: red;
    }

then

    #main {
      @import "example";
    }

would compile to

    #main .example {
      color: red;
    }

Directives that are only allowed at the base level of a document,
like `@mixin` or `@charset`, are not allowed in files that are `@import`ed
in a nested context.

It's not possible to nest `@import` within mixins or control directives.

### `@media` {#media}

`@media` directives in Sass behave just like they do in plain CSS,
with one extra capability: they can be nested in CSS rules.
If a `@media` directive appears within a CSS rule,
it will be bubbled up to the top level of the stylesheet,
putting all the selectors on the way inside the rule.
This makes it easy to add media-specific styles
without having to repeat selectors
or break the flow of the stylesheet.
例如：

    .sidebar {
      width: 300px;
      @media screen and (orientation: landscape) {
        width: 500px;
      }
    }

被編譯成：

    .sidebar {
      width: 300px; }
      @media screen and (orientation: landscape) {
        .sidebar {
          width: 500px; } }

`@media` queries can also be nested within one another.
The queries will then be combined using the `and` operator.
例如：

    @media screen {
      .sidebar {
        @media (orientation: landscape) {
          width: 500px;
        }
      }
    }

被編譯成：

    @media screen and (orientation: landscape) {
      .sidebar {
        width: 500px; } }

Finally, `@media` queries can contain SassScript expressions (including
variables, functions, and operators) in place of the feature names and feature
values. 例如：

    $media: screen;
    $feature: -webkit-min-device-pixel-ratio;
    $value: 1.5;

    @media #{$media} and ($feature: $value) {
      .sidebar {
        width: 500px;
      }
    }

被編譯成：

    @media screen and (-webkit-min-device-pixel-ratio: 1.5) {
      .sidebar {
        width: 500px; } }

### `@extend` {#extend}

There are often cases when designing a page
when one class should have all the styles of another class,
as well as its own specific styles.
The most common way of handling this is to use both the more general class
and the more specific class in the HTML.
For example, suppose we have a design for a normal error
and also for a serious error. We might write our markup like so:

    <div class="error seriousError">
      Oh no! You've been hacked!
    </div>

And our styles like so:

    .error {
      border: 1px #f00;
      background-color: #fdd;
    }
    .seriousError {
      border-width: 3px;
    }

Unfortunately, this means that we have to always remember
to use `.error` with `.seriousError`.
This is a maintenance burden, leads to tricky bugs,
and can bring non-semantic style concerns into the markup.

The `@extend` directive avoids these problems
by telling Sass that one selector should inherit the styles of another selector.
例如：

    .error {
      border: 1px #f00;
      background-color: #fdd;
    }
    .seriousError {
      @extend .error;
      border-width: 3px;
    }

This means that all styles defined for `.error`
are also applied to `.seriousError`,
in addition to the styles specific to `.seriousError`.
In effect, everything with class `.seriousError` also has class `.error`.

Other rules that use `.error` will work for `.seriousError` as well.
For example, if we have special styles for errors caused by hackers:

    .error.intrusion {
      background-image: url("/image/hacked.png");
    }

Then `<div class="seriousError intrusion">`
will have the `hacked.png` background image as well.

#### How it Works

`@extend` works by inserting the extending selector (e.g. `.seriousError`)
anywhere in the stylesheet that the extended selector (.e.g `.error`) appears.
Thus the example above:

    .error {
      border: 1px #f00;
      background-color: #fdd;
    }
    .error.intrusion {
      background-image: url("/image/hacked.png");
    }
    .seriousError {
      @extend .error;
      border-width: 3px;
    }

被編譯成：

    .error, .seriousError {
      border: 1px #f00;
      background-color: #fdd; }

    .error.intrusion, .seriousError.intrusion {
      background-image: url("/image/hacked.png"); }

    .seriousError {
      border-width: 3px; }

When merging selectors, `@extend` is smart enough
to avoid unnecessary duplication,
so something like `.seriousError.seriousError` gets translated to `.seriousError`.
In addition, it won't produce selectors that can't match anything, like `#main#footer`.

#### Extending Complex Selectors

Class selectors aren't the only things that can be extended.
It's possible to extend any selector involving only a single element,
such as `.special.cool`, `a:hover`, or `a.user[href^="http://"]`.
例如：

    .hoverlink {
      @extend a:hover;
    }

Just like with classes, this means that all styles defined for `a:hover`
are also applied to `.hoverlink`.
例如：

    .hoverlink {
      @extend a:hover;
    }
    a:hover {
      text-decoration: underline;
    }

被編譯成：

    a:hover, .hoverlink {
      text-decoration: underline; }

Just like with `.error.intrusion` above,
any rule that uses `a:hover` will also work for `.hoverlink`,
even if they have other selectors as well.
例如：

    .hoverlink {
      @extend a:hover;
    }
    .comment a.user:hover {
      font-weight: bold;
    }

被編譯成：

    .comment a.user:hover, .comment .user.hoverlink {
      font-weight: bold; }

#### Multiple Extends

A single selector can extend more than one selector.
This means that it inherits the styles of all the extended selectors.
例如：

    .error {
      border: 1px #f00;
      background-color: #fdd;
    }
    .attention {
      font-size: 3em;
      background-color: #ff0;
    }
    .seriousError {
      @extend .error;
      @extend .attention;
      border-width: 3px;
    }

被編譯成：

    .error, .seriousError {
      border: 1px #f00;
      background-color: #fdd; }

    .attention, .seriousError {
      font-size: 3em;
      background-color: #ff0; }

    .seriousError {
      border-width: 3px; }

In effect, everything with class `.seriousError`
also has class `.error` *and* class `.attention`.
Thus, the styles defined later in the document take precedence:
`.seriousError` has background color `#ff0` rather than `#fdd`,
since `.attention` is defined later than `.error`.

Multiple extends can also be written using a comma-separated list of selectors.
For example, `@extend .error, .attention`
is the same as `@extend .error; @extend.attention`.

#### Chaining Extends

It's possible for one selector to extend another selector
that in turn extends a third.
例如：

    .error {
      border: 1px #f00;
      background-color: #fdd;
    }
    .seriousError {
      @extend .error;
      border-width: 3px;
    }
    .criticalError {
      @extend .seriousError;
      position: fixed;
      top: 10%;
      bottom: 10%;
      left: 10%;
      right: 10%;
    }

Now everything with class `.seriousError` also has class `.error`,
and everything with class `.criticalError` has class `.seriousError`
*and* class `.error`.
It's compiled to:

    .error, .seriousError, .criticalError {
      border: 1px #f00;
      background-color: #fdd; }

    .seriousError, .criticalError {
      border-width: 3px; }

    .criticalError {
      position: fixed;
      top: 10%;
      bottom: 10%;
      left: 10%;
      right: 10%; }

#### Selector Sequences

Selector sequences, such as `.foo .bar` or `.foo + .bar`, currently can't be extended.
However, it is possible for nested selectors themselves to use `@extend`.
例如：

    #fake-links .link {
      @extend a;
    }

    a {
      color: blue;
      &:hover {
        text-decoration: underline;
      }
    }

is compiled to

    a, #fake-links .link {
      color: blue; }
      a:hover, #fake-links .link:hover {
        text-decoration: underline; }

##### Merging Selector Sequences

Sometimes a selector sequence extends another selector that appears in another sequence.
In this case, the two sequences need to be merged.
例如：

    #admin .tabbar a {
      font-weight: bold;
    }
    #demo .overview .fakelink {
      @extend a;
    }

While it would technically be possible
to generate all selectors that could possibly match either sequence,
this would make the stylesheet far too large.
The simple example above, for instance, would require ten selectors.
Instead, Sass generates only selectors that are likely to be useful.

When the two sequences being merged have no selectors in common,
then two new selectors are generated:
one with the first sequence before the second,
and one with the second sequence before the first.
例如：

    #admin .tabbar a {
      font-weight: bold;
    }
    #demo .overview .fakelink {
      @extend a;
    }

被編譯成：

    #admin .tabbar a,
    #admin .tabbar #demo .overview .fakelink,
    #demo .overview #admin .tabbar .fakelink {
      font-weight: bold; }

If the two sequences do share some selectors,
then those selectors will be merged together
and only the differences (if any still exist) will alternate.
In this example, both sequences contain the id `#admin`,
so the resulting selectors will merge those two ids:

    #admin .tabbar a {
      font-weight: bold;
    }
    #admin .overview .fakelink {
      @extend a;
    }

This 被編譯成：

    #admin .tabbar a,
    #admin .tabbar .overview .fakelink,
    #admin .overview .tabbar .fakelink {
      font-weight: bold; }

#### `@extend`-Only Selectors {#placeholders}

Sometimes you'll write styles for a class
that you only ever want to `@extend`,
and never want to use directly in your HTML.
This is especially true when writing a Sass library,
where you may provide styles for users to `@extend` if they need
and ignore if they don't.

If you use normal classes for this, you end up creating a lot of extra CSS
when the stylesheets are generated, and run the risk of colliding with other classes
that are being used in the HTML.
That's why Sass supports "placeholder selectors" (for example, `%foo`).

Placeholder selectors look like class and id selectors,
except the `#` or `.` is replaced by `%`.
They can be used anywhere a class or id could,
and on their own they prevent rulesets from being rendered to CSS.
例如：

    // This ruleset won't be rendered on its own.
    #context a%extreme {
      color: blue;
      font-weight: bold;
      font-size: 2em;
    }

However, placeholder selectors can be extended, just like classes and ids.
The extended selectors will be generated, but the base placeholder selector will not.
例如：

    .notice {
      @extend %extreme;
    }

被編譯成：

    #context a.notice {
      color: blue;
      font-weight: bold;
      font-size: 2em; }

#### The `!optional` Flag

Normally when you extend a selector, it's an error if that `@extend` doesn't
work. For example, if you write `a.important {@extend .notice}`, it's an error
if there are no selectors that contain `.notice`. It's also an error if the only
selector containing `.notice` is `h1.notice`, since `h1` conflicts with `a` and
so no new selector would be generated.

Sometimes, though, you want to allow an `@extend` not to produce any new
selectors. To do so, just add the `!optional` flag after the selector. For
example:

    a.important {
      @extend .notice !optional;
    }

#### `@extend` in Directives

There are some restrictions on the use of `@extend` within directives such as
`@media`. Sass is unable to make CSS rules outside of the `@media` block apply
to selectors inside it without creating a huge amount of stylesheet bloat by
copying styles all over the place. This means that if you use `@extend` within
`@media` (or other CSS directives), you may only extend selectors that appear
within the same directive block.

For example, the following works fine:

    @media print {
      .error {
        border: 1px #f00;
        background-color: #fdd;
      }
      .seriousError {
        @extend .error;
        border-width: 3px;
      }
    }

But this is an error:

    .error {
      border: 1px #f00;
      background-color: #fdd;
    }

    @media print {
      .seriousError {
        // INVALID EXTEND: .error is used outside of the "@media print" directive
        @extend .error;
        border-width: 3px;
      }
    }

Someday we hope to have `@extend` supported natively in the browser, which will
allow it to be used within `@media` and other directives.

### `@debug`

The `@debug` directive prints the value of a SassScript expression
to the standard error output stream.
It's useful for debugging Sass files
that have complicated SassScript going on.
例如：

    @debug 10em + 12em;

outputs:

    Line 1 DEBUG: 22em

### `@warn`

The `@warn` directive prints the value of a SassScript expression
to the standard error output stream.
It's useful for libraries that need to warn users of deprecations
or recovering from minor mixin usage mistakes.
There are two major distinctions between `@warn` and `@debug`:

1. You can turn warnings off with the `--quiet` command-line option
   or the `:quiet` Sass option.
2. A stylesheet trace will be printed out along with the message
   so that the user being warned can see where their styles caused the warning.

Usage Example:

    @mixin adjust-location($x, $y) {
      @if unitless($x) {
        @warn "Assuming #{$x} to be in pixels";
        $x: 1px * $x;
      }
      @if unitless($y) {
        @warn "Assuming #{$y} to be in pixels";
        $y: 1px * $y;
      }
      position: relative; left: $x; top: $y;
    }

## Control Directives

SassScript supports basic control directives
for including styles only under some conditions
or including the same style several times with variations.

**Note that control directives are an advanced feature,
and are not recommended in the course of day-to-day styling**.
They exist mainly for use in [mixins](#mixins),
particularly those that are part of libraries like [Compass](http://compass-style.org),
and so require substantial flexibility.

### `@if`

The `@if` directive takes a SassScript expression
and uses the styles nested beneath it if the expression returns
anything other than `false` or `null`:

    p {
      @if 1 + 1 == 2 { border: 1px solid;  }
      @if 5 < 3      { border: 2px dotted; }
      @if null       { border: 3px double; }
    }

被編譯成：

    p {
      border: 1px solid; }

The `@if` statement can be followed by several `@else if` statements
and one `@else` statement.
If the `@if` statement fails,
the `@else if` statements are tried in order
until one succeeds or the `@else` is reached.
例如：

    $type: monster;
    p {
      @if $type == ocean {
        color: blue;
      } @else if $type == matador {
        color: red;
      } @else if $type == monster {
        color: green;
      } @else {
        color: black;
      }
    }

被編譯成：

    p {
      color: green; }

### `@for`

The `@for` directive repeatedly outputs a set of styles. For each repetition, a
counter variable is used to adjust the output. The directive has two forms:
`@for $var from <start> through <end>` and `@for $var from <start> to <end>`.
Note the difference in the keywords `through` and `to`. `$var` can be any
variable name, like `$i`; `<start>` and `<end>` are SassScript expressions that
should return integers.

The `@for` statement sets `$var` to each successive number in the specified
range and each time outputs the nested styles using that value of `$var`. For
the form `from ... through`, the range *includes* the values of `<start>` and
`<end>`, but the form `from ... to` runs up to *but not including* the value of
`<end>`. Using the `through` syntax,

    @for $i from 1 through 3 {
      .item-#{$i} { width: 2em * $i; }
    }

被編譯成：

    .item-1 {
      width: 2em; }
    .item-2 {
      width: 4em; }
    .item-3 {
      width: 6em; }

### `@each` {#each-directive}

The `@each` rule has the form `@each $var in <list>`.
`$var` can be any variable name, like `$length` or `$name`,
and `<list>` is a SassScript expression that returns a list.

The `@each` rule sets `$var` to each item in the list,
then outputs the styles it contains using that value of `$var`.
例如：

    @each $animal in puma, sea-slug, egret, salamander {
      .#{$animal}-icon {
        background-image: url('/images/#{$animal}.png');
      }
    }

被編譯成：

    .puma-icon {
      background-image: url('/images/puma.png'); }
    .sea-slug-icon {
      background-image: url('/images/sea-slug.png'); }
    .egret-icon {
      background-image: url('/images/egret.png'); }
    .salamander-icon {
      background-image: url('/images/salamander.png'); }

### `@while`

The `@while` directive takes a SassScript expression
and repeatedly outputs the nested styles
until the statement evaluates to `false`.
This can be used to achieve more complex looping
than the `@for` statement is capable of,
although this is rarely necessary.
例如：

    $i: 6;
    @while $i > 0 {
      .item-#{$i} { width: 2em * $i; }
      $i: $i - 2;
    }

被編譯成：

    .item-6 {
      width: 12em; }

    .item-4 {
      width: 8em; }

    .item-2 {
      width: 4em; }

## Mixin Directives {#mixins}

Mixins allow you to define styles
that can be re-used throughout the stylesheet
without needing to resort to non-semantic classes like `.float-left`.
Mixins can also contain full CSS rules,
and anything else allowed elsewhere in a Sass document.
They can even take [arguments](#mixin-arguments)
which allows you to produce a wide variety of styles
with very few mixins.

### Defining a Mixin: `@mixin` {#defining_a_mixin}

Mixins are defined with the `@mixin` directive.
It's followed by the name of the mixin
and optionally the [arguments](#mixin-arguments),
and a block containing the contents of the mixin.
For example, the `large-text` mixin is defined as follows:

    @mixin large-text {
      font: {
        family: Arial;
        size: 20px;
        weight: bold;
      }
      color: #ff0000;
    }

Mixins may also contain selectors,
possibly mixed with properties.
The selectors can even contain [parent references](#referencing_parent_selectors_).
例如：

    @mixin clearfix {
      display: inline-block;
      &:after {
        content: ".";
        display: block;
        height: 0;
        clear: both;
        visibility: hidden;
      }
      * html & { height: 1px }
    }

### Including a Mixin: `@include` {#including_a_mixin}

Mixins are included in the document
with the `@include` directive.
This takes the name of a mixin
and optionally [arguments to pass to it](#mixin-arguments),
and includes the styles defined by that mixin
into the current rule.
例如：

    .page-title {
      @include large-text;
      padding: 4px;
      margin-top: 10px;
    }

被編譯成：

    .page-title {
      font-family: Arial;
      font-size: 20px;
      font-weight: bold;
      color: #ff0000;
      padding: 4px;
      margin-top: 10px; }

Mixins may also be included outside of any rule
(that is, at the root of the document)
as long as they don't directly define any properties
or use any parent references.
例如：

    @mixin silly-links {
      a {
        color: blue;
        background-color: red;
      }
    }

    @include silly-links;

被編譯成：

    a {
      color: blue;
      background-color: red; }

Mixin definitions can also include other mixins.
例如：

    @mixin compound {
      @include highlighted-background;
      @include header-text;
    }

    @mixin highlighted-background { background-color: #fc0; }
    @mixin header-text { font-size: 20px; }

Mixins that only define descendent selectors, can be safely mixed
into the top most level of a document.

### Arguments {#mixin-arguments}

Mixins can take arguments SassScript values as arguments,
which are given when the mixin is included
and made available within the mixin as variables.

When defining a mixin,
the arguments are written as variable names separated by commas,
all in parentheses after the name.
Then when including the mixin,
values can be passed in in the same manner.
例如：

    @mixin sexy-border($color, $width) {
      border: {
        color: $color;
        width: $width;
        style: dashed;
      }
    }

    p { @include sexy-border(blue, 1in); }

被編譯成：

    p {
      border-color: blue;
      border-width: 1in;
      border-style: dashed; }

Mixins can also specify default values for their arguments
using the normal variable-setting syntax.
Then when the mixin is included,
if it doesn't pass in that argument,
the default value will be used instead.
例如：

    @mixin sexy-border($color, $width: 1in) {
      border: {
        color: $color;
        width: $width;
        style: dashed;
      }
    }
    p { @include sexy-border(blue); }
    h1 { @include sexy-border(blue, 2in); }

被編譯成：

    p {
      border-color: blue;
      border-width: 1in;
      border-style: dashed; }

    h1 {
      border-color: blue;
      border-width: 2in;
      border-style: dashed; }

#### Keyword Arguments

Mixins can also be included using explicit keyword arguments.
For instance, we the above example could be written as:

    p { @include sexy-border($color: blue); }
    h1 { @include sexy-border($color: blue, $width: 2in); }

While this is less concise, it can make the stylesheet easier to read.
It also allows functions to present more flexible interfaces,
providing many arguments without becoming difficult to call.

Named arguments can be passed in any order, and arguments with default values can be omitted.
Since the named arguments are variable names, underscores and dashes can be used interchangeably.

#### Variable Arguments

Sometimes it makes sense for a mixin to take an unknown number of arguments. For
example, a mixin for creating box shadows might take any number of shadows as
arguments. For these situations, Sass supports "variable arguments," which are
arguments at the end of a mixin declaration that take all leftover arguments and
package them up as a [list](#lists). These arguments look just like normal
arguments, but are followed by `...`. 例如：

    @mixin box-shadow($shadows...) {
      -moz-box-shadow: $shadows;
      -webkit-box-shadow: $shadows;
      box-shadow: $shadows;
    }

    .shadows {
      @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
    }

被編譯成：

    .shadowed {
      -moz-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
      -webkit-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
      box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
    }

Variable arguments can also be used when calling a mixin. Using the same syntax,
you can expand a list of values so that each value is passed as a separate
argument. 例如：

    @mixin colors($text, $background, $border) {
      color: $text;
      background-color: $background;
      border-color: $border;
    }

    $values: #ff0000, #00ff00, #0000ff;
    .primary {
      @include colors($values...);
    }

被編譯成：

    .primary {
      color: #ff0000;
      background-color: #00ff00;
      border-color: #0000ff;
    }

You can use variable arguments to wrap a mixin and add additional styles without
changing the argument signature of the mixin. If you do so, even keyword
arguments will get passed through to the wrapped mixin. 例如：

    @mixin wrapped-stylish-mixin($args...) {
      font-weight: bold;
      @include stylish-mixin($args...);
    }

    .stylish {
      // The $width argument will get passed on to "stylish-mixin" as a keyword
      @include wrapped-stylish-mixin(#00ff00, $width: 100px);
    }

### Passing Content Blocks to a Mixin {#mixin-content}

It is possible to pass a block of styles to the mixin for placement within the styles included by
the mixin. The styles will appear at the location of any `@content` directives found within the mixin. This makes is possible to define abstractions relating to the construction of
selectors and directives.

例如：

    @mixin apply-to-ie6-only {
      * html {
        @content;
      }
    }
    @include apply-to-ie6-only {
      #logo {
        background-image: url(/logo.gif);
      }
    }

Generates:

    * html #logo {
      background-image: url(/logo.gif);
    }

The same mixins can be done in the `.sass` shorthand syntax:

    =apply-to-ie6-only
      * html
        @content

    +apply-to-ie6-only
      #logo
        background-image: url(/logo.gif)

**Note:** when the `@content` directive is specified more than once or in a loop, the style block will be duplicated with each invocation.

#### Variable Scope and Content Blocks

The block of content passed to a mixin are evaluated in the scope where the block is defined,
not in the scope of the mixin. This means that variables local to the mixin **cannot** be used
within the passed style block and variables will resolve to the global value:

    $color: white;
    @mixin colors($color: blue) {
      background-color: $color;
      @content;
      border-color: $color;
    }
    .colors {
      @include colors { color: $color; }
    }

Compiles to:

    .colors {
      background-color: blue;
      color: white;
      border-color: blue;
    }

Additionally, this makes it clear that the variables and mixins that are used within the
passed block are related to the other styles around where the block is defined. 例如：

    #sidebar {
      $sidebar-width: 300px;
      width: $sidebar-width;
      @include smartphone {
        width: $sidebar-width / 3;
      }
    }


## Function Directives {#function_directives}

It is possible to define your own functions in sass and use them in any
value or script context. 例如：

    $grid-width: 40px;
    $gutter-width: 10px;

    @function grid-width($n) {
      @return $n * $grid-width + ($n - 1) * $gutter-width;
    }

    #sidebar { width: grid-width(5); }

Becomes:

    #sidebar {
      width: 240px; }

As you can see functions can access any globally defined variables as well as
accept arguments just like a mixin. A function may have several statements
contained within it, and you must call `@return` to set the return value of
the function.

As with mixins, you can call Sass-defined functions using keyword arguments.
In the above example we could have called the function like this:

    #sidebar { width: grid-width($n: 5); }

It is recommended that you prefix your functions to avoid naming conflicts
and so that readers of your stylesheets know they are not part of Sass or CSS. For example, if you work for ACME Corp, you might have named the function above `-acme-grid-width`.

User-defined functions also support [variable arguments](#variable_arguments)
in the same way as mixins.

## Output Style

Although the default CSS style that Sass outputs is very nice
and reflects the structure of the document,
tastes and needs vary and so Sass supports several other styles.

Sass allows you to choose between four different output styles
by setting the [`:style` option](#style-option)
or using the `--style` command-line flag.

### `:nested`

Nested style is the default Sass style,
because it reflects the structure of the CSS styles
and the HTML document they're styling.
Each property has its own line,
but the indentation isn't constant.
Each rule is indented based on how deeply it's nested.
例如：

    #main {
      color: #fff;
      background-color: #000; }
      #main p {
        width: 10em; }

    .huge {
      font-size: 10em;
      font-weight: bold;
      text-decoration: underline; }

Nested style is very useful when looking at large CSS files:
it allows you to easily grasp the structure of the file
without actually reading anything.

### `:expanded`

Expanded is a more typical human-made CSS style,
with each property and rule taking up one line.
Properties are indented within the rules,
but the rules aren't indented in any special way.
例如：

    #main {
      color: #fff;
      background-color: #000;
    }
    #main p {
      width: 10em;
    }

    .huge {
      font-size: 10em;
      font-weight: bold;
      text-decoration: underline;
    }

### `:compact`

Compact style takes up less space than Nested or Expanded.
It also draws the focus more to the selectors than to their properties.
Each CSS rule takes up only one line,
with every property defined on that line.
Nested rules are placed next to each other with no newline,
while separate groups of rules have newlines between them.
例如：

    #main { color: #fff; background-color: #000; }
    #main p { width: 10em; }

    .huge { font-size: 10em; font-weight: bold; text-decoration: underline; }

### `:compressed`

Compressed style takes up the minimum amount of space possible,
having no whitespace except that necessary to separate selectors
and a newline at the end of the file.
It also includes some other minor compressions,
such as choosing the smallest representation for colors.
It's not meant to be human-readable.
例如：

    #main{color:#fff;background-color:#000}#main p{width:10em}.huge{font-size:10em;font-weight:bold;text-decoration:underline}

## Extending Sass

Sass provides a number of advanced customizations for users with unique requirements.
Using these features requires a strong understanding of Ruby.

### Defining Custom Sass Functions

Users can define their own Sass functions using the Ruby API.
For more information, see the [source documentation](Sass/Script/Functions.html#adding_custom_functions).

### Cache Stores

Sass caches parsed documents so that they can be reused without parsing them again
unless they have changed. By default, Sass will write these cache files to a location
on the filesystem indicated by [`:cache_location`](#cache_location-option). If you
cannot write to the filesystem or need to share cache across ruby processes or machines,
then you can define your own cache store and set the[`:cache_store`
option](#cache_store-option). For details on creating your own cache store, please
see the {Sass::CacheStores::Base source documentation}.

### Custom Importers

Sass importers are in charge of taking paths passed to `@import` and finding the
appropriate Sass code for those paths. By default, this code is loaded from
the {Sass::Importers::Filesystem filesystem}, but importers could be added to load
from a database, over HTTP, or use a different file naming scheme than what Sass expects.

Each importer is in charge of a single load path (or whatever the corresponding notion
is for the backend). Importers can be placed in the {file:SASS_REFERENCE.md#load_paths-option
`:load_paths` array} alongside normal filesystem paths.

When resolving an `@import`, Sass will go through the load paths looking for an importer
that successfully imports the path. Once one is found, the imported file is used.

User-created importers must inherit from {Sass::Importers::Base}.
