---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 710bb8cba7fbbe4bc9b9fdc52b0767c96f97fe72
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630920"
---
## <a name="create-base-resources"></a>Skapa grundläggande resurser

Innan du kan konfigurera den meddelanderoutning, måste du skapa en IoT-hubb, ett lagringskonto och en Service Bus-kö. De här resurserna kan skapas med någon av fyra artiklar som är tillgängliga för del 1 av självstudien: Azure CLI, Azure PowerShell, Azure-portalen eller en Azure Resource Manager-mall.

Använd samma resursgrupp och plats för alla resurser. Sedan i slutet, du kan ta bort alla resurser i ett steg genom att ta bort resursgruppen.

I följande avsnitt beskrivs de steg som ska utföras.

1. Skapa en [resursgrupp](../articles/azure-resource-manager/resource-group-overview.md).

2. Skapa en IoT-hubb på S1-nivån. Lägg till en konsumentgrupp till din IoT-hubb. Konsumentgruppen används av Azure Stream Analytics när data hämtas.

   > [!NOTE]
   > Du måste använda en Iot-hubb på en betalnivå för att slutföra den här självstudien. På den kostnadsfria nivån kan bara ställa in en slutpunkt, och den här självstudien kräver flera slutpunkter.
   > 

3. Skapa ett V1-standardlagringskonto med Standard_LRS-replikering.

4. Skapa ett namnområde och en kö för Service Bus.

5. Skapa en enhetsidentitet för den simulerade enheten som skickar meddelanden till din hubb. Spara nyckeln för testfasen. (Om du skapar en Resource Manager-mall, det gör du när du har distribuerat mallen.)