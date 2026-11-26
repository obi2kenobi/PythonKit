# PythonKit

Framework Swift per interagire con Python: carica la libreria Python a runtime e espone gli oggetti Python da Swift.

## Requisiti

- **Swift 5.1** o superiore
- macOS, Linux o Windows

## Installazione

Con il Swift Package Manager, aggiungi questa dipendenza al manifest `Package.swift`:

```swift
.package(url: "https://github.com/pvieito/PythonKit.git", .branch("master")),
```

Poi, nel target che usa PythonKit, dichiara la dipendenza:

```swift
.target(
    name: "TuoTarget",
    dependencies: ["PythonKit"]
)
```

Contenuto del repository:

- `PythonKit/` — sorgenti Swift (`Python.swift`, `PythonLibrary.swift`, `PythonLibrary+Symbols.swift`, `NumpyConversion.swift`) e `Info.plist`
- `Tests/PythonKitTests/` — test (`PythonRuntimeTests.swift`, `NumpyConversionTests.swift`)
- `Package.swift` — manifest SPM (lib `PythonKit` + test target)
- `CMakeLists.txt`, `cmake/` — supporto CMake
- `.github/workflows/` — CI (macOS/Linux, Windows)

## Uso

```python
import sys
print(f"Python {sys.version_info.major}.{sys.version_info.minor}")
print(f"Python Version: {sys.version}")
print(f"Python Encoding: {sys.getdefaultencoding().upper()}")
```

Equivalente in Swift:

```swift
import PythonKit

let sys = try Python.import("sys")
print("Python \(sys.version_info.major).\(sys.version_info.minor)")
print("Python Version: \(sys.version)")
print("Python Encoding: \(sys.getdefaultencoding().upper())")
```

Esempi da terminale:

```bash
$ PYTHON_VERSION=3 swift run
[*] Python 3.x
$ PYTHON_VERSION=2.7 swift run
[*] Python 2.7
$ PYTHON_LIBRARY=libpython3.5.so swift run
[*] Python 3.5
$ PYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython2.7.so swift run
[*] Python 2.7
```

## Variabili d'ambiente

- `PYTHON_VERSION` — forza la versione di Python.
- `PYTHON_LIBRARY` — forza il percorso/nome della libreria Python.
- `PYTHON_LOADER_LOGGING` — impostata a `TRUE` per log degli sforzi di caricamento della libreria.

```bash
$ PYTHON_LOADER_LOGGING=TRUE PYTHON_VERSION=3.8 swift run
```

## Test

```bash
swift test
```

## Risoluzione problemi

- Su macOS con Hardened Runtime attivo: firmare e incorporare correttamente il framework Python che PythonKit carica.
- Se PythonKit non trova la libreria Python, imposta `PYTHON_LIBRARY` con il percorso della libreria.

## Note

- Nato come fork del modulo `Python` del progetto sperimentale [Swift for TensorFlow](https://github.com/tensorflow/swift).

## Istruzioni d'uso

Vedi [docs/ISTRUZIONI.md](docs/ISTRUZIONI.md).

Scheda tecnica: [docs/SCHEDA-TECNICA.md](docs/SCHEDA-TECNICA.md).
