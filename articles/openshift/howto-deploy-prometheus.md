---
title: Distribuera en fristående Prometheus instans i ett kluster i Azure Red Hat OpenShift | Microsoft Docs
description: Skapa en Prometheus-instans i ett Azure Red Hat OpenShift-kluster för att övervaka programmets mått.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus aro, openshift, mått, red hat
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827025"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Distribuera en fristående Prometheus instans i ett kluster i Azure Red Hat OpenShift

Den här artikeln beskriver hur du konfigurerar en fristående Prometheus-instans som använder tjänstidentifiering i ett kluster i Azure Red Hat OpenShift.

> [!NOTE]
> Kunden administratörsåtkomst till Azure Red Hat OpenShift kluster krävs inte.

Mål-inställning:

- Ett projekt (prometheus-projekt), som innehåller Prometheus och Alertmanager.
- Två projekt (app-projekt 1 och app-projekt 2), som innehåller de program som ska övervaka.

Förbereder du vissa Prometheus konfigurationsfiler lokalt. Skapa en ny mapp för att lagra dem. Konfigurationsfiler lagras i klustret som hemligheter, om hemlig token läggs till senare i klustret.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Logga in till klustret med hjälp av verktyget OC

1. Öppna en webbläsare och gå sedan till webbkonsolen för ditt kluster (https://openshift. *slumpmässigt id*. *region*. azmosa.io).
2. Logga in med dina Azure autentiseringsuppgifter.
3. Välj ditt användarnamn i det övre högra hörnet och välj sedan **kopia inloggningskommandot**.
4. Klistra in ditt användarnamn i terminalen som du använder.

> [!NOTE]
> För att se om du är inloggad i rätt klustret, kör den `oc whoami -c` kommando.

## <a name="prepare-the-projects"></a>Förbereda projekt

För att skapa projekt, kör du följande kommandon:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Du kan använda den `-n` eller `--namespace` parametern eller välj en aktiva projektet genom att köra den `oc project` kommando.

## <a name="prepare-the-prometheus-configuration-file"></a>Förbereda Prometheus konfigurationsfilen
Skapa en prometheus.yml-fil genom att ange följande innehåll:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Skapa en hemlighet som kallas Höj genom att ange följande konfiguration:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Prometheus.yml-filen är en grundläggande Prometheus-konfigurationsfil. Anger intervallen och konfigurerar den automatiska identifieringen i tre projekt (prometheus-projekt, app-projekt 1, app-projekt 2). I föregående konfigurationsfilen skrapats identifierades automatiskt-slutpunkter via HTTP utan autentisering.

Läs mer om skrapning slutpunkter [Prometheus ESC config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Förbereda Alertmanager config-fil
Skapa en alertmanager.yml-fil genom att ange följande innehåll:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Skapa en hemlighet som kallas Höj aviseringar genom att ange följande konfiguration:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml är Alert Manager-konfigurationsfil.

> [!NOTE]
> Kontrollera de två föregående stegen med den `oc get secret -n prometheus-project` kommando.

## <a name="start-prometheus-and-alertmanager"></a>Starta Prometheus och Alertmanager
Gå till [openshift/ursprungslagringsplats](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) och ladda ned den [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) mall. Använda mallen till prometheus-projekt genom att ange följande konfiguration:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus standalone.yaml-filen är en mall för OpenShift. Den skapar en Prometheus-instans med oauth-proxy framför den och en Alertmanager-instans som också skyddas med oauth-proxy. I den här mallen oauth-proxy har konfigurerats för att tillåta alla användare som kan ”hämta” prometheus-projekt namnområdet (se den `-openshift-sar` flaggan).

> [!NOTE]
> Kontrollera om Höj StatefulSet innehåller lika med ÖNSKAD och aktuellt antal repliker med den `oc get statefulset -n prometheus-project` kommando. Kontrollera alla resurser i projektet genom att köra den `oc get all -n prometheus-project` kommando.

## <a name="add-permissions-to-allow-service-discovery"></a>Lägg till behörigheter för att tillåta identifiering av tjänst

Skapa en prometheus sdrole.yml-fil genom att ange följande innehåll:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Om du vill använda mallen för alla projekt som du vill tillåta tjänstidentifiering, kör du följande kommandon:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Om du vill att Prometheus att samla in mått från själva tillämpa behörigheterna i prometheus-projekt.

> [!NOTE]
> Kontrollera att rollen och RoleBinding skapades korrekt med den `oc get role` och `oc get rolebinding` kommandon.

## <a name="optional-deploy-example-application"></a>Valfritt: Distribuera exempelprogram

Allt fungerar, men det finns inga mått-källor. Gå till URL: en för Prometheus (https://prom-prometheus-project.apps. *slumpmässigt id*. *region*.azmosa.io/). Du hittar det genom att använda följande kommando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Kom ihåg att lägga till prefixet https:// i början av värdnamnet.

Den **Status > tjänstidentifiering** sidan visar 0/0 aktiva mål.

Om du vill distribuera ett exempelprogram som visar grundläggande Python-måtten under /metrics-slutpunkten kör du följande kommandon:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
De nya program som ska visas som giltig mål på sidan tjänstidentifiering inom 30 sekunder efter distributionen.

Mer information, Välj **Status** > **mål**.

> [!NOTE]
> Lägger till en datapunkt i dig mått för varje har scraped mål, Prometheus. Välj **Prometheus** i det övre vänstra hörnet, ange **upp** som uttryck och välj sedan **kör**.

## <a name="next-steps"></a>Nästa steg

Du kan lägga till anpassad Prometheus instrumentation i dina program. Prometheus klientbiblioteket, vilket förenklar Prometheus mått förberedelse är redo för olika programmeringsspråk.

Mer information finns i följande GitHub-bibliotek:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
