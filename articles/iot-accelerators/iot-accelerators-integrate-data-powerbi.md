---
title: Visualisera fjärrstyrda data med Power BI – Azure | Microsoft Docs
description: I den här självstudien används Power BI Desktop och Cosmos DB för att integrera data från en fjärran sluten övervaknings lösning i en anpassad visualisering. På så sätt kan användarna bygga egna anpassade instrument paneler och dela ut dem till användare som inte är med i lösningen.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 8b0f395936c8628ff7b8dab601168538f97ccdb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74184242"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualisera data för fjärrövervakning med Power BI

I den här självstudien får du lära dig hur du ansluter data från CosmosDB till Power BI. När den här anslutningen har upprättats kan du skapa egna anpassade instrument paneler och lägga till dem på instrument panelen för fjärr styrning. Den här Workstream gör det möjligt för fler specialiserade grafer att skapas, utöver de som finns i rutan. Du kan sedan använda den här självstudien för att integrera med andra data strömmar eller bygga anpassade instrument paneler som ska konsumeras utanför din lösning för fjärr styrning. Genom att skapa instrument paneler i Power BI innebär det att du även kan göra så att varje panel interagerar med varandra när du väljer vissa delar. Du kan till exempel ha ett filter som visar bara information om dina simulerade Last bilar och varje del av din instrument panel skulle se till att du bara visar simulerad Last bils information. Om du vill använda ett annat verktyg än Power BI kan du också utöka de här stegen till att använda visualiserings verktyget och hookar till Cosmos-databasen eller den anpassade databasen om du har konfigurerat en. 

## <a name="prerequisites"></a>Krav

- Du måste ha en fjärran sluten övervaknings lösning som körs
- Du måste ha åtkomst till [Azure Portal](https://portal.azure.com) och din prenumeration på vilken IoT Hub och lösning körs
- Du måste ha [Power BI Desktop](https://powerbi.microsoft.com) installerat, vilken version som helst fungerar


## <a name="information-needed-from-azure-portal"></a>Information som behövs från Azure Portal

1. Navigera till [Azure Portal](https://portal.azure.com) och logga in om det behövs

2. Klicka på resurs grupper på den vänstra panelen

    ![Sido panels navigerings fält](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navigera till resurs gruppen som din IoT-lösning körs på och klicka på för resurs gruppens översikts sida. 

4. På sidan Översikt klickar du på objektet, som har typen "Azure Cosmos DB konto", sedan kommer du till sidan Översikt i Cosmos DB Stream för den IoT-lösningen.

    ![Resursgrupp](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. I panelen till vänster klickar du på avsnittet "nycklar" och noterar följande värden som ska användas i Power BI:

   - URI
   - Primärnyckel

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Konfigurera data strömmen i Power BI
  
1. Öppna Power BI Desktop-appen och klicka på Hämta data i det övre vänstra hörnet. 

    ![Hämta data](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. När du uppmanas att ange data väljer du att söka efter "Azure Cosmos DB" och väljer den här anslutningen. Den här anslutningen hämtar i princip data direkt från Cosmos-databasen i din Azure IoT-lösning
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Ange informationen som du har registrerat ovan:

    * URI
    * Primärnyckel

4. Välj alla tabeller som ska importeras till Power BI. Den här åtgärden startar inläsningen av data. Den längre din lösning har körts, den längre tid det tar för data att läsas in (upp till några timmar). 

    ![Importera tabeller](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. När data har lästs in klickar du på "redigera frågor" på den översta raden i Power BI och expanderar alla tabeller genom att klicka på pilarna i det gula fältet för varje tabell. Detta är i stort sett expanderat för att visa alla kolumner. Du kommer att märka hur data för sådant som fuktighet, hastighets tid osv. är inte av rätt typ.

    ![Ny kolumn](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Till exempel har data som kommer till Power BI ändrats till UNIX-tid när de kom via anslutningen. Om du vill justera för konverteringen kan du skapa en ny kolumn och använda den här ekvationen för att hämta den till datum/tid-format: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Uppdaterad tabell](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document. Device. msg. Received är bara en av kolumnerna med UNIX-formatering och kan ersättas med andra som behöver konverteras. 
  
    Andra data punkter har konverterats till typ strängen kan ändras till dubbla eller int, där det är lämpligt med samma steg som ovan.

## <a name="creating-a-dashboard"></a>Skapa en instrument panel

När strömmen är ansluten är du redo att skapa dina anpassade instrument paneler! Instrument panelen nedan är ett exempel på att ta telemetri som skickas av våra simulerade enheter och visa olika Pivot-animeringar runt den som: 

* Enhets plats på en karta (höger)
* Enheter med deras status och allvarlighets grad. (övre vänstra)
* Enheter med regler på plats och om det finns aviseringar som går ut för dem (längst ned till vänster)

![PowerBi-visualisering](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publicera instrument panelen och uppdatera data

När du har skapat dina instrument paneler rekommenderar vi att du [publicerar dina Power BI-instrumentpaneler](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) för att dela med andra.

Du vill också [Uppdatera data](https://docs.microsoft.com/power-bi/refresh-data) på den publicerade instrument panelen för att kontrol lera att du har den senaste data uppsättningen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kan visualisera data för fjärrövervakning med Power BI

Mer information om hur du anpassar fjärr styrnings lösningen finns i:

* [Anpassa gränssnittet för fjärr styrnings lösning](iot-accelerators-remote-monitoring-customize.md)
* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

