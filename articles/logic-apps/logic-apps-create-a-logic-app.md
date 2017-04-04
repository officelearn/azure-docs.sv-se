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
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Skapa en ny logikapp genom att koppla SaaS-tjänster
I det här avsnittet visas hur du på bara några minuter kan komma igång med [Azure Logic Apps](logic-apps-what-are-logic-apps.md). Vi går igenom ett enkelt arbetsflöde genom vilket du kan skicka intressanta tweets till din e-post.

Om du vill använda det här scenariot behöver du ha:

* En Azure-prenumeration
* Ett Twitter-konto
* Ett Outlook.com- eller Office 365 Outlook-konto

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Skapa en ny logikapp för att skicka dina tweets via e-post

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj **Ny** > **Enterprise-integration** > **Logikapp**.

    Du kan också välja **Ny**, skriva `logic app` i sökrutan och sedan trycka på Retur. Välj **Logikapp** > **Skapa**.

3. Ange ett namn på logikappen, välj din Azure-prenumeration, skapa eller välj en Azure-resursgrupp, välj en plats och välj **Skapa**.

    Om du väljer **Fäst på instrumentpanelen** öppnas logikappen automatiskt efter distributionen.

4. När du öppnar logikappen för första gången kan du välja att starta från en mall.
Skapa denna från grunden genom att klicka på **Tom logikapp**. 

5. Det första objektet du måste skapa är utlösaren. Detta är den händelse som startar logikappen. Sök på **twitter** i sökrutan och välj **När en ny tweet publiceras**. Logga in med användarnamnet och lösenordet för ditt Twitter-konto.

6. Skriv nu in en sökterm som ska utlösa logikappen.

   ![Twitter-sökning](media/logic-apps-create-a-logic-app/twittersearch.png)

    Alternativen **Frekvens** och **Intervall** anger hur ofta din logikapp ska söka efter nya tweets (och returnera alla tweets under det angivna tidsintervallet).

7. Välj **Nytt steg** och välj sedan **Lägg till en åtgärd** eller **Lägg till ett villkor**.

    När du väljer **Lägg till en åtgärd** kan du söka via [tillgängliga anslutningar](../connectors/apis-list.md) och välja en åtgärd. 

8. Sök på **outlook** i sökrutan och välj **Skicka ett e-postmeddelande** för att skicka ett e-postmeddelande från ditt Outlook-konto till den e-postadress som du anger.

   ![Åtgärder](media/logic-apps-create-a-logic-app/actions.png)

9. Nu måste du fylla i de e-postparametrarna som du vill ha:

   ![Parametrar](media/logic-apps-create-a-logic-app/parameters.png)

10. Slutligen kan du välja **Spara** för att publicera din logikapp.

## <a name="manage-your-logic-app-after-creation"></a>Hantera logikappen när den har skapats

Nu är din logikapp igång. Den söker regelbundet efter tweets med den angivna söktermen. När appen hittar en matchande tweet skickar den ett e-postmeddelande till dig. Slutligen kan du se hur du inaktiverar appen eller hur det går för den.

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Klicka på **Fler tjänster** på den vänstra menyn. Välj **Logikappar** under **Enterprise-integration**. Välj din logikapp.

    *    Om du vill visa appens status, körningshistorik och allmän information väljer du **Översikt** på logikappmenyn. Om inte informationen du förväntade dig visas kan du välja **Uppdatera** i kommandofältet.

    *    Välj **Logic App Designer** på logikappmenyn om du vill redigera din app.

    *    Välj **Översikt** på logikappmenyn om du vill inaktivera din app tillfälligt. Välj **Inaktivera** i kommandofältet.

    *    Välj **Översikt** på logikappmenyn om du vill ta bort appen. 
    Välj **Ta bort** i kommandofältet. Ange logikappens namn och välj **Ta bort**.

På mindre än 5 minuter har du ställt in en enkel logikapp som körs i molnet. Mer information om att använda funktioner i Logic Apps finns i [Använda funktioner i Logic Apps]. Mer information om själva definitionerna för Logic Apps finns i [Redigera definitioner för Logic Apps](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Använda funktioner i Logic Apps]: logic-apps-create-a-logic-app.md
