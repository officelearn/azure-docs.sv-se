---
title: Använda Azure portal för att skapa en IoT Hub | Microsoft Docs
description: Så här att skapa, hantera och ta bort Azure IoT Hub via Azure-portalen. Innehåller information om prisnivåer, skalning, säkerhet, och meddelanden konfiguration.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: 21758d112cd7eaf45b8b279cf2a6f6060c44c9c1
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515853"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Skapa en IoT hub med Azure portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln beskriver hur du skapar och hanterar IoT-hubbar med hjälp av den [Azure-portalen](https://portal.azure.com).

Om du vill använda stegen i den här självstudien behöver du en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Ändra inställningarna för IoT hub

Du kan ändra inställningarna för en befintlig IoT hub när den har skapats från fönstret IoT Hub.

![Skärmbild som visar inställningarna för IoT hub](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Här följer några av de egenskaper som du kan ange för en IoT-hubb:

**Pris- och**: du kan använda den här egenskapen för att migrera till en annan nivå eller ange antalet IoT Hub enheter. 

**Åtgärdsövervakning**: aktivera och inaktivera olika övervakning kategorier, till exempel loggning för händelser relaterade till meddelanden från enheten till molnet eller meddelanden från molnet till enheten.

**IP-adressfilter**: Ange ett intervall med IP-adresser som ska godkännas eller avvisas av IoT hub.

**Egenskaper för**: innehåller en lista över egenskaper som du kan kopiera och använda någon annanstans, till exempel resurs-ID, resursgrupp, plats och så vidare.

### <a name="shared-access-policies"></a>Principer för delad åtkomst

Du kan också visa eller ändra listan över principer för delad åtkomst genom att klicka på **principer för delad åtkomst** i den **inställningar** avsnittet. Dessa principer ange behörigheter för enheter och tjänster att ansluta till IoT Hub. 

Klicka på **Lägg till** att öppna den **lägga till en princip för delad åtkomst** bladet.  Du kan ange det nya principnamnet och de behörigheter som du vill associera med den här principen enligt följande bild:

![Skärmbild som visar att lägga till en princip för delad åtkomst](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Den **läsa register** och **skriva i register** principerna beviljar behörighet för Läs- och skrivåtkomst till identitetsregistret. Välja skrivalternativet automatiskt väljer läsningsalternativet.

* Den **tjänsten ansluta** princip ger behörighet att komma åt tjänstslutpunkter som **ta emot enhet-till-moln**. 

* Den **enheten ansluta** princip ger behörighet för att skicka och ta emot meddelanden med IoT Hub enhetssidan-slutpunkter.

Klicka på **skapa** att lägga till den nya principen i den befintliga listan.

## <a name="message-routing-for-an-iot-hub"></a>Meddelandet routning för en IoT-hubb

Klicka på **meddelanderoutning** under **Messaging** att se meddelanderoutning fönstret där du definierar vägar och anpassade slutpunkter för hubben. [Meddelanderoutning](iot-hub-devguide-messages-d2c.md) hjälper dig att hantera hur data skickas från dina enheter till dina slutpunkter. Det första steget är att lägga till en ny väg. Du kan sedan lägga till en befintlig slutpunkt vägen eller skapa ett nytt lösenord för de typer som stöds, till exempel blob-lagring. 

![Meddelandefönstret för Routning](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Vägar

Vägar är den första fliken i fönstret meddelanderoutning. Lägg till en ny väg, klicka på +**Lägg till**. Du ser följande skärmbild. 

![Skärmbild som visar att lägga till en ny väg](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Ge hubben ett namn. Namnet måste vara unikt i listan över vägar för den hubben. 

För **Endpoint**, du kan välja en från den nedrullningsbara listan eller lägga till en ny. I det här exemplet är finns ett lagringskonto och en behållare redan. Lägg till dem som en slutpunkt, klicka på +**Lägg till** bredvid Endpoint listrutan och välj **Blob Storage**. I följande skärm visas där lagringskontot och behållaren har angetts.

![Skärmbild som visar att lägga till en slutpunkt för lagring för en routningsregel](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Klicka på **Välj en behållare** att välja lagringskontot och behållaren. När du har valt dessa fält returneras till fönstret slutpunkt. Använd standardvärdena för resten av fälten och **skapa** att skapa slutpunkten för storage-kontot och lägga till den i regler för vidarebefordran.

För **datakälla**, Välj Telemetrimeddelanden för enheten. 

Lägg sedan till en routning fråga. I det här exemplet, meddelanden som har en programegenskap som kallas `level` med ett värde som är lika med `critical` dirigeras till lagringskontot.

![Skärmbild som visar sparar en ny regel för vidarebefordran](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Klicka på **spara** att spara en routningsregel för. Du kommer tillbaka till fönstret meddelanderoutning och nya routningsregeln visas.

### <a name="custom-endpoints"></a>Anpassade slutpunkter

Klicka på den **anpassade slutpunkter** fliken. Du kan se alla anpassade slutpunkter som redan har skapats. Härifrån kan du lägga till nya slutpunkter eller ta bort befintliga slutpunkter. 

> [!NOTE]
> Om du tar bort en väg tas inte bort de slutpunkter som tilldelats till den vägen. Om du vill ta bort en slutpunkt, klicka på fliken anpassade slutpunkter, väljer du slutpunkten som du vill ta bort och klicka på Ta bort.
>

Du kan läsa mer om anpassade slutpunkter i [referens – IoT hub-slutpunkter](iot-hub-devguide-endpoints.md).

Du kan definiera upp till 10 anpassade slutpunkter för en IoT-hubb. 

Ett fullständigt exempel på hur du använder anpassade slutpunkter med routning finns i [meddelanderoutning med IoT Hub](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Hitta en specifik IoT-hubb

Här följer två sätt att hitta en specifik IoT-hubb i din prenumeration:

1. Om du känner till resursen som IoT hub tillhör, klickar du på **resursgrupper**, Välj resursgruppen i listan. Resource group skärmen visar alla resurser i gruppen, inklusive IoT-hubbar. Klicka på hubben som du behöver.

2. Klicka på **alla resurser**. På den **alla resurser** rutan finns en listruta som standard `All types`. Klicka på listrutan och avmarkera `Select all`. Hitta `IoT Hub` och markera den. Klicka på listrutan Stäng den och posterna filtreras, som visar bara din IoT-hubbar.

## <a name="delete-the-iot-hub"></a>Ta bort IoT-hubb

Om du vill ta bort en Iot-hubb, IoT-hubben som du vill ta bort och klicka sedan på att hitta den **ta bort** knappen under namnet för IoT-hubb.

## <a name="next-steps"></a>Nästa steg

Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Meddelanderoutning med IoT Hub](tutorial-routing.md)
* [IoT Hub-mått](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)