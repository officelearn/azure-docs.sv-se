---
title: Skapa din första Azure-funktion | Microsoft Docs
description: Skapa din första Azure-funktion, ett program utan server, på mindre än två minuter.
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: ''
tags: ''

ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/08/2016
ms.author: glenga

---
# Skapa din första Azure-funktion
## Översikt
Azure Functions är en händelsedriven beräkna-på-begäran-upplevelse som utökar den befintliga Azure-programplattformen med funktioner för att implementera kod som utlöses av händelser i andra Azure-tjänster, SaaS-produkter och lokala system. Med Azure Functions skalas dina program baserat på efterfrågan och du betalar bara för de resurser som du använder. Med Azure Functions kan du skapa schemalagda eller utlösta kodenheter som implementerats med många olika programmeringsspråk. Läs mer om Azure Functions i [Översikt över Azure Functions](functions-overview.md).

Det här ämnet visar hur du använder Azure Functions-snabbstart i portalen för att skapa en enkel "hello world" Node.js-funktion som anropas av en HTTP-utlösare. Du kan också titta på en kort video om du vill se hur dessa steg utförs i portalen.

## Titta på videon
Följande videoklipp visar hur du utför de grundläggande stegen i de här självstudierna. 

[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]


## Skapa en funktion från snabbstarten
En funktionsapp är värd för körningen av dina funktioner i Azure. Följ dessa steg om du vill skapa en ny funktionsapp samt den nya funktionen. Den nya funktionsappen skapas med en standardkonfiguration. Ett exempel på hur du uttryckligen skapar funktionsappen finns i [Snabbstartsguide för de andra Azure-funktionerna](functions-create-first-azure-function-azure-portal.md).

Innan du kan skapa din första funktion måste du ha ett aktivt Azure-konto. Om du inte redan har ett Azure-konto, [finns kostnadsfria konton tillgängliga](https://azure.microsoft.com/free/).

1. Gå till [Azure Functions-portalen](https://functions.azure.com/signin) och logga in med ditt Azure-konto.
2. Skriv ett unikt **namn** för din nya funktionsapp eller acceptera det genererade namnet, välj **Region** och klicka på **Skapa + Kom igång**. 
3. På fliken **Snabbstart** klickar du på **WebHook + API**och**JavaScript**, klicka sedan på **Skapa en funktion**. En ny fördefinierad Node.js-funktion skapas. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Valfritt) I det här läget i Snabbstart kan du välja att få en snabb genomgång av funktionerna i Azure Functions i portalen.   När du har slutfört eller hoppat över genomgången kan du testa den nya funktionen med hjälp av HTTP-utlösaren.

## Testa funktionen
Eftersom Azure Functions-snabbstart innehåller funktionell kod kan du testa den nya funktionen omedelbart.

1. I fliken **Utveckla** kan du granska fönstret **Kod** och se att den här Node.js-koden förväntar sig en HTTP-begäran med ett *namn*-värde skickat antingen i meddelandetexten eller i en frågesträng. När funktionen körs returneras värdet i svarsmeddelandet.
   
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)
2. Rulla ned till textrutan **Begärandetext**, ändra värdet för egenskapen *Namn* till ditt namn och klicka på **Kör**. Du kommer att se att utförandet utlöses av en HTTP-testbegäran, informationen skrivs till direktuppspelningsloggarna och svaret "hello" visas i **Utdata**. 
3. Om du vill starta körningen av samma funktion i ett annat fönster eller i en annan flik i webbläsaren kopierar du värdet **Funktions-URL** från fliken **Utveckla**, klistrar in det i webbläsarens adressfält, lägger sedan till frågesträngsvärdet `&name=yourname` och trycker på retur. Samma information skrivs till loggarna och webbläsaren visar svaret "hello" som innan.

## Nästa steg
Den här snabbstarten visar en väldigt enkel körning av en grundläggande funktion som utlöses av HTTP. I följande ämne finns mer information om att utnyttja kraften i Azure Functions i dina appar.

* [Azure Functions, info för utvecklare](functions-reference.md)  
  Info för programmerare om att koda funktioner och definiera utlösare och bindningar.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.
* [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive den dynamiska serviceplanen, och hur du väljer rätt plan. 
* [Vad är Azure App Service?](../app-service/app-service-value-prop-what-is.md)  
  Azure Functions utnyttjar plattformen Azure App Service för grundläggande funktioner som distributioner, miljövariabler och diagnostik. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]

<!--HONumber=Sep16_HO3-->


