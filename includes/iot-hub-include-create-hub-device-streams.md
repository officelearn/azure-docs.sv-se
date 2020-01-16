---
title: inkludera fil (förhandsversion av enhetsströmmar)
description: inkludera fil (förhandsversion av enhetsströmmar)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 700dd305cf5365a604a5613bd5b48dbd2ac8f840
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020952"
---
I det här avsnittet beskrivs hur du använder [Azure-portalen](https://portal.azure.com) för att skapa en IoT-hubb.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **skapa en resurs**och ange sedan *IoT Hub* i fältet **Sök i Marketplace** .

1. Välj **IoT Hub** från Sök resultaten och välj sedan **skapa**.

1. På fliken **grundläggande** inställningar fyller du i fälten på följande sätt:

   - **Prenumeration**: Välj den prenumeration som ska användas för hubben.

   - **Resurs grupp**: Välj en resurs grupp eller skapa en ny. Om du vill skapa en ny väljer du **Skapa ny** och fyller i det namn som du vill använda. Om du vill använda en befintlig resurs grupp väljer du den resurs gruppen. Mer information finns i [Hantera Azure Resource Manager-resursgrupper](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Region**: Välj den region där du vill att hubben ska placeras. Välj en region som stöder förhands granskning av IoT Hub enhets strömmar, antingen **centrala USA** eller **centrala US EUAP**.

   - **IoT Hub namn**: Ange ett namn för hubben. Det här namnet måste vara globalt unikt. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   ![Skapa en IoT-hubb i Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-device-streams.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Välj **Nästa: storlek och skala** för att fortsätta skapa hubben.

   ![Ange storlek och skala för en ny IoT-hubb med hjälp av Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   I **storlek och skala**kan du acceptera standardinställningarna och välja **Granska + skapa** längst ned. Överväg följande alternativ:

   - **Pris-och skalnings nivå**: den valda nivån. Välj en av standard nivåerna (**S1**, **S2**eller **S3**) eller **F1: kostnads fri nivå**. Det här alternativet kan också vägledas av storleken på din flotta och de icke-strömmande arbets belastningar som du förväntar dig i hubben, till exempel telemetri-meddelanden. Till exempel är den kostnadsfria nivån avsedd för testning och utvärdering. Det gör att 500 enheter kan anslutas till IoT-hubben och upp till 8 000 meddelanden per dag. Varje Azure-prenumeration kan skapa en IoT-hubb på den kostnads fria nivån. 

   - **Antal IoT Hub enheter**: antalet meddelanden som tillåts per enhet per dag beror på hubbens pris nivå. Det här valet beror på arbets belastningen som inte är strömmande som du förväntar dig i Hub. Du kan välja 1 för tillfället.

   - **Avancerade inställningar** > **enhet-till-moln-partitioner**: den här egenskapen relaterar enhets-till-moln-meddelanden till antalet samtidiga läsare av meddelanden. De flesta hubbar behöver bara fyra partitioner.

   Mer information om nivå alternativ finns i [välja rätt nivå för IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

1. Om du vill granska dina val väljer du **Granska + skapa**. Resultatet ser ut ungefär så här:

   ![Information om hur du skapar den nya IoT-hubben](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Om du vill skapa en ny IoT-hubb väljer du **skapa**. Processen tar några minuter.
