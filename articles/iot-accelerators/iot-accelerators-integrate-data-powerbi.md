---
title: Visualisera fjärrövervakning data med Power BI – Azure | Microsoft Docs
description: Den här självstudien använder Power BI Desktop och Cosmos DB till integerate data från en lösning för fjärrövervakning i en anpassad visualisering. Den här hur användarna kan skapa sina egna anpassade instrumentpaneler och dela dem till användare inte på lösningen.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 9daf0f0d1c7ca94308bfb0d894366b457a045770
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470655"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualisera fjärrövervakning data med Power BI

Den här självstudien vägleder dig genom hur du ansluter din fjärrövervakning lösningsdata från CosmosDB till Power BI. Med den här anslutningen har upprättats, kan du sedan skapa dina egna anpassade instrumentpaneler och lägga till dem tillbaka till instrumentpanelen för fjärrövervakning av lösningen. Den här workstream möjliggör mer specialiserade diagram som ska skapas, förutom som direkt ur lådan. Du kan sedan använda den här självstudien för att integrera med andra dataströmmar eller skapa anpassade instrumentpaneler som ska konsumeras utanför din lösning för övervakning. Skapa instrumentpaneler i Power BI innebär att du kan också göra varje panel interagera med varandra som du väljer specifika delar. Exempelvis kan du kan ha ett filter som visar endast information om din simulerade lastbilar och varje del av instrumentpanelen skulle interagera så att du endast simulerade lastbil information. Du kan också utöka dessa steg för att använda din visualiseringsverktyg och anslutas till Cosmos-databasen eller anpassad databas om du har konfigurerat en om du vill använda ett verktyg än Power BI. 

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en lösning för fjärrövervakning som för närvarande körs
- Du måste ha åtkomst till [Azure-portalen](https://portal.azure.com) och din prenumeration som IoT Hub och lösningen körs
- Du måste ha [Power BI desktop](https://powerbi.microsoft.com) installerat någon version kommer att göra


## <a name="information-needed-from-azure-portal"></a>Information som behövs från Azure-portalen

1. Gå till [Azure-portalen](https://portal.azure.com) och logga in om det behövs

2. På den vänstra panelen klickar du på resursgrupper

    ![Sida panelen Nav](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navigera till den resursgrupp som din Iot-lösning är igång på och klicka om du vill att den resursgruppen översiktssidan. 

4. På översiktssidan klickar du på objektet som har typen ”Azure Cosmos DB-konto”, sedan tas du till översiktssidan på Cosmos DB-dataströmmen för den IoT-lösning.

    ![Resursgrupp](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Klicka på avsnittet ”nycklar” på panelen till vänster och anteckna följande värden som ska användas i Power BI:

    - URI
    - Primär nyckel

    ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Konfigurera Stream i Power BI
  
1. Öppna Power BI desktop-appen och klicka på ”Hämta Data” i det övre vänstra hörnet. 

    ![Hämta Data](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. När du ombeds ange data, Välj söker du efter ”Azure Cosmos DB” och välj den här anslutningen. Den här anslutningen i stort sett hämtar data direkt från cosmos-databasen för din Azure IoT-lösning
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Ange den information som du har spelat in ovan:

    * URI
    * Primär nyckel

4. Markera alla tabeller som ska importeras till Power BI. Den här åtgärden startar inläsning av data. Ju längre din lösning har körts, desto längre tid det kan ta att läsa in data (upp till några timmar). 

    ![Importera tabeller](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. När data har slutförts klickar du på ”Redigera frågor” på den översta raden i Power BI läser in, och expandera alla tabeller genom att klicka på pilarna i det gula fältet för varje tabell. Detta kommer i stort sett utökas för att visa alla kolumner. Du ser hur data för sådant som fuktighet, påskynda osv är inte av rätt typ.

    ![Ny kolumn](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Exempelvis har data som kommer till Power BI ändrats till UNIX-tiden när det gällde via anslutningen. Om du vill justera för den här konverteringen, kan vi rekommenderar att du skapa en ny kolumn och använda den här formeln hämtar den i formatet för datum-tid: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Uppdaterade tabell](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.Device.msg.Received är bara en av kolumnerna med UNIX formatering och kan ersättas med andra som behöver konvertering. 
  
    Andra datapunkter har omvandlats till typen sträng kan ändras till Double-värden eller Int där lämpliga med samma steg som ovan.

## <a name="creating-a-dashboard"></a>Skapa en instrumentpanel

När dataströmmen har anslutits, är du redo att skapa dina anpassade instrumentpaneler! Instrumentpanelen nedan är ett exempel på att ta den telemetri som genereras av våra simulerade enheter och som visar olika pivoteringsmöjligheter runt den som: 

* Enhetsplats på en karta (höger)
* Enheter med deras status och allvarlighetsgrad. (upp till vänster)
* Enheter med regler på plats, och om det finns några aviseringar som spelas upp för dem (vänster nederkant)

![Power BI-visualisering](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publicera instrumentpanelen och uppdatera data

När du har skapat dina instrumentpaneler, rekommenderar vi att du [publicera Power BI-instrumentpaneler](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) att dela med andra.

Du också vill [uppdaterar data](https://docs.microsoft.com/power-bi/refresh-data) på publicerade instrumentpanelen för att se till att du har den senaste datauppsättningen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om hur du kan visualisera remote övervakningsdata med Power BI

Mer information om hur du anpassar lösningen för fjärrövervakning finns:

* [Anpassa lösningen för fjärrövervakning UI](iot-accelerators-remote-monitoring-customize.md)
* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

