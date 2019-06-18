---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66167420"
---
Med Azure Resource Manager kan du definiera parametrar för värdena som används när du distribuerar mallen. Mallen innehåller en `parameters` avsnitt som innehåller alla parametervärden. Varje parametervärde används av mallen för att definiera de resurser som du vill distribuera.

> [!NOTE]
> Definiera inte parametrar för värden som aldrig ändras. Definiera parametrar endast för värden som varierar utifrån det projekt som du distribuerar eller utifrån den miljö där du distribuerar.

När du definierar parametrar:

* Om du vill ange de tillåtna värdena som en användare kan ange under distributionen, använda den **allowedValues** fält.

* Om du vill tilldela värden till parametern när inga värden anges under distributionen, använda den **defaultValue** fält. 
