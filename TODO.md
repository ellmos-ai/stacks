# TODO — Fremdmodule und Manifest-Governance

- [ ] Rückwärtskompatible Manifest-Erweiterung für `provenance`, `license`,
  `lifecycle`, `quality`, `review`, `data_boundary` und `removal` spezifizieren.
- [ ] Für Fremdmodule einen unveränderlichen Commit-SHA im Lock-/Installationsplan
  erzwingen; Branch-/Tag-Angaben dürfen nur Discovery-Hinweise sein.
- [ ] Validatoren für Pflichtfelder und Zustandsübergänge ergänzen. `adopted`
  muss ohne Lizenz, Reviewbeleg, Verantwortlichkeit oder Rückbauplan fehlschlagen.
- [ ] Dry-Run-Installations- und Deinstallationsplan mit Hash-/NOTICE-Beleg und
  Rollback-Receipt definieren; Ausführung bleibt beim Installer/Adapter.
- [ ] ControlCenter- und `policy-registry`-Verträge gegen `KONZEPT.md` abstimmen,
  ohne eine zweite Registry oder einen zweiten Executor zu schaffen.
- [ ] `ai-firewall` zunächst nur als quarantänisierten Companion-Kandidaten prüfen:
  Go-Tests auf unterstütztem Host, aktuelle `errcheck`-Lintbefunde im MITM-/
  Proxy-Pfad schließen oder nachvollziehbar bewerten, expliziter Proxy ohne MITM
  als Default, Windows-Rückbau, Netzwerk-Allowlist und End-to-End-Datenfluss mit
  synthetischen Geheimnissen. Erst danach entscheidet der Operator über `adopted`.
- [ ] Review-/Ausstiegstest ergänzen: Qualitätsabfall oder verfehlter Zweck führt
  reproduzierbar zu `watch`/`deprecated`/`removed`, während Auditbelege erhalten
  bleiben.

Keine Aufgabe autorisiert Installation, Fork, Veröffentlichung oder Ausführung
eines Fremdmoduls.
