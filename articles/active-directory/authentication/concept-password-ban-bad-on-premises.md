---
title: Azure AD-lösenordsskydd – Azure Active Directory
description: Förbjud svaga lösenord i den lokala Active Directory med hjälp av Azure AD-lösenordsskydd
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
ms.openlocfilehash: 6e6623e18fa319066f121dced551dcada133ebd5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58479537"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Använda Azure AD-lösenordsskydd för Windows Server Active Directory

Azure AD-lösenordsskydd är en funktion som förbättrar lösenordsprinciper i en organisation. Lösenordsskydd i den lokala distributionen använder båda globala och anpassade förbjudna lösenord listorna som lagras i Azure AD. Detta sker i samma kontroller på plats som Azure AD för molnbaserad ändringar.

## <a name="design-principles"></a>Designprinciper

Azure AD-lösenordsskydd har utformats med dessa principer i åtanke:

* Domänkontrollanter får aldrig direkt kommunicera med internet.
* Inga nya nätverksportarna öppnas på domänkontrollanter.
* Det krävs inga ändringar för Active Directory-schemat. Programvaran använder befintliga Active Directory **behållare** och **serviceConnectionPoint** schemaobjekt.
* Inga minsta Active Directory domänens eller skogens funktionsnivå (DFL/FFL) krävs.
* Programvaran inte skapa eller kräver konton i Active Directory-domäner som den skyddar.
* Lösenord i klartext lämna inte domänkontrollanten under verifieringen lösenordsåtgärder eller vid en annan tidpunkt.
* Stegvis distribution stöds, men lösenordsprincipen som gäller endast där Domain Controller agenten (DC-Agent) är installerad. Se nästa avsnitt för mer information.

## <a name="incremental-deployment"></a>Stegvis distribution

Azure AD-lösenordsskydd har stöd för stegvis distribution mellan domänkontrollanter i en Active Directory-domän, men det är viktigt att förstå vad det verkligen innebär och vad kompromisser är.

Azure AD lösenord DC skyddsagentprogramvaran kan bara verifiera lösenord när den är installerad på en domänkontrollant, och endast för lösenordsändringar som skickas till domänkontrollanten. Det går inte att kontrollera vilka domänkontrollanter väljs av Windows-klientdatorer för att bearbeta ändringar av lösenord för användaren. För att garantera konsekvent beteende och tvingande för universal lösenord protection säkerhet, måste DC-agentprogramvaran installeras på alla domänkontrollanter i en domän.

Många organisationer vill göra noggrann testning av Azure AD-lösenordsskydd för en delmängd av sina domänkontrollanter innan du gör en fullständig distribution. Azure AD-lösenordsskydd stöder partiella distribution, ie DC klientprogrammet på en viss Domänkontrollant verifierar aktivt lösenord även när andra domänkontrollanter i domänen inte har DC-Agentprogrammet som är installerad. Partiell distributioner av den här typen är inte säker och rekommenderas inte än i testsyfte.

## <a name="architectural-diagram"></a>Arkitekturdiagram

Det är viktigt att förstå de underliggande design och funktionsbegrepp innan du distribuerar Azure AD-lösenordsskydd i en lokal Active Directory-miljö. Följande diagram visar hur komponenterna i lösenordsskydd tillsammans:

