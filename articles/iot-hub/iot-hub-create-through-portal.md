---
title: Använda Azure portal för att skapa en IoT Hub | Microsoft Docs
description: Så här att skapa, hantera och ta bort Azure IoT Hub via Azure-portalen. Innehåller information om prisnivåer, skalning, säkerhet, och meddelanden konfiguration.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 0b03ae434e93dbab45235fe67c499497e1257064
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054185"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Skapa en IoT hub med Azure portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln beskrivs:

* Så här att hitta tjänsten IoT Hub i Azure-portalen.
* Så här att skapa och hantera IoT-hubbar.

## <a name="where-to-find-the-iot-hub-service"></a>Var du hittar tjänsten IoT Hub

Du kan hitta IoT Hub-tjänsten på följande platser i portalen:

* Välj **+ ny**, välj sedan **Internet of Things**.
* På Marketplace, Välj **Internet of Things**.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Du kan skapa en IoT-hubb på följande sätt:

* Den **+ ny** alternativet öppnas bladet som visas i följande skärmbild. Stegen för att skapa IoT-hubben via den här metoden och via marketplace är identiska.

* På Marketplace, Välj **skapa** att öppna bladet som visas i följande skärmbild.

I följande avsnitt beskrivs stegen för att skapa en IoT-hubb.

### <a name="choose-the-name-of-the-iot-hub"></a>Välj namnet på IoT hub

Om du vill skapa en IoT-hubb, måste du kalla IoT-hubben. Det här namnet måste vara unikt inom alla IoT-hubbar.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Välj prisnivå

Du kan välja bland flera nivåer beroende på hur många funktioner som du vill ha och hur många meddelanden du skicka via din lösning per dag. Den kostnadsfria nivån är avsedd för testning och utvärdering. Det gör 500 enheter kan anslutas till IoT hub och upp till 8 000 meddelanden per dag. Varje Azure-prenumeration kan skapa en IoT-hubb på den kostnadsfria nivån. 

Mer information om de andra alternativen för nivån finns [välja rätt nivå för IoT Hub](iot-hub-scaling.md).

### <a name="iot-hub-units"></a>IoT hub-enheter

Hur många meddelanden som är tilldelat per enhet per dag beror på prisnivå för din hubb. Till exempel IoT-hubb för ingångshändelser på 700 000 meddelanden kan välja du två enheter av S1-nivån.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Enheten till molnet partitioner och resursgruppen

Du kan ändra antalet partitioner för en IoT-hubb. Standardvärdet för antal partitioner är 4. Du kan välja ett annat antal från den nedrullningsbara listan.

Du behöver inte uttryckligen skapa en tom resursgrupp. När du skapar en resurs, kan du välja att skapa en ny resursgrupp eller använda en befintlig resursgrupp.

![Skärmbild som visar hur du skapar en hubb i Azure-portalen](./media/iot-hub-create-through-portal/location1.png)

### <a name="choose-subscription"></a>Välj prenumeration

Azure IoT Hub visas automatiskt de Azure-prenumerationer användarkontot är länkad till. Du kan välja Azure-prenumeration att associera IoT-hubben till.

### <a name="choose-the-location"></a>Välj plats

Alternativet plats innehåller en lista över de regioner där IoT Hub är tillgänglig.

### <a name="create-the-iot-hub"></a>Skapa IoT-hubb

När alla föregående steg har slutförts kan skapa du IoT-hubben. Klicka på **skapa** att starta backend-processen för att skapa och distribuera IoT-hubb med de alternativ som du har valt.

Det kan ta några minuter att skapa IoT-hubb eftersom det tar tid för backend-distribution ska köras på rätt plats-servrar.

## <a name="change-the-settings-of-the-iot-hub"></a>Ändra inställningarna för IoT hub
<!--robinsh these screenshots are out of date -->

Du kan ändra inställningarna för en befintlig IoT hub när den har skapats från IoT Hub-bladet.

![Skärmbild som visar inställningarna för IoT hub](./media/iot-hub-create-through-portal/portal-settings.png)

**Delade åtkomstprinciper**: dessa principer ange behörigheter för enheter och tjänster att ansluta till IoT Hub. Du kan komma åt dessa principer genom att klicka på **principer för delad åtkomst** under **Allmänt**. I det här bladet kan du ändra befintliga principer eller lägga till en ny princip.

### <a name="create-a-policy"></a>Skapa en princip

* Klicka på **Lägg till** att öppna ett blad. Här kan du ange det nya principnamnet och de behörigheter som du vill associera med den här principen enligt följande bild:

    Det finns flera behörigheter som kan associeras med dessa principer för delad. Den **läsa register** och **skriva i register** principerna beviljar behörighet för Läs- och skrivåtkomst till identitetsregistret. Välja skrivalternativet automatiskt väljer läsningsalternativet.

    Den **tjänsten ansluta** princip ger behörighet att komma åt tjänstslutpunkter som **ta emot enhet-till-moln**. Den **enheten ansluta** princip ger behörighet för att skicka och ta emot meddelanden med IoT Hub enhetssidan-slutpunkter.

