---
title: Förhandsversionen protection Azure AD-lösenord
description: Förbjud svaga lösenord i den lokala Active Directory med hjälp av Azure AD lösenord protection preview
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: ff349c480ca14b4242fb7597751b4eb6acb0ee78
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056039"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Förhandsversion: Använda Azure AD-lösenordsskydd för Windows Server Active Directory

|     |
| --- |
| Azure AD-lösenordsskydd och listan över anpassade förbjudna lösenord är funktioner i offentlig förhandsversion av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Azure AD-lösenordsskydd är en ny funktion i offentlig förhandsversion som tillhandahålls av Azure Active Directory (Azure AD) för att förbättra lösenordsprinciper i en organisation. Lokal distribution av Azure AD-lösenordsskydd använder den globala och anpassade förbjudna lösenordslistor som lagras i Azure AD och utför samma kontroller lokala platser som molnbaserad Azure AD-ändringar.

Det finns tre programvarukomponenter som utgör Azure AD-lösenordsskydd:

* Azure AD lösenord protection proxy-tjänsten körs på alla domänanslutna datorer i den aktuella Active Directory-skogen. Den vidarebefordrar begäranden från domänkontrollanter till Azure AD och returnerar svaret från Azure AD tillbaka till domänkontrollanten.
* Azure AD lösenord protection DC agent-tjänsten tar emot lösenord verifiering förfrågningar från DLL-filen för DC-agenten lösenord filter, bearbetar dem med hjälp av den aktuella lokalt tillgängliga lösenordsprincipen och returnerar resultatet (pass\fail). Den här tjänsten ansvarar för att med jämna mellanrum (en gång per timme) anropa Azure AD lösenord protection proxy-tjänsten för att hämta nya versioner av lösenordsprincipen. Kommunikation för anrop till och från Azure AD lösenord protection proxy-tjänsten hanteras via RPC (Remote Procedure Call) via TCP. Vid hämtning lagras nya principer i en sysvol-mappen där de kan replikeras till andra domänkontrollanter. DC-agenttjänsten övervakar även sysvol-mappen för ändringar om andra domänkontrollanter har skrivit det nya lösenordsprinciper, om en lämpligt senaste princip är redan tillgänglig kontroll av Azure AD lösenord protection proxy-tjänsten kommer att hoppas över.
* DC-agenten lösenord filter-DLL: en tar emot förfrågningar för verifiering av lösenord från operativsystemet och vidarebefordrar dem till Azure AD lösenord protection DC agent-tjänsten körs lokalt på domänkontrollanten.

![Hur Azure AD-lösenord protection komponenter fungerar tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

## <a name="requirements"></a>Krav

* Alla datorer där Azure AD lösenord protection komponenter är installerade, inklusive domänkontrollanter måste köra Windows Server 2012 eller senare.
* Alla datorer där Azure AD lösenord protection komponenter är installerade, inklusive domänkontrollanter måste ha den universella C som är installerad. Detta åstadkoms helst genom att helt åtgärda datorn via Windows Update. I annat fall ett lämpligt operativsystemspecifika uppdateringspaketet vara installerat – Se [uppdatera för Universal C-körning i Windows](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* Nätverksanslutning måste det finnas mellan minst en domänkontrollant i varje domän och minst en server som är värd för Azure AD lösenord protection proxy-tjänsten.
* Alla Active Directory-domän som kör domänkontrollanten agentprogramvaran för tjänsten måste använda DFSR för sysvol-replikering.
* Ett globalt administratörskonto för att registrera Azure AD lösenord protection proxy-tjänsten med Azure AD.
* Ett konto med administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen.

### <a name="license-requirements"></a>Licenskrav

Fördelarna med listan över globala förbjudna lösenord gäller för alla användare av Azure Active Directory (AD Azure).

Lista med anpassade förbjudna lösenord kräver Azure AD Basic-licenser.

Azure AD-lösenordsskydd för Windows Server Active Directory kräver Azure AD Premium-licenser.

Ytterligare information om licenser går inklusive kostnader, kan hittas på den [priser platsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Ladda ned

Det finns två nödvändiga installationsprogram för Azure AD-lösenordsskydd som kan laddas ned från den [Microsoft download center](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="answers-to-common-questions"></a>Svar på vanliga frågor

* Ingen Internetanslutning krävs från domänkontrollanterna. Datorer som kör Azure AD lösenord protection proxy-tjänsten är endast datorer som kräver Internetanslutning.
* Inga nätverksportarna öppnas på domänkontrollanter.
* Det krävs inga ändringar för Active Directory-schemat.
* Programvaran använder befintliga Active Directory-behållare och serviceConnectionPoint schemaobjekt.
* Det finns inga Active Directory-domän eller skogens funktionella nivå (DFL\FFL) minimikrav.
* Programvaran inte skapar eller kräver några konton i Active Directory-domäner som den skyddar.
* Stegvis distribution stöds med kompromiss att lösenordsprinciper gäller endast där domain controller-agenten är installerad.
* Azure AD-lösenordsskydd är inte en i realtid principmodulen för programmet. Det kan finnas en fördröjning under tiden mellan en lösenordsändring princip för konfiguration och hur lång tid det når och tillämpas på alla domänkontrollanter.

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises.md)