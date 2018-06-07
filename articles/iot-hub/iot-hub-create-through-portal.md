---
title: Använda Azure portal för att skapa en IoT-hubb | Microsoft Docs
description: Så här skapa, hantera och ta bort Azure IoT-hubbar via Azure-portalen. Innehåller information om prisnivåer, skalning, säkerhet, och messaging konfiguration.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: ca0eff415c4ba0e887c3999e7a03e3c4fa1cc156
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635941"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Skapa en IoT-hubb med Azure-portalen

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln beskrivs:

* Så här hittar du tjänsten IoT-hubb i Azure-portalen.
* Så här skapar och hanterar IoT-hubbar.

## <a name="where-to-find-the-iot-hub-service"></a>Här hittar du tjänsten IoT-hubb

Du hittar tjänsten IoT-hubb i portalen på följande platser:

* Välj **+ ny**, Välj **Sakernas Internet**.
* Välj i Marketplace, **Sakernas Internet**.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Du kan skapa en IoT-hubb med hjälp av följande metoder:

* Den **+ ny** alternativet öppnas bladet som visas i följande skärmbild visar. Steg för att skapa IoT-hubben via den här metoden och via marketplace är identiska.
* Välj i Marketplace, **skapa** att öppna bladet som visas i följande skärmbild visar.

I följande avsnitt beskrivs stegen för att skapa en IoT-hubb:

### <a name="choose-the-name-of-the-iot-hub"></a>Välj namnet på IoT-hubb

Om du vill skapa en IoT-hubb, måste du namnge IoT-hubben. Det här namnet måste vara unikt inom alla IoT-hubbar.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Välj prisnivå

Du kan välja mellan flera nivåer beroende på hur många funktioner som du vill ha och hur många meddelanden du skickar via din lösning per dag. Den kostnadsfria nivån är avsedd för testning och utvärdering. Det gör 500 enheter kan anslutas till IoT-hubb och upp till 8 000 meddelanden per dag. Varje Azure-prenumeration kan skapa en IoT-hubb i den kostnadsfria nivån. 

Mer information om andra alternativ för nivån finns [att välja rätt IoT-hubb nivån](iot-hub-scaling.md).

### <a name="iot-hub-units"></a>IoT-hubbenheter

Antal meddelanden som tillåts per enhet per dag beror på din hubb prisnivå. Om du vill IoT-hubb som stöd för ingång av 700 000 meddelanden, Välj till exempel två S1 nivå enheter.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Enheten till molnet partitioner och resursgruppen.

Du kan ändra antalet partitioner för en IoT-hubb. Standardvärdet för antalet partitioner är 4 kan du välja ett annat antal från den nedrullningsbara listan.

Du behöver inte skapa en tom resursgrupp explicit. När du skapar en resurs, kan du välja att skapa en ny eller använda en befintlig resursgrupp.

![][5]

### <a name="choose-subscription"></a>Välj prenumeration

Azure IoT-hubb visas automatiskt i Azure-prenumerationer användarkontot är kopplad till. Du kan välja att associera IoT-hubben till Azure-prenumerationen.

### <a name="choose-the-location"></a>Välj plats

Alternativet plats innehåller en lista över de regioner där IoT-hubben är tillgängliga.

### <a name="create-the-iot-hub"></a>Skapa IoT-hubb

När alla föregående steg har slutförts kan skapa du IoT-hubben. Klicka på **skapa** att starta backend-processen för att skapa och distribuera IoT-hubb med alternativen som du har valt.

Det kan ta några minuter att skapa IoT-hubb som det tar tid för backend-distributionen för att köras på rätt plats-servrar.

## <a name="change-the-settings-of-the-iot-hub"></a>Ändra inställningarna för IoT-hubb

Du kan ändra inställningarna för en befintlig IoT-hubb när den har skapats från IoT-hubb-bladet.

![][8]

**Delade åtkomstprinciper**: dessa principer ange behörigheter för enheter och tjänster för att ansluta till IoT-hubb. Du kan komma åt dessa principer genom att klicka på **principer för delad åtkomst** under **allmänna**. I det här bladet kan du ändra befintliga principer eller lägga till en ny princip.

### <a name="create-a-policy"></a>Skapa en princip

