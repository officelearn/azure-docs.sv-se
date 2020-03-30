---
title: Visualisera fjärrövervakningsdata med Power BI - Azure | Microsoft-dokument
description: Den här självstudien använder Power BI Desktop och Cosmos DB för att integrera data från en lösning för fjärrövervakning till en anpassad visualisering. På så sätt kan användare skapa sina egna anpassade instrumentpaneler och dela dem till användare som inte finns på lösningen.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 8b0f395936c8628ff7b8dab601168538f97ccdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184242"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualisera fjärrövervakningsdata med Power BI

Den här självstudien hjälper dig att koppla in lösningsdata för fjärrövervakning från CosmosDB till Power BI. Med den här anslutningen upprättad kan du sedan skapa egna anpassade instrumentpaneler och lägga till dem på instrumentpanelen för fjärrövervakningslösningen. Denna workstream möjliggör mer specialiserade grafer som ska skapas, förutom de ur lådan. Du kan sedan använda den här självstudien för att integrera med andra dataströmmar eller skapa anpassade instrumentpaneler som ska förbrukas utanför fjärrövervakningslösningen. Att skapa instrumentpaneler i Power BI innebär att du också kan få varje panel att interagera med varandra när du väljer specifika delar. Du kan till exempel ha ett filter som bara visar information om dina simulerade lastbilar och varje del av instrumentpanelen skulle interagera för att visa dig bara simulerad lastbilsinformation. Om du vill använda ett annat verktyg än Power BI kan du också utöka de här stegen för att använda det visuella verktyget som du väljer och koppla in i Cosmos-databasen eller anpassad databas om du har konfigurerat en. 

## <a name="prerequisites"></a>Krav

- Du måste ha en lösning för fjärrövervakning som körs för tillfället
- Du måste ha tillgång till [Azure-portalen](https://portal.azure.com) och din prenumeration där IoT Hub och Solution körs
- Du måste ha [Power BI-skrivbordet](https://powerbi.microsoft.com) installerat, alla versioner


## <a name="information-needed-from-azure-portal"></a>Information som behövs från Azure-portalen

1. Navigera till [Azure-portalen](https://portal.azure.com) och inloggningen om det behövs

2. Klicka på Resursgrupper på den vänstra panelen

    ![Sidopanel nav](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navigera till den resursgrupp som din Iot-lösning körs på och klicka på för att tas till sidan Översikt för resursgruppen. 

4. På den översiktssidan klickar du på objektet, som har typen "Azure Cosmos DB Account", och kommer sedan till översiktssidan för Cosmos DB-strömmen för den IoT-lösningen.

    ![Resursgrupp](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Klicka på avsnittet "Nycklar" på panelen till vänster och ta del av följande värden som ska användas i Power BI:

   - URI
   - Primärnyckel

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Konfigurera strömmen i Power BI
  
1. Öppna Power BI-skrivbordsappen och klicka på "Hämta data" från det övre vänstra hörnet. 

    ![Hämta data](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. När du uppmanas att ange data väljer du att söka efter "Azure Cosmos DB" och väljer den här kopplingen. Den här anslutningen hämtar i huvudsak data direkt från kosmosdatabasen för din Azure IoT-lösning
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Ange informationen, som du har registrerat ovan:

    * URI
    * Primärnyckel

4. Markera alla tabeller som ska importeras till Power BI. Denna åtgärd kommer att sparka igång inläsningen av data. Ju längre lösningen har körts, desto längre tid kan det ta för data att läsas in (upp till några timmar). 

    ![Importera tabeller](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. När data har lästs in klickar du på "Redigera frågor" på den översta raden i Power BI och expanderar alla tabeller genom att klicka på pilarna i det gula fältet för varje tabell. Detta kommer i huvudsak att expandera för att visa alla kolumner. Du kommer att märka hur data för saker som luftfuktighet, hastighetstid, etc. inte är av rätt typ.

    ![Ny kolumn](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Data som kom in i Power BI ändrades till unix-tid när de kom in via kopplingen. Om du vill justera för den här konverteringen kan du skapa en ny kolumn framåt och använda den här ekvationen för att få in den i datumtidsformatet: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Uppdaterad tabell](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received är bara en av kolumnerna med UNIX-formatering och kan ersättas med andra som behöver konvertering. 
  
    Andra datapunkter konverterades till typSträng kan ändras till Dubbel eller Int där så är lämpligt med samma steg som ovan.

## <a name="creating-a-dashboard"></a>Skapa en instrumentpanel

När strömmen har anslutits är du redo att skapa dina personliga instrumentpaneler! Instrumentpanelen nedan är ett exempel på att ta telemetri som avges av våra simulerade enheter, och visar olika pivoter runt den, till exempel: 

* Enhetsplats på en karta (höger)
* Enheter med status och allvarlighetsgrad. (överst till vänster)
* Enheter med regler på plats, och om det finns några varningar som går ut för dem (nere till vänster)

![PowerBi-visualisering](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publicera instrumentpanelen och uppdatera data

När du har skapat instrumentpanelerna rekommenderar vi att du [publicerar dina Power BI-instrumentpaneler](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) för att dela med andra.

Du vill också [uppdatera data](https://docs.microsoft.com/power-bi/refresh-data) på den publicerade instrumentpanelen för att se till att du har den senaste datauppsättningen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du visualiserar fjärrövervakningsdata med Power BI

Mer information om hur du anpassar lösningen för fjärrövervakning finns i:

* [Anpassa användargränssnittet för fjärrövervakningslösning](iot-accelerators-remote-monitoring-customize.md)
* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

