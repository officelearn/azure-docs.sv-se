<properties
    pageTitle="Skapa en logikapp | Microsoft Azure"
    description="Lär dig hur du skapar en logikapp genom att koppla SaaS-tjänster"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="app-service\logic"
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/16/2016"
    ms.author="stepsic"/>

# Skapa en ny logikapp genom att koppla SaaS-tjänster

| Snabbreferens |
| --------------- |
| [Definitionsspråk för Logic Apps](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Dokumentation om logikappskopplingar](../connectors/apis-list.md) |
| [Forum för Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

Det här avsnittet visar hur du på bara några minuter kan komma igång med [Logic Apps i Apptjänst](app-service-logic-what-are-logic-apps.md). Vi går igenom ett arbetsflöde som gör att du kan skicka en uppsättning tweets som du är intresserad av till en postlåda.

Om du vill använda det här scenariot behöver du ha:

- En Azure-prenumeration
- Ett Twitter-konto
- Ett Office 365-konto

## Skapa en ny logikapp för att skicka dina tweets via e-post

1. På instrumentpanelen i Azure-portalen markerar du  **Marketplace**. 
2. Sök efter Logic Apps under Allt och välj sedan **Logikapp (förhandsgranskning)**. Du kan också välja **Ny**, **Webb + mobilt** och sedan **Logikapp (förhandsgranskning)**. 
3. Ange ett namn för din logikapp, välj apptjänstplan och välj sedan **Skapa**.  
    I det här steget förutsätter vi att du har en apptjänstplan och att du är bekant med de obligatoriska egenskaperna. Oroa dig inte om det inte är så – du kan starta vid [Djupgående översikt över Azure Apptjänstplaner](azure-web-sites-web-hosting-plans-in-depth-overview.md). 

4. När logikappen öppnas för första gången behöver du en utlösare. Sök efter **Twitter** i sökrutan för utlösare och välj den.

7. Nu ska du ange vilka nyckelord som du vill söka efter på Twitter. 
    ![Twitter-sökning](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Klicka på plustecknet och välj sedan **Lägg till en åtgärd** eller **Lägg till ett villkor**:  
    ![Plus](./media/app-service-logic-create-a-logic-app/plus.png)
6. När du väljer **Lägg till en åtgärd** visas alla kopplingar med sina tillgängliga åtgärder. Du kan sedan välja vilka kopplingar och åtgärder du ska lägga till i din logikapp. Du kan till exempel välja **Office 365 – skicka e-post** och fler Office 365-åtgärder:  
    ![Åtgärder](./media/app-service-logic-create-a-logic-app/actions.png)

7. Nu måste du fylla i de e-postparametrarna som du vill ha:  ![Parametrar](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Slutligen kan du välja **Spara** för att publicera din logikapp.

## Hantera logikappen när den har skapats

Nu är din logikapp igång. Varje gång det schemalagda arbetsflödet körs söker den efter tweets med specifika hashtaggar. När den hittar en matchande tweet placeras den i din Dropbox. Slutligen kan du se hur du inaktiverar appen eller hur det går för den.

1. Klicka på **Bläddra** på vänster sida av skärmen och välj **Logic Apps**.

2. Klicka på den nya logikappen som du nyss skapade om du vill visa aktuell status och allmän information.

3. Om du vill redigera den nya logikappen klickar du på **Utlösare och åtgärder**.

5. Om du vill inaktivera appen klickar du på **Inaktivera** i kommandofältet.

På mindre än 5 minuter har du ställt in en enkel logikapp som körs i molnet. Mer information om att använda funktioner i Logic Apps finns i [Använda funktioner i Logic Apps]. Mer information om själva definitionerna för Logic Apps finns i [Redigera definitioner för Logic Apps](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure-portalen]: https://portal.azure.com
[Använda funktioner i Logic Apps]: app-service-logic-create-a-logic-app.md



<!--HONumber=Jun16_HO2-->


