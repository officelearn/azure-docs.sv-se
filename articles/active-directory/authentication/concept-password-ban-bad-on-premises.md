---
title: Lösenordsskydd för Azure AD - Azure Active Directory
description: Spärra svaga lösenord i lokal Active Directory med hjälp av Azure AD-lösenordsskydd
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848654"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Kräv Azure AD-lösenordsskydd för Windows Server Active Directory

Azure AD lösenordsskydd är en funktion som förbättrar lösenordsprinciper i en organisation. Lokal distribution av lösenordsskydd använder både globala och anpassade listor med förbjudna lösenord som lagras i Azure AD. Det gör samma kontroller lokalt som Azure AD gör för molnbaserade ändringar. Dessa kontroller utförs under lösenordsändringar och scenarier för återställning av lösenord.

## <a name="design-principles"></a>Designprinciper

Azure AD lösenordsskydd är utformat med dessa principer i åtanke:

* Domänkontrollanter behöver aldrig kommunicera direkt med internet.
* Inga nya nätverksportar öppnas på domänkontrollanter.
* Inga Active Directory-schemaändringar krävs. Programmet använder befintliga Active **Directory-behållare och** **serviceConnectionPoint-schemaobjekt.**
* Ingen lägsta Active Directory-domän eller skogsfunktionsnivå (DFL/FFL) krävs.
* Programvaran skapar eller kräver inte konton i Active Directory-domäner som den skyddar.
* Användarens lösenord för klartext lämnar aldrig domänkontrollanten, antingen under lösenordsverifieringsåtgärder eller vid någon annan tidpunkt.
* Programvaran är inte beroende av andra Azure AD-funktioner. Till exempel Azure AD lösenord hash sync är inte relaterad och krävs inte för att Azure AD lösenordsskydd ska fungera.
* Inkrementell distribution stöds, men lösenordsprincipen tillämpas endast där DOMÄNKONTROLLANTAGENTEN (DC Agent) är installerad. Se nästa avsnitt för mer information.

## <a name="incremental-deployment"></a>Inkrementell distribution

Azure AD-lösenordsskydd stöder inkrementell distribution över domänkontrollanter i en Active Directory-domän, men det är viktigt att förstå vad detta egentligen innebär och vad kompromisserna är.

Azure AD-lösenordsskydd dc-agentprogramvara kan bara validera lösenord när det installeras på en domänkontrollant och endast för lösenordsändringar som skickas till domänkontrollanten. Det går inte att styra vilka domänkontrollanter som väljs av Windows-klientdatorer för bearbetning av ändringar av användarlösenord. För att garantera konsekvent beteende och universell lösenordsskydd säkerhet verkställighet, DC agent programvara måste installeras på alla domänkontrollanter i en domän.

Många organisationer kommer att vilja göra noggrann testning av Azure AD lösenordsskydd på en delmängd av sina domänkontrollanter innan de gör en fullständig distribution. Azure AD lösenordsskydd stöder partiell distribution, dvs DC agent programvara på en viss DOMÄNKONTROLLANT kommer aktivt validera lösenord även när andra DOMÄNEN i domänen inte har DC agent programvara installerad. Partiella distributioner av den här typen är INTE säkra och rekommenderas INTE annat än för testning.

## <a name="architectural-diagram"></a>Arkitektoniskt diagram

Det är viktigt att förstå de underliggande design- och funktionskoncepten innan du distribuerar Azure AD-lösenordsskydd i en lokal Active Directory-miljö. Följande diagram visar hur komponenterna i lösenordsskydd fungerar tillsammans:

