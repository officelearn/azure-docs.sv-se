---
title: "Skapa din första Azure-funktion | Microsoft Docs"
description: "Skapa din första Azure-funktion, ett program utan server, på mindre än två minuter."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: ae5837b4fce52aad4c8b39434c27c450aafc1310
ms.openlocfilehash: 6f42f79abed80df12148463e25935893a4bbdcde


---
# <a name="create-your-first-azure-function"></a>Skapa din första Azure-funktion
## <a name="overview"></a>Översikt
Azure Functions är en händelsedriven beräkna-på-begäran-upplevelse som utökar den befintliga Azure-programplattformen med funktioner för att implementera kod som utlöses av händelser i andra Azure-tjänster, SaaS-produkter och lokala system. Med Azure Functions skalas dina program baserat på efterfrågan och du betalar bara för de resurser som du använder. Med Azure Functions kan du skapa schemalagda eller utlösta kodenheter som implementerats i olika programmeringsspråk. Läs mer om Azure Functions i [Översikt över Azure Functions](functions-overview.md).

Det här ämnet visar hur du använder Azure Functions-snabbstart i portalen för att skapa en enkel "hello world" Node.js-funktion som anropas av en HTTP-utlösare. Du kan också titta på en kort video om du vill se hur dessa steg utförs i portalen.

## <a name="watch-the-video"></a>Titta på videon
Följande videoklipp visar hur du utför de grundläggande stegen i den här självstudiekursen. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Skapa en funktion från snabbstarten
En funktionsapp är värd för körningen av dina funktioner i Azure. Följ dessa steg för att skapa en funktionsapp med den nya funktionen. Funktionsappen skapas med en standardkonfiguration. Ett exempel på hur du uttryckligen skapar funktionsappen finns i [Snabbstartsguide för de andra Azure-funktionerna](functions-create-first-azure-function-azure-portal.md).

Innan du kan skapa din första funktion måste du ha ett aktivt Azure-konto. Om du inte redan har ett Azure-konto, [finns kostnadsfria konton tillgängliga](https://azure.microsoft.com/free/).

1. Gå till [Azure Functions-portalen](https://functions.azure.com/signin) och logga in med ditt Azure-konto.
2. Skriv ett unikt **namn** för din nya funktionsapp eller acceptera det genererade namnet, välj **Region** och klicka på **Skapa + Kom igång**. 
3. På fliken **Snabbstart** klickar du på **WebHook + API**och**JavaScript**, klicka sedan på **Skapa en funktion**. En ny fördefinierad Node.js-funktion skapas. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Valfritt) I det här läget i Snabbstart kan du välja att få en snabb genomgång av funktionerna i Azure Functions i portalen. När du har slutfört eller hoppat över genomgången kan du testa den nya funktionen med hjälp av HTTP-utlösaren.

## <a name="test-the-function"></a>Testa funktionen
Eftersom Azure Functions-snabbstart innehåller funktionell kod kan du testa den nya funktionen omedelbart.

1. I fliken **Utveckla** kan du granska fönstret **Kod** och se att den här Node.js-koden förväntar sig en HTTP-begäran med ett *namn*-värde skickat antingen i meddelandetexten eller i en frågesträng. När funktionen körs returneras värdet i svarsmeddelandet.
   
2. Klicka på **Testa** för att visa det inbyggda fönstret för HTTP-testbegäran för funktionen.
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. I textrutan **Begärandetext** ändrar du värdet för egenskapen *Namn* till ditt namn och klickar på **Kör**. Du ser att utförandet utlöses av en HTTP-testbegäran, informationen skrivs till direktuppspelningsloggarna och svaret "hello" visas i **Utdata**.
 
3. Om du vill starta körningen av samma funktion i ett annat fönster eller på en annan flik i webbläsaren kopierar du värdet för **Funktionswebbadress** från fliken **Utveckla** och klistrar in det i webbläsarens adressfält. Lägg till frågesträngsvärdet `&name=yourname` till URL:en och tryck på Retur. Samma information skrivs till loggarna och webbläsaren visar svaret "hello" som innan.

## <a name="next-steps"></a>Nästa steg
Den här snabbstarten visar en enkel körning av en grundläggande funktion som utlöses av HTTP. Mer information om hur du använder Azure Functions i dina appar finns i följande avsnitt:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)  
  Info för programmerare om att koda funktioner och definiera utlösare och bindningar.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.
* [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive värdplanen för förbrukning, och hur du väljer rätt plan. 
* [Vad är Azure App Service?](../app-service/app-service-value-prop-what-is.md)  
  Azure Functions använder plattformen Azure App Service för grundläggande funktioner som distributioner, miljövariabler och diagnostik. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO4-->


