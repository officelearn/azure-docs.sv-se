---
title: Förstå åtkomst till LUIS-program – Azure | Microsoft Docs
description: Lär dig hur du kommer åt LUIS redigering.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: fddffbcabba753e9ef214f924d5ff2cee38427a5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301701"
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
|Granska endpoint yttranden för [aktiv inlärning](luis-how-to-review-endoint-utt.md)|
|Träna|

## <a name="access-to-endpoint"></a>Åtkomst till slutpunkten
Åtkomst till slutpunkten så att frågan LUIS styrs av den **offentliga** inställningen av appen på den **inställningar** sidan. En privat app endpoint fråga är markerat för en auktoriserad nyckel med återstående kvot träffar. En offentlig app endpoint frågan måste du också ange en slutpunktsnyckeln (från den är att göra frågan) som också är markerat för återstående kvot träffar. 

Slutpunktsnyckeln skickas antingen i frågesträngen för GET-begäran eller be rubriken för INLÄGGET.

![Set-app till att vara offentligt](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Privat app slutpunktssäkerhet
En privat app slutpunkten är endast tillgänglig för följande:

|Nyckel och användare|Förklaring|
|--|--|--|
|Ägarens redigering nyckel| Upp till 1000 endpoint träffar|
|Medarbetare authoring nycklar| Upp till 1000 endpoint träffar|
|Slutpunkten nycklar har lagts till från **[publicera](luis-how-to-publish-app.md)** sidan|Ägaren och medarbetare kan lägga till slutpunkten nycklar|

Andra redigering eller slutpunkt nycklar har **inga** åtkomst.

### <a name="public-app-endpoint-access"></a>Offentliga slutpunkten för appåtkomst
Konfigurera appen som **offentliga** på den **inställningar** sida i appen. När en app har konfigurerats som offentliga, _alla_ giltig LUIS redigering nyckel eller LUIS slutpunktsnyckeln kan fråga din app, så länge nyckeln inte har använt hela slutpunkt kvoten.

En användare som inte är ägare eller deltagare i, kan endast komma åt en offentlig appen, om en annan app-ID. LUIS saknar en offentlig _marknaden_ eller annat sätt att söka efter en offentlig app.  

## <a name="microsoft-user-accounts"></a>Microsoft-användarkonton
Författare och medarbetare kan lägga till nycklar LUIS på sidan Publicera. Det Microsoft-konto som skapas LUIS-nyckel i Azure-portalen måste vara antingen appägare eller deltagare i en app. 

Se [Azure Active Directory-klientanvändare](luis-how-to-collaborate.md#azure-active-directory-tenant-user) mer information om Active Directory-användarkonton. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>Skydda slutpunkten 
Du kan styra vilka som får se din LUIS slutpunktsnyckeln genom att anropa den i en server till server-miljö. Om du använder LUIS från en robot är anslutningen mellan bot och LUIS redan säker. Om du anropar slutpunkten LUIS direkt, bör du skapa ett API för serversidan (till exempel en Azure [funktionen](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (till exempel [AAD](https://azure.microsoft.com/services/active-directory/)). När anropas från serversidan API och autentisering och auktorisering har verifierats, skicka anropet till LUIS. När den här strategin inte hindra man-in-the-middle-attacker, den obfuscates slutpunkten från användarna, kan du spåra åtkomst, och kan du lägga till slutpunkten svar loggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Säkerhet
LUIS slutförts ISO 27001: 2013 och ISO-granskningen 27018:2014 med noll avvikelser (resultat) i granskningsrapporten. Dessutom hämta LUIS också CSA STAR-certifiering med högsta möjliga guld utmärkelsen för mognad kapaciteten för utvärderingen. Azure är den enda stora offentliga molntjänstleverantören att vinna denna certifiering. Mer information hittar du LUIS ingår i den uppdaterade scope-instruktionen i Azures huvudsakliga [efterlevnad översikt](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) dokumentet som refereras i [Säkerhetscenter](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) ISO-sidor.  

## <a name="next-steps"></a>Nästa steg

Se [metodtips](luis-concept-best-practices.md) att lära dig hur du använder avsikter och entiteter för bästa förutsägelser.
