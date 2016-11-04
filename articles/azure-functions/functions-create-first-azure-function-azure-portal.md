---
title: Skapa en funktion från Azure Portal | Microsoft Docs
description: Skapa din första Azure-funktion, ett program utan server, på mindre än två minuter.
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: ''
tags: ''

ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/08/2016
ms.author: glenga

---
# Skapa en funktion från Azure Portal
## Översikt
Azure Functions är en händelsedriven beräkna-på-begäran-upplevelse som utökar den befintliga Azure-programplattformen med funktioner för att implementera kod som utlöses av händelser i andra Azure-tjänster, SaaS-produkter och lokala system. Med Azure Functions skalas dina program baserat på efterfrågan och du betalar bara för de resurser som du använder. Med Azure Functions kan du skapa schemalagda eller utlösta kodenheter som implementerats i olika programmeringsspråk. Läs mer om Azure Functions i [Översikt över Azure Functions](functions-overview.md).

Det här ämnet visar hur du använder Azure Portal för att skapa en enkel "hello world".Node.js Azure-funktion som anropas av en HTTP-utlösare. Du måste uttryckligen skapa en funktionsapp i Azure App Service innan du kan skapa en funktion i Azure Portal. Om du vill att funktionsappen skapas automatiskt finns [de andra snabbstartssjälvstudierna för Azure Functions ](functions-create-first-azure-function.md), som är en enklare snabbstart och innehåller en video.

## Skapa en funktionsapp
En funktionsapp är värd för körningen av dina funktioner i Azure. Följ dessa steg för att skapa en funktionsapp i Azure Portal.

Innan du kan skapa din första funktion måste du ha ett aktivt Azure-konto. Om du inte redan har ett Azure-konto [finns kostnadsfria konton tillgängliga](https://azure.microsoft.com/free/).

1. Gå till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.
2. Klicka på **+Ny** > **Webb + Mobilt** > **Funktionsapp**, välj din **Prenumeration**, skriv ett unikt **Appnamn** som identifierar din funktionsapp och ange följande inställningar:
   
   * **[Resursgrupp](../azure-portal/resource-group-portal.md)**: Välj **Skapa ny** och ange ett namn för en ny resursgrupp. Du kan också välja en befintlig resursgrupp, men kanske inte skapa en dynamisk App Service plan för din funktionsapp.
   * **[App Service plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**: Välj antingen *dynamisk* eller *klassisk*. 
     * **Dynamisk**: Standardplantypen för Azure Functions. När du väljer en dynamisk plan måste du också välja **Plats** och ange **Minnesallokering** (i MB). Information om hur minnesallokering påverkar kostnader finns i [priser för Azure Functions](https://azure.microsoft.com/pricing/details/functions/). 
     * **Klassisk**: För en klassisk App Service-plan måste du skapa en **App Service-plan/plats** eller välja en befintlig. Dessa inställningar avgör [plats, funktioner, kostnad och beräkningsresurser](https://azure.microsoft.com/pricing/details/app-service/) som associeras med din app.  
   * **Lagringskontot**: Varje funktionsapp kräver ett lagringskonto. Du kan välja ett befintligt lagringskonto, eller så kan du skapa ett. 
     
     ![Skapa den nya funktionsappen i Azure Portal](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)
3. Klicka på **Skapa** för att etablera och distribuera den nya funktionsappen.  

Du kan skapa din första funktion nu när funktionsappen har etablerats.

## Skapa en funktion
De här stegen skapar en funktion från snabbstart för Azure Functions.

1. På fliken **Snabbstart** klickar du på **WebHook + API**och**JavaScript**, klicka sedan på **Skapa en funktion**. En ny fördefinierad Node.js-funktion skapas. 
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)
2. (Valfritt) I det här läget i Snabbstart kan du välja att få en snabb genomgång av funktionerna i Azure Functions i portalen.   När du har slutfört eller hoppat över genomgången kan du testa den nya funktionen med hjälp av HTTP-utlösaren.

## Testa funktionen
Eftersom Azure Functions-snabbstart innehåller funktionell kod kan du testa den nya funktionen omedelbart.

1. I fliken **Utveckla** kan du granska fönstret **Kod** och se att den här Node.js-koden förväntar sig en HTTP-begäran med ett *namn*-värde skickat antingen i meddelandetexten eller i en frågesträng. När funktionen körs returneras värdet i svarsmeddelandet.
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-develop-tab-testing.png)
2. Rulla ned till textrutan **Begärandetext**, ändra värdet för egenskapen *Namn* till ditt namn och klicka på **Kör**. Du ser att utförandet utlöses av en HTTP-testbegäran, informationen skrivs till direktuppspelningsloggarna och svaret "hello" visas i **Utdata**. 
3. Om du vill starta körningen av samma funktion i ett annat fönster eller i en annan flik i webbläsaren kopierar du värdet **Funktions-URL** från fliken **Utveckla**, klistrar in det i webbläsarens adressfält, lägger sedan till frågesträngsvärdet `&name=yourname` och trycker på retur. Samma information skrivs till loggarna och webbläsaren visar svaret "hello" som innan.

## Nästa steg
Den här snabbstarten visar en väldigt enkel körning av en grundläggande funktion som utlöses av HTTP. I följande ämne finns mer information om att använda kraften i Azure Functions i dina appar.

* [Azure Functions, info för utvecklare](functions-reference.md)  
  Info för programmerare om att koda funktioner och definiera utlösare och bindningar.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.
* [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive den dynamiska serviceplanen, och hur du väljer rätt plan. 
* [Vad är Azure App Service?](../app-service/app-service-value-prop-what-is.md)  
  Azure Functions använder plattformen Azure App Service för grundläggande funktioner som distributioner, miljövariabler och diagnostik. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]

<!--HONumber=sep12_HO2-->


