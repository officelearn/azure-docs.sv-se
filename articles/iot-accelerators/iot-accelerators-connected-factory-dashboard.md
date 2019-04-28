---
title: Använd instrumentpanelen för ansluten fabrik – Azure | Microsoft Docs
description: Förstå hur du använder funktioner för ansluten fabrik-instrumentpanelen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 05befc72f512f502456f798f25b6c72571451363
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451044"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Använda funktioner i instrumentpanelen för lösningen accelerator för ansluten fabrik

Den [distribuera en molnbaserad lösning för att hantera mina industriella IoT-enheter](quickstart-connected-factory-deploy.md) Snabbstart såg du hur du navigerar på instrumentpanelen och svara på larm. Den här guiden visar vissa ytterligare instrumentpanel-funktioner som du kan använda för att övervaka och hantera dina industriella IoT-enheter.

## <a name="apply-filters"></a>Använda filter

Du kan filtrera informationen som visas på instrumentpanelen, antingen i den **Fabriksplatser** panelen eller **larm** panelen:

1. Klicka på **tratten** för att visa en lista över tillgängliga filter på panelen med fabriksplatser eller på larmpanelen.

1. Filterpanelen visas:

    [![Ansluten fabrik solution accelerator filter](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Välj önskat filter som krävs och klicka på **tillämpa**. Du kan också skriva fritext i filterfälten.

1. Filtret tillämpas sedan. Den extra tratt-ikonen indikerar att ett filter tillämpas:

    [![Ansluten fabrik solution accelerator applicerat filter](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Ett aktivt filter påverkar inte de visade OEE och KPI-värdena det filtrerar bara listinnehållet.

1. Om du vill ta bort ett filter klickar du på tratten och klicka på **Rensa** i filterpanelen.

## <a name="browse-an-opc-ua-server"></a>Navigera i en OPC UA-serverläsare

När du distribuerar solution accelerator, etablerar du automatiskt en uppsättning simulerade OPC UA-servrar som du kan bläddra från instrumentpanelen. Simulerade servrar gör det enkelt att experimentera med lösningsaccelerator utan att behöva distribuera verkliga servrar. Om du vill ansluta en verklig OPC UA-server till lösningen finns i den [ansluta OPC UA-enheten till ansluten fabrik lösningsaccelerator](iot-accelerators-connected-factory-gateway-deployment.md) självstudien.

1. Klicka på den **bläddringsikonen** i navigeringsfältet instrumentpanelen:

    [![Ansluten fabrik solution accelerator-servrar](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Välj en av servrarna i listan som visar de servrar som är distribuerade åt dig i solution accelerator:

    [![Ansluten fabrik solution accelerator serverlista](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Klicka på **Connect** (Anslut) för att visa en dialogruta för säkerhet. För simuleringen är det säkert att klicka på **Fortsätt**.

1. Om du vill expandera någon av noderna i serverträdet klickar du på noden. Noder som publicerar telemetri har en bock bredvid dem:

    [![Ansluten fabrik solution accelerator serverträdet](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Högerklicka på ett objekt om du vill läsa, skriva, publicera eller anropa noden. Vilka åtgärder som är tillgängliga beror på dina behörigheter och nodens attribut. Läsningsalternativet visar en kontextpanel med värdet för den specifika noden. Skrivalternativet visar en kontextpanel där du kan ange ett nytt värde. Anropsalternativet visar en nod där du kan ange parametrar för anropet.

## <a name="publish-a-node"></a>Publicera en nod

När du navigerar i en *simulerad OPC UA-server* kan du välja att publicera nya noder. Du kan analysera telemetri från dessa noder i lösningen. Dessa *simulerade OPC UA-servrar* gör det enkelt att experimentera med solution accelerator utan att distribuera verkliga enheter:

1. Bläddra till en nod i OPC UA-serverläsarträdet som du vill publicera.

1. Högerklicka på noden. Klicka på **publicera**:

    [![Lösningsaccelerator för ansluten fabrik publicerar nod](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. En kontextpanel visas som anger att publiceringen är klar. Noden visas i vyn på stationsnivå med en bock bredvid den:

    [![Slutförd publicering i den anslutna fabriken lösningsaccelerator](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Kommando och kontroll

Med Ansluten fabrik kan du styra dina industriella enheter direkt från molnet. Du kan använda funktionen till att vidta åtgärder vid larm som genereras av enheten. Du kan till exempel skicka ett kommando till enheten för att öppna en övertrycksventil. Tillgängliga kommandon finns i noden **StationCommands** i OPC UA-serverläsarträdet. I det här scenariot öppnar du en övertrycksventil på en monteringsstation i en produktionslinje i München. Om du vill använda funktionen för kommando och kontroll, måste du vara i den **administratör** för solution accelerator distributionen:

1. Bläddra till den **StationCommands** nod i OPC UA-serverläsarträdet för München, produktionslinje 0, monteringsstation.

1. Välj det kommando som du vill använda. Högerklicka på den **OpenPressureReleaseValve** noden. Klicka på **anropa**:

    [![Ansluten fabrik solution accelerator anropskommando](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. En kontextpanel visas som talar om vilken metod som du håller på att anrop och parameterinformation som. Klicka på **anropa**:

    [![Anslutna fabriken solution accelerator anrop parametrar](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Kontextpanelen uppdateras för att informera dig att metodanropet har slutförts. Du kan verifiera att anropet har slutförts genom att läsa av värdet för trycknoden som uppdaterades av anropet.

    [![Slutfört anrop accelerator för lösningen för ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>I bakgrunden

När du distribuerar en lösningsaccelerator skapar distributionsprocessen flera resurser i den valda Azure-prenumerationen. Du kan visa dessa resurser i Azure [portal](https://portal.azure.com). Under distributionsprocessen skapas en **resursgrupp** med ett namn som baseras på det namn som du valde för lösningsacceleratorn :

[![Ansluten fabrik solution accelerator resursgrupp](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Du kan visa inställningarna för varje resurs genom att välja resursen i listan över resurser i resursgruppen.

Du kan också visa källkoden för solution accelerator i den [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory) GitHub-lagringsplatsen.

När du är klar kan du ta bort solution accelerator från Azure-prenumerationen på den [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) plats. På den här webbplatsen kan du enkelt ta bort alla resurser som etablerades när du skapade lösningsacceleratorn.

> [!NOTE]
> För att säkerställa att du tar bort allt relaterat till solution accelerator, ta bort den på den [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) plats. Ta inte bort resursgruppen i portalen.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat en fungerande lösningsaccelerator kan du fortsätta lära dig mer om IoT-lösningsacceleratorer genom att läsa följande artiklar:

* [Ansluten fabrik accelerator genomgång av lösning](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Anslut enheten till lösningsaccelerator ansluten fabrik](iot-accelerators-connected-factory-gateway-deployment.md)
* [Behörigheter på webbplatsen azureiotsolutions.com](iot-accelerators-permissions.md)