* Klicka på **Lägg till** så öppnas ett blad. Här kan du ange det nya principnamnet och de behörigheter som du vill associera med den här principen som visas i följande bild:

    Det finns flera behörigheter som kan associeras med dessa principer för delad. Den **läsa registret** och **registret skrivåtgärder** principer bevilja rättigheter för Läs- och skrivåtkomst till identitetsregistret. Om du väljer alternativet skrivåtgärder väljer skrivskyddade.

    Den **tjänsten ansluta** princip ger behörighet att komma åt slutpunkter som **får enhet till moln**. Den **enhet ansluta** princip ger behörighet för att skicka och ta emot meddelanden med IoT-hubb enhetssidan.

* Klicka på **skapa** att lägga till den nya principen i den befintliga listan.

![][10]

## <a name="endpoints"></a>Slutpunkter

Klicka på **slutpunkter** att visa en lista över slutpunkter för IoT-hubb som du ändrar. Det finns två typer av slutpunkter: slutpunkter som är inbyggda i IoT-hubb och slutpunkter som du lägger till IoT-hubben efter skapades.

![][11]

### <a name="built-in-endpoints"></a>Inbyggda slutpunkter

Det finns två inbyggda slutpunkter: **moln till enhet feedback** och **händelser**.

* **Moln till enhet feedback** inställningar: den här inställningen har två subsettings: **moln till enhet TTL** (time-to-live) och **kvarhållningstiden** (i timmar) för meddelanden. När först skapar en IoT-hubb kan ha båda de här inställningarna standardvärdet för en timme. Använd skjutreglagen för att justera inställningarna eller ange värdena.
* **Händelser** inställningar: den här inställningen har flera subsettings, vilket är skrivskyddade. I följande lista beskrivs de här inställningarna:

  * **Partitioner**: en är standardvärdet när IoT-hubben har skapats. Du kan ändra antalet partitioner via den här inställningen.

  * **Händelsenamn hubb-kompatibel och slutpunkten**: när det IoT-hubben har skapats, en Händelsehubb har skapats internt att behöva åtkomst till under vissa omständigheter. Du kan anpassa Event Hub-kompatibelt namn och en slutpunkt värden men du kan kopiera dem genom att klicka på **kopiera**.

  * **Kvarhållningstiden**: inställd på en dag som standard men du kan ändra den med hjälp av den nedrullningsbara listan. Det här värdet är i dagar för inställningen enhet till moln.

  * **Konsumentgrupper**: konsumentgrupper aktivera flera läsare att oberoende läsa meddelanden från IoT-hubben. Alla IoT-hubben har skapats med en förinställd konsumentgrupp. Du kan lägga till eller ta bort konsumentgrupper till din IoT-hubbar som använder den här inställningen.

  > [!NOTE]
  > Standard-konsumentgrupp inte redigeras eller tas bort.

### <a name="custom-endpoints"></a>Anpassade slutpunkter

Du kan lägga till anpassade slutpunkter på din IoT-hubb i portalen. Från den **slutpunkter** bladet, klickar du på **Lägg till** längst upp för att öppna den **lägga till slutpunkten** bladet. Ange informationen som krävs och klicka sedan på **OK**. Din anpassade slutpunkt visas nu i huvudsakliga **slutpunkter** bladet.

![][13]

Du kan läsa mer om anpassade slutpunkter i [referens - IoT-hubbslutpunkter][lnk-devguide-endpoints].

## <a name="routes"></a>Vägar

Klicka på **vägar** att hantera hur IoT-hubb skickar meddelanden från din enhet till moln.

![][14]

Du kan lägga till vägar för din IoT-hubb genom att klicka på **Lägg till** överst i den **vägar*** bladet för att ange informationen som krävs och klicka **OK**. Rutten visas sedan i huvudsakliga **vägar** bladet. Du kan redigera en väg genom att klicka på den i listan över vägar. Om du vill aktivera en väg, klickar du på den i listan över vägar och ange den **aktiverad** växla till **av**. Spara ändringen genom att klicka på **OK** längst ned på bladet.

![][15]

## <a name="delete-the-iot-hub"></a>Ta bort IoT-hubb

Du kan bläddra till IoT-hubb som du vill ta bort genom att klicka på **Bläddra**, och sedan välja lämpliga hubben att ta bort. Om du vill ta bort IoT-hubben, klickar du på den **ta bort** nedan IoT-hubbnamnet.

## <a name="next-steps"></a>Nästa steg

Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT-hubb:

* [Massinläsning hantera IoT-enheter][lnk-bulk]
* [IoT-hubb mått][lnk-metrics]
* [Åtgärder som övervakning][lnk-monitor]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]
* [Skydda din IoT-lösning från grunden upp][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
