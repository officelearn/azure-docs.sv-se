---
title: Konfigurera webbsida programmets Azure IoT Central | Microsoft Docs
description: Lär dig hur du konfigurerar du startsidan för Azure IoT Central programmet som en builder.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 599616f046b2eccee009e8a08e8a2f51cf968644
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773827"
---
# <a name="configuring-homepage"></a>Konfigurera startsida

Startsidan är den sida som läses in när användare som har åtkomst till programmet går du till programmets URL. Om du har valt antingen ”exempel Contoso” eller ”exempel Devkits” programmallar när du skapar ditt program, ska ditt program ha fördefinierade startsidor. Om du valde å andra sidan programmallen ”anpassade program”, kommer din startsida måste anges.

## <a name="add-tiles"></a>Lägg till paneler

Här är till exempel startsidan för programmen baserat på mallen ”Contoso Sample”. Välj först för att anpassa startsidan för ditt program, **redigera** längst upp till höger. 

![Startsida för programmen baserat på mallen ”Contoso exemplet”](media/howto-configure-homepage-experimental/image1.png)

Att välja **redigera**, öppnas instrumentpanelen biblioteket på en panel till vänster. Det finns många typer av paneler och instrumentpanelen primitiver som kan läggas till att anpassa din startsida.

![Instrumentpanelen bibliotek](media/howto-configure-homepage-experimental/image2.png)

Du kan till exempel lägga till en **inställningar och egenskaper** rutan för att visa ett urval av de aktuella värdena för inställningar och egenskaper. Om du vill göra det väljer du först en **enheten mallen** Välj sedan en **enhetsinstansen**. Efter som tillhandahåller panelen en rubrik och väljer en **inställningen** eller en **egenskapen** ska visas. I det här fallet har vi valt **ange temperatur**. Klicka på **klar** leder till den här panelen visas på startsidan.

![”Konfigurera enhetsinformation” formuläret med information om inställningar och egenskaper](media/howto-configure-homepage-experimental/image3.png)

När en operatör visar startsidan, kan de nu se den här panelen som visar de egenskaper eller inställningarna för enheten:

![Fliken ”instrumentpanel” med visas inställningar och egenskaper för panelen](media/howto-configure-homepage-experimental/image4.png)

Experimentera med de olika andra typerna av paneler i biblioteket att upptäcka hur du kan anpassa ditt programs startsida ännu mer.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar din Azure IoT Central startsida, kan du:

> [!div class="nextstepaction"]
> [Lär dig hur du förbereder och ladda upp bilder](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
