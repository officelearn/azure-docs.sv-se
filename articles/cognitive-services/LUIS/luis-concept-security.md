---
title: Säkerhet när de samarbeta
titleSuffix: Language Understanding - Azure Cognitive Services
description: Redigera åtkomst är tillgänglig för ägare och medarbetare. För en privat app är slutpunkt för åtkomst tillgänglig för ägare och medarbetare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 5abc2026362e19b406b34a4321e766c3229cc0b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894192"
---
# <a name="authoring-and-endpoint-user-access"></a>Skriv- och slutpunkt användaråtkomst
Redigera åtkomst är tillgänglig för ägare och medarbetare. För en privat app är slutpunkt för åtkomst tillgänglig för ägare och medarbetare. För en app för offentlig är åtkomst för slutpunkten tillgänglig för alla som har sina egna LUIS-konto och har den offentliga app-ID. 

## <a name="access-to-authoring"></a>Åtkomst till redigering
Åtkomst till appen från den [LUIS](luis-reference-regions.md#luis-website) webbplats eller [redigera API: er](https://aka.ms/luis-authoring-apis) styrs av ägaren till appen. 

Ägare och alla medarbetare behörighet att redigera appen. 

|Redigera åtkomst innehåller|Anteckningar|
|--|--|
|Lägg till eller ta bort slutpunkten nycklar||
|Exportera version||
|Exportloggar slutpunkt||
|Importerar version||
|Gör appen offentlig|När en app är offentlig kan alla med en nyckel för redigering eller slutpunkt fråga appen.|
|Ändra modellen|
|Publicera|
|Granska endpoint yttranden för [aktiv inlärning](luis-how-to-review-endpoint-utterances.md)|
|Träna|

## <a name="access-to-endpoint"></a>Åtkomst till slutpunkten

Åtkomst till fråga slutpunkten styrs av en inställning på den **programinformation** sidan i den **hantera** avsnittet. 

![Set-app till att vara offentligt](./media/luis-concept-security/set-application-as-public.png)

|[Privat slutpunkt](#private-app-endpoint-security)|[Offentlig slutpunkt](#public-app-endpoint-access)|
|:--|:--|
|Tillgängliga för ägaren och medarbetare|Tillgänglig för ägare, medarbetare och vem som helst annan som känner app-ID|

### <a name="private-app-endpoint-security"></a>Privat app slutpunktssäkerhet

En privat app slutpunkten är endast tillgänglig för följande:

|Nyckel och användare|Förklaring|
|--|--|
|Ägarens redigering nyckel| Upp till 1000 endpoint träffar|
|Medarbetare authoring nycklar| Upp till 1000 endpoint träffar|
|Valfri tangent som tilldelats LUIS av en författare eller deltagare|Baserat på nivån för nyckelanvändning|

#### <a name="microsoft-user-accounts"></a>Microsoft-användarkonton

Författare och medarbetare kan tilldela nycklar till en privat LUIS-app. Det Microsoft-konto som skapas LUIS-nyckel i Azure-portalen måste vara antingen appägare eller deltagare i en app. Du kan inte tilldela en nyckel till en privat app från en annan Azure-konto.

Se [Azure Active Directory-klientanvändare](luis-how-to-collaborate.md#azure-active-directory-tenant-user) mer information om Active Directory-användarkonton. 

### <a name="public-app-endpoint-access"></a>Offentliga slutpunkten för appåtkomst

När en app har konfigurerats som offentliga, _alla_ giltig LUIS redigering nyckel eller LUIS slutpunktsnyckeln kan fråga din app, så länge nyckeln inte har använt hela slutpunkt kvoten.

En användare som inte är ägare eller deltagare i, kan endast komma åt en offentlig appen, om en annan app-ID. LUIS saknar en offentlig _marknaden_ eller annat sätt att söka efter en offentlig app.  

En offentlig app publiceras i alla regioner så att en användare med en region-baserade LUIS Resursnyckeln har åtkomst till appen i regionen som är associerad med resursnyckel.

## <a name="microsoft-user-accounts"></a>Microsoft-användarkonton

Författare och medarbetare kan lägga till nycklar LUIS på sidan Publicera. Det Microsoft-konto som skapas LUIS-nyckel i Azure-portalen måste vara antingen appägare eller deltagare i en app. 

Se [Azure Active Directory-klientanvändare](luis-how-to-collaborate.md#azure-active-directory-tenant-user) mer information om Active Directory-användarkonton. 

## <a name="securing-the-endpoint"></a>Skydda slutpunkten 

Du kan styra vilka som får se din LUIS slutpunktsnyckeln genom att anropa den i en server till server-miljö. Om du använder LUIS från en robot är anslutningen mellan bot och LUIS redan säker. Om du anropar slutpunkten LUIS direkt, bör du skapa ett API för serversidan (till exempel en Azure [funktionen](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (till exempel [AAD](https://azure.microsoft.com/services/active-directory/)). När anropas från serversidan API och autentisering och auktorisering har verifierats, skicka anropet till LUIS. När den här strategin inte hindra man-in-the-middle-attacker, den obfuscates slutpunkten från användarna, kan du spåra åtkomst, och kan du lägga till slutpunkten svar loggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Säkerhet
 
[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>Nästa steg

Se [metodtips](luis-concept-best-practices.md) att lära dig hur du använder avsikter och entiteter för bästa förutsägelser.
