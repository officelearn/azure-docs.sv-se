---
title: Anropa en funktion från PowerApps | Microsoft Docs
description: Skapa en anpassad koppling och sedan anropa en funktion med hjälp av kopplingen.
services: functions
keywords: molnappar, cloud services, PowerApps, processer, business-program
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
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082673"
---
# <a name="call-a-function-from-powerapps"></a>Anropa en funktion från PowerApps
Den [PowerApps](https://powerapps.microsoft.com) plattformen är avsedd för affärsexperter att skapa appar utan traditionella programkod. Professionella utvecklare kan använda Azure Functions för att utöka funktionerna i PowerApps, samtidigt PowerApps app builders från teknisk information.

Du kan skapa en app i den här artikeln baserat på ett scenario för underhåll för vind turbiner. Det här avsnittet beskrivs hur du anropar funktionen som du definierade i [en OpenAPI definition för en funktion för att skapa](functions-openapi-definition.md). Funktionen avgör om en reparation på en vind turbin är kostnadseffektiv.

![Klar app i PowerApps](media/functions-powerapps-scenario/finished-app.png)

Mer information om anropa samma funktion från Microsoft Flow finns [anropa en funktion från Microsoft Flow](functions-flow-scenario.md).

I det här avsnittet lär du dig hur du:

> [!div class="checklist"]
> * Förbereda exempeldata i Excel.
> * Exportera en API-definition.
> * Lägga till en anslutning till API.
> * Skapa en app och Lägg till datakällor.
> * Lägga till kontroller för att visa data i appen.
> * Lägga till kontroller för att anropa funktionen och visa data.
> * Kör appen för att avgöra om en reparation är kostnadseffektiv.

## <a name="prerequisites"></a>Förutsättningar

+ En aktiv [PowerApps konto](https://docs.microsoft.com/en-us/powerapps/maker/signup-for-powerapps) med samma tecken i autentiseringsuppgifter som din Azure-konto. 
+ Excel och [Excel exempelfilen](https://procsi.blob.core.windows.net/docs/turbine-data.xlsx) som du vill använda som en datakälla för din app.
+ Slutför guiden [en OpenAPI definition för en funktion för att skapa](functions-openapi-definition.md).

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Lägga till en anslutning till API
Anpassade API: et (även kallat en anpassad koppling) finns i PowerApps, men du måste upprätta en anslutning till API innan du kan använda den i en app.

1. I [web.powerapps.com](https://web.powerapps.com), klickar du på **anslutningar**.

    ![PowerApps anslutningar](media/functions-powerapps-scenario/powerapps-connections.png)

1. Klicka på **ny anslutning**, rulla ned till den **turbinen reparera** connector, och klicka på den.

    ![Ny anslutning](media/functions-powerapps-scenario/new-connection.png)

1. Ange API-nyckeln och klickar på **skapa**.

    ![Skapa anslutning](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Om du delar din app med andra, måste varje person som fungerar på och använder appen för också ange API-nyckel för att ansluta till API: et. Detta kan ändras i framtiden, och vi kommer att uppdatera det här avsnittet för att återspegla.

## <a name="create-an-app-and-add-data-sources"></a>Skapa en app och Lägg till datakällor
Nu är du redo att skapa appen i PowerApps och lägga till Excel-data och anpassade API som datakällor för appen.

1. I [web.powerapps.com](https://web.powerapps.com) väljer du **Starta från tom** > ![Telefonappikon](media/functions-powerapps-scenario/icon-phone-app.png) (telefon) > **Skapa den här appen**.

    ![Starta från tom - telefonapp](media/functions-powerapps-scenario/create-phone-app.png)

    Appen öppnas i PowerApps Studio för webbplatsen. Följande bild visar de olika delarna av PowerApps Studio.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(A) vänstra navigeringsfältet**, som visas i en hierarkisk vy över alla kontroller på varje skärm

    **(B) mittenrutan**, som visar skärmen för att du arbetar med

    **(C) högra**, var du anger alternativ, till exempel layout och datakällor

    **(D) egenskapen** listrutan väljer du de egenskaper som formler som gäller för

    **(E) formelfältet**, där du lägger till formler (som i Excel) som definierar beteendet för app
    
    **F menyfliksområdet**, där du lägger till kontroller och anpassa designelement

1. Lägg till Excel-filen som en datakälla.

    De data som du vill importera ser ut som följande:

    ![Data som ska importeras](media/functions-powerapps-scenario/excel-table.png)

    1. På apparbetsytan väljer du **anslut till data**.

    1. På den **Data** klickar du på **lägga till statiska data i appen**.

        ![Lägg till datakälla](media/functions-powerapps-scenario/add-static-data.png)

        Du skulle normalt läser och skriver data från en extern källa, men du vill lägga till Excel-data som statiska data eftersom det här är ett exempel.

    1. Navigera till Excel-filen som du sparade, Välj den **turbiner** tabell och klicka på **Anslut**.

        ![Lägg till datakälla](media/functions-powerapps-scenario/choose-table.png)


1. Lägg till anpassade API som en datakälla.

    1. På den **Data** klickar du på **Lägg till datakälla**.

    1. Klicka på **turbinen reparera**.

        ![Turbinen reparera connector](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Lägga till kontroller för att visa data i appen
Nu när datakällorna som är tillgängliga i appen, du lägger till en skärm i appen så att du kan visa data för turbinen.

1. På den **Start** klickar du på **nya skärmen** > **lista skärmen**.

    ![Lista över skärmen](media/functions-powerapps-scenario/list-screen.png)

    PowerApps lägger till en skärm som innehåller en *galleriet* att visa objekt och andra kontroller som används för att aktivera sökning, sortering och filtrering.

1. Ändra namnlisten till `Turbine Repair`, och ändra storlek på galleriet så att det finns utrymme för fler kontroller under den.

    ![Ändra rubrik och ändra storlek på galleriet](media/functions-powerapps-scenario/gallery-title.png)

1. Med galleriet som valts i den högra rutan, under **egenskaper**, klickar du på **CustomGallerySample**.

    ![Datakälla för förändring](media/functions-powerapps-scenario/change-data-source.png)

1. I den **Data** fönstret väljer **turbiner** från listan.

    ![Välja datakälla](media/functions-powerapps-scenario/select-data-source.png)

    Datauppsättningen innehåller inte en bild bredvid du ändra layouten så att de bättre passar data. 

1. Fortfarande i den **Data** panelen, ändra **Layout** till **rubrik, underrubrik och brödtext**.

    ![Ändra galleriet layout](media/functions-powerapps-scenario/change-layout.png)

1. Som det sista steget i den **Data** panelen, ändra fälten som visas i galleriet.

    ![Ändra galleriet fält](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Rubrik2** = Title 

1. Med galleriet markerat, ange den **TemplateFill** egenskapen till följande formel: `If(ThisItem.IsSelected, Orange, White)`.

    ![Mallen fill formel](media/functions-powerapps-scenario/formula-fill.png)

    Nu är det lättare att se vilka galleriobjektet har valts.

    ![Valda objekt](media/functions-powerapps-scenario/selected-item.png)

1. Du behöver inte den ursprungliga skärmen i appen. I den vänstra rutan hovra över **Screen1**, klickar du på **...** , och **ta bort**.

    ![Ta bort skärmen](media/functions-powerapps-scenario/delete-screen.png)

1. Klicka på **filen**, och ger den namnet appen. Klicka på **spara** klicka sedan på den vänstra menyn **spara** i nederkant högra hörnet.

Det finns en mängd annan formatering som du vanligtvis gör i ett produktionsprogram, men vi ska flytta in viktig del i det här scenariot - anropar funktionen.

## <a name="add-controls-to-call-the-function-and-display-data"></a>Lägga till kontroller för att anropa funktionen och visa data
Du har en app som visar sammanfattningsdata för varje turbinen, nu är det dags att lägga till kontroller som anropa sedan funktionen som du skapade och visa data som returneras. Du har åtkomst till baserat på hur du namnger den i OpenAPI-definition. i det här fallet har `TurbineRepair.CalculateCosts()`.

1. I menyfliksområdet på den **infoga** klickar du på **knappen**. Klicka på fliken samma **etikett**

    ![Knappen Infoga och etikett](media/functions-powerapps-scenario/insert-controls.png)

1. Dra knappen och etiketten nedan galleriet och ändra storlek på etiketten. 

1. Texten på knappen Välj och ändra den till `Calculate costs`. Appen bör se ut som följande bild.

    ![Appen med knappen](media/functions-powerapps-scenario/move-button-label.png)

1. Klicka på knappen och ange följande formel för att knappen **OnSelect** egenskapen.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    Den här formeln körs när användaren klickar på knappen och gör följande om det markerade objektet har en **ServiceRequired** värdet för `Yes`:

    + Tar bort den *samling* `DetermineRepair` att ta bort data från tidigare anrop. En samling är en tabell variabel.

    + Tilldelar mängden data som returneras genom att anropa funktionen `TurbineRepair.CalculateCosts()`. 
    
        Värdena som skickas till funktionen komma från den **EstimatedEffort** och **MaxOutput** fält för det valda objektet i galleriet. Dessa fält visas inte i galleriet, men de är fortfarande tillgänglig för användning i formler.

1. Väljer du etiketten och ange följande formel för etiketten **Text** egenskapen.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    Den här formeln använder den `First()` funktion för att få åtkomst till den första (och endast) raden i det `DetermineRepair` samling. Visar värdena som returnerar funktionen: `message`, `costToFix`, och `revenueOpportunity`. Dessa värden är tomt innan appen körs för första gången.

    Den färdiga appen bör se ut som följande bild.

    ![Färdiga appen innan du kör](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Kör appen
Du har en fullständig app! Nu är det dags att köra det och se den anropar i åtgärden.

1. Klicka på knappen Kör i det övre högra hörnet av PowerApps Studio: ![Kör app-knappen](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Välj en turbin med värdet `Yes` för **ServiceRequired**, klicka på den **beräkna kostnader** knappen. Du bör se ett resultat som på följande bild.

    ![Klar app i PowerApps](media/functions-powerapps-scenario/finished-app.png)

1. Försök med andra turbiner om du vill se vad som returneras av funktionen varje gång.

## <a name="next-steps"></a>Nästa steg
I det här avsnittet får du lära dig hur du:

> [!div class="checklist"]
> * Förbereda exempeldata i Excel.
> * Exportera en API-definition.
> * Lägga till en anslutning till API.
> * Skapa en app och Lägg till datakällor.
> * Lägga till kontroller för att visa data i appen.
> * Lägga till kontroller för att anropa funktionen och visa data
> * Kör appen för att avgöra om en reparation är kostnadseffektiv.

Läs mer om PowerApps i [introduktion till PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Mer information om andra intressanta scenarier som använder Azure Functions, se [anropa en funktion från Microsoft Flow](functions-flow-scenario.md) och [skapa en funktion som kan integreras med Azure Logikappar](functions-twitter-email.md).