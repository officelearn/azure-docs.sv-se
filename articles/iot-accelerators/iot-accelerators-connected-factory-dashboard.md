---
title: Använda instrumentpanelen Ansluten fabrik – Azure | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder funktioner på instrumentpanelen för ansluten fabrik för att övervaka och hantera dina industriella IoT-enheter.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: b53177d578768428665891704269e63bd8edb09e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820183"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Använda funktioner i instrumentpanelen för anslutningsfabrikens lösningsaccelerator

Distribuera [en molnbaserad lösning för att hantera min industriella IoT-enheter](quickstart-connected-factory-deploy.md) snabbstart visade dig hur du navigerar på instrumentpanelen och svarar på larm. Den här programguiden visar några ytterligare instrumentpanelsfunktioner som du kan använda för att övervaka och hantera dina industriella IoT-enheter.

## <a name="apply-filters"></a>Använda filter

Du kan filtrera informationen som visas på instrumentpanelen antingen på panelen **Fabriksplatser** eller på **alarmpanelen:**

1. Klicka på **tratten** för att visa en lista över tillgängliga filter på panelen med fabriksplatser eller på larmpanelen.

1. Filterpanelen visas:

    [![Filter för lösningsacceleratorn Ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Välj det filter som du behöver och klicka på **Använd**. Det är också möjligt att skriva fri text i filterfälten.

1. Filtret används sedan. Den extra trattikonen anger att ett filter används:

    [![Acceleratorfilter för ansluten fabrikslösning tillämpas](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Ett aktivt filter påverkar inte de OEE- och KPI-värden som visas, och det filtrerar bara listinnehållet.

1. Om du vill ta bort ett filter klickar du på tratten och klickar på **Rensa** på filterpanelen.

## <a name="browse-an-opc-ua-server"></a>Navigera i en OPC UA-serverläsare

När du distribuerar lösningsacceleratorn etablerar du automatiskt en uppsättning simulerade OPC UA-servrar som du kan bläddra bland från instrumentpanelen. Simulerade servrar gör det enkelt för dig att experimentera med lösningsacceleratorn utan att behöva distribuera riktiga servrar.

1. Klicka på **webbläsarikonen** i navigeringsfältet på instrumentpanelen:

    [![Serverwebbläsare för lösningsacceleratorn Ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Välj en av servrarna i listan som visar de servrar som distribueras åt dig i lösningsacceleratorn:

    [![Serverlista för ansluten fabrikslösningsaccelerator](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Klicka på **Connect** (Anslut) för att visa en dialogruta för säkerhet. För simuleringen är det säkert att klicka på **Fortsätt**.

1. Om du vill expandera någon av noderna i serverträdet klickar du på noden. Noder som publicerar telemetri är markerat bredvid dem:

    [![Serverträd för lösningsacceleratorn Ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Högerklicka på ett objekt om du vill läsa, skriva, publicera eller anropa noden. Vilka åtgärder som är tillgängliga beror på dina behörigheter och nodens attribut. Läsningsalternativet visar en kontextpanel med värdet för den specifika noden. Skrivalternativet visar en kontextpanel där du kan ange ett nytt värde. Anropsalternativet visar en nod där du kan ange parametrar för anropet.

## <a name="publish-a-node"></a>Publicera en nod

När du navigerar i en *simulerad OPC UA-server* kan du välja att publicera nya noder. Du kan analysera telemetri från dessa noder i lösningen. Dessa *simulerade OPC UA-servrar* gör det enkelt att experimentera med lösningsacceleratorn utan att distribuera riktiga enheter:

1. Bläddra till en nod i OPC UA-serverläsarträdet som du vill publicera.

1. Högerklicka på noden. Klicka på **Publicera:**

    [![Publicera nod för anslutningsfabrikslösningsaccelerator](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. En kontextpanel visas som anger att publiceringen är klar. Noden visas i stationsnivåvyn med en bock bredvid den:

    [![Ansluten fabrikslösningsaccelerator publicerar framgång](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Kommando och kontroll

Med Ansluten fabrik kan du styra dina industriella enheter direkt från molnet. Du kan använda funktionen till att vidta åtgärder vid larm som genereras av enheten. Du kan till exempel skicka ett kommando till enheten för att öppna en tryckavlastningsventil. Tillgängliga kommandon finns i noden **StationCommands** i OPC UA-serverläsarträdet. I det här scenariot öppnar du en övertrycksventil på en monteringsstation i en produktionslinje i München. Om du vill använda kommando- och kontrollfunktionerna måste du ha rollen **Administratör** för distributionen av lösningsacceleratorer:

1. Bläddra till **StationCommands-noden** i OPC UA-serverwebbläskträdet för München, produktionslinje 0, monteringsstation.

1. Välj det kommando som du vill använda. Högerklicka på noden **OpenPressureReleaseValve.** Klicka på **Ring:**

    [![Anropskommando för lösningsacceleratorn Ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. En kontextpanel visas med information om vilken metod du ska anropa och eventuell parameterinformation. Klicka på **Ring:**

    [![Anropade parametrar för ansluten fabrikslösningsaccelerator](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Kontextpanelen uppdateras för att informera dig att metodanropet har slutförts. Du kan verifiera att anropet har slutförts genom att läsa av värdet för trycknoden som uppdaterades av anropet.

    [![Lyckat anrop för lösningsacceleratorn Ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>I bakgrunden

När du distribuerar en lösningsaccelerator skapar distributionsprocessen flera resurser i den valda Azure-prenumerationen. Du kan visa dessa resurser på Azure-[portalen](https://portal.azure.com). Under distributionsprocessen skapas en **resursgrupp** med ett namn som baseras på det namn som du valde för lösningsacceleratorn :

[![Resursgrupp för ansluten fabrikslösningsaccelerator](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Du kan visa inställningarna för varje resurs genom att välja resursen i listan över resurser i resursgruppen.

Du kan också visa källkoden för lösningsacceleratorn i GitHub-databasen med [azure-iot-connected-factory.](https://github.com/Azure/azure-iot-connected-factory)

När du är klar kan du ta bort lösningsacceleratorn från din Azure-prenumeration på [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) webbplats. På den här webbplatsen kan du enkelt ta bort alla resurser som etablerades när du skapade lösningsacceleratorn.

> [!NOTE]
> Om du vill vara att du tar bort allt som har med lösningsacceleratorn att göra tar du bort det på [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) webbplats. Ta inte bort resursgruppen i portalen.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat en fungerande lösningsaccelerator kan du fortsätta lära dig mer om IoT-lösningsacceleratorer genom att läsa följande artiklar:

* [Konfigurera lösningsacceleratorn för Ansluten fabrik](iot-accelerators-connected-factory-configure.md)
* [Behörigheter på azureiotsolutions.com webbplats](iot-accelerators-permissions.md)
