---
title: Lösen ords skydd i Azure AD – Azure Active Directory
description: Förbjuda svaga lösen ord i lokala Active Directory med hjälp av lösen ords skydd i Azure AD
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74848654"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Kräv Azure AD-lösenordsskydd för Windows Server Active Directory

Lösen ords skydd i Azure AD är en funktion som förbättrar lösen ords principerna i en organisation. Lokal distribution av lösen ords skydd använder både globala och anpassade listor över blockerade lösen ord som lagras i Azure AD. Det sker samma kontroller lokalt som i Azure AD för molnbaserade ändringar. Dessa kontroller utförs under scenarier med lösen ords ändringar och lösen ords återställning.

## <a name="design-principles"></a>Designprinciper

Lösen ords skyddet i Azure AD har utformats med dessa principer i åtanke:

* Domänkontrollanter behöver aldrig kommunicera direkt med Internet.
* Inga nya nätverks portar öppnas på domän kontrol Lanterna.
* Inga Active Directory schema ändringar krävs. Program varan använder den befintliga Active Directory **containern** och schema objekt för **serviceConnectionPoint** .
* Ingen minsta Active Directory domän-eller skogs funktions nivå (DFL/FFL) krävs.
* Program varan skapar eller kräver inte konton i Active Directory domäner som den skyddar.
* Användarens lösen ord för klartext lämnar aldrig domänkontrollanten, antingen under åtgärder för lösen ords validering eller vid någon annan tidpunkt.
* Program varan är inte beroende av andra funktioner i Azure AD. till exempel är inte är relaterat till Azure AD Password hash Sync och krävs inte för att lösen ords skydd i Azure AD ska fungera.
* Stegvis distribution stöds, men lösen ords principen tillämpas bara om domänkontrollantens agent (DC-agent) är installerad. Mer information finns i nästa avsnitt.

## <a name="incremental-deployment"></a>Stegvis distribution

Azure AD Password Protection stöder stegvis distribution över domänkontrollanter i en Active Directory domän, men det är viktigt att förstå vad det innebär och vad kompromisserna är.

Azure AD Password Protection-agentprogram varan kan endast verifiera lösen ord när det är installerat på en domänkontrollant och endast för lösen ords ändringar som skickas till den domänkontrollanten. Det går inte att kontrol lera vilka domänkontrollanter som väljs av Windows-klientdatorer för bearbetning av ändringar av användar lösen ord. För att garantera konsekvent användning av säkerhet och för universellt lösen ords skydd måste DC-agentens program vara installeras på alla domänkontrollanter i en domän.

Många organisationer vill göra noggrann testning av lösen ords skydd i Azure AD på en delmängd av deras domänkontrollanter innan en fullständig distribution görs. Lösen ords skydd i Azure AD stöder delvis distribution, IE-agentens program vara på en viss DOMÄNKONTROLLANT kommer aktivt att verifiera lösen ord även om andra domänkontrollanter i domänen inte har installerat DC-agenten. Partiella distributioner av den här typen är inte säkra och rekommenderas inte, förutom för test ändamål.

## <a name="architectural-diagram"></a>Arkitektur diagram

Det är viktigt att förstå de underliggande design-och funktions begreppen innan du distribuerar Azure AD Password Protection i en lokal Active Directory miljö. Följande diagram visar hur komponenterna i lösen ords skydd fungerar tillsammans:

