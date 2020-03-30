---
title: Hantera resurser i Azure Red Hat OpenShift | Microsoft-dokument
description: Hantera projekt, mallar, bildströmmar i ett Azure Red Hat OpenShift-kluster
services: openshift
keywords: röd hatt openshift projekt begär själv-provisioner
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139121"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Hantera projekt, mallar, bildströmmar i ett Azure Red Hat OpenShift-kluster 

I en OpenShift Container Platform används projekt för att gruppera och isolera relaterade objekt. Som administratör kan du ge utvecklare åtkomst till specifika projekt, låta dem skapa sina egna projekt och ge dem administrativa rättigheter till enskilda projekt.

## <a name="self-provisioning-projects"></a>Självetablerande projekt

Du kan göra det möjligt för utvecklare att skapa sina egna projekt. En API-slutpunkt ansvarar för etablering av ett projekt enligt en mall med namnet projektbegäran. Webbkonsolen `oc new-project` och kommandot använder den här slutpunkten när en utvecklare skapar ett nytt projekt.

När en projektbegäran skickas ersätter API:et följande parametrar i mallen:

| Parameter               | Beskrivning                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | Namnet på projektet. Krävs.             |
| PROJECT_DISPLAYNAME     | Projektets visningsnamn. Kan vara tom. |
| PROJECT_DESCRIPTION     | Beskrivning av projektet. Kan vara tom.  |
| PROJECT_ADMIN_USER      | Användarnamnet för den administrerande användaren.       |
| PROJECT_REQUESTING_USER | Användarnamnet för den begärande användaren.           |

Åtkomst till API beviljas utvecklare med självetableringsrollbindning. Den här funktionen är som standard tillgänglig för alla autentiserade utvecklare.

## <a name="modify-the-template-for-a-new-project"></a>Ändra mallen för ett nytt projekt 

1. Logga in som `customer-admin` användare med behörighet.

2. Redigera standardmallen för projektbegäran.

   ```
   oc edit template project-request -n openshift
   ```

3. Ta bort standardprojektmallen från uppdateringsprocessen för Azure Red Hat OpenShift (ARO) genom att lägga till följande anteckning:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Mallen för projektbegäran uppdateras inte av ARO-uppdateringsprocessen. På så sätt kan kunder anpassa mallen och bevara dessa anpassningar när klustret uppdateras.

## <a name="disable-the-self-provisioning-role"></a>Inaktivera rollen för självetablering

Du kan förhindra att en autentrad användargrupp etablerar nya projekt.

1. Logga in som `customer-admin` användare med behörighet.

2. Redigera självetableringsrollbindningen.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Ta bort rollen från ARO-uppdateringsprocessen genom att `openshift.io/reconcile-protect: "true"`lägga till följande anteckning: .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Ändra klusterrollbindningen `system:authenticated:oauth` för att förhindra att projekt skapas:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>Hantera standardmallar och imageStreams

I Azure Red Hat OpenShift kan du inaktivera uppdateringar för `openshift` alla standardmallar och bildströmmar inuti namnområdet.
Så här inaktiverar `ImageStreams` `openshift` du uppdateringar för ALLA `Templates` och i namnområdet:

1. Logga in som `customer-admin` användare med behörighet.

2. Redigera `openshift` namnområde:

   ```
   oc edit namespace openshift
   ```

3. Ta `openshift` bort namnområdet från ARO-uppdateringsprocessen genom att lägga till följande anteckning:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Alla enskilda objekt `openshift` i namnområdet kan tas bort från `openshift.io/reconcile-protect: "true"` uppdateringsprocessen genom att lägga till anteckningar i den.

## <a name="next-steps"></a>Nästa steg

Prova självstudien:
> [!div class="nextstepaction"]
> [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)
