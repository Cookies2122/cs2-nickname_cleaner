<h1 align=center><code>Nickname Cleaner</code></h1>

<div align=center>
  <img src=https://img.shields.io/badge/Counter--Strike%202-Metamod%202.0%20(KHook)-orange?style=flat-square alt=metamod>
  <a href=https://github.com/Cookies2122/cs2-nickname_cleaner/commits><img src=https://img.shields.io/github/last-commit/Cookies2122/cs2-nickname_cleaner?style=flat-square alt="last commit"></a>
  <a href=LICENSE><img src=https://img.shields.io/github/license/Cookies2122/cs2-nickname_cleaner?style=flat-square alt=license></a>
</div>
<br>

Убирает из ников рекламу: сайты и слова из `blocklist.txt`. Если от ника ничего не осталось, ставит имя из настроек.

> Автор плагина: [ABKAM](https://github.com/ABKAM2023). Здесь версия 1.0.3 под новый SDK и Metamod 2.0, оригинал: [ABKAM2023/cs2-nickname_cleaner](https://github.com/ABKAM2023/cs2-nickname_cleaner).

## Что нужно

- [Metamod:Source](https://github.com/alliedmodders/metamod-source) 2.0 (KHook)
- [Utils](https://github.com/Pisex/cs2-menus)

## Установка

Соберите плагин (раздел [Сборка](#сборка)) и скопируйте содержимое `build/package` в папку `csgo` на сервере. В пакете лежит:

| Файл | Куда |
| --- | --- |
| `nickname_cleaner.so` | `addons/nickname_cleaner/` |
| `nickname_cleaner.vdf` | `addons/metamod/` |
| `settings.ini` | `addons/configs/nickname_cleaner/` |
| `whitelist.txt` | `addons/configs/nickname_cleaner/` |
| `blocklist.txt` | `addons/configs/nickname_cleaner/` |

После этого перезапустите сервер или смените карту.

## Настройки

### settings.ini

`addons/configs/nickname_cleaner/settings.ini`

| Ключ | По умолчанию | Что делает |
| --- | --- | --- |
| `DefaultName` | `Игрок` | имя, если от ника ничего не осталось |

```
"NicknameCleaner"
{
    "DefaultName" "Игрок" // Имя, которое будет использоваться, если ник игрока полностью отфильтрован
}
```

### blocklist.txt

`addons/configs/nickname_cleaner/blocklist.txt`

Шаблон `*.ru` убирает части ника, которые заканчиваются на `.ru`. Просто слово, например `test`, убирает любое слово ника, где оно есть.

<details>
<summary>Весь файл blocklist.txt</summary>

```
// Добавьте слова или шаблоны для блокировки.
// Шаблоны, начинающиеся с *., будут совпадать с концом части никнейма.
// Пример: *.ru заблокирует "site.ru"
// Если указать просто слово (например, test), то будет удалено любое слово в нике, содержащее его.

*.com
*.net
*.org
*.ru
*.ua
*.kz
*.by
*.info
*.biz
*.io
*.gg
*.tv
*.site
*.xyz
*.online
*.pro
*.club
*.vip
*.me
*.cc
*.cn
*.de
*.uk
*.pl
*.fr
*.eu
*.us
*.top
*.fun
*.shop
*.su
*.ro
*.cz
*.sk
*.hu
*.bg
*.rs
*.hr
*.si
*.lt
*.lv
*.ee
*.fi
*.se
*.no
*.dk
*.nl
*.be
*.ch
*.at
*.it
*.es
*.pt
*.gr
*.tr
```

</details>

### whitelist.txt

`addons/configs/nickname_cleaner/whitelist.txt`

Что не трогать, даже если похоже на ссылку.

```
// Добавьте сюда слова, которые не должны считаться рекламой, даже если они похожи на ссылки.
// Например, если вы хотите разрешить ники с "my-site.com", добавьте это сюда.

test.su
```

## Что изменено

По сравнению с оригиналом:

- Переведено на новый SDK ([Wend4r/sourcesdk](https://github.com/Wend4r/sourcesdk)), старый после обновления CS2 от 12.11.2026 уже не подходит.
- Работает только на Metamod 2.0 (KHook), старый Metamod на SourceHook больше не поддерживается.
- Заголовки Utils обновлены под текущую версию, имена интерфейсов теперь `UTILS_INTERFACE` и `MENUS_INTERFACE`.
- Своя функция `GameEntitySystem()` убрана, в новом SDK entity system берётся через `IGameResourceService`.
- SchemaEntity обновлена: актуальные оффсеты виртуальных функций.

## Сборка

Собирается на Linux. Нужны clang, python3, cmake и [AMBuild](https://github.com/alliedmodders/ambuild). Все команды ниже выполняются из одной папки.

**1. SDK.** Нужен [sourcesdk](https://github.com/Wend4r/sourcesdk) не старее обновления CS2 от 12.11.2026. Собирается один раз:

```bash
git clone --recursive https://github.com/Wend4r/sourcesdk hl2sdk-cs2
cmake -S hl2sdk-cs2 -B hl2sdk-cs2/build -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DSOURCESDK_AM_DEFINES=ON -DSOURCESDK_USE_MINIMAL_PROTOS=OFF
cmake --build hl2sdk-cs2/build -j$(nproc)
export HL2SDKCS2=$PWD/hl2sdk-cs2
```

**2. Metamod.** Нужен Metamod 2.0 с KHook, ветка `master`. Со старым Metamod на SourceHook сборка остановится с ошибкой.

```bash
git clone --recursive https://github.com/alliedmodders/metamod-source
```

**3. Плагин.** Папка SchemaEntity должна лежать рядом с папкой плагина.

```bash
git clone https://github.com/Cookies2122/SchemaEntity
git clone https://github.com/Cookies2122/cs2-nickname_cleaner
cd cs2-nickname_cleaner
mkdir build && cd build
python3 ../configure.py --enable-optimize --sdks=cs2 --targets=x86_64 --mms_path=$PWD/../../metamod-source --hl2sdk-manifests=../hl2sdk-manifests
ambuild
```

Готовые файлы появятся в `build/package`.

## Автор

Плагин написал [ABKAM](https://github.com/ABKAM2023), оригинал: [ABKAM2023/cs2-nickname_cleaner](https://github.com/ABKAM2023/cs2-nickname_cleaner).
Перенос на новый SDK и Metamod 2.0, исправления: \_ded\_cookies.

## Лицензия

GPL-3.0, текст в файле [LICENSE](LICENSE). В коде плагина лицензия тоже указана как GPL. Авторские права на оригинальный код у ABKAM.
