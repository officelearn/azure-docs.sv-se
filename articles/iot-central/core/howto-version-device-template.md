---
title: Så här fungerar versionshantering av enhetsmallar för dina Azure IoT Central-appar | Microsoft-dokument
description: Iterera över enhetsmallarna genom att skapa nya versioner och utan att påverka dina live-anslutna enheter
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bb77d1a09cb2692765c4c834ce617d13465d4d67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157576"
---
# <a name="create-a-new-device-template-version"></a>Skapa en ny enhetsmallversion



Azure IoT Central möjliggör snabb utveckling av IoT-program. Du kan snabbt iterera över enhetsmalldesignen genom att lägga till, redigera eller ta bort enhetsfunktioner, vyer och anpassningar. När du har publicerat enhetsmallen visas enhetsfunktionsmodellen som Publicerad med låsikoner **bredvid** modellen. För att kunna göra ändringar i enhetskapacitetsmodellen måste du skapa en ny version av enhetsmallen. Under tiden kan molnegenskaper, anpassningar och vyer redigeras när som helst utan att behöva version av enhetsmallen. När du har sparat någon av dessa ändringar kan du publicera enhetsmallen så att de senaste ändringarna är tillgängliga för operatören att visa i Enhetsutforskaren.

> [!NOTE]
> Mer information om hur du skapar en enhetsmall finns i [Konfigurera och hantera en enhetsmall](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Lägga till anpassningar i enhetsmallen utan versionshantering

Vissa delar av enhetens funktioner kan redigeras utan att du behöver version av enhetsmallen och gränssnitten. Vissa av dessa fält innehåller till exempel visningsnamn, semantisk typ, lägsta värde, högsta värde, decimaler, färg, enhet, visningsenhet, kommentar och beskrivning. Så här lägger du till någon av dessa anpassningar:

1. Gå till sidan **Enhetsmallar.**
1. Välj den enhetsmall som du vill anpassa.
1. Välj fliken **Anpassa.**
1. Alla funktioner som definieras i enhetskapacitetsmodellen visas här. Alla fält som du kan redigera här kan sparas och användas i hela programmet, utan att behöva göra en version av enhetsmallen. Om det finns fält som du vill redigera som är skrivskyddade måste du göra en version av enhetsmallen för att ändra dessa. Välj ett fält som du vill redigera och ange i nya värden.
1. Klicka på **Spara**. Nu åsidosätter dessa värden allt som ursprungligen sparades i enhetsmallen och kommer att användas i hela programmet.

## <a name="versioning-a-device-template"></a>Versionsa en enhetsmall

Om du skapar en ny version av enhetsmallen skapas en utkastversion av mallen där enhetskapacitetsmodellen kan redigeras. Alla publicerade gränssnitt kommer att publiceras tills de är individuellt versionsversionerade. Om du vill ändra ett publicerat gränssnitt måste du först skapa en ny enhetsmallversion.

Enhetsmallen bör endast versionsutnyttjas när du försöker redigera en del av enhetskapacitetsmodellen som du inte kan redigera i anpassningsavsnittet i enhetsmallen. 

För att kunna göra en enhetsmall:

1. Gå till sidan **Enhetsmallar.**
1. Välj den enhetsmall som du försöker göra.
1. Klicka på knappen **Version** högst upp på sidan och ge mallen ett nytt namn. Vi har föreslagit ett nytt namn för dig som kan redigeras.
1. Klicka på **Skapa**.
1. Nu är enhetsmallen i utkastläge. Dina gränssnitt är fortfarande låsta och måste versionsversioneras individuellt för att kunna redigeras. 

### <a name="versioning-an-interface"></a>Versionshantering av ett gränssnitt

Med versionshantering av ett gränssnitt kan du lägga till, uppdatera och ta bort funktionerna i gränssnittet som du redan har skapat. 

För att kunna skapa ett gränssnitt:

1. Gå till sidan **Enhetsmallar.**
1. Välj den enhetsmall du har i utkastläge.
1. Välj det gränssnitt som är i publicerat läge som du vill version och redigera.
1. Klicka på knappen **Version** högst upp på gränssnittssidan. 
1. Klicka på **Skapa**.
1. Nu är gränssnittet i utkastläge. Du kan lägga till eller redigera funktioner i gränssnittet utan att bryta befintliga anpassningar och vyer. 

> [!NOTE]
> Standardgränssnitt som publiceras av Azure IoT kan inte versionsutredas eller redigeras. Dessa standardgränssnitt används för enhetscertifiering.

> [!NOTE]
> När gränssnittet har publicerats, kan du inte ta bort någon av dess funktioner även i ett utkastläge. Funktioner kan bara redigeras eller läggas till i gränssnittet i utkastläge.


## <a name="migrate-a-device-across-device-template-versions"></a>Migrera en enhet över enhetsmallversioner

Du kan skapa flera versioner av enhetsmallen. Med tiden har du flera anslutna enheter med hjälp av dessa enhetsmallar. Du kan migrera enheter från en version av enhetsmallen till en annan. I följande steg beskrivs hur du migrerar en enhet:

1. Gå till sidan **Enhetsutforskaren.**
1. Välj den enhet som du behöver för att migrera till en annan version.
1. Välj **Migrera**.
1. Markera enhetsmallen med det versionsnummer som du vill migrera enheten till och välj **Migrera**.

![Så här migrerar du en enhet](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder enhetsmallversioner i ditt Azure IoT Central-program, här är det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Så här skapar du telemetriregler](tutorial-create-telemetry-rules.md)