* Klicka på **skapa** att lägga till den nya principen i den befintliga listan.

   ![Skärmbild som visar att lägga till en princip för delad åtkomst](./media/iot-hub-create-through-portal/shared-access-policies.png)

## <a name="endpoints"></a>Slutpunkter

Klicka på **slutpunkter** att visa en lista över slutpunkter för IoT-hubben som du ändrar. Det finns två typer av slutpunkter: slutpunkter som är inbyggda i IoT-hubben och slutpunkter som du lägger till IoT hub när den har skapandet.

![Skärmbild som visar att lägga till en slutpunkt](./media/iot-hub-create-through-portal/messaging-settings.png)

### <a name="built-in-endpoints"></a>Inbyggda slutpunkter

Det finns två inbyggda slutpunkter: **Cloud enhet feedback** och **händelser**.

* **Cloud enhet feedback** inställningar: den här inställningen har två subsettings: **Cloud enhet TTL** (time-to-live) och **kvarhållningstid** (i timmar) för meddelanden. När din första skapar en IoT-hubb kan ha båda de här inställningarna standardvärdet på en timme. Använd skjutreglagen eller ange värden för att justera de här inställningarna.

* **Händelser** inställningar: den här inställningen har flera subsettings, vilket är skrivskyddade. I följande lista beskrivs de här inställningarna:

  * **Partitioner**: ett standardvärde anges när IoT-hubben har skapats. Du kan ändra antalet partitioner via den här inställningen.

  * **Event Hub-kompatibla namnet och slutpunkt**: när IoT-hubben har skapats, en Händelsehubb skapas internt att du kan behöva åtkomst till under vissa omständigheter. Du kan inte anpassa värdena för Event Hub-kompatibla namnet och slutpunkten men du kan kopiera dem genom att klicka på **kopiera**.

  * **Kvarhållningstid**: inställd på en dag som standard men du kan ändra den med hjälp av den nedrullningsbara listan. Det här värdet är i dagar för inställningen enhet till molnet.

  * **Konsumentgrupper**: konsumentgrupper aktivera flera läsare kan oberoende läsa meddelanden från IoT hub. Varje IoT-hubb skapas med en förinställd konsumentgrupp. Du kan dock lägga till eller ta bort konsumentgrupper till din IoT-hubbar som använder den här inställningen.

  > [!NOTE]
  > Förinställd konsumentgrupp inte redigeras eller tas bort.

### <a name="custom-endpoints"></a>Anpassade slutpunkter

Du kan lägga till anpassade slutpunkter på IoT hub med hjälp av portalen. Från den **slutpunkter** bladet klickar du på **Lägg till** överst för att öppna den **Lägg till slutpunkt** bladet. Ange informationen som krävs och klicka sedan på **OK**. Din anpassade slutpunkt visas nu i huvudsakliga **slutpunkter** bladet.

![Skärmbild som visar hur du skapar en anpassad slutpunkt](./media/iot-hub-create-through-portal/endpoint-creation.png)

Du kan läsa mer om anpassade slutpunkter i [referens – IoT hub-slutpunkter]( iot-hub-devguide-endpoints.md).

## <a name="routes"></a>Vägar

Klicka på **vägar** att hantera hur IoT-hubben skickar enhet-till-moln-meddelanden.

![Skärmbild som visar att lägga till en ny väg](./media/iot-hub-create-through-portal/routes-list.png)

Du kan lägga till vägar till din IoT hub genom att klicka på **Lägg till** överst i den **vägar*** bladet att ange informationen som krävs och klicka **OK**. Färdvägen visas sedan i huvudsakliga **vägar** bladet. Du kan redigera en väg genom att klicka på den i listan över vägar. Om du vill aktivera en väg, klicka på den i listan över vägar och ange den **aktiverad** växla till **av**. Om du vill spara ändringarna klickar du på **OK** längst ned på bladet.

![Skärmbild som visar redigerar en ny regel för vidarebefordran](./media/iot-hub-create-through-portal/route-edit.png)

## <a name="delete-the-iot-hub"></a>Ta bort IoT-hubb

Du kan bläddra till IoT-hubben som du vill ta bort genom att klicka på **Bläddra**, och sedan välja lämplig hubben att ta bort. Ta bort IoT-hubben genom att klicka på den **ta bort** knappen under namnet för IoT-hubb.

## <a name="next-steps"></a>Nästa steg

Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-mått](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Skydda din IoT-lösning från grunden upp](../iot-fundamentals/iot-security-ground-up.md)
