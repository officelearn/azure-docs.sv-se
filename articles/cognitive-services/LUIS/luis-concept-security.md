---
title: Förstå åtkomst till THOMAS - program i Azure | Microsoft Docs
description: Lär dig att komma åt THOMAS redigering.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 44380e12e6d095e8d40675af0b6b2fddc5e4c4e9
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264275"
---
# <a name="authoring-and-endpoint-user-access"></a>Redigering och slutpunkten användaråtkomst
Redigera åtkomst är tillgänglig för ägare och medarbetare. För en privat app är endpoint åtkomst tillgängliga för ägare och medarbetare. För en offentlig app har slutpunkt du tillgång till alla som har sina egna THOMAS konton och offentliga app-ID. 

## <a name="access-to-authoring"></a>Åtkomst till redigering
Åtkomst till appen från den [THOMAS] [ LUIS] webbplats eller [redigering API: er](https://aka.ms/luis-authoring-apis) styrs av ägaren av appen. 

Ägare och alla medarbetare behörighet att redigera appen. 

|Redigera åtkomst innehåller|Anteckningar|
|--|--|
|Lägg till eller ta bort slutpunkten nycklar||
|Exportera version||
|Exportera endpoint loggar||
|Importerar version||
|Offentliggöra app|När en app är offentlig kan alla med en nyckel för redigering eller slutpunkt fråga appen.|
|Ändra modellen|
|Publicera|
|Granska endpoint utterances för [active learning](label-suggested-utterances.md)|
|Lär in|

## <a name="access-to-endpoint"></a>Åtkomst till slutpunkten
Åtkomsten till slutpunkten frågan THOMAS styrs av den **offentliga** inställningen av appen på den **inställningar** sidan. En privat app endpoint frågan är markerat för en auktoriserad nyckel med kvarvarande kvoten träffar. En offentlig app endpoint frågan har också ange en slutpunktsnyckel (från den som har att göra frågan) som också är markerat för återstående kvot träffar. 

Nyckeln endpoint skickas antingen i frågesträngen för GET-begäran eller rubriken för POST-begäran.

![Ange app offentlig](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Privata app slutpunktssäkerhet
En privat app slutpunkten är bara tillgängligt för följande:

|Nyckeln och användare|Förklaring|
|--|--|--|
|Ägarens redigering nyckel| Upp till 1000 endpoint träffar|
|Medarbetare redigering nycklar| Upp till 1000 endpoint träffar|
|Slutpunkten nycklar som lagts till från **[publicera](publishapp.md)** sidan|Ägare och medarbetare Lägg till nycklar för slutpunkten|

Andra redigering eller slutpunkt nycklar har **inga** åtkomst.

### <a name="public-app-endpoint-access"></a>Åtkomst till offentliga appen slutpunkt
Konfigurera appen som **offentliga** på den **inställningar** sida i appen. När en app har konfigurerats som public _alla_ giltig THOMAS redigering för eller THOMAS endpoint kan fråga din app, förutsatt att nyckeln inte har använt hela endpoint kvoten.

En användare som inte är ägare eller deltagare, kan endast komma åt offentliga appen, om en app-ID. THOMAS saknar en offentlig _marknaden_ eller andra sätt att söka efter en offentlig app.  

## <a name="microsoft-user-accounts"></a>Microsoft-användarkonton
Författare och medarbetare kan lägga till nycklar THOMAS på sidan Publicera. Det Microsoft-konto som skapar nyckeln THOMAS i Azure-portalen måste appen ägare eller deltagare i en app. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>Skydda slutpunkten 
Du kan styra vem som kan se din THOMAS endpoint nyckel genom att anropa i en server till server-miljö. Om du använder THOMAS från en bot är anslutningen mellan bot och THOMAS redan skyddad. Om du anropar THOMAS slutpunkten direkt, bör du skapa en API för serversidan (till exempel en Azure [funktionen](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (som [AAD](https://azure.microsoft.com/services/active-directory/)). Skicka anrop till THOMAS om serversidan API kallas och autentisering och auktorisering har verifierats. När den här strategin inte förhindra att man-in-the-middle-attacker, den obfuscates slutpunkten från användarna, kan du spåra åtkomst, och du kan lägga till slutpunkten svar loggning (exempelvis [Programinsikter](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Säkerhetsefterlevnaden
THOMAS slutförts ISO 27001: 2013 och ISO 27018:2014 audit med noll avvikelser (resultat) i rapporten. Dessutom hämta THOMAS också CSA STAR certifikatutfärdare med högsta möjliga guld tilldelning för förfall kapaciteten assessment. Azure är den enda större offentliga molntjänstleverantören att detta certifikat. Mer information hittar du i THOMAS ingår i den uppdaterade scope-instruktionen i Azures huvudsakliga [översikt över kompatibilitet](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) dokument som refereras i [Säkerhetscenter](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) ISO-sidor.  

## <a name="next-steps"></a>Nästa steg

Se [metodtips](luis-concept-best-practices.md) att lära dig hur du använder avsikter och entiteter för bästa förutsägelser.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website