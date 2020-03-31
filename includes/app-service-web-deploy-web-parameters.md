---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187151"
---
Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt med namnet Parametrar som innehåller alla parametervärden.
Du bör definiera en parameter för de värden som varierar beroende på det projekt som du distribuerar eller baserat på den miljö som du distribuerar till. Definiera inte parametrar för värden som alltid förblir desamma. Varje parametervärde används i mallen för att definiera de resurser som distribueras. 

När du definierar parametrar använder du fältet **tillåtna värden** för att ange vilka värden en användare kan tillhandahålla under distributionen. Använd **standardvärdefältet** för att tilldela parametern ett värde, om inget värde anges under distributionen.

Vi kommer att beskriva varje parameter i mallen.

### <a name="sitename"></a>Platsnamn
Namnet på den webbapp som du vill skapa.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
Namnet på apptjänstplanen som ska användas för att vara värd för webbappen.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
Prisnivån för värdplanen.

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

Mallen definierar de värden som är tillåtna för den här parametern och tilldelar ett standardvärde (S1) om inget värde anges.

### <a name="workersize"></a>arbetarSize
Instansstorleken på värdplanen (liten, medel eller stor).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Mallen definierar de värden som är tillåtna för den här parametern (0, 1 eller 2) och tilldelar ett standardvärde (0) om inget värde anges. Värdena motsvarar små, medelstora och stora.

