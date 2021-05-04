---
title: "Migration: Loadbalancer"
lang: de
permalink: /optimist/migration_loadbalancer/
nav_order: 7000
last_modified_date: 2021-04-30
---

# Migration: Neutron LBaas (Deprecated) zu Octavia Loadbalancer

Mit dem Release von OpenStack Train (erwartet in Q4/2021) entfällt die Unterstützung für die veralteten Neutron LBaas-Loadbalancer. Als Vorbereitung zu diesem Update betrachten wir auch bei uns Neutron Loadbalancer als "Deprecated". Zukünftig unterstützen wir ausschließlich die Loadbalancer der Octavia-Komponente. Daher ist es wichtig, bestehende Neutron Balancer zu Octavia Balancern zu migrieren, bevor die Unterstützung weg fällt.

Um den Prozess möglichst einfach zu gestalten, haben wir hier einen kurzen Guide zusammen gestellt, um Sie bei der Migration zu unterstützen.

Die folgenden Schritte sind nötig:
- Neutron Loadbalancer auflisten
- Details der Loadbalancer ausgeben
- Einen neuen Octavia-Balancer mit den gleichen Einstellungen anlegen
- Abhängen der Floating IP des LBaas
- Anhängen der Floating IP an den neuen Loadbalancer

Sobald alle Schritte durchgeführt wurden, ist die Migration abgeschlossen. Nachfolgend beschreiben wir die Prozedur im Detail.

Alle Schritte finden auf der Kommandozeile statt.

## Wichtige Hinweise

Die Migration ist nur für Loadbalancer nötig, die **nicht** durch IMKE angelegt wurden.

Außerdem ist für den Zeitraum zwischen Abhängen und Wieder-Anhängen der Floating IP Ihr dahinter betriebener Service **offline**. Um das zu vermeiden, können Sie auch alternativ dem neuen Load-Balancer eine neue Floating IP zuweisen und Ihre verwendeten Nameserver entsprechend umkonfigurieren. Dabei sind dann für einen gewissen Zeitraum beide Loadbalancer aktiv.


## Neutron Loadbalancer auflisten

Für die ersten Schritte ist der Neutron-Client notwendig. Diesen installieren Sie mit `pip install python-neutronclient`.

Mit folgendem Befehl listen Sie alle Neutron Balancer in Ihrem Projekt auf:

```bash
$ neutron lbaas-loadbalancer-list
neutron CLI is deprecated and will be removed in the future. Use openstack CLI instead.
+--------------------------------------+--------------------+-------------+---------------------+----------+
| id                                   | name               | vip_address | provisioning_status | provider |
+--------------------------------------+--------------------+-------------+---------------------+----------+
| 2550d3ea-fa73-4923-a6df-1b0c828a8ff1 | Load Balancer Test | 10.0.0.19   | ACTIVE              | haproxy  |
<<<<<<< HEAD
| f731f07d-4612-4f94-81a3-ccb65a1ae1f0 | Apr27NeutronLB     | 192.168.5.7 | ACTIVE              | haproxy  |
=======
| f731f07d-4612-4f94-81a3-ccb65a1ae1f0 | ExampleNeutronLB   | 192.168.5.7 | ACTIVE              | haproxy  |
>>>>>>> e09c08eba2065a7f9a7aa9e4e1a574e743d88bdc
+--------------------------------------+--------------------+-------------+---------------------+----------+
```

## Details der Loadbalancer ausgeben

Details zu den einzelnen Balancern:

```bash
<<<<<<< HEAD
$ neutron lbaas-loadbalancer-show Apr27NeutronLB
=======
$ neutron lbaas-loadbalancer-show ExampleNeutronLB
>>>>>>> e09c08eba2065a7f9a7aa9e4e1a574e743d88bdc
neutron CLI is deprecated and will be removed in the future. Use openstack CLI instead.
+---------------------+--------------------------------------+
| Field               | Value                                |
+---------------------+--------------------------------------+
| admin_state_up      | True                                 |
| description         |                                      |
| id                  | f731f07d-4612-4f94-81a3-ccb65a1ae1f0 |
| listeners           |                                      |
<<<<<<< HEAD
| name                | Apr27NeutronLB                       |
=======
| name                | ExampleNeutronLB                     |
>>>>>>> e09c08eba2065a7f9a7aa9e4e1a574e743d88bdc
| operating_status    | ONLINE                               |
| pools               |                                      |
| provider            | haproxy                              |
| provisioning_status | ACTIVE                               |
| tenant_id           | c906ac5a32b34b54a0398d28ad448301     |
| vip_address         | 192.168.5.7                          |
| vip_port_id         | 2cc1b3f9-0457-410b-93dd-a96338de3b12 |
| vip_subnet_id       | c2bff369-0e62-44b3-89af-59ba6950094f |
+---------------------+--------------------------------------+
```

Alle Ressourcen innerhalb des Balancers finden Sie mit folgenden Befehlen:

```bash
$ neutron lbaas-healthmonitor-list
$ neutron lbaas-member-list
$ neutron lbaas-pool-list
$ neutron lbaas-listener-list
```

## Die Floating IP Ihres Balancers

