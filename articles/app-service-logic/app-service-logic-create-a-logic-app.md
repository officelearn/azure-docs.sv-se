<properties
    pageTitle="Skapa en logikapp | Microsoft Azure"
    description="Lär dig hur du skapar en logikapp genom att koppla SaaS-tjänster"
    authors="jeffhollan"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/16/2016"
    ms.author="jehollan"/>

# Skapa en ny logikapp genom att koppla SaaS-tjänster

I det här avsnittet visas hur du på bara några minuter kan komma igång med [Azure Logic Apps](app-service-logic-what-are-logic-apps.md). Vi går igenom ett enkelt arbetsflöde genom vilket du kan skicka intressanta tweets till din e-post.

Om du vill använda det här scenariot behöver du ha:

- En Azure-prenumeration
- Ett Twitter-konto
- En e-postlåda i Outlook.com eller värdbaserat Office 365

## Skapa en ny logikapp för att skicka dina tweets via e-post

1. Välj **Ny** på [instrumentpanelen i Azure Portal](https://portal.azure.com). 
2. Sök efter ”logikapp” i sökfältet och välj sedan **Logikapp**. Du kan också välja **Ny**, **Webb + mobilt** och sedan **Logikapp**. 
3. Ange ett namn för din logikapp, välj en plats, en resursgrupp och sedan **Skapa**.  Om du väljer **Fäst på instrumentpanelen** öppnas logikappen automatiskt när den har distribuerats.  
4. Efter det att du har öppnat din logikapp första gången kan du välja att starta från en mall.  Skapa denna från grunden genom att klicka på **Tom logikapp**. 
1. Det första objektet du måste skapa är utlösaren.  Detta är den händelse som startar logikappen.  Sök efter **Twitter** i sökrutan för utlösare och välj den.
7. Skriv sedan in den sökterm som ska aktivera utlösaren.  Alternativen **Frekvens** och **Intervall** anger hur ofta din logikapp ska söka efter nya tweets (och att den ska returnera alla tweets under det angivna tidsintervallet).
    ![Twitter-sökning](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Klicka på knappen **Nytt steg** och välj sedan **Lägg till en åtgärd** eller **Lägg till ett villkor**
6. När du väljer **Lägg till en åtgärd** kan du söka via [tillgängliga anslutningar](../connectors/apis-list.md) och välja en åtgärd. Du kan t.ex. välja **Outlook.com – Skicka e-post** om du vill skicka e-post från en outlook.com-adress:  
    ![Åtgärder](./media/app-service-logic-create-a-logic-app/actions.png)

7. Nu måste du fylla i de e-postparametrarna som du vill ha:  ![Parametrar](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Slutligen kan du välja **Spara** för att publicera din logikapp.

## Hantera logikappen när den har skapats

Nu är din logikapp igång. Den söker regelbundet efter tweets med den angivna söktermen. När appen hittar en matchande tweet skickar den ett e-postmeddelande till dig. Slutligen kan du se hur du inaktiverar appen eller hur det går för den.

1. Gå till [Azure Portal](https://portal.azure.com)

1. Klicka på **Bläddra** på vänster sida av skärmen och välj **Logic Apps**.

2. Klicka på den nya logikappen som du nyss skapade om du vill visa aktuell status och allmän information.

3. Om du vill redigera din nya logikapp klickar du på **Redigera**.

5. Om du vill inaktivera appen klickar du på **Inaktivera** i kommandofältet.

1. Visa körnings- och utlösningshistoriker om du vill övervaka hur din logikapp körs.  Du kan visa den senaste informationen genom att klicka på **Uppdatera**.

På mindre än 5 minuter har du ställt in en enkel logikapp som körs i molnet. Mer information om att använda funktioner i Logic Apps finns i [Använda funktioner i Logic Apps]. Mer information om själva definitionerna för Logic Apps finns i [Redigera definitioner för Logic Apps](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure-portalen]: https://portal.azure.com
[Använda funktioner i Logic Apps]: app-service-logic-create-a-logic-app.md



<!--HONumber=sep16_HO1-->