![Så här fungerar Azure AD Password Protection-komponenter tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Proxy tjänsten Azure AD Password Protection körs på alla domänanslutna datorer i den aktuella Active Directory skogen. Det primära syftet är att vidarebefordra begär Anden om hämtning av lösen ords principer från domänkontrollanter till Azure AD. Den returnerar sedan svaren från Azure AD till domänkontrollanten.
* Lösen ords filter-DLL: en för DC-agenten tar emot förfrågningar om användar lösen ord från operativ systemet. Den vidarebefordrar dem till DC-agenttjänsten som körs lokalt på domänkontrollanten.
* DC-agenttjänsten för lösen ords skydd tar emot lösen ords verifierings begär Anden från DLL-filen med lösen ords filter för DC-agenten. Den bearbetar dem med hjälp av den aktuella (lokalt tillgängliga) lösen ords principen och returnerar resultatet: *pass* eller *misslyckande*.

## <a name="how-password-protection-works"></a>Så här fungerar lösen ords skydd

Varje Azure AD Password Protection proxy-tjänstinstans meddelar sig själv om domän kontrol Lanterna i skogen genom att skapa ett **serviceConnectionPoint** -objekt i Active Directory.

Varje DC-agenttjänsten för lösen ords skydd skapar också ett **serviceConnectionPoint** -objekt i Active Directory. Det här objektet används främst för rapportering och diagnostik.

DC-agenttjänsten ansvarar för att initiera hämtningen av en ny lösen ords princip från Azure AD. Det första steget är att leta upp en Azure AD-proxy för lösen ords skydd genom att fråga skogen efter objekt i **serviceConnectionPoint** för proxy. När en tillgänglig proxyvärd hittas skickar DC-agenten en begäran om att hämta en lösen ords princip till proxyservern. Proxy-tjänsten i sin tur skickar begäran till Azure AD. Proxy-tjänsten returnerar sedan svaret till DC-agenttjänsten.

När DC-agenttjänsten tar emot en ny lösen ords princip från Azure AD, lagrar tjänsten principen i en särskild mapp i roten för *SYSVOL* -mappens resurs resurs. DC-agenttjänsten övervakar även den här mappen om nyare principer replikeras från andra DC agent-tjänster i domänen.

DC-agenttjänsten begär alltid en ny princip när tjänsten startas. När DC-agenttjänsten har startats kontrollerar den åldern för den aktuella lokalt tillgängliga principen varje timme. Om principen är äldre än en timme begär DC-agenten en ny princip från Azure AD via proxyservern, enligt beskrivningen ovan. Om den aktuella principen inte är äldre än en timme fortsätter DC-agenten att använda den principen.

När en lösen ords princip för lösen ords skydd i Azure AD laddas ned, är den principen unik för en klient. Lösen ords principer är med andra ord alltid en kombination av den globala listan över blockerade lösen ord och per klient, anpassad lista över blockerade lösen ord.

DC-agenten kommunicerar med proxyservern via RPC via TCP. Proxy-tjänsten lyssnar efter dessa anrop på en dynamisk eller statisk RPC-port, beroende på konfigurationen.

DC-agenten lyssnar aldrig på en nätverks tillgänglig port.

Proxy-tjänsten anropar aldrig DC-agenttjänsten.

Proxy-tjänsten är tillstånds lös. De cachelagrar aldrig principer eller andra tillstånd som hämtats från Azure.

DC-agenttjänsten använder alltid den senaste lokalt tillgängliga lösen ords principen för att utvärdera en användares lösen ord. Om ingen lösen ords princip är tillgänglig på den lokala DOMÄNKONTROLLANTen godkänns lösen ordet automatiskt. När detta inträffar loggas ett händelse meddelande som varnar administratören.

Lösen ords skydd i Azure AD är inte en tillämpnings motor för real tids principer. Det kan finnas en fördröjning mellan när en konfigurations ändring av lösen ords principen görs i Azure AD och när ändringen når och tillämpas på alla domänkontrollanter.

Azure AD Password Protection fungerar som ett tillägg till befintliga Active Directory lösen ords principer, inte en ersättning. Detta inkluderar eventuella andra tredjeparts lösen ords filter från tredje part som kan installeras. Active Directory kräver alltid att alla lösen ords verifierings komponenter samtycker till innan ett lösen ord accepteras.

## <a name="foresttenant-binding-for-password-protection"></a>Skog/klient bindning för lösen ords skydd

Distribution av lösen ords skydd i Azure AD i en Active Directory skog kräver registrering av den skogen med Azure AD. Varje proxy-tjänst som distribueras måste också registreras med Azure AD. Dessa skogar och proxy-registreringar är kopplade till en särskild Azure AD-klient, som identifieras implicit av de autentiseringsuppgifter som används under registreringen.

Active Directory skogen och alla distribuerade Proxy tjänster i en skog måste registreras med samma klient organisation. Det finns inte stöd för att en Active Directory skog eller några Proxy-tjänster i skogen registreras på olika Azure AD-klienter. Symptom på en sådan mis-konfigurerad distribution är möjligheten att ladda ned lösen ords principer.

## <a name="download"></a>Ladda ned

De två agent installations program som krävs för Azure AD Password Protection är tillgängliga från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Nästa steg
[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
