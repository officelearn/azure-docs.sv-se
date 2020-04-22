---
title: Definiera en ny gateway-enhetstyp i Azure IoT Central | Microsoft-dokument
description: Den här självstudien visar hur du som byggare definierar en ny IoT-gatewayenhetstyp i ditt Azure IoT Central-program.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2411eab50cc921a09ba55780b3c6620744a78f3f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758125"
---
# <a name="define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Definiera en ny IoT-gatewayenhetstyp i ditt Azure IoT Central-program

*Den här artikeln gäller lösningsbyggare och enhetsutvecklare.*

Den här självstudien visar hur du som lösningsbyggare använder en gateway-enhetsmall för att definiera en gateway-enhet i ditt IoT Central-program. Du kan sedan konfigurera flera underordnade enheter som ansluter till ditt IoT Central-program via gateway-enheten. 

I den här självstudien skapar du en enhetsmall **för Smart Building-gateway.** En **Smart Building** gateway-enhet har relationer med andra underordnade enheter.

![Diagram över förhållandet mellan gateway-enhet och nedströmsenheter](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Förutom att aktivera nedströmsenheter för att kommunicera med ditt IoT Central-program kan en gateway-enhet också:

* Skicka sin egen telemetri, till exempel temperatur.
* Svara på skrivbara egenskapsuppdateringar som gjorts av en operatör. En operator kan till exempel ändra telemetrissändintervallet.
* Svara på kommandon, till exempel starta om enheten.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien måste du [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Skapa underordnade enhetsmallar

Den här självstudien använder enhetsmallar för en **S1-sensorenhet** och en **RS40-enhet för beläggningssensor** för att generera simulerade nedströmsenheter.

Så här skapar du en enhetsmall för en **S1-sensorenhet:**

1. Välj **Enhetsmallar**i den vänstra rutan . Välj **+** sedan för att börja lägga till mallen.

1. Bläddra nedåt tills du kan se panelen för **S1-sensorenheten.** Markera panelen och välj sedan **Nästa: Anpassa**.

1. På sidan **Granska** väljer du **Skapa** för att lägga till enhetsmallen i programmet. 

Så här skapar du en enhetsmall för en ***RS40-enhet för beläggningssensor:**

1. Välj **Enhetsmallar**i den vänstra rutan . Välj **+** sedan för att börja lägga till mallen.

1. Bläddra nedåt tills du kan se panelen för ***RS40 Beläggningssensor** enhet. Markera panelen och välj sedan **Nästa: Anpassa**.

1. På sidan **Granska** väljer du **Skapa** för att lägga till enhetsmallen i programmet. 

Du har nu enhetsmallar för de två underordnade enhetstyperna:

![Enhetsmallar för nedströmsenheter](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Mall för att skapa en gateway-enhet

I den här självstudien skapar du en enhetsmall för en gateway-enhet från grunden. Du använder den här mallen senare för att skapa en simulerad gateway-enhet i ditt program.

Så här lägger du till en ny gateway-enhetsmall i ditt program:

1. Välj **Enhetsmallar**i den vänstra rutan . Välj **+** sedan för att börja lägga till mallen.

1. På sidan **Välj malltyp** markerar du panelen **IoT-enhet** och väljer sedan **Nästa: Anpassa**.

1. Markera kryssrutan **Gateway-enhet** på sidan **Anpassa enhet.**

1. På sidan **Granska** väljer du **Skapa**. 

1. Ange **Smart Building gateway-enhet** som mallnamn.

1. På sidan **Skapa en kapacitetsmodell** väljer du den **anpassade** panelen.

1. Välj **+** om du vill lägga till ett gränssnitt.  Välj standardgränssnittet **Enhetsinformation.**

### <a name="add-relationships"></a>Lägga till relationer

Därefter lägger du till relationer i mallarna för nedströmsenhetsmallarna:

1. Välj **Relationer** **i mallen Smart Building gateway-enhet** .

1. Välj **+ Lägg till relation**. Ange **miljösensor** som visningsnamn och välj **S1 Sensor** som mål.

1. Välj **+ Lägg till relation** igen. Ange **beläggningssensor** som visningsnamn och välj **RS40 Beläggningssensor** som mål.

1. Välj **Spara**.

![Enhetsmall för Smart Building-gateway, som visar relationer](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Lägga till molnegenskaper

En gateway-enhetsmall kan innehålla molnegenskaper. Molnegenskaper finns bara i IoT Central-programmet och skickas aldrig till eller tas emot från en enhet.

Så här lägger du till molnegenskaper i **smartbyggarkördsenhetens** mall.

1. I **mallen För Smart Building-gateway-enhet** väljer du **Molnegenskaper**.

1.  Använd informationen i följande tabell för att lägga till två molnegenskaper i gateway-enhetsmallen.

    | Visningsnamn      | Semantiktyp | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Ingen          | Date   |
    | Kundens namn     | Ingen          | Sträng |

2. Välj **Spara**.

### <a name="create-views"></a>Skapa vyer

Som byggare kan du anpassa programmet så att relevant information om miljösensorenheten visas för en operatör. Dina anpassningar gör det möjligt för operatören att hantera de miljösensorenheter som är anslutna till programmet. Du kan skapa två typer av vyer som en operatör kan använda för att interagera med enheter:

* Formulär för att visa och redigera enhets- och molnegenskaper.
* Instrumentpaneler för att visualisera enheter.

Så här skapar du standardvyerna för enhetsmallen **för Smart Building-gateway:**

1. Välj **Vyer** **i mallen Smart Building gateway-enhet** .

1. Välj **Skapa standardvypanel och** se till att alla alternativ är markerade.

1. Välj **Generera standardinstrumentpanelsvyer.**

## <a name="publish-the-device-template"></a>Publicera enhetsmallen

Innan du kan skapa en simulerad gateway-enhet eller ansluta en riktig gateway-enhet måste du publicera enhetsmallen.

Så här publicerar du gateway-enhetsmallen:

1. Välj **mallen För Smart Building-gateway-enhet** på sidan **Enhetsmallar.**

2. Välj **Publicera**.

3. Välj **Publicera**i dialogrutan **Publicera en enhetsmall** .

När en enhetsmall har publicerats visas den på sidan **Enheter** och för operatorn. I en publicerad enhetsmall kan du inte redigera en enhetskapacitetsmodell utan att skapa en ny version. Du kan dock göra uppdateringar av molnegenskaper, anpassningar och vyer i en publicerad enhetsmall. Dessa uppdateringar gör inte att en ny version skapas. När du har gjort några ändringar väljer du **Publicera** för att skicka ut ändringarna till operatören.

## <a name="create-the-simulated-devices"></a>Skapa de simulerade enheterna

Den här självstudien använder simulerade nedströmsenheter och en simulerad gateway-enhet.

Så här skapar du en simulerad gateway-enhet:

1. På sidan **Enheter** väljer du **Smart Building-gatewayenhet** i listan över enhetsmallar.

1. Välj **+** om du vill börja lägga till en ny enhet.

1. Behåll det genererade **enhets-ID:t** och **enhetsnamnet**. Kontrollera att den **simulerade** brytaren är **på**. Välj **Skapa**.

Så här skapar du en simulerad nedströmsenheter:

1. På sidan **Enheter** väljer du **RS40 Beläggningssensor** i listan över enhetsmallar.

1. Välj **+** om du vill börja lägga till en ny enhet.

1. Behåll det genererade **enhets-ID:t** och **enhetsnamnet**. Kontrollera att den **simulerade** brytaren är **på**. Välj **Skapa**.

1. På sidan **Enheter** väljer du **S1-sensor** i listan över enhetsmallar.

1. Välj **+** om du vill börja lägga till en ny enhet.

1. Behåll det genererade **enhets-ID:t** och **enhetsnamnet**. Kontrollera att den **simulerade** brytaren är **på**. Välj **Skapa**.

![Simulerade enheter i ditt program](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Lägga till underordnade enhetsrelationer på en gateway-enhet

Nu när du har de simulerade enheterna i ditt program kan du skapa relationer mellan nedströmsenheterna och gateway-enheten:

1. På sidan **Enheter** väljer du **S1-sensor** i listan över enhetsmallar och väljer sedan den simulerade **S1-sensorenheten.**

1. Välj **Anslut till gateway**.

1. I dialogrutan **Anslut till en gateway** väljer du enhetsmallen för Smart **Building-gateway** och väljer sedan den simulerade instans som du skapade tidigare.

1. Välj **Gå med**.

1. På sidan **Enheter** väljer du **RS40 Beläggningssensor** i listan över enhetsmallar och väljer sedan din simulerade **RS40-enhet för beläggningssensor.**

1. Välj **Anslut till gateway**.

1. I dialogrutan **Anslut till en gateway** väljer du enhetsmallen för Smart **Building-gateway** och väljer sedan den simulerade instans som du skapade tidigare.

1. Välj **Gå med**.

Båda dina simulerade nedströmsenheter är nu anslutna till din simulerade gateway-enhet. Om du navigerar till vyn **Underordnade enheter** för gateway-enheten kan du se relaterade underordnade enheter:

![Nedströmsenheter vyn](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Välj en gateway-enhetsmall och gateway-enhetsinstans och välj **Gå med**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en ny IoT-gateway som en enhetsmall.
* Skapa molnegenskaper.
* Skapa anpassningar.
* Definiera en visualisering för enhetens telemetri.
* Lägg till relationer.
* Publicera enhetsmallen.

> [!NOTE]
> VS-kodbaserad kodgenerering stöds för närvarande inte för gateway-enheter som modelleras i IoT Central.

Som enhetsutvecklare kan du sedan läsa om hur du:

> [!div class="nextstepaction"]
> [Lägga till en Azure IoT Edge-enhet i ditt Azure IoT Central-program](tutorial-add-edge-as-leaf-device.md)
