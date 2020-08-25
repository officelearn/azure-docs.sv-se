---
title: Hantera resurser i Azure Red Hat OpenShift | Microsoft Docs
description: Hantera projekt, mallar, bild strömmar i ett kluster med OpenShift i Azure Red Hat
services: openshift
keywords: Red Hat OpenShift Projects begär själv etablering
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: 35474df96254b158a39b6d913171c553bf30aea4
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816447"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Hantera projekt, mallar, bild strömmar i ett kluster med OpenShift i Azure Red Hat

I en OpenShift container-plattform används projekt för att gruppera och isolera relaterade objekt. Som administratör kan du ge utvecklare åtkomst till specifika projekt, tillåta dem att skapa sina egna projekt och ge dem administrativa rättigheter till enskilda projekt.

## <a name="self-provisioning-projects"></a>Själv etablering av projekt

Du kan göra det möjligt för utvecklare att skapa sina egna projekt. En API-slutpunkt ansvarar för att tillhandahålla ett projekt enligt en mall med namnet Project-Request. Webb konsolen och `oc new-project` kommandot använder den här slut punkten när en utvecklare skapar ett nytt projekt.

När en projekt förfrågan skickas, ersätter API följande parametrar i mallen:

| Parameter               | Beskrivning                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | Projektets namn. Krävs.             |
| PROJECT_DISPLAYNAME     | Projektets visnings namn. Kan vara tom. |
| PROJECT_DESCRIPTION     | Projektets beskrivning. Kan vara tom.  |
| PROJECT_ADMIN_USER      | Användar namnet för den administrerande användaren.       |
| PROJECT_REQUESTING_USER | Användar namnet för den begär ande användaren.           |

Åtkomst till API: et beviljas till utvecklare med roll bindningen för självbetjänings kluster. Den här funktionen är tillgänglig för alla autentiserade utvecklare som standard.

## <a name="modify-the-template-for-a-new-project"></a>Ändra mallen för ett nytt projekt 

1. Logga in som en användare med `customer-admin` behörighet.

2. Redigera standard mal len för projekt begär Anden.

   ```
   oc edit template project-request -n openshift
   ```

3. Ta bort standard projekt mal len från uppdaterings processen för Azure Red Hat OpenShift (ARO) genom att lägga till följande anteckning: `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Mallen för projekt förfrågan kommer inte att uppdateras av uppdaterings processen för ARO. Detta gör det möjligt för kunderna att anpassa mallen och bevara dessa anpassningar när klustret uppdateras.

## <a name="disable-the-self-provisioning-role"></a>Inaktivera själv etablerings rollen

Du kan hindra en autentiserad användar grupp från att själv allokera nya projekt.

1. Logga in som en användare med `customer-admin` behörighet.

2. Redigera roll bindningen för självbetjänings kluster.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Ta bort rollen från ARO uppdaterings processen genom att lägga till följande anteckning: `openshift.io/reconcile-protect: "true"` .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Ändra kluster roll bindningen för att förhindra att `system:authenticated:oauth` projekt skapas:

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

I Azure Red Hat OpenShift kan du inaktivera uppdateringar för alla standardmallar och bild strömmar i `openshift` namn området.
Så här inaktiverar du uppdateringar för alla `Templates` och `ImageStreams` i `openshift` namn området:

1. Logga in som en användare med `customer-admin` behörighet.

2. Redigera `openshift` namnrymd:

   ```
   oc edit namespace openshift
   ```

3. Ta bort `openshift` namn området från Aro uppdaterings processen genom att lägga till följande anteckning: `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Enskilda objekt i `openshift` namn området kan tas bort från uppdaterings processen genom att lägga till anteckningar `openshift.io/reconcile-protect: "true"` i det.

## <a name="next-steps"></a>Nästa steg

Prova själv studie kursen:
> [!div class="nextstepaction"]
> [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)
