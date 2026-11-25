# BUG-G4 — candidati (M-031)

CANDIDATO-1: PythonKit/PythonLibrary.swift:70 — `loadSymbol` fa `unsafeBitCast(self.loadSymbol(handle, name), to: type)` senza controllare il risultato. Se `dlsym` ritorna nil (simbolo mancante — es. versione Python non supportata o misura legacy), si casta un puntatore nil a un tipo funzione ed eseguire una simile funzione poi crasha / UB. Crash evidente, niente fallback.
CANDIDATO-2: PythonKit/NumpyConversion.swift:136-137 — `Array.init?(numpy:)` alloca un dummyPointer di capacity 1, fa `dummyPointer.move()` (legge memoria non inizializzata e la usa come valore di riempimento) e poi si affida a `withUnsafeMutableBufferPointer` per sovrascrivere. Lettura di memoria non inizializzata = comportamento indefinito / value non definito; per scalarCount > 1 il repeat viene scartato, ma la lettura avviene comunque.
CANDIDATO-3: PythonKit/Python.swift:127 — `String(Python.str(self))!` forzato: se `str` fallisce (eccezione attiva) crasha. Forzato unwrap su valore fallibile.

## Verifica
- CANDIDATO-1: `dlsym` restituisce nil per simbolo mancante; `unsafeBitCast(nil, to: T.self)` produces nil function pointer → crash a invocazione. (non verificato eseguendo: non avviabile in questo ambiente senza Python reale)
- CANDIDATO-2: codice sorgente letto (righe 135-138); non eseguito (richiede Python/numpy runtime).
- CANDIDATO-3: codice sorgente letto (riga 127); non eseguito.

NON TROVATO: nessuno (3 candidati). File letti: PythonKit/Python.swift, PythonKit/PythonLibrary.swift, PythonKit/PythonLibrary+Symbols.swift, PythonKit/NumpyConversion.swift.
