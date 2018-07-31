---
title: ta med fil
description: ta med fil
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 2f894549951a0ef23a95acaab48ab20ce6646443
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39189301"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Skapa ett Kubernetes-kluster som är aktiverat för Azure Dev Spaces

1. Logga in på Azure Portal på http://portal.azure.com.
1. Välj **Skapa en resurs** > sök efter **Kubernetes** > välj **Kubernetes Service** > **Skapa**.

   Utför följande steg under varje rubrik för att skapa AKS-klustret.

    - **PROJEKTINFORMATION**: välj en Azure-prenumeration och en ny eller befintlig Azure-resursgrupp.
    - **KLUSTERINFORMATION**: ange namn, region (för närvarande måste du välja USA, östra, Europa, västra, USA, västra 2, Kanada, centrala eller Kanada, östra), version och DNS-prefix för AKS-klustret.
    - **SKALNING**: välj en VM-storlek för AKS-agentnoderna och sedan antalet noder. Om du håller på att komma igång med Azure Dev Spaces är en nod tillräckligt för att utforska alla funktioner. Antalet noder kan enkelt justeras när som helst efter att klustret har distribuerats. Observera att VM-storleken inte kan ändras efter att ett AKS-kluster har skapats. Men när ett AKS-kluster har distribuerats kan du enkelt skapa ett nytt AKS-kluster med större virtuella datorer och använda Dev Spaces för att distribuera till större kluster igen om du behöver skala upp.

   Se till att välja Kubernetes version 1.10.3 eller senare.

   ![Konfigurationsinställningar för Kubernetes](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Välj **Nästa: Autentisering** när det är klart.

1. Välj din önskade inställning för rollbaserad åtkomstkontroll (RBAC). Azure Dev Spaces har stöd för kluster med RBAC aktiverat eller inaktiverat.

    ![RBAC-inställning](../media/common/k8s-RBAC.PNG)

1. Se till att routning av HTTP-program är aktiverat.

   ![Aktivera routning av HTTP-program](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > Du måste se till att aktivera routning av HTTP-program när du skapar ditt AKS-kluster. Det går inte att ändra den här inställningen senare.

1. Välj **Granska + skapa** och välj sedan **Skapa** när du är klar.
