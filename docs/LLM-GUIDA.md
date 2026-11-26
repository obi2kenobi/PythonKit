# Guida LLM — PythonKit

## Scopo
Framework Swift per chiamare codice Python a runtime. La libreria Python è caricata dinamicamente; gli oggetti Python sono esposti come `PythonObject`.

## Mappa dei file
- `PythonKit/Python.swift` — interfaccia pubblica: `PythonObject`, `CheckingPythonObject`, `ThrowingPythonObject`, `PythonConvertible`, `ConvertibleFromPython`, `PythonError`. Entry point per l'utente: `Python.import(...)`.
- `PythonKit/PythonLibrary.swift` — caricamento dinamico della libreria Python (`PythonLibrary`), `useVersion`, `useLibrary(at:)`.
- `PythonKit/PythonLibrary+Symbols.swift` — simboli C della libreria Python.
- `PythonKit/NumpyConversion.swift` — conversioni NumPy (array).
- `PythonKit/Info.plist` — metadati.
- `Tests/PythonKitTests/PythonRuntimeTests.swift`, `NumpyConversionTests.swift` — test.
- `Package.swift` — manifest SPM. `CMakeLists.txt`, `cmake/` — build CMake.

## Entry point
`Python.import("nome_modulo")` (nel target Swift) per importare moduli Python. Wrapper dinamici: `CheckingPythonObject` (verifiche) e `ThrowingPythonObject` (errori gestiti).

## Comandi
- Test: `swift test`
- Build: `swift build`
- Variabili d'ambiente: `PYTHON_VERSION`, `PYTHON_LIBRARY`, `PYTHON_LOADER_LOGGING` (vedi README).

## Convenzioni
- API Swift: file `.swift` in `PythonKit/`, naming PascalCase per tipi, camelCase per metodi.
- Moduli Python caricati dinamicamente; nessun link statico a libpython.

## Punti delicati
- La libreria Python deve essere disponibile a runtime: verificare percorso con `PYTHON_LIBRARY` se non viene trovata.
- Hardened Runtime su macOS richiede firma corretta.
- Versioni Python multiple: `PYTHON_VERSION` forza la versione.
- Non modificare `PythonLibrary+Symbols.swift` manualmente: è generato.

## Cosa NON toccare
- Codice Swift in `PythonKit/` (fuori perimetro di un audit docs).
- `.github/workflows/` (CI) senza mandato.
- File in `Tests/` senza mandato.

## Esito verifiche (2026-11-25)
- `swift build`: esito VERDE (exit 0, ~12 s, solo warning deprecati in NumpyConversion.swift).
- `swift test`: non verificato in questa sessione.

## Istruzioni d'uso
Dettagli completi: `docs/ISTRUZIONI.md`.

## Scheda tecnica
Dettagli tecnici: `docs/SCHEDA-TECNICA.md`.
