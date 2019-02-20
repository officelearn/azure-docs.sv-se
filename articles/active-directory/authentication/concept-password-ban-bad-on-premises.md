---
title: Förhandsversionen protection Azure AD-lösenord
description: Förbjud svaga lösenord i den lokala Active Directory med hjälp av Azure AD lösenord protection preview
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415756"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Förhandsversion: Använda Azure AD-lösenordsskydd för Windows Server Active Directory

|     |
| --- |
| Azure AD-lösenordsskydd och listan över anpassade förbjudna lösenord är funktioner i offentlig förhandsversion av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Azure AD-lösenordsskydd är en ny funktion i offentlig förhandsversion som tillhandahålls av Azure Active Directory (Azure AD) för att förbättra lösenordsprinciper i en organisation. Lokal distribution av Azure AD-lösenordsskydd använder den globala och anpassade förbjudna lösenordslistor som lagras i Azure AD och utför samma kontroller lokala platser som molnbaserad Azure AD-ändringar.

## <a name="design-principles"></a>Designprinciper

Azure AD-lösenordsskydd för Active Directory har utformats med följande principer i åtanke:

* Domänkontrollanter är aldrig krävs för att kommunicera direkt med Internet
* Inga nya nätverksportarna öppnas på domänkontrollanter.
* Det krävs inga ändringar för Active Directory-schemat. Programvaran använder befintliga Active Directory-behållare och serviceConnectionPoint schemaobjekt.
* Det krävs ingen minsta Active Directory-domän eller skogens funktionella nivå (DFL\FFL).
* Programvaran inte skapar eller kräver några konton i Active Directory-domäner som den skyddar.
* Lösenord i klartext lämnar aldrig domänkontrollanten (oavsett om under verifieringen lösenordsåtgärder eller när som helst andra).
* Stegvis distribution stöds med kompromiss att lösenordsprinciper gäller endast där domain controller-agenten är installerad.
* Det rekommenderas att installera DC-agenten på alla domänkontrollanter för att säkerställa allt vanligare lösenord protection säkerhet.

## <a name="architectural-diagram"></a>Arkitekturdiagram

Det är viktigt att förstå de underliggande design och funktionella begrepp innan du distribuerar Azure AD-lösenord skydd i en lokal Active Directory-miljö. Följande diagram visar hur komponenterna i Azure AD-lösenordsskydd tillsammans:

