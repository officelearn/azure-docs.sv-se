---
title: Distribuera en fristående Prometheus i ett kluster i Azure Red Hat OpenShift | Microsoft Docs
description: Nedan visas hur du skapar en Prometheus-instans på ett Azure Red Hat OpenShift-kluster för att övervaka programmets mått.
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus aro, openshift, mått, red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342619"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>Distribuera en fristående Prometheus i ett kluster i Azure Red Hat OpenShift

Den här guiden beskriver hur du konfigurerar en fristående Prometheus med tjänstidentifiering i ett kluster i Azure Red Hat OpenShift.  ”Kunden” administratörsåtkomst till klustret behövs inte.

Mål-installationen är följande:

- ett projekt (prometheus-projekt), som innehåller Prometheus och Alertmanager
- två projekt (app-projekt 1 och app-projekt 2), som innehåller de program som ska övervaka

Förbereder du vissa Prometheus configuration-filer lokalt. Skapa en ny mapp för att lagra dem.
Dessa konfigurationsfiler lagras i klustret som hemligheter om hemlig token har lagts till dem senare.

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>Steg 1: Logga in till klustret med den `oc` verktyget
Använd en webbläsare, navigera till webbkonsolen på klustret (https://openshift. *slumpmässigt id*. *region*. azmosa.io).
Logga in med dina Azure autentiseringsuppgifter.
Klicka på ditt användarnamn i längst upp till höger och välj ”Kopiera inloggningskommandot”. Klistra in den i terminalen du ska använda.

Du kan kontrollera om du är inloggad på rätt klustret med den `oc whoami -c` kommando.

## <a name="step-2-prepare-the-projects"></a>Steg 2: Förbereda projekt

Skapa projekt.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Du kan använda den `-n` eller `--namespace` parametern eller välj en aktiv projektet med den `oc project` kommando

## <a name="step-3-prepare-prometheus-config"></a>Steg 3: Förbereda Prometheus config
Skapa en fil med namnet prometheus.yml med följande innehåll.
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
Skapa en hemlighet med namnet ”Höj” med konfigurationen.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Filen som anges ovan är en grundläggande Prometheus-konfigurationsfil.
Anger intervallen och konfigurerar den automatiska identifieringen i tre projekt (prometheus-projekt, app-projekt 1, app-projekt 2).
I det här exemplet identifierade automatiskt slutpunkter kommer skrapats via HTTP utan autentisering.
Mer information om skrapning slutpunkter finns i https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config.


## <a name="step-4-prepare-alertmanager-config"></a>Steg 4: Förbereda Alertmanager config
Skapa en fil med namnet alertmanager.yml med följande innehåll.
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
Skapa en hemlighet med namnet ”Höj aviseringar” med konfigurationen.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Den fil som anges ovan är Alert Manager-konfigurationsfil.

> [!NOTE]
> Du kan kontrollera de två föregående stegen med `oc get secret -n prometheus-project`

## <a name="step-5-start-prometheus-and-alertmanager"></a>Steg 5: Starta Prometheus och Alertmanager
Ladda ned den [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) mall från den [openshift/ursprungslagringsplats](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) och tillämpa den i prometheus-projektet
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus standalone.yaml-filen är en mall för OpenShift, vilket skapar en Prometheus-instans med en oauth-proxy framför den och en Alertmanager-instans som också skyddas med oauth-proxy.  I den här mallen oauth-proxy har konfigurerats för att tillåta alla användare som kan ”hämta” ”prometheus-projekt” namnområdet (se den `-openshift-sar` flaggan).

> [!NOTE]
> Du kan kontrollera om ”Höj” StatefulSet har lika *ÖNSKAD* och *aktuella* number repliker med den `oc get statefulset -n prometheus-project` kommando.
> Du kan också kontrollera alla resurser i projektet med `oc get all -n prometheus-project`.

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>Steg 6: Lägg till behörigheter för att tillåta identifiering av tjänst
Skapa prometheus sdrole.yml med följande innehåll.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
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
Tillämpa den för alla projekt där du vill tillåta tjänstidentifiering.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Om du vill också Prometheus för att kunna samla in mått från själva, Kom ihåg att tillämpa behörigheterna i prometheus-projekt för.

> [!NOTE]
> Du kan kontrollera om rollen och RoleBinding skapades korrekt med den `oc get role` och `oc get rolebinding` respektive-kommandon

## <a name="optional-deploy-example-application"></a>Valfritt: Distribuera exempelprogram
Allt fungerar, men det finns inga mått-källor. Gå till URL: en för Prometheus (https://prom-prometheus-project.apps. *slumpmässigt id*. *region*.azmosa.io/), som finns med följande kommando.
```
oc get route prom -n prometheus-project
```
Kom ihåg att prefixet värdnamn med https://.

Den **Status > tjänstidentifiering** sidan visar 0/0 aktiva mål.

Du distribuerar ett exempelprogram som visar grundläggande python-måtten under /metrics-slutpunkten kör du följande kommandon.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
De nya program visas som giltig mål på sidan tjänstidentifiering inom 30 sekunder efter distributionen. Du kan få mer information om den **Status > mål** sidan.

> [!NOTE]
> Lägger till en datapoint i mått ”upp” för varje har scraped mål Prometheus. Klicka på **Prometheus** hörnet längst upp till vänster och ange ”dig” som uttryck och klicka på **kör**.

## <a name="next-steps"></a>Nästa steg

Du kan lägga till anpassad Prometheus instrumentation i dina program.

Prometheus klientbiblioteket, vilket förenklar förberedande Prometheus mått är redo för olika programmeringsspråk.

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - Go https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
