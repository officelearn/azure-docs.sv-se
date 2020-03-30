---
title: Kör privilegierade behållare i ett Azure Red Hat OpenShift-kluster | Microsoft-dokument
description: Kör privilegierade behållare för att övervaka säkerhet och efterlevnad.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, röd hatt
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271364"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Köra privilegierade containers i ett Azure Red Hat OpenShift-kluster

Du kan inte köra godtyckliga privilegierade behållare på Azure Red Hat OpenShift-kluster.
Två säkerhetsövervaknings- och efterlevnadslösningar tillåts köras på ARO-kluster.
I det här dokumentet beskrivs skillnaderna från den allmänna OpenShift-distributionsdokumentationen för leverantörerna av säkerhetsprodukter.


Läs igenom dessa instruktioner innan du följer leverantörens instruktioner.
Avsnittsrubriker i produktspecifika steg nedan refererar direkt till avsnittsrubriker i leverantörens dokumentation.

## <a name="before-you-begin"></a>Innan du börjar

Dokumentationen för de flesta säkerhetsprodukter förutsätter att du har klusteradministratörsbehörighet.
Kundadministratörer har inte alla privilegier i Azure Red Hat OpenShift. Behörigheter som krävs för att ändra klusteromfattande resurser är begränsade.

Kontrollera först att användaren är inloggad i klustret som `oc get scc`kundadministratör genom att köra . Alla användare som är medlemmar i kundadministratörsgruppen har behörighet att visa säkerhetskontextbegränsningar (SPC: er) i klustret.

Kontrollera sedan att `oc` den `3.11.154`binära versionen är .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Produktspecifika steg för Aqua Security
De grundläggande instruktioner som kommer att ändras finns i [Dokumentationen](https://docs.aquasec.com/docs/openshift-red-hat)för Distribution av Aqua Security . Stegen här kommer att köras i samband med Aqua-distributionsdokumentationen.

Det första steget är att kommentera de nödvändiga SCC:erna som ska uppdateras. Dessa anteckningar förhindrar att klustrets synkroniseringspod återställer alla ändringar i dessa SSC:er.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Steg 1: Förbered förutsättningar
Kom ihåg att logga in i klustret som ARO-kundadministratör i stället för rollen klusteradministratör.

Skapa projektet och servicekontot.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

I stället för att tilldela rollen klusterläsare tilldelar du rollen customer-admin-cluster till aqua-kontot med följande kommando.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Fortsätt att följa de återstående instruktionerna i steg 1.  Dessa instruktioner beskriver inrättandet av hemligheten för Aqua-registret.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Steg 2: Distribuera Aqua-servern, databasen och gatewayen
Följ stegen i Aqua-dokumentationen för installation av aqua-console.yaml.

Ändra den `aqua-console.yaml`medföljande .  Ta bort de två `kind: ClusterRole` översta `kind: ClusterRoleBinding`objekten med etiketten .  Dessa resurser skapas inte eftersom kundadministratören inte har behörighet `ClusterRole` att `ClusterRoleBinding` ändra och objekt.

Den andra ändringen kommer `kind: Route` att `aqua-console.yaml`vara den del av . Ersätt följande yaml `kind: Route` för objektet `aqua-console.yaml` i filen.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Följ de återstående instruktionerna.

### <a name="step-3-login-to-the-aqua-server"></a>Steg 3: Logga in på Aqua Server
Det här avsnittet ändras inte på något sätt.  Följ Aqua-dokumentationen.

Använd följande kommando för att hämta Aqua Console-adressen.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Steg 4: Distribuera Aqua Enforcers
Ange följande fält när du distribuerar tillskolnings- och tills-till-s-

| Field          | Värde         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceKonto | aqua-konto  |
| Project        | aqua-säkerhet |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Produktspecifika steg för Prisma Cloud / Twistlock

De grundinstruktioner vi ska ändra finns i [prisma-distributionsdokumentationen](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Börja med `twistcli` att installera verktyget enligt beskrivningen i avsnitten "Installera Prisma Cloud" och "Ladda ned Prisma Cloud software".

Skapa ett nytt OpenShift-projekt
```
oc new-project twistlock
```

Hoppa över det valfria avsnittet "Push the Prisma Cloud images to a private registry". Det fungerar inte på Azure Red Hat Openshift. Använd onlineregistret i stället.

Du kan följa den officiella dokumentationen när du använder de korrigeringar som beskrivs nedan.
Börja med avsnittet "Installera konsol".

### <a name="install-console"></a>Installera konsol

Under `oc create -f twistlock_console.yaml` i steg 2 får du ett felmeddelande när du skapar namnområdet.
Du kan ignorera det på ett säkert sätt, `oc new-project` namnområdet har skapats tidigare med kommandot.

Används `azure-disk` för lagringstyp.

### <a name="create-an-external-route-to-console"></a>Skapa en extern väg till konsol

Du kan antingen följa dokumentationen eller instruktionerna nedan om du föredrar kommandot OC.
Kopiera följande flödesdefinition till en fil som heter twistlock_route.yaml på datorn
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
kör sedan:
```
oc create -f twistlock_route.yaml
```

Du kan få webbadressen tilldelad Twistlock-konsolen med det här kommandot:`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Konfigurera konsol

Följ Twistlock-dokumentationen.

### <a name="install-defender"></a>Installera Defender

Under `oc create -f defender.yaml` i steg 2 får du fel när du skapar klusterrollen och klusterrollbindningen.
Du kan ignorera dem.

Försvarare kommer endast att distribueras på beräkningsnoder. Du behöver inte begränsa dem med en nodväljare.
