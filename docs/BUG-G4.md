# BUG-G4 — valutazione e correzione (M-031)

## BUG-1
Dove: PythonKit/PythonLibrary.swift:70 — `loadSymbol<T>` fa `unsafeBitCast(self.loadSymbol(handle, name), to: type)` senza controllare il risultato.
Gravità: alta (crash / UB a invocazione del simbolo).
Prova: codice sorgente letto; dlsym ritorna nil per simbolo mancante (non eseguito: richiede Python reale).
Correzione: guardia `guard let raw = ... else { fatalError("PythonKit: symbol '\(name)' not found in the Python library") }` prima del bitCast. Commit d14ce1d.
Test: `swift build` OK (warning deprecazione assign(from:count:) preesistente). `swift test` non eseguibile in questo ambiente (XCTest non presente in Command Line Tools). No regressione verificabile oltre al build.

## BUG-2
Dove: PythonKit/NumpyConversion.swift:135-141 — `Array.init?(numpy:)` alloca `dummyPointer` capacity 1, fa `dummyPointer.move()` (lettura memoria non inizializzata) e poi sovrascrive con `withUnsafeMutableBufferPointer`.
Gravità: alta (lettura memoria non inizializzata, UB; per scalarCount > 1 il repeat viene scartato ma la lettura avviene comunque).
Prova: codice sorgente letto (righe 135-142 pre-fix); non eseguito (richiede Python/numpy runtime).
Correzione: rimosso dummyPointer; `scalarCount == 0` → `self = []`; altrimenti `self.init(repeating: ptr[0], count: scalarCount)` (primo elemento reale, valido perché ptr non-nil e scalarCount > 0) poi `assign(from: count:)`. Commit 200cb5b.
Test: `swift build` OK. `swift test` non eseguibile in questo ambiente (XCTest mancante).

## CANDIDATO-3 (non trattato — fuori dal perimetro del mandato)
CANDIDATO-3: PythonKit/Python.swift:127 — `String(Python.str(self))!` forzato. NON trattato in questo giro (mandato istruisce solo CANDIDATO-1 e CANDIDATO-2).

## Verifica
- Build OK. Test del repo: NO-TEST (esegui-test-repo.sh: nessun package.json/Makefile/pytest in questo Swift package). swift test bloccato per ambiente (XCTest mancante).
- Albero: pulito dopo commit docs.
