# Merge-Zwischenstand: upstream/main -> merge-upstream (ERLEDIGT, uncommittet)

## Status: ALLE KONFLIKTE GELOEST, BUILD LAEUFT, tsc SAUBER (nur @ha-Typ-Imports offen, normal)

## Was im Merge kombiniert wurde
### Upstream-Features uebernommen (18 Commits)
- Dashboard-Fix fuer HA 2026.11 / HACS->Marketplace (makeURL: script-Suche + performance-Entries + hacstag)
- 3 Glass-Layouts (dark/light/petrol) + config.layout + DEFAULT_LAYOUT glass-light
- Neuer Editor (Tabs Darstellung/Geraete/Sensoren, devices, sortierbare Info-Eintrage, Suche)
- data-state-Architektur (textState, updateDynamicStyle mit data-run-map/data-temp-vars)
- valve-flaps (setFlap), Fan-Rotation-Fix, Water-Tank-Gradients, room_setpoint, Package-Updates (lit 3.3)
- hacs.json 2026.11.0

### Fork-Features erhalten (22 Commits)
- Kamin (kamin_vl/rl/spr), Domestic (domestic_vl/domstic_rl), alle als optionale Werte
- Perf/Security: SVG-Cache (max 20) + fetchWithTimeout(10s) + Same-Origin-Guard in createDashboard
- shouldUpdate-Gating auf values[].entityId (an neues Modell angepasst)
- flowRate-Kopplung: Pfeile/Arrows nur sichtbar bei reellem Durchfluss (durchfluss-Entity)
- eev/buh-Faerbung (pressure_equalization/buh_power) - parallel zur Upstream-data-state-Loesung
- dist/hpsu.svg als 4. Layout "classic" (Klassisch (Kamin)) registriert

## Wichtige Korrekturen im Merge (gegenueber Fork main)
1. KAMIN CASE-BUG: Fork nutzte parent:"Kamin"/getDomElement("Kamin"), aber die SVG-Gruppe
   heisst id="kamin" (klein, seit Commit 26c6ef1). Kamin-Hiding war seit 26c6ef1 wirkungslos.
   -> Einheitlich auf "kamin" (klein) korrigiert. Grund: getElementById ist case-sensitiv.
2. Kamin/Domestic ins neue Gruppenmodell portiert (group_id + values[] + label_rect_id auf
   Gruppenebene): g6 (kamin_rl), TA2-3 (kamin_vl), g10 (kamin_spr), g11 (domestic_vl),
   g11-6 (domstic_rl sic - Historisch so benannt, unbenannt lassen fuer Config-Kompatibilitaet!)
   parent: "kamin" fuer die 3 Kamin-Gruppen (Layer-Verstecken).
3. domain: "select" BEIBEHALTEN (nicht zu "sensor" geaendert!) - editor.ts filtert Entities
   nach value_item.domain; die Kamin/Domestic-Entities des Nutzers sind select-Entities.
4. device: "MISC" ENTFERNT aus den 5 neuen Werten - Upstream-Editor gruppiert nach
   CAN/UART/Extern(!device); ohne device landen sie in "Extern / ohne Geraet" und sind
   im Editor sichtbar und konfigurierbar. Interface erlaubt MISC weiterhin (abwaertskompatibel).
5. Upstream-Bug unangetastet: doppeltes forEach in updateLabels (upstream/main Z. 589-591)
   - identisch uebernommen, NICHT im Merge gefixt (separater PR an wrfz moeglich).

## Verifikation (alles gruen)
- tsc --noEmit: 0 Fehler (ausser bekannte @ha-Typ-Import-Resolution, nur Typ-Imports)
- parcel build: OK (156.97 kB)
- node --check dist: OK
- 17/17 Inhalts-Checks im Bundle (makeURL-Fix, Kamin, flowRate, SVG-Cache, Layouts, ...)

## Offen / Nacharbeit
- Commit als Merge (git commit - fertig vorbereitet)
- Push + PR-Entscheidung (siehe PR-Kandidaten)
- PR-Kandidaten an wrfz: flowRate-Kopplung, SVG-Cache+Timeout, Kamin/Domestic-Feature,
  forEach-Doppelbug-Fix, Editor-MISC-Luecke ist durch Wegfall von device geloest
