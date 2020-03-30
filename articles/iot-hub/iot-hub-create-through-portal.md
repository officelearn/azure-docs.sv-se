---
title: Använd Azure-portalen för att skapa en IoT Hub | Microsoft-dokument
description: Så här skapar, hanterar och tar du bort Azure IoT-hubbar via Azure-portalen. Innehåller information om prisnivåer, skalning, säkerhet och meddelandekonfiguration.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: c43c142b22709d42416b2dd14dfc78812970916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284738"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Skapa en IoT-hubb med Azure-portalen

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

I den här artikeln beskrivs hur du skapar och hanterar IoT-hubbar med [Azure-portalen](https://portal.azure.com).

Om du vill använda stegen i den här självstudien behöver du en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Ändra inställningarna för IoT-hubben

Du kan ändra inställningarna för en befintlig IoT-hubb när den har skapats från IoT Hub-fönstret.

![Skärmbild som visar inställningarna för IoT-hubben](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Här är några av de egenskaper som du kan ange för en IoT-hubb:

**Prissättning och skala**: Du kan använda den här egenskapen för att migrera till en annan nivå eller ange antalet IoT Hub-enheter. 

**Åtgärder övervakning**: Aktivera eller inaktivera de olika övervakningskategorierna, till exempel loggning för händelser relaterade till enhet-till-moln-meddelanden eller moln-till-enhet-meddelanden.

**IP-filter:** Ange ett intervall med IP-adresser som ska accepteras eller avvisas av IoT-hubben.

**Egenskaper**: Innehåller en lista över egenskaper som du kan kopiera och använda någon annanstans, till exempel resurs-ID, resursgrupp, plats och så vidare.

### <a name="shared-access-policies"></a>Principer för delad åtkomst

Du kan också visa eller ändra listan över principer för delad åtkomst genom att klicka på **principer för delad åtkomst** i avsnittet **Inställningar.** Dessa principer definierar behörigheter för enheter och tjänster för att ansluta till IoT Hub. 

Klicka på **Lägg till** för att öppna **principbladet Lägg till en princip för delad åtkomst.**  Du kan ange det nya principnamnet och de behörigheter som du vill associera med den här principen, som visas i följande bild:

![Skärmbild som visar att lägga till en princip för delad åtkomst](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Registret **läs-och** **registret skrivprinciper** bevilja läs-och skrivbehörighet till identitetsregistret. Dessa behörigheter används av backend-molntjänster för att hantera enhetsidentiteter. Om du väljer skrivalternativet väljs läsalternativet automatiskt.

* **Principen För tjänstanslutning** ger behörighet att komma åt tjänstslutpunkter. Den här behörigheten används av backend-molntjänster för att skicka och ta emot meddelanden från enheter samt för att uppdatera och läsa tvilling- och modultvillingdata.

* **Enhetsanslutningsprincipen** ger behörighet för att skicka och ta emot meddelanden med hjälp av slutpunkterna på IoT Hub-enheten. Den här behörigheten används av enheter för att skicka och ta emot meddelanden från en IoT-hubb, uppdatera och läsa tvilling- och modultvillingdata och utföra filöverföringar.

Klicka på **Skapa** om du vill lägga till den här nyskapade principen i den befintliga listan.

Mer detaljerad information om åtkomsten som beviljas av specifika behörigheter finns i [IoT Hub-behörigheter](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Meddelanderoutning för en IoT-hubb

Klicka på **Meddelanderoutning** under **Meddelanden** om du vill visa fönstret Meddelanderoutning, där du definierar vägar och anpassade slutpunkter för navet. [Med meddelanderoutning](iot-hub-devguide-messages-d2c.md) kan du hantera hur data skickas från dina enheter till slutpunkterna. Det första steget är att lägga till en ny rutt. Sedan kan du lägga till en befintlig slutpunkt i flödet eller skapa en ny av de typer som stöds, till exempel blob-lagring. 

![Fönstret För routning av meddelanden](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Vägar

Rutter är den första fliken i fönstret Meddelanderoutning. Om du vill lägga till en ny rutt klickar du på +**Lägg till**. Du ser följande skärm. 

![Skärmbild som visar att lägga till en ny rutt](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Namnge ditt nav. Namnet måste vara unikt i listan över vägar för det navet. 

För **Slutpunkt**kan du välja en i listrutan eller lägga till en ny. I det här exemplet är ett lagringskonto och en behållare redan tillgängliga. Om du vill lägga till dem som en slutpunkt klickar du på +**Lägg** till bredvid listrutan Slutpunkt och väljer **Blob Storage**. Följande skärm visar var lagringskontot och behållaren anges.

![Skärmbild som visar att lägga till en lagringsslutpunkt för routningsregeln](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Klicka på **Välj en behållare** för att välja lagringskonto och behållare. När du har markerat dessa fält återgår de till slutpunktsfönstret. Använd standardvärdena för resten av fälten och **Skapa** för att skapa slutpunkten för lagringskontot och lägg till den i routningsreglerna.

För **datakälla**väljer du Enhetstelemetrimeddelanden. 

Lägg sedan till en routningsfråga. I det här exemplet dirigeras de `level` meddelanden som har `critical` en programegenskap som anropas med ett värde som är lika med lagringskontot.

![Skärmbild som visar hur du sparar en ny routningsregel](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Klicka på **Spara** om du vill spara routningsregeln. Du går tillbaka till fönstret Meddelanderoutning och den nya routningsregeln visas.

### <a name="custom-endpoints"></a>Anpassade slutpunkter

Klicka på fliken **Anpassade slutpunkter.** Du ser alla anpassade slutpunkter som redan har skapats. Härifrån kan du lägga till nya slutpunkter eller ta bort befintliga slutpunkter. 

> [!NOTE]
> Om du tar bort en väg tas inte de slutpunkter som tilldelats vägen bort. Om du vill ta bort en slutpunkt klickar du på fliken Anpassade slutpunkter, markerar den slutpunkt som du vill ta bort och klickar på Ta bort.
>

Du kan läsa mer om anpassade slutpunkter i [Referens - IoT-hubbslutpunkter](iot-hub-devguide-endpoints.md).

Du kan definiera upp till 10 anpassade slutpunkter för en IoT-hubb. 

Information om hur du använder anpassade slutpunkter med routning finns i [Meddelanderoutning med IoT Hub](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Hitta en specifik IoT-hubb

Här är två sätt att hitta en specifik IoT-hubb i din prenumeration:

1. Om du känner till resursgruppen som IoT-hubben tillhör klickar du på **Resursgrupper**och väljer sedan resursgruppen i listan. På skärmen Resursgrupp visas alla resurser i den gruppen, inklusive IoT-hubbar. Klicka på det nav som du letar efter.

2. Klicka på **Alla resurser**. I fönstret **Alla resurser** finns en listruta som `All types`standard används . Klicka på listrutan, avmarkera `Select all`. Hitta `IoT Hub` och kolla det. Klicka på listrutan för att stänga den, så filtreras posterna, vilket bara visar dina IoT-hubbar.

## <a name="delete-the-iot-hub"></a>Ta bort IoT-hubben

Om du vill ta bort en Iot-hubb hittar du den IoT-hubb som du vill ta bort och klickar sedan på knappen **Ta bort** under IoT-hubbens namn.

## <a name="next-steps"></a>Nästa steg

Följ de här länkarna om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Meddelanderoutning med IoT Hub](tutorial-routing.md)
* [IoT Hub-mått](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)