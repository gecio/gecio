---
title: Application Credentials
lang: de
permalink: /optimist/specs/application_credentials/
parent: Spezifikationen
nav_order: 9300
---

# Einführung

Benutzer können Application Credentials erstellen, damit sich ihre Anwendungen bei der OpenStack-Komponente Keystone authentifizieren können, ohne ihre eigenen Anmeldeinformationen des Benutzers verwenden zu müssen.

Mit Application Credentials können sich Anwendungen mit der Application Credential-ID und einer geheimen Zeichenfolge authentifizieren, die nicht das Kennwort des Benutzers ist. Auf diese Weise wird das Passwort des Benutzers nicht in die Konfiguration der Anwendung eingebettet.

Benutzer können eine Teilmenge ihrer Rollenzuweisungen für ein Projekt an Application Credentials delegieren und der Anwendung dieselben oder eingeschränkte Berechtigungen innerhalb eines Projekts erteilen.

# Anforderungen für Application Credentials

### Name / Secrets

Application Credentials für Ihr Projekt können über die Befehlszeile oder über das Dashboard generiert werden. Diese werden dem Projekt zugeordnet, in dem sie erstellt werden.

Der einzige erforderliche Parameter zum Erstellen der Anmeldeinformationen ist ein Name, jedoch kann mit dem Parameter `—-secret` ein bestimmtes Secret festgelegt werden. Wenn der Parameter geheim gelassen wird, wird stattdessen automatisch ein Secret in der Ausgabe generiert.

 Es ist in jedem Fall wichtig, das Secret zu notieren, das Secret vor dem Speichern gehasht wird und nach dem Festlegen nicht wiederhergestellt werden kann. Wenn das Secret verloren geht, sollten neue Application Credential für die Anwendung erstellt werden.

### Roles

Wir empfehlen außerdem, die Roles festzulegen, die die Application Credentials der Anwendung im Projekt haben sollen, da standardmäßig ein neu erstellter Satz von Anmeldeinformationen alle verfügbaren Roles erbt.

Im Folgenden sind die verfügbaren Roles aufgeführt, die einem Satz von Application Credentials zugewiesen werden können. Wenn Sie diese Roles mithilfe des Parameters `--role` auf einen Satz von Application Credentials anwenden, beachten Sie bitte, dass bei allen Role-Namen die Groß-/Kleinschreibung beachtet wird:

- `Member`: Die Rolle "Member" hat nur administrativen Zugriff auf das zugewiesene Projekt.
- `heat_stack_owner`: Als "heat_stack_owner" können Sie vorhandene HEAT-Templates verwenden und ausführen.
- `load-balancer_member`: Als „load-balancer_member“ können Sie die Octavia LoadBalancer-Ressourcen nutzen.
- `reader`: Dies ist eine schreibgeschützte Rolle und erlaubt daher nur Lesezugriff ohne Schreibrechte.

### Expiration

Standardmäßig laufen erstellte Application Credentials nicht ab, jedoch können feste Ablaufdaten/-zeiten für Application Credentials bei der Erstellung festgelegt werden, indem der Parameter `--expires` im Befehl verwendet wird (zum Beispiel: `—expiration '2021-07-15T21: 00:00'`).

# Erstellen von Application Credentials über die CLI

Ein Set von Application Credentials kann im gewünschten Projekt über die CLI erstellt werden, das folgende Beispiel zeigt, wie ein Set von Credentials mit den folgenden Parametern erstellt wird:

- Name: test-credentials
- Secret: ZYQZm2k6pk
- Roles: Member, heat_stack_owner, load-balancer_member
- Expiration Date/Time: 2021-07-12 at 21:00:00

Die neuen Zugangsdaten sollten wie folgt aussehen:

```bash
$ openstack application credential create test-credentials --secret ZYQZm2k6pk --role Member --role heat_stack_owner --role load-balancer_member --expiration '2021-07-15T21:00:00'
+--------------+----------------------------------------------+
| Field        | Value                                        |
+--------------+----------------------------------------------+
| description  | None                                         |
| expires_at   | 2021-07-15T21:00:00.000000                   |
| id           | 707d14e835124b4f957938bb5a57d1be             |
| name         | test-credentials                             |
| project_id   | c704ac5a32b84b54a0407d28ad448399             |
| roles        | Member heat_stack_owner load-balancer_member |
| secret       | ZYQZm2k6pk                                   |
| system       | None                                         |
| unrestricted | False                                        |
| user_id      | 1d9f1ecb5de3607e8982695f72036fa5             |
+--------------+----------------------------------------------+
```

Hinweis: Das Secret (ob vom Benutzer festgelegt oder automatisch generiert) wird beim Erstellen der Application Credentials angezeigt. Bitte beachten Sie das Secret zu diesem Zeitpunkt.

# Anzeigen von Application Credentials über die CLI

Die Liste der zu einem Projekt gehörenden Application-Credentials kann mit dem folgenden Befehl aufgelistet werden.

```bash
$ openstack application credential list
+----------------------------------+-------------------+----------------------------------+-------------+------------+
| ID                               | Name              | Project ID                       | Description | Expires At |
+----------------------------------+-------------------+----------------------------------+-------------+------------+
| 707d14e835124b4f957938bb5a57d1be | test-credentials  | c704ac5a32b84b54a0407d28ad448399 | None        | None       |
+----------------------------------+-------------------+----------------------------------+-------------+------------+
```

Einzelne Credentials können mit dem `$ openstack application credential show <name>` Befehl angezeigt werden.

# Löschen von Application Credentials über die CLI

Application Credentials können über die CLI mit dem folgenden Befehl mit dem Namen oder der ID des spezifischen Satzes von Anmeldeinformationen gelöscht werden:

```bash
$ openstack application credential delete test-credentials
```

# Erstellen und Löschen von Application Credentials für Anwendungen über das Optimist Dashboard

Alternativ können Bewerbungsdaten auch über das Optimist Dashboard unter Identität > Bewerbungsdaten generiert werden:

![](attachments/createappcredentials.png)

Hinweis: Hier können mehrere Rollen ausgewählt werden, indem Sie die Umschalttaste gedrückt halten und durch die Optionen navigieren.

Nach der Erstellung wird ein Dialogfeld angezeigt, in dem Sie aufgefordert werden, die ID und das Secret zu erfassen. Wenn Sie fertig sind, klicken Sie auf "Close".

![](attachments/secretappcredentials.png)

Die Zugangsdaten hier können auch jederzeit gelöscht werden, indem Sie das Kontrollkästchen markieren, um den zu löschenden Zugangsdatensatz zu markieren und dann auf "DELETE APPLICATION CREDENTIAL" klicken.

![](attachments/deleteappcredentials.png)

# Application Credentials testen

Sobald wir über die CLI oder das Dashboard einen Satz von Application Credentials erstellt haben, können wir sie mit dem folgenden curl-Befehl testen, um zu überprüfen, ob sie funktionieren.

Wir müssen unsere `<name>` und `<secret>` im curl-Befehl verwenden:

```bash
$ curl -i -H "Content-Type: application/json" -d ' { "auth": { "identity": { "methods": ["application_credential"],  "application_credential": {  "id": “<id>", "secret": “<secret>"}}}}' "[https://identity.optimist.innovo.cloud/v3/auth/tokens](https://identity.optimist.innovo.cloud/v3/auth/tokens)"
```

Ein erfolgreicher curl-Versuch gibt ein `x-subject-token` aus, erfolglose Versuche mit falschen Anmeldeinformationen führen zu einem 401-Fehler.
