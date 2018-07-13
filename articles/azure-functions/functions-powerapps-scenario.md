---
title: Anropa en funktion från PowerApps | Microsoft Docs
description: Skapa en anpassad anslutningsapp och sedan anropa en funktion med hjälp av kopplingen.
services: functions
keywords: molnappar, cloud services, PowerApps, affärsprocesser, affärsprogram
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 3693b167f196b82c520703fa50ffabb27b555050
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38586984"
---
# <a name="call-a-function-from-powerapps"></a>Anropa en funktion från PowerApps
Den [PowerApps](https://powerapps.microsoft.com) -plattformen är utformad för företagsexperter för att skapa appar utan traditionell programkod. Professionella utvecklare kan använda Azure Functions för att utöka funktionerna i PowerApps, samtidigt PowerApps app builders från de tekniska detaljerna.

Du skapar en app i det här avsnittet baseras på ett scenario med Underhåll för vind syfte. Det här avsnittet visar hur du anropar funktionen som du definierade i [skapa en OpenAPI-definition för en funktion](functions-openapi-definition.md). Funktionen avgör om en nödreparation på en vindturbin är kostnadseffektiv.

![Färdig app i PowerApps](media/functions-powerapps-scenario/finished-app.png)

Information om anropa samma funktion från Microsoft Flow finns i [anropa en funktion från Microsoft Flow](functions-flow-scenario.md).

I det här avsnittet lär du dig hur du:

> [!div class="checklist"]
> * Förbered exempeldata i Excel.
> * Exportera en API-definition.
> * Lägga till en anslutning till API: et.
> * Skapa en app och lägga till datakällor.
> * Lägga till kontroller för att visa data i appen.
> * Lägga till kontroller för att anropa funktionen och visa data.
> * Kör appen för att avgöra om en reparation är kostnadseffektiv.

## <a name="prerequisites"></a>Förutsättningar

+ En aktiv [PowerApps-konto](https://docs.microsoft.com/en-us/powerapps/maker/signup-for-powerapps) med samma tecken i autentiseringsuppgifter som din Azure-konto. 
+ Excel och [Excel-exempelfilen](https://procsi.blob.core.windows.net/docs/turbine-data.xlsx) som du vill använda som en datakälla för din app.
+ I självstudiekursen [skapa en OpenAPI-definition för en funktion](functions-openapi-definition.md).

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Lägg till en anslutning till API
Anpassade API: et (även känt som en anpassad anslutningsapp) är tillgänglig i PowerApps, men du måste upprättar en anslutning till API: et innan du kan använda den i en app.

1. I [web.powerapps.com](https://web.powerapps.com), klickar du på **anslutningar**.

    ![PowerApps-anslutningar](media/functions-powerapps-scenario/powerapps-connections.png)

1. Klicka på **ny anslutning**, rulla ned till den **turbinen** connector, och klicka på den.

    ![Ny anslutning](media/functions-powerapps-scenario/new-connection.png)

1. Ange API-nyckeln och klickar på **skapa**.

    ![Skapa anslutning](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Om du delar appen med andra, måste varje person som fungerar på och använder appen också ange API-nyckel för att ansluta till API: et. Detta kan ändras i framtiden, och vi kommer att uppdatera det här avsnittet för att avspegla.

## <a name="create-an-app-and-add-data-sources"></a>Skapa en app och lägga till datakällor
Nu är du redo att skapa appen i PowerApps och lägga till Excel-data och anpassade API: et som datakällor för appen.

1. I [web.powerapps.com](https://web.powerapps.com) väljer du **Starta från tom** > ![Telefonappikon](media/functions-powerapps-scenario/icon-phone-app.png) (telefon) > **Skapa den här appen**.

    ![Starta från tom - telefonapp](media/functions-powerapps-scenario/create-phone-app.png)

    Appen öppnas i PowerApps Studio för webben. Följande bild visar de olika delarna i PowerApps Studio.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(A) vänstra navigeringsfältet**, där du ser en hierarkisk vy över alla kontroller på varje skärm

    **(B) mittenrutan**, som visar skärmen för att du arbetar med

    **(C) högra fönstret för**, där du anger alternativ som layout och datakällor

    **(D) egenskapen** listrutan, där du väljer de egenskaper som formler tillämpas på

    **(E) formelfältet**, där du lägger till formler (som i Excel) som definierar appbeteendet
    
    **(F) menyfliksområdet**, där du lägger till kontroller och anpassar designelement

1. Lägg till Excel-filen som en datakälla.

    Data importeras ser ut som följande:

    ![Excel-data som ska importeras](media/functions-powerapps-scenario/excel-table.png)

    1. På apparbetsytan väljer du **anslut till data**.

    1. På den **Data** panelen, klickar du på **lägga till statiska data till din app**.

        ![Lägg till datakälla](media/functions-powerapps-scenario/add-static-data.png)

        Du skulle normalt läser och skriver data från en extern källa, men du vill lägga till Excel-data som statiska data eftersom det här är ett exempel.

    1. Navigera till Excel-filen som du sparade, Välj den **sig** tabellen och klicka på **Connect**.

        ![Lägg till datakälla](media/functions-powerapps-scenario/choose-table.png)


1. Lägg till anpassat API som en datakälla.

    1. På den **Data** panelen, klickar du på **Lägg till datakälla**.

    1. Klicka på **turbinen**.

        ![Turbinen reparera anslutningen](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Lägga till kontroller för att visa data i appen
Nu när datakällorna är tillgängliga i appen kan du lägga till en skärm i din app så att du kan visa informationen som turbinen.

1. På den **Start** fliken **ny skärm** > **listskärm**.

    ![Listskärm](media/functions-powerapps-scenario/list-screen.png)

    PowerApps lägger till en skärm som innehåller en *galleriet* att visa objekt och andra kontroller som gör det möjligt för sökning, sortering och filtrering.

1. Ändra namnlisten att `Turbine Repair`, och ändra storlek på galleriet så att det finns utrymme för fler kontroller under den.

    ![Ändra rubriken och ändra storlek på galleriet](media/functions-powerapps-scenario/gallery-title.png)

1. Galleriet är markerat i den högra rutan under **egenskaper**, klickar du på **CustomGallerySample**.

    ![Ändra datakälla](media/functions-powerapps-scenario/change-data-source.png)

1. I den **Data** fönstret väljer **sig** i listan.

    ![Välja datakälla](media/functions-powerapps-scenario/select-data-source.png)

    Datauppsättningen innehåller inte en avbildning, sedan du ändra layouten för att bättre passa för data. 

1. Fortfarande är i den **Data** panelen, ändra **Layout** till **rubrik, underrubrik och brödtext**.

    ![Ändring av gallerilayout](media/functions-powerapps-scenario/change-layout.png)

1. Som det sista steget i den **Data** panelen, ändrar vilka fält som visas i galleriet.

    ![Ändra gallerifält](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Rubrik2** = Title 

1. Galleriet är markerat, ange den **TemplateFill** egenskap enligt följande formel: `If(ThisItem.IsSelected, Orange, White)`.

    ![Mallen fyller formel](media/functions-powerapps-scenario/formula-fill.png)

    Nu är det lättare att se vilka galleri-objekt har valts.

    ![Markerat objekt](media/functions-powerapps-scenario/selected-item.png)

1. Du behöver inte den ursprungliga skärmen i appen. I den vänstra rutan, hovrar du över **Screen1**, klickar du på **...** , och **ta bort**.

    ![Ta bort skärm](media/functions-powerapps-scenario/delete-screen.png)

1. Klicka på **filen**, och ge appen ett namn. Klicka på **spara** på den vänstra menyn, klicka sedan på **spara** i nederkant högra hörnet.

Det finns en mängd annan formatering som du normalt skulle göra i ett produktionsprogram, men vi ska gå vidare till viktig del i det här scenariot – anropa funktionen.

## <a name="add-controls-to-call-the-function-and-display-data"></a>Lägga till kontroller för att anropa funktionen och visa data
Du har en app som visar sammanfattningsdata för varje turbinen, så nu är det dags att lägga till kontroller som anropar funktionen som du skapade och visa data som returneras. Du har åtkomst till baserat på hur du namnger den i OpenAPI-definitionen. i det här fallet den har `TurbineRepair.CalculateCosts()`.

1. I menyfliksområdet på den **infoga** fliken **knappen**. Klicka på fliken samma **etikett**

    ![Infoga knappen och etikett](media/functions-powerapps-scenario/insert-controls.png)

1. Dra knappen och etikett under galleriet och ändra storlek på etiketten. 

1. Välj Knapptext och ändra den till `Calculate costs`. Appen bör se ut som på följande bild.

    ![App med knappen](media/functions-powerapps-scenario/move-button-label.png)

1. Klicka på knappen och ange följande formel för knappens **OnSelect** egenskapen.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    Den här formeln körs när du klickar på knappen och gör följande om det markerade galleriobjektet har en **ServiceRequired** värdet för `Yes`:

    + Tar bort den *samling* `DetermineRepair` ta bort data från föregående anrop. En samling är en tabellvariabel.

    + Tilldelar samlingen data som returneras genom att anropa funktionen `TurbineRepair.CalculateCosts()`. 
    
        De värden som skickas till funktionen kommer från den **EstimatedEffort** och **MaxOutput** fält för det valda objektet i galleriet. Dessa fält visas inte i galleriet, men de är fortfarande tillgänglig för användning i formler.

1. Välj etiketten och ange följande formel för etikettens **Text** egenskapen.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    Den här formeln använder den `First()` funktionen för att få åtkomst till den första (och enda) raden i det `DetermineRepair` samling. Den visar sedan de tre värden som returneras: `message`, `costToFix`, och `revenueOpportunity`. Dessa värden är tom innan den körs för första gången.

    Den färdiga appen bör se ut som på följande bild.

    ![Färdiga appen innan du kör](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Kör appen
Du har en fullständig app! Nu är det dags att köra den och se funktionen anropar i praktiken.

1. Klicka på knappen Kör i det övre högra hörnet av PowerApps Studio: ![Kör app-knappen](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Välj en turbinen med värdet `Yes` för **ServiceRequired**, klicka sedan på den **beräkna kostnader** knappen. Du bör se ett resultat som på följande bild.

    ![Färdig app i PowerApps](media/functions-powerapps-scenario/finished-app.png)

1. Prova andra sig om du vill se det som returneras av funktionen varje gång.

## <a name="next-steps"></a>Nästa steg
I det här avsnittet får du lära dig hur du:

> [!div class="checklist"]
> * Förbered exempeldata i Excel.
> * Exportera en API-definition.
> * Lägga till en anslutning till API: et.
> * Skapa en app och lägga till datakällor.
> * Lägga till kontroller för att visa data i appen.
> * Lägga till kontroller för att anropa funktionen och visa data
> * Kör appen för att avgöra om en reparation är kostnadseffektiv.

Läs mer om PowerApps i [introduktion till PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Mer information om andra intressanta scenarier som använder Azure Functions, se [anropa en funktion från Microsoft Flow](functions-flow-scenario.md) och [skapa en funktion som kan integreras med Azure Logic Apps](functions-twitter-email.md).