![Hur Azure AD-lösenord protection komponenter fungerar tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

I ovanstående diagram visas de tre grundläggande programvarukomponenter som utgör Azure AD-lösenordsskydd:

* Azure AD-lösenord Protection Proxy-tjänsten körs på alla domänanslutna datorer i den aktuella Active Directory-skogen. Dess primära syfte är att vidarebefordra hämtningsbegäranden princip för lösenord från domänkontrollanter till Azure AD och returnerar svaret från Azure AD tillbaka till domänkontrollanten.
* DLL-filen för Azure AD-lösenord Protection DC agenten lösenord filter tar emot användarbegäranden lösenord verifiering från operativsystemet och vidarebefordrar dem till Azure AD-lösenord DC Protection Agent-tjänsten som körs lokalt på domänkontrollanten.
* Azure AD-lösenord DC Protection Agent-tjänsten tar emot lösenord verifiering förfrågningar från DLL-filen för DC-agenten lösenord filter, bearbetar dem med hjälp av den aktuella lösenordsprincipen (lokalt tillgängligt) och returnerar resultatet (pass\fail).

## <a name="theory-of-operations"></a>Typ av åtgärder

Så får de ovanstående diagram- och designegenskaperna hur Azure AD-lösenordsskydd faktiskt fungerar?

Varje Azure AD-lösenord Protection proxytjänsten annonserar själva för domänkontrollanter i skogen genom att skapa ett serviceConnectionPoint-objekt i Active Directory.

Varje Azure AD-lösenord DC Protection Agent-tjänsten skapar också ett serviceConnectionPoint-objekt i Active Directory. Men används främst för rapportering och diagnostik.

Azure AD-lösenord Protection DC-agenttjänsten ansvarar för att påbörja hämtningen av en ny princip från Azure AD. Det första steget är att hitta en Azure AD-lösenord Protection proxytjänst genom att fråga skogen för proxy serviceConnectionPoint objekt. När en tillgänglig proxytjänst har hittats, skickas en hämtningsbegäran för lösenord princip från DC-agenttjänsten proxy-tjänst, vilket i sin tur skickar den till Azure AD och returnerar svaret till DC-agenttjänsten. När du har fått en ny lösenordsprincip för från Azure AD lagrar DC-agenttjänsten principen till en mapp i roten på dess domän sysvol-resursen. DC-agenttjänsten övervakar även den här mappen om nyare principer replikera i från andra DC agent-tjänster i domänen.

Azure AD-lösenord Protection DC-agenttjänsten begär alltid en ny princip på tjänsten startades. När DC-agenttjänsten är igång genom att med jämna mellanrum (en gång i timmen) kontrollerar du åldern på den aktuella lokalt tillgängliga principen; Om den aktuella principen är äldre än en timme DC agent-tjänsten kommer att begära en ny princip från Azure AD som beskrivs ovan, kommer annars DC-agenten fortsätta att använda den aktuella principen.

Azure AD-lösenord DC skyddsagenttjänsten kommunicerar med Azure AD-lösenord Protection Proxy-tjänsten med hjälp av RPC (Remote Procedure Call) via TCP. Proxy-tjänsten lyssnar efter dessa anrop på antingen en dynamisk eller statisk RPC-port (som konfigurerats).

Azure AD-lösenord Protection DC-agenten lyssnar aldrig på en nätverk finns port och proxytjänsten försöker aldrig anropa DC-agenttjänsten.

Azure AD-lösenord Protection Proxy-tjänsten är tillståndslösa; den cachelagrar aldrig principer eller några andra tillstånd ned från Azure.

Azure AD-lösenord Protection DC-agenttjänsten utvärderas bara en användares lösenord med hjälp av den senaste tillgängliga lokalt lösenordsprincipen. Om det finns ingen princip för lösenord på den lokala domänkontrollanten lösenordet godkänns automatiskt och ett händelseloggmeddelande loggas för att varna administratören.

Azure AD-lösenordsskydd är inte en i realtid principmodulen för programmet. Det kan finnas en fördröjning under tiden mellan en lösenordsändring princip konfiguration görs i Azure AD och hur lång tid det når och tillämpas på alla domänkontrollanter.

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Forest\tenant bindningen för Azure AD-lösenordsskydd

Distribution av Azure AD-lösenord skydd i en Active Directory-skog kräver registrering av Active Directory-skogen och eventuella distribuerade Azure AD-lösenord Protection proxytjänster med Azure AD. Båda dessa registreringar (skog och proxyservrar) som är associerade med en viss Azure AD-klient som identifieras implicit via de autentiseringsuppgifter som används under registreringen. Varje gång en lösenordsprincip för Azure AD-lösenordsskydd hämtas det alltid är specifika för den här klienten (t.ex, att principen alltid är en kombination av Microsofts globala förbjudna lösenord per klient anpassade förbjudna lösenord och listan med). Det går inte för att konfigurera olika domäner eller proxyservrar i en skog vara bundna till annan Azure AD klienter.

## <a name="license-requirements"></a>Licenskrav

Fördelarna med listan över globala förbjudna lösenord gäller för alla användare av Azure Active Directory (AD Azure).

Lista med anpassade förbjudna lösenord kräver Azure AD Basic-licenser.

Azure AD-lösenordsskydd för Windows Server Active Directory kräver Azure AD Premium-licenser.

Ytterligare information om licenser går inklusive kostnader, kan hittas på den [priser platsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Ladda ned

Två krävs agenten installationsprogram för Azure AD-lösenord skydd som kan laddas ned från den [Microsoft download center](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
