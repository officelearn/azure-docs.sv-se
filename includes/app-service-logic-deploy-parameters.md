---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385754"
---
Med Azure Resource Manager kan du definiera parametrar för de värden som ska användas när du distribuerar mallen. Mallen innehåller ett `parameters` avsnitt som innehåller alla parameter värden. Varje parameter värde används av mallen för att definiera de resurser som du vill distribuera.

> [!NOTE]
> Definiera inte parametrar för värden som aldrig ändras. Definiera parametrar enbart för värden som varierar beroende på vilket projekt du distribuerar eller baserat på den miljö där du distribuerar.

När du definierar parametrar:

* Använd fältet **allowedValues** för att ange de tillåtna värden som en användare kan ange under distributionen.

* Använd fältet **DefaultValue** om du vill tilldela standardvärden till parameter när inga värden anges under distributionen. 
