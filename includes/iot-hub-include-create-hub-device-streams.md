---
title: inkludera fil (förhandsversion av enhetsströmmar)
description: inkludera fil (förhandsversion av enhetsströmmar)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a02c16a75bbdf4827ce6a6c5f50751caf9c5aab8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445998"
---
Det här avsnittet beskriver hur du skapar en IoT-hubb med hjälp av den [Azure-portalen](https://portal.azure.com).

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **skapa en resurs**, och välj sedan **Internet of Things**.

1. Välj i listan till höger, **Iot Hub**. Den första sidan för att skapa en IoT-hubb öppnas.

   ![Skapa en IoT-hubb i Azure portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Fyll i fälten:

   a. I den **prenumeration** listrutan väljer du prenumerationen som ska användas för din IoT hub.

   b. För **resursgrupp**, gör du något av följande: 
      * Om du vill skapa en ny resursgrupp, Välj **Skapa nytt** och ange namnet som du vill använda. 
      * Om du vill använda en befintlig resursgrupp, Välj **Använd befintlig** och välj sedan resursgruppen i den nedrullningsbara listan. 
      
        Mer information finns i [Hantera Azure Resource Manager-resursgrupper](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. I den **Region** listrutan väljer du den region som du vill att din hubb ska finnas. Välj en region som har stöd för IoT Hub device strömmar förhandsversion, antingen **centrala USA** eller **centrala USA – EUAP**.

   d. I den **namnet IoT-hubb** ange namn för din IoT hub. Namnet måste vara globalt unikt. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Om du vill fortsätta att skapa din IoT-hubb, Välj **nästa: Storlek och skala**.

   ![Ange storlek och skala för en ny IoT hub med Azure portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   I det här fönstret kan du acceptera standardinställningarna och väljer **granska + skapa** längst ned på sidan. Överväg följande alternativ:

   * I den **pris- och skalnivåer** listrutan, Välj något av standard-nivåerna (**S1**, **S2**, eller **S3**) eller **F1: Kostnadsfri nivå**. Det här alternativet kan också leds av storleken på flygplanen och icke-streaming arbetsbelastningar som du förväntar dig i din hubb (till exempel telemetrimeddelanden). Till exempel är den kostnadsfria nivån avsedd för testning och utvärdering. Det gör att 500 enheter kan anslutas till IoT-hubben och upp till 8 000 meddelanden per dag. Varje Azure-prenumeration kan skapa en IoT-hubb på den kostnadsfria nivån. 

   * För **många av IoT Hub-enheter**: Det här alternativet är beroende av icke-streaming arbetsbelastning som du förväntar dig av din hubb. Du kan välja 1 för tillfället.

   Mer information om alternativen finns i [välja rätt nivå för IoT hub](../articles/iot-hub/iot-hub-scaling.md).

1. Om du vill granska dina val, Välj den **granska + skapa** fliken. Fönstret som öppnas är ungefär så här:

   ![Information för att skapa den nya IoT-hubben](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Om du vill skapa nya IoT-hubben, Välj **skapa**. Processen tar några minuter.
