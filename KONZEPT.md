# KONZEPT — Fremdmodule im ellmos-Baukasten

**Stand:** 2026-08-15  
**Status:** Governance beschlossen; Schema- und Installer-Unterstützung offen.

## Grundentscheidung

Fremdmodule dürfen in Stack-Manifeste aufgenommen werden, aber nie allein wegen
eines interessanten README oder eines ähnlichen Funktionsversprechens. Der
Manifest-Eintrag ist ein überprüfbarer Adoptionsvertrag: Herkunft, unveränderlicher
Stand, Lizenz, Grenzen, Qualitätsbelege, Verantwortlichkeit, Review-Termin und
Ausbaupfad müssen sichtbar sein.

Die Zuständigkeiten bleiben getrennt:

- `stacks` definiert Manifestfelder, Komposition und Lebenszyklus.
- `policy-registry` hält die explizit angenommene Policy-/Entscheidungsreferenz.
- ControlCenter zeigt Herkunft, Status und Warnungen read-only an.
- Ein Installer oder Adapter vollzieht Installation und Entfernung; weder
  ControlCenter noch `policy-registry` führen Fremdcode aus.

## Lebenszyklus

`candidate` → `quarantined` → `adopted` → `watch` → `deprecated` → `removed`

Zusätzlich ist `rejected` ein terminaler Prüfstatus. Übergänge sind explizite,
belegte Entscheidungen. Ein vormals aufgenommenes Modul darf und soll wieder
entfernt werden, wenn Qualität, Sicherheit, Wartung oder Zwecknutzen unter die
vereinbarte Schwelle fallen.

## Einschlusskriterien

Alle Pflichtkriterien müssen erfüllt sein:

1. **Lizenz:** eindeutige, zum vorgesehenen Einsatz kompatible Lizenz; notwendige
   `LICENSE`-/`NOTICE`-Pflichten sind dokumentiert. Keine Lizenz bedeutet Ausschluss.
2. **Herkunft:** kanonische Upstream-URL, unveränderlicher Commit-SHA und optional
   Release-Signatur/Hash; ein beweglicher Branch ist kein Installationsstand.
3. **Zweck und Kapselung:** benannter Nutzen, Capability-Owner und keine verdeckte
   Übernahme fremder Modulzuständigkeiten.
4. **Sicherheit und Datenschutz:** dokumentierte Netzwerk-, Datei-, Geheimnis-,
   Telemetrie- und Datenaufbewahrungsgrenzen; Berechtigungen sind minimal.
5. **Qualität:** reproduzierbarer Build oder Installationsweg, relevante Tests,
   klare Fehlersemantik und belegter Rückbau. README-Behauptungen allein genügen nicht.
6. **Betrieb:** unterstützte Plattformen, Abhängigkeiten, Updatepfad, Health-Signal,
   verantwortliche Person/Rolle sowie `last_reviewed` und `next_review_due`.
7. **Integration:** Dry-Run, explizite Aktivierung, kein stiller Autostart und ein
   getesteter Export-/Rollbackpfad für modul-eigene Daten.

Für Gateways, Security-Tools, Codeausführung, Credential-Zugriff oder externe
Netzwerkdienste ist zusätzlich eine manuelle Operatorfreigabe Pflicht.

## Ausschluss- und Ausstiegskriterien

Ein Modul wird abgelehnt, quarantänisiert oder entfernt bei:

- fehlender, unklarer oder inkompatibler Lizenz;
- nicht erklärtem Netzwerkverkehr, Telemetrie, Secret-Zugriff oder überbreiten
  Berechtigungen;
- nicht reproduzierbarer Quelle/Version, dauerhaft defektem Build oder fehlendem
  sicheren Deinstallationsweg;
- ungelösten kritischen Sicherheitsproblemen oder ausbleibender Wartung innerhalb
  des festgelegten Reviewfensters;
- dauerhaft unzureichender Qualität oder verfehltem, messbar definiertem Modulzweck;
- Capability-Doppelung, die Kapselung und Zuständigkeit im Baukasten verschlechtert.

Bei Entfernung verschwinden Manifest- und Lock-Eintrag sowie die Installation.
Erhalten bleiben Entscheidungsbeleg, verwendeter Commit, Lizenz-/NOTICE-Nachweis,
Entfernungsgrund und gegebenenfalls ein exportierter Daten-/Rollbackbeleg.

## Clone, Fork, Vendor oder Eigenbau?

| Form | Wann verwenden |
|---|---|
| Gepinnter Clone | Standard, wenn Upstream unverändert nutzbar ist. Der Manifest-Lock zeigt auf einen Commit-SHA. |
| Fork | Nur bei dauerhaftem ellmos-spezifischem Delta, notwendigem Security-Fix ohne rechtzeitige Upstream-Lösung oder verwaistem Upstream. Upstream, Fork-Grund, Sync-Takt und Divergenz müssen dokumentiert sein. |
| Vendoring | Ausnahme für kleine, auditierte und stabile Bestandteile; Lizenz und `NOTICE` werden mitgeführt, Updates bleiben nachweisbar. |
| Eigenbau | Wenn Lizenz/Sicherheitsmodell unvereinbar ist, der Fremdmodul-Scope zu breit ist oder ein kleines internes Modul die benötigte Capability deutlich sauberer kapselt. |
| Link/API | Wenn nur ein externer Dienst genutzt wird und kein Code übernommen werden muss; Netzwerk- und Datenvertrag bleiben manifestiert. |

Ein Fork ist daher kein vorsorglicher Standard. Er erzeugt eine dauerhafte
Wartungsverpflichtung und darf nicht bloß einen Clone mit eigenem Logo ersetzen.

## Fallentscheidung: `3mre0s/ai-firewall` / Anonmyz

Das Projekt ist Apache-2.0-lizenziert und deckt als lokaler DLP-HTTP(S)-Proxy eine
andere Schicht ab als unser Dokument-Anonymizer. Sinnvoll ist ein **optionaler,
commit-gepinnter Companion-Kandidat**, nicht die Aufnahme in den Anonymizer-Kern
und derzeit kein neues ellmos-Repo.

Startstatus ist `quarantined`: Quellstruktur und Sicherheitskonzept wurden geprüft,
aber die Go-Tests konnten am 2026-08-15 auf diesem Host mangels Go-Laufzeit nicht
lokal ausgeführt werden. Beim aktuellen Upstream-Commit liefen Build, Vet, Tests,
Format und Cross-Compiles in GitHub Actions erfolgreich; der Gesamt-CI-Lauf ist
dennoch rot, weil der Linter ungeprüfte Fehlerrückgaben unter anderem im MITM-
und Proxy-Pfad meldet. Vor `adopted` sind ein vollständig grünes oder bewusst
begründetes CI-Gate, reproduzierbare Tests, ein expliziter Proxy-Modus ohne
standardmäßige MITM-CA, ein Threat-Model-Abgleich, Windows-Deinstallation und ein
Datenfluss-Test mit dem Anonymizer-Vertrag Pflicht.

Fork erst dann, wenn eine notwendige Sicherheits-/Integrationsänderung upstream
nicht zeitnah aufgenommen wird oder ein dauerhaftes ellmos-spezifisches Delta
entsteht. Scheitern Nutzen oder Qualitätsgate, wird der Kandidat ohne Ersatzpflicht
entfernt.
