---
title: Distribuera en fristående Prometheus-instans i ett Azure Red Hat OpenShift-kluster | Microsoft-dokument
description: Skapa en Prometheus-instans i ett Azure Red Hat OpenShift-kluster för att övervaka programmets mått.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, statistik, röd hatt
ms.openlocfilehash: f81a993caa31578e689fb3a90108f3cf0ca81fc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69875124"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Distribuera en fristående Prometheus-instans i ett Azure Red Hat OpenShift-kluster

I den här artikeln beskrivs hur du konfigurerar en fristående Prometheus-instans som använder tjänstidentifiering i ett Azure Red Hat OpenShift-kluster.

> [!NOTE]
> Kundadministratörsåtkomst till Azure Red Hat OpenShift-klustret krävs inte.

Målinställning:

- Ett projekt (prometheus-projekt), som innehåller Prometheus och Alertmanager.
- Två projekt (app-projekt1 och app-projekt2), som innehåller de program som ska övervakas.

Du förbereder några Prometheus config filer lokalt. Skapa en ny mapp för att lagra dem. Config-filer lagras i klustret som hemligheter, om hemliga token läggs till senare i klustret.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Logga in i klustret med hjälp av OC-verktyget

1. Öppna en webbläsare och gå sedan till konsolenhttps://openshifti klustret ( .* slumpmässigt id*. *regionen*.azmosa.io).
2. Logga in med dina Azure autentiseringsuppgifter.
3. Välj ditt användarnamn i det övre högra hörnet och välj sedan **Kopiera inloggningskommando**.
4. Klistra in ditt användarnamn i terminalen som du ska använda.

> [!NOTE]
> Om du vill se om du är inloggad `oc whoami -c` i rätt kluster kör du kommandot.

## <a name="prepare-the-projects"></a>Förbered projekten

Om du vill skapa projekten kör du följande kommandon:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Du kan antingen `-n` `--namespace` använda parametern eller välja ett `oc project` aktivt projekt genom att köra kommandot.

## <a name="prepare-the-prometheus-configuration-file"></a>Förbered prometheus-konfigurationsfilen
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
Skapa en hemlighet som kallas Prom genom att ange följande konfiguration:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Prometheus.yml filen är en grundläggande Prometheus konfigurationsfil. Den anger intervall och konfigurerar automatisk identifiering i tre projekt (prometheus-projekt, app-projekt1, app-projekt2). I den tidigare konfigurationsfilen skrapas de automatiskt identifierade slutpunkterna över HTTP utan autentisering.

Mer information om skrapning av slutpunkter finns i [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Förbereda Alertmanager-config-filen
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
Skapa en hemlighet som kallas Prom-Alerts genom att ange följande konfiguration:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml är konfigurationsfilen för Alert Manager.

> [!NOTE]
> Om du vill kontrollera de `oc get secret -n prometheus-project` två föregående stegen kör du kommandot.

## <a name="start-prometheus-and-alertmanager"></a>Starta Prometheus och Alertmanager
Gå till [openshift/origin-databasen](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) och ladda ned [mallen prometheus-standalone.yaml.](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) Använd mallen på prometheus-projektet genom att ange följande konfiguration:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Filen prometheus-standalone.yaml är en OpenShift-mall. Det kommer att skapa en Prometheus instans med oauth-proxy framför den och en Alertmanager instans, också säkrad med oauth-proxy. I den här mallen är oauth-proxy konfigurerad så att alla användare som kan `-openshift-sar` "få" prometheus-projektets namnområde (se flaggan).

> [!NOTE]
> Om du vill kontrollera om prom statefulSet har repliker med önskat och aktuellt nummer kör `oc get statefulset -n prometheus-project` du kommandot. Om du vill kontrollera alla `oc get all -n prometheus-project` resurser i projektet kör du kommandot.

## <a name="add-permissions-to-allow-service-discovery"></a>Lägga till behörigheter för att tillåta identifiering av tjänsten

Skapa en prometheus-sdrole.yml fil genom att ange följande innehåll:
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
Om du vill använda mallen på alla projekt som du vill tillåta identifiering av tjänsten från kör du följande kommandon:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Om du vill kontrollera att roll- och rollbindning har skapats korrekt kör du `oc get role` kommandona och. `oc get rolebinding`

## <a name="optional-deploy-example-application"></a>Valfritt: Distribuera exempelprogram

Allt fungerar, men det finns inga mätkällor. Gå till Prometheushttps://prom-prometheus-project.appsURL ( .* slumpmässigt id*. *regionen*.azmosa.io/). Du hittar den med följande kommando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Kom ihåg att lägga till https:// prefixet i början av värdnamnet.

Sidan **Status > Service Discovery** visar 0/0 aktiva mål.

Om du vill distribuera ett exempelprogram som exponerar grundläggande Python-mått under slutpunkten /mått kör du följande kommandon:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
De nya programmen ska visas som giltiga mål på sidan Tjänstidentifiering inom 30 sekunder efter distributionen.

Om du vill ha mer information väljer du > **Statusmål**. **Status**

> [!NOTE]
> För varje framgångsrikt skrapat mål lägger Prometheus till en datapunkt i uppmåttet. Välj **Prometheus** i det övre vänstra hörnet, ange **som** uttryck och välj sedan **Kör**.

## <a name="next-steps"></a>Nästa steg

Du kan lägga till anpassade Prometheus instrumentation till dina program. Prometheus Client-biblioteket, som förenklar Prometheus-metriska förberedelse, är redo för olika programmeringsspråk.

Mer information finns i följande GitHub-bibliotek:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Kör](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
