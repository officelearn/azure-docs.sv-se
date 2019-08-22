---
title: Förstå versions hantering av enhets mallar för dina Azure IoT Central-appar | Microsoft Docs
description: Iterera över dina enhetsspecifika mallar genom att skapa nya versioner och utan att påverka dina Live-anslutna enheter
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ac2af605859d9555cc1c165afac4ca5e7ba343cc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879661"
---
# <a name="create-a-new-device-template-version-preview-features"></a>Skapa en ny version av enhets mal len (förhands gransknings funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Med Azure IoT Central kan du snabbt utveckla IoT-program. Du kan snabbt iterera över mallarnas design genom att lägga till, redigera eller ta bort enhets funktioner, vyer och anpassningar. När du har publicerat din enhets mall visas enhetens kapacitets modell som **publicerad** med lås ikoner bredvid modellen. För att kunna göra ändringar i enhets kapacitets modellen måste du skapa en ny version av enhets mal len. Samtidigt kan du när som helst redigera moln egenskaper, anpassningar och vyer, utan att behöva version av enhets mal len. När du har sparat någon av dessa ändringar kan du publicera enhets mal len för att göra de senaste ändringarna som är tillgängliga för operatören att visa i Device Explorer.

> [!NOTE]
> Mer information om hur du skapar en enhets mall finns i [Konfigurera och hantera en enhets mall](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Lägg till anpassningar i enhets mal len utan versions hantering

Vissa delar av enhetens funktioner kan redige ras utan att du behöver version av enhets mal len och gränssnitten. Till exempel innehåller vissa av dessa fält visnings namn, semantisk typ, minimalt värde, högsta värde, decimaler, färg, enhet, visnings enhet, kommentar och beskrivning. För att lägga till en av dessa anpassningar:

1. Gå till **Device templates** -sidan.
1. Välj den enhets mall som du vill anpassa.
1. Välj fliken **Anpassa** .
1. Alla funktioner som definieras i enhetens kapacitets modell visas här. Alla fält som du kan redigera här kan sparas och användas i hela programmet, utan att du behöver ha en version av din enhets mall. Om det finns fält som du vill redigera som är skrivskyddade måste du konfigurera enhets mal len för att ändra dessa. Välj ett fält som du vill redigera och ange i alla nya värden.
1. Klicka på **Spara**. Dessa värden kommer nu att åsidosätta allting som ursprungligen sparades i din enhets mall och som kommer att användas i hela programmet.

## <a name="versioning-a-device-template"></a>Versions hantering av enhets mal len

Om du skapar en ny version av din enhets mall skapas en utkast version av mallen där enhetens kapacitets modell kan redige ras. Alla publicerade gränssnitt förblir publicerade tills de är individuellt versions bara. För att kunna ändra ett publicerat gränssnitt måste du först skapa en ny version av enhets mal len.

Enhets mal len ska bara vara versions hantering när du försöker redigera en del av enhetens kapacitets modell som du inte kan redigera i avsnittet anpassningar i enhets mal len. 

För att få en enhets mall version:

1. Gå till **Device templates** -sidan.
1. Välj den enhets mall som du försöker att köra en version av.
1. Klicka på knappen **version** överst på sidan och ge mallen ett nytt namn. Vi har föreslagit ett nytt namn som kan redige ras.
1. Klicka på **Skapa**.
1. Nu är din enhets mall i utkast läge. Du ser att gränssnitten fortfarande är låsta och måste vara individuellt versions bara för att kunna redige ras. 

### <a name="versioning-an-interface"></a>Versioner av ett gränssnitt

Med versions hanterings gränssnittet kan du lägga till, uppdatera och ta bort funktionerna i gränssnittet som du redan har skapat. 

För att kunna version ett gränssnitt:

1. Gå till **Device templates** -sidan.
1. Välj den enhets mall som du har i utkast läge.
1. Välj det gränssnitt som är i publicerat läge som du vill använda som version och redigera.
1. Klicka på knappen **version** överst på gränssnitts sidan. 
1. Klicka på **Skapa**.
1. Nu är ditt gränssnitt i utkast läge. Du kommer att kunna lägga till eller redigera funktioner i gränssnittet utan att behöva bryta befintliga anpassningar och vyer. 

> [!NOTE]
> Standard gränssnitt som publicerats av Azure IoT kan inte vara versioner eller redige ras. Dessa standard gränssnitt används för enhets certifiering.

> [!NOTE]
> När gränssnittet har publicerats kan du inte ta bort någon av dess funktioner även i ett utkast läge. Funktioner kan bara redige ras eller läggas till i gränssnittet i utkast läge.


## <a name="migrate-a-device-across-device-template-versions"></a>Migrera en enhet över enhets mal len versioner

Du kan skapa flera versioner av enhets mal len. Med tiden kommer du att ha flera anslutna enheter som använder dessa enhets mallar. Du kan migrera enheter från en version av din enhets mall till en annan. Följande steg beskriver hur du migrerar en enhet:

1. Gå till sidan **Device Explorer** .
1. Välj den enhet som du vill migrera till en annan version.
1. Välj **migrera**.
1. Välj enhets mal len med det versions nummer som du vill migrera enheten till och välj **migrera**.

![Så här migrerar du en enhet](media/howto-version-device-template-pnp/pick-version.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder versioner av enhets mallar i ditt Azure IoT Central-program är du det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Skapa regler för telemetri](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
