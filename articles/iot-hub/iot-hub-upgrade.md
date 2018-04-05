---
title: Uppgradera Azure IoT-hubb | Microsoft Docs
description: Ändra prissättnings- och skalningsnivå för IoT-hubb att hämta ytterligare hanteringsfunktioner för meddelanden och enhet.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: d383d26b406c012b6b76225faf89f4b5dbd6bb9c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-upgrade-your-iot-hub"></a>Så här uppgraderar du din IoT-hubb

IoT-lösningen växer är Azure IoT Hub redo att hjälpa dig att skala upp. Azure IoT-hubb erbjuder två nivåer, basic (B) och standard (S) för kunder som vill använda olika funktioner. Varje nivå består av tre storlekar (1, 2 och 3) som bestämmer hur många meddelanden som kan skickas varje dag. 

När du har flera enheter och måste flera funktioner, finns det tre sätt att anpassa din IoT-hubb så att de passar dina behov:

* Lägg till enheter i IoT-hubben. Varje ytterligare enhet i en B1 IoT-hubb kan till exempel en ytterligare 400 000 meddelanden per dag. 
* Ändra storlek på IoT-hubben. Till exempel migrera från B1 nivå B2-nivån för att öka mängden meddelanden som har stöd för varje enhet per dag.
* Uppgradera till en högre nivå. Till exempel uppgradera från B1 lager S1-nivån för samma asynkrona kapacitet men med de avancerade funktionerna som ingår i standardnivån.

Dessa förändringar kan uppstå utan att avbryta befintliga drift.

Om du vill nedgradera din IoT-hubb kan du ta bort enheter och minska storleken på IoT-hubben. Du kan dock Nedgradera till en lägre nivå. Du kan exempelvis flytta från lager S2 S1-nivån, men inte från S2 nivå B1-nivån. 

Exemplen är avsedda att hjälpa dig att förstå hur du justerar din IoT-hubb som ändringarna lösning. Mer information om funktioner för varje nivå bör du alltid referera till [Azure IoT Hub-priser](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>Uppgradera din befintliga IoT-hubb 

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och navigera till din IoT-hubb. 
2. Välj **priser och skalning**. 

   ![Priser och skalning](./media/iot-hub-upgrade/pricing-scale.png)

3. Om du vill ändra nivån för din hubb, Välj **priser och skalnivå**. Välj ny nivå och klicka sedan på **Välj**.

   ![Priser och skalning](./media/iot-hub-upgrade/select-tier.png)

4. Om du vill ändra antalet enheter i din hubb, ange ett nytt värde under **IoT-hubbenheter**. 
5. Välj **spara** att spara ändringarna. 

IoT-hubb justeras nu och konfigurationerna har inte ändrats. 

## <a name="next-steps"></a>Nästa steg

Få mer information [hur du väljer rätt IoT-hubb nivån](iot-hub-scaling.md). 

