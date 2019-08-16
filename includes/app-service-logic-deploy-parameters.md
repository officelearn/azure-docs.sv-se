---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "68385754"
---
Med Azure Resource Manager kan du definiera parametrar för de värden som ska användas när du distribuerar mallen. Mallen innehåller ett `parameters` avsnitt som innehåller alla parameter värden. Varje parameter värde används av mallen för att definiera de resurser som du vill distribuera.

> [!NOTE]
> Definiera inte parametrar för värden som aldrig ändras. Definiera parametrar enbart för värden som varierar beroende på vilket projekt du distribuerar eller baserat på den miljö där du distribuerar.

När du definierar parametrar:

* Använd fältet **allowedValues** för att ange de tillåtna värden som en användare kan ange under distributionen.

* Använd fältet **DefaultValue** om du vill tilldela standardvärden till parameter när inga värden anges under distributionen. 
