---
title: "Skapa arbetsflöden med din första Azure-logikapp | Microsoft Docs"
description: "Kom igång med att ansluta appar och SaaS-tjänster med din första logikapp"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: ce1e0cfe7c7f52d521ef3318376af75331323083
ms.openlocfilehash: 178a57624345a4b3e5d73e64ce4ccf81b8b90a88


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Skapa en ny logikapp genom att koppla SaaS-tjänster
I det här avsnittet visas hur du på bara några minuter kan komma igång med [Azure Logic Apps](logic-apps-what-are-logic-apps.md). Vi går igenom ett enkelt arbetsflöde genom vilket du kan skicka intressanta tweets till din e-post.

Om du vill använda det här scenariot behöver du ha:

* En Azure-prenumeration
* Ett Twitter-konto
* En e-postlåda i Outlook.com eller värdbaserat Office 365

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Skapa en ny logikapp för att skicka dina tweets via e-post
1. Välj **Ny** på [instrumentpanelen i Azure Portal](https://portal.azure.com). 
2. Sök efter ”logikapp” i sökfältet och välj sedan **Logikapp**. Du kan också välja **Ny**, **Webb + mobilt** och sedan **Logikapp**. 
3. Ange ett namn för din logikapp, välj en plats, en resursgrupp och sedan **Skapa**.  Om du väljer **Fäst på instrumentpanelen** öppnas logikappen automatiskt när den har distribuerats.  
4. Efter det att du har öppnat din logikapp första gången kan du välja att starta från en mall.  Skapa denna från grunden genom att klicka på **Tom logikapp**. 
5. Det första objektet du måste skapa är utlösaren.  Detta är den händelse som startar logikappen.  Sök efter **Twitter** i sökrutan för utlösare och välj den.
6. Skriv sedan in den sökterm som ska aktivera utlösaren.  Alternativen **Frekvens** och **Intervall** anger hur ofta din logikapp ska söka efter nya tweets (och att den ska returnera alla tweets under det angivna tidsintervallet).
    ![Twitter-sökning](media/logic-apps-create-a-logic-app/twittersearch.png)
7. Klicka på knappen **Nytt steg** och välj sedan **Lägg till en åtgärd** eller **Lägg till ett villkor**
8. När du väljer **Lägg till en åtgärd** kan du söka via [tillgängliga anslutningar](../connectors/apis-list.md) och välja en åtgärd. Du kan t.ex. välja **Outlook.com – Skicka e-post** om du vill skicka e-post från en outlook.com-adress:  
    ![Åtgärder](media/logic-apps-create-a-logic-app/actions.png)
9. Nu måste du fylla i de e-postparametrarna som du vill ha:  ![Parametrar](media/logic-apps-create-a-logic-app/parameters.png)
10. Slutligen kan du välja **Spara** för att publicera din logikapp.

## <a name="manage-your-logic-app-after-creation"></a>Hantera logikappen när den har skapats
Nu är din logikapp igång. Den söker regelbundet efter tweets med den angivna söktermen. När appen hittar en matchande tweet skickar den ett e-postmeddelande till dig. Slutligen kan du se hur du inaktiverar appen eller hur det går för den.

1. Gå till [Azure Portal](https://portal.azure.com)
2. Klicka på **Bläddra** på vänster sida av skärmen och välj **Logic Apps**.
3. Klicka på den nya logikappen som du nyss skapade om du vill visa aktuell status och allmän information.
4. Om du vill redigera din nya logikapp klickar du på **Redigera**.
5. Om du vill inaktivera appen klickar du på **Inaktivera** i kommandofältet.
6. Visa körnings- och utlösningshistoriker om du vill övervaka hur din logikapp körs.  Du kan visa den senaste informationen genom att klicka på **Uppdatera**.

På mindre än 5 minuter har du ställt in en enkel logikapp som körs i molnet. Mer information om att använda funktioner i Logic Apps finns i [Använda funktioner i Logic Apps]. Mer information om själva definitionerna för Logic Apps finns i [Redigera definitioner för Logic Apps](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Använda funktioner i Logic Apps]: logic-apps-create-a-logic-app.md



<!--HONumber=Jan17_HO4-->