![Så här fungerar Azure AD-lösenordsskyddskomponenter tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD Password Protection Proxy-tjänsten körs på alla domänanslutna datorer i den aktuella Active Directory-skogen. Dess främsta syfte är att vidarebefordra begäranden om hämtning av lösenordsprinciper från domänkontrollanter till Azure AD. Sedan returneras svaren från Azure AD till domänkontrollanten.
* DLL-filen för lösenordsfilter för DC-agenten tar emot begäranden om lösenordsverifiering från operativsystemet. Den vidarebefordrar dem till dc-agenttjänsten som körs lokalt på domänkontrollanten.
* DC Agent-tjänsten för lösenordsskydd tar emot begäranden om lösenordsverifiering från DLL-agentens lösenordsfilter. Den bearbetar dem med hjälp av den aktuella (lokalt tillgängliga) lösenordsprincipen och returnerar resultatet: *godkänd* eller *misslyckad*.

## <a name="how-password-protection-works"></a>Så här fungerar lösenordsskydd

Varje Azure AD Password Protection Proxy-tjänstinstans annonserar sig till domänkontrollanterna i skogen genom att skapa ett **serviceConnectionPoint-objekt** i Active Directory.

Varje DC-agenttjänst för lösenordsskydd skapar också ett **serviceConnectionPoint-objekt** i Active Directory. Det här objektet används främst för rapportering och diagnostik.

DC-agenttjänsten ansvarar för att initiera hämtningen av en ny lösenordsprincip från Azure AD. Det första steget är att hitta en Azure AD Password Protection Proxy-tjänst genom att fråga skogen efter **proxytjänstenConnectionPoint-objekt.** När en tillgänglig proxytjänst hittas skickar DC-agenten en begäran om hämtning av lösenordsprincip till proxytjänsten. Proxytjänsten skickar i sin tur begäran till Azure AD. Proxytjänsten returnerar sedan svaret till DC Agent-tjänsten.

När DC Agent-tjänsten har fått en ny lösenordsprincip från Azure AD lagrar tjänsten principen i en dedikerad mapp roten till *domänen sysvol-mappresursen.* Dc Agent-tjänsten övervakar även den här mappen om nyare principer replikeras från andra DC Agent-tjänster i domänen.

DC Agent-tjänsten begär alltid en ny princip vid start av tjänsten. När DC-agenttjänsten har startats kontrollerar den åldern på den aktuella lokalt tillgängliga principen varje timme. Om principen är äldre än en timme begär DC-agenten en ny princip från Azure AD via proxytjänsten, som beskrivits tidigare. Om den aktuella principen inte är äldre än en timme fortsätter DC-agenten att använda den principen.

När en Azure AD lösenordsskydd lösenordsprincip hämtas, är den principen specifik för en klient. Med andra ord är lösenordsprinciper alltid en kombination av Microsofts globala lista över förbjudna lösenord och listan över anpassade förbjudna lösenord per klient.

DC-agenten kommunicerar med proxytjänsten via RPC via TCP. Proxytjänsten lyssnar efter dessa anrop på en dynamisk eller statisk RPC-port, beroende på konfigurationen.

DC-agenten lyssnar aldrig på en nätverkstillgängande port.

Proxytjänsten anropar aldrig dc-agenttjänsten.

Proxytjänsten är tillståndslös. Den cachelagrar aldrig principer eller någon annan stat som hämtats från Azure.

Dc Agent-tjänsten använder alltid den senaste lokalt tillgängliga lösenordsprincipen för att utvärdera en användares lösenord. Om det inte finns någon lösenordsprincip på den lokala domänkontrollanten accepteras lösenordet automatiskt. När det händer loggas ett händelsemeddelande för att varna administratören.

Azure AD lösenordsskydd är inte en realtidsprincipprogrammotor. Det kan finnas en fördröjning mellan när en lösenordsprincipkonfigurationsändring görs i Azure AD och när ändringen når och tillämpas på alla domänkontrollanter.

Azure AD lösenordsskydd fungerar som ett komplement till de befintliga Active Directory lösenordsprinciper, inte en ersättning. Detta inkluderar alla andra lösenordsdyller från tredje part som kan installeras. Active Directory kräver alltid att alla lösenordsverifieringskomponenter godkänner innan du accepterar ett lösenord.

## <a name="foresttenant-binding-for-password-protection"></a>Skogs-/klientbindning för lösenordsskydd

Distribution av Azure AD lösenordsskydd i en Active Directory-skog kräver registrering av den skogen med Azure AD. Varje proxytjänst som distribueras måste också registreras med Azure AD. Dessa skogs- och proxyregistreringar är associerade med en specifik Azure AD-klientorganisation, som identifieras implicit av de autentiseringsuppgifter som används vid registreringen.

Active Directory-skogen och alla distribuerade proxytjänster i en skog måste registreras hos samma klient. Det stöds inte att ha en Active Directory-skog eller proxytjänster i den skogen som registreras på olika Azure AD-klienter. Symptom på en sådan felaktig konfigurerad distribution inkluderar oförmåga att hämta lösenordsprinciper.

## <a name="download"></a>Ladda ned

De två nödvändiga agentinstallationsinstituten för azure AD-lösenordsskydd är tillgängliga från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Nästa steg
[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
