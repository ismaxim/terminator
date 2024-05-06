![Build Status](https://img.shields.io/github/actions/workflow/status/maximgrynykha/winkill/main.yml?style=for-the-badge&logo=github&logoColor=white&label=build)

# __Winkill__

Work with processes as objects. Find specific ones by attributes and kill them.

## ⚙️ Installation

To install this library - run the command below in your terminal:

```shell
composer require maximgrynykha/winkill
```

## Usage  

```php
<?php

use Winkill\Winkill;

require_once 'vendor/autoload.php';

try {
    $winkill = new Winkill();
    $processes = $winkill->scan();
```

```php
    // Get scanned processes
    $scanned = $processes->get();
 
    dd($scanned);
```

```php
    // Select specific process(es)
    $selected = $processes->where(
        attribute: 'process_name',
        compareAs: '=',
        value: 'phpstorm64.exe'
    )->get();

    dd($selected);
```

```php
    // Kill specific process(es)
    $killed = $processes->where(
        attribute: 'process_id',
        compareAs: '=',
        value: 11492
    )->kill();  

    // Note: If the processes have been killed or not found 
    // by the where-condition then returns an empty array.
    dd($killed);
```

```php
} catch (\Winkill\Kernel\Interface\Exception|\Throwable $throwable) {
    die($throwable->getMessage());
}
```

### API (based on 'tasklist' command)
| Attribute name    | Compare operator                          | Attribute value                 | Example                |
|:------------------|:------------------------------------------|:--------------------------------|:-----------------------|
| `process_name`    | [string]: `=`, `!=`                       | [string]: simple name           | chrome / figma         |
|                   | [string]: `=`, `!=`                       | [string]: name with .ext        | chrome.exe / figma.exe |
|                   | [string]: `=`, `!=`                       | [string]: uppercase name        | Chrome.exe / Figma.exe |
| `process_id`      | [string]: `=`, `!=`                       | [int]: number of the ID         |                        |
| `session_name`    | [string]: `=`, `!=`                       | [string]: Console / Services    |                        |
| `session_number`  | [string]: `=`, `!=`                       | [int]: number in range of {0-1} |                        |
| `consumed_memory` | [string]: `>`, `<`, `=`, `>=`, `<=`, `!=` | [int]: number in Kb(kilobytes)  |                        |

### Select processes

```php
$processes->where('process_name', '=', 'chrome')->get();

$processes->where('process_id', '=', 11455)->get();

$processes->where('session_name', '=', 'console')->get();

$processes->where('session_number', '=', 1)->get();

// ⚠️ Note: consumed memory is estimated in Kb(kilobytes)
$processes->where('consumed_memory', '=', 128920)->get(); 
```

### Terminate processes

```php
$processes->where('process_name', '=', 'chrome')->kill();

$processes->where('process_id', '=', 11455)->kill();

//❗Alert: killing process(es) by attribute [session_name]
// may break you 🤯 and/or your computer 💥. Use it only 
// if you are 100% confident at the ending result.
$processes->where('session_name', '=', 'console')->kill();

//❗Alert: killing process(es) by attribute [session_number] 
// is the same danger as was said previously about attribute 
// [session_name], so be warned about using it at your risk.
$processes->where('session_number', '=', 1)->kill();

// ⚠️ Note: consumed memory is estimated in Kb(kilobytes)
$processes->where('consumed_memory', '=', 128920)->kill(); 
```

### 🧱 Snippets

```php
// Terminate processes by an array of process names (all names are an example)
array_walk($processes_names = ['chrome', 'firefox', 'slack'],
    static fn(string $process_name): Process => 
        $processes->where('process_name', '=', $process_name)->kill(),
);

// Terminate processes by an array of process ids (all ids are an example)
array_walk($processes_ids = [1000, 5595, 17820],
    static fn(string $process_id): Process => 
        $processes->where('process_id', '=', $process_id)->kill(),
);

// You can switch between array_walk and array_map for your needs.
// [Confusing in difference?](https://stackoverflow.com/a/3432266/11591375)
```

```php
$processes = $processes->where('consumed_memory', '<', 1000)->get();

// Sort processes on consumed memory by ASC
usort($processes, static fn(Process $process, Process $_process): int =>
    $process->consumed_memory <=> $_process->consumed_memory
);

// Sort processes on consumed memory by DESC
usort($processes, static fn(Process $process, Process $_process): int =>
    $_process->consumed_memory <=> $process->consumed_memory
);
```

## 📝 Footnotes

_`DRY`, `SOLID` principles and `Design Patterns`_ are used.

| Creational                                                                      | Structural                                                  | Behavioral                                                      | Others*                                                                 |
|---------------------------------------------------------------------------------|:------------------------------------------------------------|:----------------------------------------------------------------|:------------------------------------------------------------------------|
| [`Abstract Factory`](https://refactoring.guru/design-patterns/abstract-factory) | [`Facade`](https://refactoring.guru/design-patterns/facade) | [`Strategy`](https://refactoring.guru/design-patterns/strategy) | [`Composition Root`](https://blog.ploeh.dk/2011/07/28/CompositionRoot/) |
| [`Factory Method`](https://refactoring.guru/design-patterns/factory-method)     |                                                             | [`Iterator`](https://refactoring.guru/design-patterns/iterator) |                                                                         |
| [`Builder`](https://refactoring.guru/design-patterns/builder)                   |                                                             | [`Command`](https://refactoring.guru/design-patterns/command)   |                                                                         |


## 🤝 Contributing

If you have a problem that cannot be solved using this library, please write your solution, and if you want to help 
other developers who also use this library (or if you want to keep your solution working after a new version is 
released, which will be in the package manager dependencies) — create a pull-request. I will be happy to add your 
excellent code to the library!

🐞 Report any bugs or issues you find on the [GitHub issues](https://github.com/MaximGrynykha/winkill/issues).

## 📃 License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
