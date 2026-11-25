# CLAUDE.md — PythonKit

## Scopo
Framework Swift che carica dinamicamente la libreria Python a runtime ed espone oggetti Python da Swift. Non linka statico libpython.

## Struttura
- `PythonKit/` — sorgenti Swift: `Python.swift` (interfaccia pubblica: `PythonObject`, `Python.import`, `PythonError`), `PythonLibrary.swift` (caricamento dinamico, `useVersion`/`useLibrary(at:)`), `PythonLibrary+Symbols.swift` (simboli C, generato), `NumpyConversion.swift`, `Info.plist`.
- `Tests/PythonKitTests/` — test.
- `Package.swift` — manifest SPM (`swift-tools-version:5.1`).
- `CMakeLists.txt`, `cmake/` — build CMake.
- `.github/workflows/` — CI macOS/Linux/Windows.

## Comandi
- Build: `swift build` — esito VERDE (exit 0, 12,42 s, solo warning deprecati)
- Test: `swift test` — non verificato in questa sessione.
- Variabili d'ambiente: `PYTHON_VERSION`, `PYTHON_LIBRARY`, `PYTHON_LOADER_LOGGING`.

## Convenzioni
- API Swift: tipi PascalCase, metodi camelCase, file `.swift` in `PythonKit/`.
- La libreria Python è caricata a runtime: nessun link statico.

## Cosa non toccare
- `PythonKit/` (codice) — fuori perimetro di audit docs.
- `PythonLibrary+Symbols.swift` — generato, no modifica manuale.
- `.github/workflows/`, `Tests/` — senza mandato.
- Contenuto di questa guida va tenuto entro 100 righe.

## Segreti
Nessun segredo nel repo. Non committare mai valori reali: usare solo segnaposto.

## Rimando
Dettagli per LLM: `docs/LLM-GUIDA.md`.