![Hur Azure AD-lösenord protection komponenter fungerar tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD-lösenord Protection Proxy-tjänsten körs på alla domänanslutna datorer i den aktuella Active Directory-skogen. Dess primära syfte är att vidarebefordra hämtningsbegäranden princip för lösenord från domänkontrollanter till Azure AD. Den returnerar sedan svar från Azure AD till domänkontrollanten.
* Lösenordsfiltrerings-DLL: av DC-agenten tar emot begäranden mellan användare och verifiering av lösenord från operativsystemet. Den vidarebefordrar dem till tjänsten DC-Agent som körs lokalt på domänkontrollanten.
* DC-agenttjänsten av lösenordsskydd tar emot lösenordsverifieringen förfrågningar från lösenordsfiltrerings-DLL: av DC-agenten. Den behandlar dem med hjälp av den aktuella lösenordsprincipen (lokalt tillgängligt) och returnerar resultatet: *skicka* eller *misslyckas*.

## <a name="how-password-protection-works"></a>Lösenordsskydd

Varje Azure AD-lösenord Protection Proxy-tjänstinstans annonserar själva för domänkontrollanter i skogen genom att skapa en **serviceConnectionPoint** objektet i Active Directory.

Varje DC-agenttjänsten lösenordsskydd skapar även en **serviceConnectionPoint** objektet i Active Directory. Det här objektet används främst för rapportering och diagnostik.

DC-agenttjänsten ansvarar för att påbörja hämtningen av en ny princip från Azure AD. Det första steget är att hitta en Azure AD-lösenord Protection proxytjänst genom att fråga skogen för proxy **serviceConnectionPoint** objekt. När en tillgänglig proxytjänst har hittats skickar DC-agenten en hämtningsbegäran för lösenord principen till proxytjänsten. Proxy-tjänsten skickar i sin tur en begäran till Azure AD. Proxy-tjänsten returnerar svaret till DC-agenttjänsten.

När tjänsten DC-Agent har hämtat en ny lösenordsprincip för från Azure AD kommer tjänsten lagrar principen till en mapp i roten för sin domän *sysvol* mappen resursen. DC-Agent-tjänsten övervakar även den här mappen om nyare principer replikera i från andra DC Agent-tjänster i domänen.

DC-agenttjänsten begär alltid en ny princip när tjänsten startar. När tjänsten DC-Agent har startat kontrollerar ålder på den aktuella lokalt tillgängliga principen per timme. Om principen är äldre än en timme, begär DC-agenten en ny princip från Azure AD som tidigare beskrivits. Om den aktuella principen inte är äldre än en timme, fortsätter DC-agenten att använda principen.

När en lösenordsprincip för Azure AD lösenord protection laddas ned är principen specifik för en klient. Med andra ord är lösenordsprinciper alltid en kombination av listan Microsoft global förbjudna lösenord och listan per klient anpassade förbjudna lösenord.

DC-agenten kommunicerar med proxytjänsten via RPC via TCP. Proxy-tjänsten lyssnar efter dessa anrop på en dynamisk eller statisk RPC-port, beroende på konfigurationen.

DC-agenten lyssnar aldrig på ett nätverk tillgänglig port.

Proxytjänsten anropar aldrig DC Agent-tjänsten.

Proxytjänsten är tillståndslösa. Den cachelagrar aldrig principer eller några andra tillstånd ned från Azure.

DC-Agent-tjänsten använder alltid den senaste tillgängliga lokalt lösenordsprincipen för att utvärdera en användares lösenord. Lösenordet godkänns automatiskt om det finns ingen princip för lösenord på den lokala domänkontrollanten. När det sker så loggas ett händelsemeddelande för att varna administratören.

Azure AD-lösenordsskydd är inte en i realtid principmodulen för programmet. Det kan finnas en fördröjning mellan när en konfigurationsändring för lösenord princip skapas i Azure AD och när som ändrar når och tillämpas på alla domänkontrollanter.

## <a name="foresttenant-binding-for-password-protection"></a>Skog/klient bindning lösenordsskydd

Distribution av Azure AD-lösenordsskydd i en Active Directory-skog kräver registrering av skogen med Azure AD. Varje proxytjänst som har distribuerats måste också vara registrerad med Azure AD. Dessa skogar och proxy-registreringar som är associerade med en viss Azure AD-klient som identifieras implicit med de autentiseringsuppgifter som används under registreringen.

Active Directory-skog och alla distribuerade proxytjänster inom en skog måste vara registrerad med samma klient. Det går inte för att ha en Active Directory-skog eller någon proxy-tjänsterna i den skog som registreras till annan Azure AD-innehavare. Symtom på en felkonfigurerad distribution är det inte går att ladda ned lösenordsprinciper.

## <a name="license-requirements"></a>Licenskrav

Fördelarna med listan över globala förbjudna lösenord gäller för alla användare av Azure AD.

Listan över anpassade förbjudna lösenord kräver Azure AD Basic-licenser.

Azure AD-lösenordsskydd för Windows Server Active Directory kräver Azure AD Premium-licenser.

Mer licensieringsinformation finns [priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Ladda ned

Det finns två nödvändiga agent-installationsprogram för Azure AD-lösenordsskydd från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Nästa steg
[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
