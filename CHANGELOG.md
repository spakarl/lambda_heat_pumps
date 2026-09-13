# Changelog

**Deutsche Version siehe unten / [German version see below](#deutsche-version)**

> 📜 Full version history: [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md) · Vollständige Versionshistorie: [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md)

<!-- lang:en -->
## English Version

> **📚 Documentation**: A German documentation is currently being built at [https://guidojeuken-6512.github.io/lambda_heat_pumps](https://guidojeuken-6512.github.io/lambda_heat_pumps)

### [3.5.5] - 2026-09-12

Fixed a register conflict reported against firmware 3+: heating-circuit register 7 is repurposed on firmware 3 from the writable setpoint request to the read-only value the controller actually acts on, but the old writable sensor (`flow_line_temperature_setpoint`) had no upper firmware bound, so it kept being created alongside the new read-only one (`target_temp_flow_line`) — both reading and writing the same address. `flow_line_temperature_setpoint` is now restricted to firmware 1-2, the range it still means what its name says. See [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md) for details. [Issue #112](https://github.com/GuidoJeuken-6512/lambda_heat_pumps/issues/112)

### [3.5.4] - 2026-09-09

Fixed another Home Assistant deprecation warning, found by auditing the real system log of two live installations after a Home Assistant 2026.9.1 upgrade: `device_registry.async_get_device()`, used to resolve a sub-device's parent for `via_device_id`, is deprecated and scheduled for removal in Home Assistant 2027.8.0. Replaced with `async_get_device_by_identifier()`. Verified against real hardware — the full test suite, a live remove/recreate of the config entry, and an end-to-end run of the actual config flow all pass unchanged. See [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md) for details.

### [3.5.3] - 2026-09-06

Fixed a functional regression from the 3.5.0 rewrite found via live testing: every Modbus write used FC06 (Write Single Register), which Lambda's own protocol documentation says is not implemented at all — every write (room-thermostat control, PV-surplus export, hot-water/heating-circuit/cooling-circuit setpoints, the generic register-write service) failed with "Illegal Function". Every writable register now writes via FC16 (Write Multiple Registers), matching the pre-3.5 code and Lambda's documented protocol. See [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md) for details.

### [3.5.2] - 2026-09-06

Follow-up to the 3.5.0 rewrite adoption: fixed three gaps found by auditing every 2.7.x/2.8.x bugfix from the pre-rewrite `main` branch against the rewritten codebase — a `via_device` deprecation warning on sub-devices, a `0xFFFF` sentinel ("no request"/"no external sensor") read as a real value (e.g. exactly -300.0 °C for the outside temperature), and Modbus reads/writes not actually being serialized against each other (the class of issue behind #105). See [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md) for details and the new regression tests.

### [3.5.0] - 2026-09-06

Adopted a ground-up rewrite of the integration (PR #115): the Modbus layer moves from `pymodbus` to [`modbus-connection`](https://github.com/home-assistant-libs/modbus-connection)/`tmodbus`, heat pumps and other modules are addressed by index and object reference instead of reconstructed entity-id strings (removing the bug class behind Issues #93/#107), and counters persist via plain Home Assistant `RestoreSensor`s. Minimum Home Assistant version is now 2026.9.0. See [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md) for the full breakdown.

<!-- /lang:en -->
## Deutsche Version {#deutsche-version}

<!-- lang:de -->

> **📚 Dokumentation**: Eine deutsche Dokumentation wird derzeit unter [https://guidojeuken-6512.github.io/lambda_heat_pumps](https://guidojeuken-6512.github.io/lambda_heat_pumps) aufgebaut

### [3.5.5] - 2026-09-12

Einen gemeldeten Register-Konflikt ab Firmware 3 behoben: Heizkreis-Register 7 wird ab Firmware 3 umgewidmet — vom schreibbaren angeforderten Sollwert zum read-only Wert, den der Regler tatsächlich verwendet — aber der alte schreibbare Sensor (`flow_line_temperature_setpoint`) hatte keine obere Firmware-Grenze und wurde weiterhin parallel zum neuen read-only Sensor (`target_temp_flow_line`) angelegt — beide lesen und schreiben dieselbe Adresse. `flow_line_temperature_setpoint` ist jetzt auf Firmware 1-2 begrenzt, den Bereich, in dem der Name noch stimmt. Details siehe [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md). [Issue #112](https://github.com/GuidoJeuken-6512/lambda_heat_pumps/issues/112)

### [3.5.4] - 2026-09-09

Eine weitere Home-Assistant-Deprecation-Warnung behoben, gefunden bei der Auswertung der echten System-Logs zweier laufender Installationen nach einem Update auf Home Assistant 2026.9.1: `device_registry.async_get_device()`, genutzt um das übergeordnete Gerät eines Untergeräts für `via_device_id` zu finden, ist deprecated und wird in Home Assistant 2027.8.0 entfernt. Ersetzt durch `async_get_device_by_identifier()`. Gegen echte Hardware verifiziert — die volle Testsuite, ein Live-Entfernen/Neuanlegen des Config-Entry sowie ein kompletter Durchlauf des echten Config-Flows laufen unverändert durch. Details siehe [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md).

### [3.5.3] - 2026-09-06

Eine Funktionsregression aus dem 3.5.0-Rewrite behoben, gefunden beim Live-Test: Jeder Modbus-Schreibvorgang nutzte FC06 (Write Single Register), das laut Lambdas eigener Protokolldokumentation gar nicht implementiert ist — jeder Schreibvorgang (Raumthermostat-Steuerung, PV-Überschuss-Export, Warmwasser-/Heizkreis-/Kühlkreis-Sollwerte, der generische Register-Schreib-Service) scheiterte mit "Illegal Function". Jedes schreibbare Register nutzt jetzt FC16 (Write Multiple Registers), wie schon der Vor-3.5-Code und wie von Lambda dokumentiert. Details siehe [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md).

### [3.5.2] - 2026-09-06

Nachzieharbeiten zur 3.5.0-Rewrite-Übernahme: drei Lücken behoben, gefunden bei der Prüfung aller 2.7.x/2.8.x-Bugfixes des Vor-Rewrite-Branches `main` gegen den neu geschriebenen Code — eine `via_device`-Deprecation-Warnung bei Sub-Geräten, ein als echter Wert gelesener `0xFFFF`-Sonderwert ("keine Anforderung"/"kein externer Sensor", z. B. exakt -300,0 °C bei der Außentemperatur) sowie nicht tatsächlich serialisierte Modbus-Lese-/Schreibvorgänge (dieselbe Fehlerklasse wie Issue #105). Details und die neuen Regressionstests siehe [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md).

### [3.5.0] - 2026-09-06

Übernahme eines von Grund auf neu geschriebenen Codes für die Integration (PR #115): Die Modbus-Schicht wechselt von `pymodbus` zu [`modbus-connection`](https://github.com/home-assistant-libs/modbus-connection)/`tmodbus`, Wärmepumpen und andere Module werden über Index und Objektreferenz statt rekonstruierter entity_id-Strings adressiert (behebt die Fehlerklasse hinter den Issues #93/#107), und Zähler persistieren über normale Home-Assistant-`RestoreSensor`s. Mindest-Home-Assistant-Version ist jetzt 2026.9.0. Vollständige Aufschlüsselung siehe [CHANGELOG_ALL_CHANGES.md](CHANGELOG_ALL_CHANGES.md).

<!-- /lang:de -->
