---
repo: PythonKit
software:
  - PythonKit (libreria Swift)
  - Test PythonKitTests (suite di test)
  - Build CMake
aggiornato: 2026-11-25
---

# Istruzioni d'uso — PythonKit

Framework Swift che carica dinamicamente la libreria Python a runtime ed espone oggetti Python da Swift. Nessun link statico a libpython.

## PythonKit (libreria Swift)

### Requisiti
- Swift 5.1 o superiore
- macOS, Linux o Windows
- Libreria Python disponibile a runtime

### Installazione
Con SPM, nel manifest `Package.swift`:

```swift
.package(url: "https://github.com/pvieito/PythonKit.git", .branch("master")),
```

Poi nel target che usa PythonKit:

```swift
.target(name: "TuoTarget", dependencies: ["PythonKit"])
```

Build locale: `swift build` (esito VERDE, exit 0).

### Avvio
Il framework viene caricato a runtime: nessun binario da eseguire. Nei target Swift si importa con `import PythonKit`.

### Uso

```swift
import PythonKit

let sys = try Python.import("sys")
print("Python Version: \(sys.version)")
```

Wrapper: `CheckingPythonObject` (verifiche) e `ThrowingPythonObject` (errori gestiti). Entry point: `Python.import(...)`.

Variabili d'ambiente:
- `PYTHON_VERSION` — forza la versione Python
- `PYTHON_LIBRARY` — forza il percorso/nome della libreria Python
- `PYTHON_LOADER_LOGGING` — `TRUE` per logging del caricamento

### Verifica
`swift build` → VERDE (exit 0, warning deprecati solo in NumpyConversion.swift). `swift test` → non verificato in questa sessione.

### Problemi noti
- macOS con Hardened Runtime: servono firma e incorporazione corretta della libreria Python.
- Se la libreria non viene trovata: imposta `PYTHON_LIBRARY`.
- `PythonLibrary+Symbols.swift` è generato: non modificarlo a mano.

## PythonKitTests (test)

### Requisiti
- Swift 5.1+

### Installazione
Inclusi nel package (testTarget PythonKitTests).

### Avvio
`swift test`

### Uso
- `Tests/PythonKitTests/PythonRuntimeTests.swift` — test runtime
- `Tests/PythonKitTests/NumpyConversionTests.swift` — conversioni NumPy

### Verifica
`swift test` → non verificato in questa sessione.

### Problemi noti
Nessuno noto.

## Build CMake

### Requisiti
- CMake + Swift toolchain

### Installazione
`cmake -B build` (CMakeLists.txt + cmake/modules)

### Avvio
`cmake --build build`

### Uso
Build alternativo a SPM.

### Verifica
Non verificato in questa sessione.

### Problemi noti
Nessuno noto.
