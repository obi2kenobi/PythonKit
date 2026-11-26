# BUG-G5 — PythonKit: bug gravi (M-032)

Base: origin/master. Branch: fix/g5-2026-11. Diversi da BUG-G4 (che tocca PythonLibrary.swift:70 e NumpyConversion.swift:135-141).
Riproduzioni: pacchetto in /tmp/m032b-repro (copia dei sorgenti, fuori dal repo), harness Swift `Repro int8|uint|bytes`, libreria Python 3.15 di Homebrew (PYTHON_LIBRARY). Non verificato su altre versioni di Python né su Linux/Windows.

## BUG-1
Dove: PythonKit/Python.swift:1446-1452 (base origin/master) — `PythonBytes.init?(_:)` ramo `default` ritorna nil senza pulire l'eccezione Python.
Gravità: alta (logica errata: un errore residuo compare in una chiamata non correlata).
Prova: `_ = PythonBytes(PythonObject("x")); try PythonObject(1).throwing.dynamicallyCall(withArguments: [])` — PRIMA: `RISULTATO bytes: errore = Python exception: expected bytes, str found` (errore del passo precedente, non di questa chiamata).
Correzione: commit 1584946 — `PyErr_Clear()` nel ramo di fallimento, come già fa `String.init?`.
Test: stesso comando — DOPO: `RISULTATO bytes: errore = Python exception: 'int' object is not callable` (errore reale della chiamata). Test del repo: `swift test` non eseguibile (XCTest assente nel toolchain CLT), esito non verificato.

## BUG-2
Dove: PythonKit/Python.swift:869-950 (base origin/master) — `Int8`, `Int16`, `Int32`, `Int64`, `UInt8`, `UInt16`, `UInt32`, `UInt64`: `init?(_:)` esegue `self.init(i)` che va in trap se `i` non sta nel tipo.
Gravità: alta (crash del processo per un dato Python ordinario, es. 300 convertito in Int8).
Prova: `print(Int8(PythonObject(300)) as Any)` — PRIMA: `Fatal error: Not enough bits to represent the passed value`, exit 133.
Correzione: commit 0263427 — `Int8(exactly:)` e simili, `nil` se fuori range.
Test: `./.build/debug/Repro int8` — DOPO: `RISULTATO int8: nil`, nessun crash.

## BUG-3
Dove: PythonKit/Python.swift:828-838 (base origin/master) — `UInt.init?(_:)` usa `PyInt_AsUnsignedLongMask`, che maschera in silenzio i valori fuori da [0, UInt.max].
Gravità: alta (dato corrotto senza errore: -1 diventa 18446744073709551615).
Prova: `print(UInt(PythonObject(-1)) as Any)` — PRIMA: `RISULTATO uint: Optional(18446744073709551615)`, exit 0.
Correzione: commit 8ec2a37 — dopo la conversione, rifiuto se il valore Python è < 0 o > UInt.max.
Test: `./.build/debug/Repro uint` — DOPO: `RISULTATO uint: nil`.
