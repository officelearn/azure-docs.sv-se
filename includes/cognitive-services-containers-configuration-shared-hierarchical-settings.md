---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: e2c8b4e74933df593dbc023dcb886dff80554a1a
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977397"
---
Inställningar för behållaren är hierarkiska alla behållare på värddatorn använda en delad hierarki.

Du kan använda något av följande för att ange inställningar:

* [Miljövariabler](#environment-variable-settings)
* [Kommandoradsargument](#command-line-argument-settings)

Värden för miljövariabler åsidosätta värden för kommandoradsargument, vilket i sin tur åsidosätter standardvärdena för behållaravbildningen. Om du anger olika värden i en miljövariabel och ett argument på kommandoraden för samma Konfigurationsinställningen, används värdet i miljövariabeln av behållaren för skapade instanser.

|Prioritet|Ange plats|
|--|--|
|1|Miljövariabel| 
|2|Kommandorad|
|3|Standardvärdet för behållaren bild|

### <a name="environment-variable-settings"></a>Miljövariabelinställningar

Fördelarna med att använda miljövariabler är:

* Flera inställningar kan konfigureras.
* Flera behållare kan använda samma inställningar.

### <a name="command-line-argument-settings"></a>Kommandoradsargumentet inställningar

Fördelen med att använda kommandoradsargument är att varje behållare kan använda olika inställningar.
