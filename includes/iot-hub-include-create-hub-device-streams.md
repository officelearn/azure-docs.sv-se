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
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "67445998"
---
I det här avsnittet beskrivs hur du skapar en IoT Hub med hjälp av [Azure Portal](https://portal.azure.com).

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **skapa en resurs**och välj sedan **Sakernas Internet**.

1. I listan till höger väljer du **IoT Hub**. Den första sidan för att skapa en IoT-hubb öppnas.

   ![Skapa en IoT-hubb i Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Fyll i fälten:

   a. I list rutan **prenumeration** väljer du den prenumeration som du vill använda för din IoT-hubb.

   b. För **resurs grupp**gör du något av följande: 
      * Om du vill skapa en ny resurs grupp väljer du **Skapa ny** och anger det namn som du vill använda. 
      * Om du vill använda en befintlig resurs grupp väljer du **Använd befintlig** och sedan i list rutan väljer du resurs gruppen. 
      
        Mer information finns i [Hantera Azure Resource Manager-resursgrupper](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. I list rutan **region** väljer du den region där du vill att hubben ska placeras. Välj en region som stöder förhands granskning av IoT Hub enhets strömmar, antingen **centrala USA** eller **centrala US EUAP**.

   d. I rutan **IoT Hub namn** anger du namnet på din IoT-hubb. Namnet måste vara globalt unikt. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Välj **nästa för att fortsätta skapa din IoT-hubb: Storlek och skala**.

   ![Ange storlek och skala för en ny IoT-hubb med hjälp av Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   I det här fönstret kan du acceptera standardinställningarna och välja **Granska + skapa** längst ned. Överväg följande alternativ:

   * I list rutan **pris-och skalnings nivå** väljer du en av standard nivåerna (**S1**, **S2**eller **S3**) eller **F1: Kostnads fri**nivå. Det här alternativet kan också vägledas av storleken på din flotta och de icke-strömmande arbets belastningar som du förväntar dig i hubben (till exempel telemetri-meddelanden). Till exempel är den kostnadsfria nivån avsedd för testning och utvärdering. Det gör att 500 enheter kan anslutas till IoT-hubben och upp till 8 000 meddelanden per dag. Varje Azure-prenumeration kan skapa en IoT-hubb på den kostnads fria nivån. 

   * För **antalet IoT Hubs enheter**: Det här valet beror på arbets belastningen som inte är strömmande som du förväntar dig i Hub. Du kan välja 1 för tillfället.

   Mer information om nivå alternativ finns i [välja rätt nivå för IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

1. Om du vill granska dina val väljer du fliken **Granska + skapa** . Fönstret som öppnas liknar följande:

   ![Information om hur du skapar den nya IoT-hubben](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Om du vill skapa en ny IoT-hubb väljer du **skapa**. Processen tar några minuter.
