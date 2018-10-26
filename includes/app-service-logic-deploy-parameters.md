---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134121"
---
Med Azure Resource Manager kan du definiera parametrar för värdena som används när du distribuerar mallen. Mallen innehåller en `parameters` avsnitt som innehåller alla parametervärden. Varje parametervärde används av mallen för att definiera de resurser som du vill distribuera.

> [!NOTE]
> Definiera inte parametrar för värden som aldrig ändras. Definiera parametrar endast för värden som varierar utifrån det projekt som du distribuerar eller utifrån den miljö där du distribuerar.

När du definierar parametrar:

* Om du vill ange de tillåtna värdena som en användare kan ange under distributionen, använda den **allowedValues** fält.

* Om du vill tilldela värden till parametern när inga värden anges under distributionen, använda den **defaultValue** fält. 
