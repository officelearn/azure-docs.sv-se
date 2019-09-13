---
title: Migrera till Azure-resurs för redigering
titleSuffix: Azure Cognitive Services
description: Migrera till en resurs nyckel för Azure-redigering.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 8e732496c4523646f02ef461b7d3c52314c28dae
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932832"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrera till en Azure-resurs redigerings nyckel

Language Understanding (LUIS) som redigerar autentiseringen har ändrats från ett e-postkonto till en Azure-resurs. 

## <a name="why-migrate"></a>Varför ska man migrera?

Med hjälp av en Azure-resurs för redigering kan du, som ägare av resursen, kontrol lera åtkomsten till redigering. Du kan skapa och namnge att skriva resurser för att hantera olika grupper av författare. 

Om du till exempel har två typer av LUIS-appar som du redigerar, med olika medlemmar, kan du skapa två olika redigerings resurser och tilldela bidrags givare. Azure Authoring-resursen styr auktoriseringen. 

> [!Note]
> Innan migreringen kallas medförfattare _för medarbetare_. Efter migreringen används Azure-rollen för _deltagare_ för samma funktion.

## <a name="what-is-migrating"></a>Vad är migrering?

Migreringen omfattar:

* Alla användare av LUIS, ägare och deltagare.
* **Alla** appar.
* En **enkelriktad migrering.**

Ägaren kan inte välja en delmängd av appar som ska migreras och processen inte kan ångras. 

Migreringen är inte: 

* En process som samlar in medarbetare och automatiskt flyttar eller lägger till i Azure Authoring-resursen. Du, som appens ägare, måste slutföra det här steget. Det här steget kräver behörighet till lämplig resurs.
* En process för att skapa och tilldela en förutsägelse körnings resurs. Om du behöver en förutsägelse körnings resurs, är det [en separat process](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) och är oförändrad. 

## <a name="how-are-the-apps-migrating"></a>Hur migrerar appar?

[Luis-portalen](https://www.luis.ai) innehåller migreringsprocessen. 

Du uppmanas att migrera om:

* Du har appar i systemet för e-postautentisering för redigering.
* Och du är appens ägare. 

Du kan försena migreringsprocessen genom att avbryta i fönstret. Du uppmanas regelbundet att migrera tills du migrerar eller så skickas tids gränsen för migreringen. Du kan starta migreringsprocessen från det övre navigerings fältets lås ikon.

## <a name="migration-for-the-app-owner"></a>Migrering av appens ägare

### <a name="before-you-migrate"></a>Innan du migrerar

* **Du kan också**säkerhetskopiera apparna från Luis-portalens lista med appar genom att exportera varje app eller använda export- [API: et](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40).
* **Du kan också**Spara varje apps collaborator's-lista. Den här e-postlistan tillhandahålls som en del av migreringsprocessen.
* **Krävs**måste du ha en Azure- [prenumeration](https://azure.microsoft.com/free/). En del av prenumerations processen kräver fakturerings information. Du kan dock använda den kostnads fria (`F0`) pris nivån när du använder Luis. 

**Redigering av din Luis-app är kostnads fri**, vilket `F0` anges av nivån. Läs [mer om pris nivåer](luis-boundaries.md#key-limits).

Om du inte har någon Azure-prenumeration kan du [Registrera dig](https://azure.microsoft.com/free/). 

### <a name="migration-steps"></a>Migreringsanvisningar

Följ [dessa steg för migrering](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Efter migreringen 

Efter migreringsprocessen är alla dina LUIS-appar nu kopplade till en enda LUIS Authoring-resurs.

Du kan skapa fler redigerings resurser och tilldela dem från sidan **hantera > Azure-resurser** på Luis- _portalen_. 

Du kan lägga till deltagare till redigerings resursen från _Azure Portal_på sidan **Access Control (IAM)** för resursen. Mer information finns i [lägga till deltagar åtkomst](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource) .

|Portalen|Syfte|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Skapa förutsägelse-och redigera resurser.<br>* Tilldela deltagare.|
|[LUIS](https://www.luis.ai)|* Migrera till nya redigerings resurser.<br>* Tilldela eller ta bort tilldelningar av förutsägelse och redigering av resurser till appar från sidan **hantera > Azure-resurser** .| 

## <a name="migration-for-the-app-contributor"></a>Migrering av appens deltagare

Varje användare av LUIS måste migreras, inklusive medarbetare/deltagare. 

### <a name="before-the-app-is-migrated"></a>Innan appen migreras

Du kan välja att exportera en app som du är en medarbetare på och sedan importera appen tillbaka till LUIS. Import processen skapar en ny app med ett nytt app-ID som du är ägare till.

### <a name="after-the-app-is-migrated"></a>När appen har migrerats

Appens ägare måste [lägga till ditt e-postmeddelande till Azure Authoring-resursen som en medarbetare](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource). 

Efter migreringen är alla appar som du äger tillgängliga på sidan **Mina appar** i Luis-portalen.  

## <a name="next-steps"></a>Nästa steg

* [Så här migrerar du din app till en redigerings resurs](luis-migration-authoring-steps.md)