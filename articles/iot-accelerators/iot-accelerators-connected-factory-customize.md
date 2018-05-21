---
title: Anpassa lösningen anslutna Factory - Azure | Microsoft Docs
description: En beskrivning av hur du anpassar anslutna Factory solution accelerator.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 6d3008fb92f2240c22680f1b9486067e8bd2ae88
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Anpassa hur lösningen anslutna Factory visar data från OPC UA-servrar

Ansluten Factory lösningen sammanställer och visar data från OPC UA-servrar som är anslutna till lösningen. Du kan bläddra och skicka kommandon till OPC UA-servrar i din lösning. Mer information om OPC UA finns i [Vanliga frågor och svar om Ansluten fabrik](iot-accelerators-faq-cf.md).

Exempel på sammanställda data i lösningen är den övergripande utrustning effektivitet (OEE) och nyckeln (nyckeltal) som kan visas på instrumentpanelen på fabriken, rad och station nivåer. Följande skärmbild visar OEE och KPI-värden för den **sammansättningen** station på **produktion rad 1**i den **München** fabriken:

![Exempel på OEE och KPI-värden i lösningen][img-oee-kpi]

Den här lösningen kan du visa detaljerad information från specifika dataobjekt från OPC UA-servrar kallas *stationer*. Följande skärmbild visar områden av antalet tillverkade artiklar från en specifik station:

![Områden av antal tillverkade artiklar][img-manufactured-items]

Om du klickar på diagrammet kan du utforska data med ytterligare tid serien insikter (TSD):

![Utforska data med hjälp av tid serien insikter][img-tsi]

Den här artikeln beskrivs:

- Hur data ska få tillgång till de olika vyerna i lösningen.
- Visar hur du kan anpassa hur lösningen data.

## <a name="data-sources"></a>Datakällor

Ansluten Factory lösningen visar data från OPC UA-servrar som är anslutna till lösningen. Standardinstallationen omfattar flera OPC UA-servrar som kör en fabrik simulering. Du kan lägga till egna OPC UA-servrar som [ansluta via en gateway] [ lnk-connect-cf] i lösningen.

Du kan bläddra dataobjekt som en anslutna OPC UA-servern kan skicka din lösning på instrumentpanelen:

1. Välj **webbläsare** att navigera till den **väljer du en OPC UA server** vy:

    ![Navigera till Markera en vy som OPC UA][img-select-server]

1. Välj en server och klicka på **Anslut**. Klicka på **Fortsätt** när säkerhetsvarningen visas.

    > [!NOTE]
    > Den här varningen visas en gång för varje server endast och upprättar en förtroenderelation mellan lösning instrumentpanelen och servern.

1. Du kan nu bläddra dataobjekt som servern kan skicka till lösningen. Objekt som skickas till lösningen har markerat:

    ![Publicerade objekt][img-published]

1. Om du är en *administratör* i lösningen kan du välja att publicera ett dataobjekt att göra den tillgänglig i den anslutna Factory-lösningen. Som administratör kan du också ändra värdet för dataobjekt och anropa metoder i OPC UA-server.

## <a name="map-the-data"></a>Mappa data

Ansluten Factory lösningen mappar och sammanställer publicerade dataobjekt från servern OPC UA till de olika vyerna i lösningen. Ansluten Factory-lösningen distribueras till din Azure-konto när du etablerar lösningen. En JSON-fil i Visual Studio anslutna Factory lösningen lagrar mappningsinformationen. Du kan visa och ändra den här JSON-konfigurationsfil i ansluten Factory Visual Studio-lösning. Du kan distribuera lösningen igen när du har gjort en ändring.

Du kan använda konfigurationsfilen till:

- Redigera den befintliga simulerade fabriker och produktion rader stationer.
- Mappa data från verkliga OPC UA-servrar som du ansluter till lösningen.

Mer information om mappning och datainsamling för att uppfylla dina specifika krav finns [hur du konfigurerar anslutna Factory solution accelerator ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Distribuera ändringarna

När du är klar med att göra ändringar i den **ContosoTopologyDescription.json** fil, du måste distribuera om anslutna Factory lösningen till din Azure-konto.

Den **azure iot-ansluten-fabrik** databasen innehåller en **build.ps1** PowerShell-skript som du kan använda för att återskapa och distribuera lösningen.

## <a name="next-steps"></a>Nästa steg

Läs mer om anslutna Factory solution accelerator genom att läsa följande artiklar:

* [Genomgång av lösningsacceleratorn Ansluten fabrik][lnk-rm-walkthrough]
* [Distribuera en gateway för anslutna Factory][lnk-connect-cf]
* [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions]
* [Vanliga frågor och svar om ansluten fabrik](iot-accelerators-faq-cf.md)
* [VANLIGA FRÅGOR OCH SVAR][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: ../iot-suite/iot-suite-v1-permissions.md
[lnk-faq]: ../iot-suite/iot-suite-v1-faq.md