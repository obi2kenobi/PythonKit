---
repo: PythonKit
linguaggi: Swift
stato: manutenzione
aggiornato: 2026-11-26
---

# Scheda tecnica — PythonKit

## Scopo
Framework Swift che carica dinamicamente la libreria Python a runtime ed espone oggetti Python da Swift. Nessun link statico a libpython. Fork del modulo `Python` di Swift for TensorFlow.

## Architettura
Libreria SPM `PythonKit` (target) + test target `PythonKitTests`; build alternativa via CMake. Il modulo Python viene importato a runtime con `Python.import(...)`; i wrapper (`CheckingPythonObject`, `ThrowingPythonObject`) espongono gli oggetti Python a Swift.

## Componenti
| Nome | Ruolo | File |
|------|-------|------|
| PythonKit | Libreria pubblica | `PythonKit/Python.swift` |
| PythonLibrary | Caricamento dinamico libpython | `PythonKit/PythonLibrary.swift` |
| Symbols | Simboli C di libpython (generato) | `PythonKit/PythonLibrary+Symbols.swift` |
| NumpyConversion | Conversioni NumPy | `PythonKit/NumpyConversion.swift` |
| Info.plist | Metadati | `PythonKit/Info.plist` |
| PythonKitTests | Test runtime e conversioni | `Tests/PythonKitTests/` |

## Dati e interfacce
API Swift: `PythonObject`, `CheckingPythonObject`, `ThrowingPythonObject`, `PythonConvertible`, `ConvertibleFromPython`, `PythonError`. Interfaccia: `Python.import("modulo")`. Libreria Python caricata dinamicamente (PATH/libpython).

## Dipendenze
- Swift 5.1+ (`swift-tools-version:5.1`)
- macOS, Linux, Windows
- CMake ≥ 3.15.1 per build alternativa
- Nessuna dipendenza SPM esterna

## Configurazione
- `PYTHON_VERSION` — forza versione Python
- `PYTHON_LIBRARY` — forza percorso/nome libpython
- `PYTHON_LOADER_LOGGING` — `TRUE` per logging caricamento

## Build e test
- `swift build` → VERDE (exit 0, ~11 s, warning deprecati in NumpyConversion.swift)
- `swift test` → fallito: XCTest non risolvibile (toolchain command-line tools senza XCTest); non verificato
- CMake `cmake -B build` → non verificato in questa sessione

## Deploy
Libreria, nessun binario da eseguire. Distribuzione via Swift Package Manager (branch master).

## Rischi e debito tecnico
- Hardened Runtime macOS: richiede firma corretta del framework Python incorporato.
- Warning deprecati in `NumpyConversion.swift` (`assign(from:count:)` → `update(from:count:)`).
- `PythonLibrary+Symbols.swift` generato: non modificare a mano.
- `swift test` non verificabile con la toolchain presente.
