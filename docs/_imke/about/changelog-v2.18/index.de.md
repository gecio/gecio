---
title: iMKE Changelog v2.18
lang: de
permalink: /imke/about/changelog-v2.18/
nav_order: 900
parent: Über iMKE
---

## Unterstützte Kubernetes Versionen

Im Rahmen des aktuellen Release werden die folgenden Kubernetes-Versionen unterstützt:

* 1.19.15
* 1.20.11
* 1.21.5

## End of Life Ankündigungen

Wir werden die Unterstützung der Kubernetes Version v1.19 am 03.11.2021 beenden.

Bitte updaten Sie alle bestehenden Cluster mit Kubernetes Version 1.19 auf mindestens 1.20 bis zu diesem Datum.

## Neue Funktionen

* [Cluster Templates](/imke/clusterlifecycle/clustertemplates/) werden ab sofort unterstützt
* Ältere Cluster (erstellt vor v1.17) können jetzt [auf den externen Cloud Controller Manager migriert werden](/imke/clusterlifecycle/clustermigrations/externalcloudprovider/)
* `containerd` wird ab sofort als Container Runtime unterstützt ([Wie Sie Ihre Cluster zu containerd umziehen können](/imke/clusterlifecycle/clustermigrations/containerruntimeengine/))

## Bugfixes

* Um [CVE-2021-25741](https://github.com/kubernetes/kubernetes/issues/104980) zu mitigieren wurden die unterstützten Kubernetes-Versionen aktualisiert. Die Controlplanes der Cluster wurden automatisch upgedated. Bitte stellen Sie ein zeitnahes Updates Ihrer Machine Deployments sicher. Bei einem Update der Machine Deployments kann es durch den rollierenden Neustart der Worker-Nodes zu rollierenden Restarts der Deployments und Statefulsets kommen.

## Änderungen in Kubernetes

### Upgrade-Hinweise für Kubernetes 1.21

Wenn Sie ein Upgrade auf Kubernetes 1.21 planen, lesen Sie bitte den Abschnitt [What's New](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.21.md#whats-new-major-themes) des offiziellen Kubernetes v1.21 Changelogs und machen Sie sich mit den bevorstehenden Änderungen vertraut.

Eine Übersicht über die Änderungen finden Sie im Abschnitt [Changes by Kind](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.21.md#changes-by-kind-2) des Changelogs.

* [Wichtige Hinweise zum Upgrade](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.21.md#urgent-upgrade-notes)
* [Deprecations](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.21.md#deprecation)
* [API-Änderungen](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.21.md#api-change-1)
* [Features](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.21.md#feature-2)