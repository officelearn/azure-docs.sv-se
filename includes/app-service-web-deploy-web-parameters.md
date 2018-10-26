---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133779"
---
Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter parametrar som innehåller alla parametervärden.
Du bör definiera en parameter för de värden som varierar utifrån det projekt som du distribuerar eller utifrån den miljö som du distribuerar till. Inte definiera parametrar för värden som ska alltid vara samma. Varje parametervärde används i mallen för att definiera de resurser som distribueras. 

När du definierar parametrar måste du använda den **allowedValues** fältet för att ange vilka värden en användare kan ange under distributionen. Använd den **defaultValue** fält som du vill tilldela ett värde i parametern, om inget värde anges under distributionen.

Vi beskriver varje parameter i mallen.

### <a name="sitename"></a>Webbplatsnamn
Namnet på webbappen som du vill skapa.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
Namnet på App Service-plan för att använda som värd för webbappen.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>SKU: n
Prisnivån för tjänstplanen.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Mallen definierar de värden som tillåts för den här parametern och tilldelar ett standardvärde (S1) om inget värde anges.

### <a name="workersize"></a>workerSize
Instansstorlek värdplanen (liten, medel eller stor).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Mallen definierar de värden som tillåts för den här parametern (0, 1 eller 2), och tilldelar ett standardvärde (0) om inget värde anges. Värdena motsvarar små, medelstora och stora.

