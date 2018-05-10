---
title: Visualisera Fjärrövervaknings data med Power BI - Azure | Microsoft Docs
description: Den här kursen använder Power BI Desktop- och Cosmos-DB integerate data från en Fjärrövervaknings lösning till en anpassad visualisering. Den här hur användare kan skapa sina egna anpassade instrumentpaneler och dela dem till användare inte i lösningen.
services: iot-suite
suite: iot-suite
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 8c9807119519dd1f93f08d0366311619a2e793eb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualisera Fjärrövervaknings data med Power BI

Den här självstudiekursen beskriver hur du ansluter din Fjärrövervaknings lösning data från CosmosDB till Power BI. Med den här anslutningen har upprättats, kan du sedan skapa egna anpassade instrumentpaneler och lägga till dem tillbaka till instrumentpanelen Fjärrövervaknings lösning. Den här workstream ger mer specialiserad diagram skapas utöver dem direkt. Du kan sedan använda den här självstudiekursen för att integrera med andra dataströmmar eller skapa anpassade instrumentpaneler som ska konsumeras utanför din lösning för fjärråtkomst övervakning. Skapa instrumentpaneler i Power BI innebär att du kan också göra varje panel interagera med varandra som du väljer specifika delar. Exempelvis kan du ha ett filter som visar bara information om din simulerade lastbilar och varje del av din instrumentpanel skulle interagera så att du bara simulerade lastbil information. Du kan också utöka dessa steg för att använda din visualiseringen verktyg och Lägg till Cosmos-databasen eller anpassad databas om du har konfigurerat en om du vill använda ett verktyg än Power BI. 

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en lösning för fjärråtkomst övervakning som för närvarande körs
- Du måste ha åtkomst till [Azure Portal](https://portal.azure.com) och din prenumeration som IoT-hubb och lösningen kör
- Du måste ha [Power BI desktop](https://powerbi.microsoft.com) installerat någon version gör


## <a name="information-needed-from-azure-portal"></a>Information som behövs från Azure-portalen

1. Gå till [Azure Portal](https://portal.azure.com) och logga in om det behövs

2. I den vänstra rutan klickar du på resursgrupper

    ![Sida panelen Nav](media/iot-suite-remote-monitoring-powerbi/side_panel.png)

3. Navigera till den resursgrupp som Iot-lösningen körs på och klicka om du vill att som resursgruppen översiktssidan. 

4. På översiktssidan klickar du på objektet som har typen ”Azure Cosmos DB konto”, sedan tas du till översiktssidan för Cosmos-DB-ström för att IoT-lösningen.

    ![Resursgrupp](media/iot-suite-remote-monitoring-powerbi/resource_groups.png)

5. Klicka på avsnittet ”nycklar” på panelen till vänster och anteckna följande värden som ska användas i PowerBi:

    - URI
    - Primär nyckel

    ![keys](media/iot-suite-remote-monitoring-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Ställa in dataströmmen i Power BI
  
1. Öppna Power BI desktop-appen och klicka på ”Hämta Data” från det övre vänstra hörnet. 

    ![Hämta Data](media/iot-suite-remote-monitoring-powerbi/get_data.png)

2. När du ombeds ange data väljer att söka efter ”Azure Cosmos databas” och väljer den här anslutningen. Den här kopplingen i stort sett hämtar data direkt från cosmos-databasen för Azure IoT-lösningen
  
    ![Cosmos DB](media/iot-suite-remote-monitoring-powerbi/cosmos_db.png)
  
3. Ange den information som du har spelat in ovan:

    * URI
    * Primär nyckel

4. Markera tabellerna som ska importeras till Power BI. Den här åtgärden kommer startar inläsningen av data. Ju längre din lösning har körts, desto längre tid det kan ta att läsa in data (upp till några timmar). 

    ![Importera tabellerna](media/iot-suite-remote-monitoring-powerbi/import_tables.png)

5. När data har slutfört inläsningen, klicka på ”Redigera frågor” på den översta raden i Power BI och expandera alla tabeller genom att klicka på pilarna i gult fält för varje tabell. Detta i stort sett expanderar för att visa alla kolumner. Du ser hur data för sådant som fuktighet, hastighet tid, etc. är inte av rätt typ.

    ![Nya kolumnen](media/iot-suite-remote-monitoring-powerbi/new_column.png)
  
    Till exempel har data till Power BI ändrats till UNIX-tid när det skickade via kopplingen. Om du vill justera för konverteringen kan framöver du skapa en ny kolumn och använda den här formeln för att få i formatet för datum tid: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Uppdaterade tabell](media/iot-suite-remote-monitoring-powerbi/updated_table.png)
  
    Document.Device.msg.Received är bara en av kolumnerna med UNIX formatering och kan ersättas med andra som behöver konvertering. 
  
    Andra datapunkter konverterades till sträng kan ändras i dubbleras eller Int där lämpliga använder samma steg som ovan.

## <a name="creating-a-dashboard"></a>Skapa en instrumentpanel

När dataströmmen har anslutits, är du redo att skapa din anpassade instrumentpaneler! Instrumentpanelen nedan är ett exempel på att telemetri som immmited av våra simulerade enheter och visar olika vrids runt som: 

* Enhetsplats på en karta (höger)
* Enheter med deras status och allvarlighetsgrad. (längst upp till vänster)
* Enheter med regler på plats, och om det inte finns några larm som spelas upp för dem (nedre vänstra hörnet)

![PowerBi visualiseringen](media/iot-suite-remote-monitoring-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publicera instrumentpanelen och uppdatera data

När du har skapat dina instrumentpaneler, rekommenderar vi att du [publicera Power BI-instrumentpaneler](https://docs.microsoft.com/en-us/power-bi/desktop-upload-desktop-files) att dela med andra.

Du också vill [datauppdatering](https://docs.microsoft.com/en-us/power-bi/refresh-data) på publicerade instrumentpanelen för att se till att du har den senaste datamängden.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om hur du visualisera remote övervakningsdata med Power BI

Mer information om hur du anpassar den fjärranslutna övervakningslösning finns:

* [Anpassa Remote övervakningslösning UI](iot-suite-remote-monitoring-customize.md)
* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

