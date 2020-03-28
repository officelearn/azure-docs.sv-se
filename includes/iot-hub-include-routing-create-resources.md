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
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76020827"
---
## <a name="create-base-resources"></a>Skapa basresurser

Innan du kan konfigurera meddelanderoutningen måste du skapa en IoT-hubb, ett lagringskonto och en servicebusskö. Dessa resurser kan skapas med hjälp av en av de fyra artiklar som är tillgängliga för del 1 av den här självstudien: Azure-portalen, en Azure Resource Manager-mall, Azure CLI eller Azure PowerShell.

Använd samma resursgrupp och plats för alla resurser. Sedan i slutet kan du ta bort alla resurser i ett steg genom att ta bort resursgruppen.

Nedan följer en sammanfattning av de steg som ska utföras i följande avsnitt: 

1. Skapa en [resursgrupp](../articles/azure-resource-manager/management/overview.md).

2. Skapa en IoT-hubb på S1-nivån. Lägg till en konsumentgrupp till din IoT-hubb. Konsumentgruppen används av Azure Stream Analytics när data hämtas.

   > [!NOTE]
   > Du måste använda en Iot-hubb på en betalnivå för att slutföra den här självstudien. På den kostnadsfria nivån kan bara ställa in en slutpunkt, och den här självstudien kräver flera slutpunkter.
   > 

3. Skapa ett V1-standardlagringskonto med Standard_LRS-replikering.

4. Skapa ett namnområde och en kö för Service Bus.

5. Skapa en enhetsidentitet för den simulerade enheten som skickar meddelanden till din hubb. Spara nyckeln för testfasen. (Om du skapar en Resource Manager-mall görs detta efter distribution av mallen.)