<<<<<<< HEAD
In der vorherigen Ausgabe (`lbass-loadbalancer-show`) sehen Sie eine Zeile `vip_port_id`. Mit dieser ID finden Sie mit nachfolgendem Befehl Ihre Floating IP (und deren UUID). In unserem Beispiel wäre das für den Port (ID: `2cc1b3f9-0457-410b-93dd-a96338de3b12`) die IP `45.94.110.184` (mit ID `cd17e8b1-3d4b-417a-9a80-a21a9879a8dc`). Diese Information brauchen wir später.
=======
In der vorherigen Ausgabe (`lbass-loadbalancer-show`) sehen Sie eine Zeile `vip_port_id`. Mit dieser ID finden Sie mit nachfolgendem Befehl Ihre Floating IP (und deren UUID). In unserem Beispiel wäre das für den Port (ID: `2cc1b3f9-0457-410b-93dd-a96338de3b12`) die IP `45.94.111.185` (mit ID `cd17e8b1-3e4c-417b-9c80-a21a9879a8dc`). Diese Information brauchen wir später.
>>>>>>> e09c08eba2065a7f9a7aa9e4e1a574e743d88bdc

```bash
$ openstack floating ip list --port 2cc1b3f9-0457-410b-93dd-a96338de3b12
+--------------------------------------+------------------+---------------------+--------------------------------------+
| id                                   | fixed_ip_address | floating_ip_address | port_id                              |
+--------------------------------------+------------------+---------------------+--------------------------------------+
<<<<<<< HEAD
| cd17e8b1-3d4b-417a-9a80-a21a9879a8dc | 192.168.5.7      | 45.94.110.184       | 2cc1b3f9-0457-410b-93dd-a96338de3b12 |
=======
| cd17e8b1-3e4c-417b-9c80-a21a9879a8dc | 192.168.5.7      | 45.94.111.185       | 2cc1b3f9-0457-410b-93dd-a96338de3b12 |
>>>>>>> e09c08eba2065a7f9a7aa9e4e1a574e743d88bdc
+--------------------------------------+------------------+---------------------+--------------------------------------+
```

## Einen neuen Octavia-Balancer mit den gleichen Einstellungen anlegen

<<<<<<< HEAD
Sobald der zu migrierende Neutron Loadbalancer identifiziert wurde, muss ein neuer Octavia-Balancer mit den gleichen Einstellungen angelegt werden (siehe auch [Schritt 24 in unserem Tutorial](/optimist/guided_tour/step24/)). 
=======
Sobald der zu migrierende Neutron Loadbalancer identifiziert wurde, muss ein neuer Octavia-Balancer mit den gleichen Einstellungen angelegt werden (siehe auch [Schritt 24 in unserem Tutorial](/optimist/guided_tour/step24)). 
>>>>>>> e09c08eba2065a7f9a7aa9e4e1a574e743d88bdc

Nachdem der neue Balancer eingerichtet wurde, notieren Sie sich bitte die ID des VIP ports, diese Information wird im letzten Schritt benötigt:

```bash
<<<<<<< HEAD
$ openstack loadbalancer show Apr27OctLB -f value -c vip_port_id
bde93be2-c0c1-4e23-af02-e0c4300ff8c0
=======
$ openstack loadbalancer show ExampleOctLB -f value -c vip_port_id
bde93be2-c0c1-5b32-02a3-e0c4300ff8c0
>>>>>>> e09c08eba2065a7f9a7aa9e4e1a574e743d88bdc
```

## Abhängen der Floating IP des LBaaS

Jetzt kommt der finale Schritt: Das Abhängen der Floating IP vom Neutron Balancer und das Wieder-Anhängen an den neuen Octavia-Balancer. 

<<<<<<< HEAD
Zuerst detachen wir die IP vom Neutron Port. Die UUID der Floating IP (`45.94.110.184`) ist in unserem Beispiel `cd17e8b1-3d4b-417a-9a80-a21a9879a8dc`.

```bash
$ openstack floating ip unset --port 2cc1b3f9-0457-410b-93dd-a96338de3b12 cd17e8b1-3d4b-417a-9a80-a21a9879a8dc
Disassociated floating IP cd17e8b1-3d4b-417a-9a80-a21a9879a8dc
=======
Zuerst detachen wir die IP vom Neutron Port. Die UUID der Floating IP (`45.94.111.185`) ist in unserem Beispiel `cd17e8b1-3e4c-417b-9c80-a21a9879a8dc`.

```bash
$ openstack floating ip unset --port 2cc1b3f9-0457-410b-93dd-a96338de3b12 cd17e8b1-3e4c-417b-9c80-a21a9879a8dc
Disassociated floating IP cd17e8b1-3e4c-417b-9c80-a21a9879a8dc
>>>>>>> e09c08eba2065a7f9a7aa9e4e1a574e743d88bdc
```

## Anhängen der Floating IP an den neuen Loadbalancer

Direkt darauffolgend attachen wir die gleiche Floating IP, die wir gerade vom Neutron LB Port abgehängt haben, an den Octavia Port:

<<<<<<< HEAD
```bash
LB VIP Port: bde93be2-c0c1-4e23-af02-e0c4300ff8c0
Floating IP: cd17e8b1-3d4b-417a-9a80-a21a9879a8dc

$ openstack floating ip set cd17e8b1-3d4b-417a-9a80-a21a9879a8dc --port bde93be2-c0c1-4e23-af02-e0c4300ff8c0
```

Wenn alles funktioniert hat, ist jetzt der neue Octavia-Balancer aktiv.
=======
LB VIP Port: `bde93be2-c0c1-5b32-02a3-e0c4300ff8c0`

Floating IP: `cd17e8b1-3e4c-417b-9c80-a21a9879a8dc`

```bash
$ openstack floating ip set cd17e8b1-3e4c-417b-9c80-a21a9879a8dc --port bde93be2-c0c1-5b32-02a3-e0c4300ff8c0
```

Wenn alles funktioniert hat, ist jetzt der neue Octavia-Balancer aktiv.
>>>>>>> e09c08eba2065a7f9a7aa9e4e1a574e743d88bdc