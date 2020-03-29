---
title: Migrera till Azure-resurs för redigering
titleSuffix: Azure Cognitive Services
description: Migrera till en Azure-redigeringsresursnyckel.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194517"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrera till en Azure-resursförfattare nyckel

Språk understanding (LUIS) redigeringsautentisering har ändrats från ett e-postkonto till en Azure-resurs. Även om det inte krävs för närvarande kommer byte till en Azure-resurs att tillämpas i framtiden.

## <a name="why-migrate"></a>Varför ska man migrera?

Med hjälp av en Azure-resurs för redigering kan du som ägare av resursen styra åtkomsten till redigering. Du kan skapa och namnge redigeringsresurser för att hantera olika grupper av författare.

Du äger till exempel två LUIS-appar och du har olika medlemmar som är medarbetare i varje app. Du kan skapa två olika redigeringsresurser och tilldela varje app till varje enskild resurs. Tilldela sedan varje medlem som deltagare till rätt redigeringsresurs beroende på vilken app de samarbetar med. Azure-redigeringsresursen styr auktoriseringen.

> [!Note]
> Före migreringen kallas medförfattare för _medarbetare_ på LUIS-appnivå. Efter migreringen används Azure-rollen _för deltagaren_ för samma funktioner men på Azure-resursnivå.

## <a name="what-is-migrating"></a>Vad är mig migrerande?

Migreringen omfattar:

* Alla användare av LUIS, ägare och bidragsgivare.
* **Alla** appar.
* En **enkelriktad** migrering.

Ägaren kan inte välja en delmängd av appar som ska migreras och processen är inte reversibel.

Migreringen är inte:

* En process som samlar in medarbetare och automatiskt flyttar eller lägger till Azure-redigeringsresursen. Du som appägare måste slutföra det här steget. Det här steget kräver behörigheter till rätt resurs.
* En process för att skapa och tilldela en förutsägelsekörningsresurs. Om du behöver en förutsägelsekörningsresurs är det [en separat process](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) och är oförändrad.

## <a name="how-are-the-apps-migrating"></a>Hur migreras apparna?

[LUIS-portalen](https://www.luis.ai) tillhandahåller migreringsprocessen.

Du kommer att bli ombedd att migrera om:

* Du har appar på e-postautentiseringssystemet för redigering.
* Och du är appägaren.

Du kan fördröja migreringsprocessen genom att avbryta fönstret. Du uppmanas regelbundet att migrera tills du migrerar eller migreringsdeadline har passerats. Du kan starta migreringsprocessen från det övre navigeringsfältets låsikon.

## <a name="migration-for-the-app-owner"></a>Migrering för appägaren

### <a name="before-you-migrate"></a>Innan du migrerar

* **Obligatoriskt**måste du ha en [Azure-prenumeration](https://azure.microsoft.com/free/). En del av prenumerationsprocessen kräver faktureringsinformation. Du kan dock använda`F0`prisnivån Gratis ( ) när du använder LUIS.
* **Du kan också**säkerhetskopiera apparna från LUIS-portalens applista genom att exportera varje app eller använda [export-API:et](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40).
* **Du kan också**spara varje apps medarbetares lista. Den här e-postlistan tillhandahålls som en del av migreringsprocessen.


**Det är gratis att redigera LUIS-appen**, vilket indikeras av `F0` nivån. Läs [mer om prisnivåer](luis-boundaries.md#key-limits).

Om du inte har en Azure-prenumeration [registrerar du dig](https://azure.microsoft.com/free/).

### <a name="migration-steps"></a>Migreringsanvisningar

Följ [dessa migreringssteg](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>När du har migrerat

Efter migreringen tilldelas nu alla LUIS-appar till en enda LUIS-redigeringsresurs.

Du kan skapa fler redigeringsresurser och tilldela från sidan **Hantera -> Azure-resurser** i _LUIS-portalen_.

Du kan lägga till deltagare i redigeringsresursen från _Azure-portalen_på sidan **Åtkomstkontroll (IAM)** för den resursen. Mer information finns i [Lägga till deltagaråtkomst](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

|Portalen|Syfte|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Skapa förutsägelse och skapa resurser.<br>* Tilldela bidragsgivare.|
|[LUIS](https://www.luis.ai)|* Migrera till nya redigeringsresurser.<br>* Tilldela eller ta bort förutsägelseförutsägelser och redigera resurser till appar från **hantera -> Azure-resurssida.**|

## <a name="migration-for-the-app-contributor"></a>Migrering för appdeltagaren

Varje användare av LUIS måste migrera, inklusive medarbetare/bidragsgivare. En medarbetare måste migrera för att ha åtkomst till appen.

> [!Note]
> Om ägaren till LUIS-appen migrerade och lade till medarbetaren som deltagare på Azure-resursen, har medarbetaren fortfarande ingen åtkomst till appen om de inte också migrerar.

### <a name="before-the-app-is-migrated"></a>Innan appen migreras

Du kan välja att exportera en app som du är medarbetare på och sedan importera appen tillbaka till LUIS. Importprocessen skapar en ny app med ett nytt app-ID som du äger.

### <a name="after-the-app-is-migrated"></a>När appen har migrerats

Appägaren måste [lägga till din e-post till Azure-redigeringsresursen som medarbetare](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource).

Efter migreringsprocessen är alla appar som du äger tillgängliga på sidan **Mina appar** på LUIS-portalen.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Felsöka migreringsprocessen för LUIS-redigering

* LUIS-redigeringsnycklar visas bara i LUIS-portalen när migreringen är klar. Om du skapar redigeringsnycklarna, till exempel med LUIS CLI, måste användaren fortfarande slutföra migreringen i LUIS-portalen.
* Om en migrerad användare lägger till en icke-migrerad användare som deltagare på sin azure-resurs, har den icke-migrerade användaren ingen åtkomst till apparna om de inte migrerar.
* Om en icke-migrerad användare inte är ägare till några appar utan är en medarbetare till andra appar som ägs av andra och ägarna har genomgått migreringen måste den här användaren migrera för att få åtkomst till apparna.
* Om en icke-migrerad användare har lagt till en annan migrerad användare som medarbetare i sin app uppstår ett fel eftersom du inte kan lägga till en migrerad användare som medarbetare i en app. Den icke-migrerade användaren måste sedan gå igenom migreringen och skapa en azure-resurs och lägga till den migrerade användaren som deltagare till den resursen.

Du får ett felmeddelande under migreringsprocessen om:
* Din prenumeration ger dig inte rätt att skapa Cognitive Services-resurser
* Migreringen påverkar alla program som körs negativt negativt. När du migrerar tas alla medarbetare bort från dina appar och du tas bort som en medarbetare från andra appar. Den här processen innebär att nycklarna som du har tilldelat tas bort också. Migreringen blockeras om du har tilldelat nycklar i andra appar. Ta bort nyckeln som du har tilldelats på ett säkert sätt innan du migrerar. Om du vet att nyckeln som du har tilldelat inte används i körningen måste du ta bort den för att kunna gå vidare i migreringen.

Få tillgång till appens Azure-resurslista med följande URL-format:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>Nästa steg

* [Så här migrerar du appen till en redigeringsresurs](luis-migration-authoring-steps.md)
