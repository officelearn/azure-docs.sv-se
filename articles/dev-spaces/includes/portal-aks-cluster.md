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
ms.openlocfilehash: 6d2940712f8a76173de47badd45d7c7f0f0be05c
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825484"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Skapa ett Kubernetes-kluster som är aktiverat för Azure Dev Spaces

1. Logga in på Azure Portal på http://portal.azure.com.
1. Välj **Skapa en resurs** > sök efter **Kubernetes** > välj **Kubernetes Service** > **Skapa**.

   Utför följande steg under varje rubrik för att skapa AKS-klustret.

    - **PROJEKTINFORMATION**: välj en Azure-prenumeration och en ny eller befintlig Azure-resursgrupp.
    - **KLUSTERINFORMATION**: ange namn, region (för närvarande måste du välja usaöstra, europavästra eller kanadaöstra), version och DNS-prefix för AKS-klustret.
    - **SKALNING**: välj en VM-storlek för AKS-agentnoderna och sedan antalet noder. Om du håller på att komma igång med Azure Dev Spaces är en nod tillräckligt för att utforska alla funktioner. Antalet noder kan enkelt justeras när som helst efter att klustret har distribuerats. Observera att VM-storleken inte kan ändras efter att ett AKS-kluster har skapats. Men när ett AKS-kluster har distribuerats kan du enkelt skapa ett nytt AKS-kluster med större virtuella datorer och använda Dev Spaces för att distribuera till större kluster igen om du behöver skala upp.

   Se till att välja Kubernetes version 1.9.6 eller senare.

   ![Konfigurationsinställningar för Kubernetes](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Välj **Nästa: nätverk** när du är klar.

1. Se till att routning av HTTP-program är aktiverat.

   ![Aktivera routning av HTTP-program](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > Du måste se till att aktivera routning av HTTP-program när du skapar ditt AKS-kluster. Det går inte att ändra den här inställningen senare.

1. Välj **Granska + skapa** och välj sedan **Skapa** när du är klar.
