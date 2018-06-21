---
title: Skydd förhandsversionen av Azure AD lösenord
description: Förbud svaga lösenord i lokala Active Directory med Azure AD lösenord skydd preview
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 0819a947229e633331253923654de56638a6c76a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296115"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Förhandsversion: Tillämpa skydd av Azure AD-lösenord för Windows Server Active Directory

|     |
| --- |
| Azure AD-lösenordsskydd och lösenordslistan över anpassade förbjudna är förhandsversion funktioner i Azure Active Directory. Läs mer om förhandsgranskningar [kompletterande användningsvillkor för Microsoft Azure-förhandsgranskningar](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Azure AD-lösenordsskydd är en ny funktion i förhandsversion som tillhandahålls av Azure Active Directory (Azure AD) för att förbättra lösenordsprinciper i en organisation. Lokal distribution av Azure AD-lösenordsskydd använder den globala och anpassade förbjudna lösenordslistor som lagras i Azure AD och utför samma kontroller lokal som molnbaserad Azure AD-ändringar.

Det finns tre programvarukomponenter som utgör lösenordsskydd för Azure AD:

* Azure AD lösenord skydd proxy-tjänsten körs på en domänansluten dator i den aktuella Active Directory-skogen. Den vidarebefordrar begäranden från domänkontrollanter till Azure AD och returnerar svaret från Azure AD tillbaka till domänkontrollanten.
* Azure AD lösenord skydd DC agent-tjänsten tar emot lösenord validering förfrågningar från den DC Agent lösenord DLL-fil, bearbetar dem med hjälp av den aktuella lokalt tillgängliga lösenordsprincipen och returnerar resultatet (pass\fail). Den här tjänsten ansvarar för att regelbundet (en gång i timmen) anropa tjänsten Azure AD lösenord skydd proxy för att hämta nya versioner av lösenordsprincipen. Kommunikation för anrop till och från Azure AD lösenord skyddstjänsten proxy hanteras via RPC (Remote Procedure Call) via TCP. Vid hämtning lagras nya principer i en sysvol-mappen där de kan replikeras till andra domänkontrollanter. DC agent-tjänsten övervakar även sysvol-mappen för att ändringarna om andra domänkontrollanter har skrivit det nya lösenordsprinciper, om en lämplig senaste princip är redan tillgänglig kontrollen av Azure AD lösenord skydd proxy-tjänsten kommer att hoppas över.
* DLL-filen för DC Agent lösenord filter tar emot förfrågningar för verifiering av lösenord från operativsystemet och vidarebefordrar dem till Azure AD lösenord DC agent-tjänsten körs lokalt på domänkontrollanten.

![Hur arbetar tillsammans för Azure AD lösenordskomponenter för skydd](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

## <a name="requirements"></a>Krav

* Alla datorer där Azure AD lösenord skydd komponenter är installerade inklusive domänkontrollanter måste köra Windows Server 2012 eller senare.
* Nätverksanslutningar finnas mellan minst en domänkontrollant i varje domän och minst en server som värd för Azure AD lösenord skydd proxy-tjänsten.
* Alla Active Directory-domän som kör domänkontrollanten Agentprogrammet som tjänsten måste använda DFSR för sysvol-replikering.
* Ett globalt administratörskonto för att registrera Azure AD lösenord skydd proxy-tjänsten med Azure AD.
* Ett konto med administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen.

### <a name="license-requirements"></a>Licenskrav

Fördelarna med lösenordslistan globala förbjudna gäller för alla användare av Azure Active Directory (AD Azure).

Lösenordslistan över anpassade förbjudna kräver Azure AD Basic licenser.

Azure AD-lösenordsskydd för Windows Server Active Directory kräver Azure AD Premium-licenser. 

Ytterligare licensinformation, inklusive kostnader, kan hittas på den [Azure Active Directory priser plats](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Ladda ned

Det finns två nödvändiga installationsprogram för Azure AD-lösenordsskydd som kan hämtas från den [Microsoft download center](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="answers-to-common-questions"></a>Svar på vanliga frågor

* Ingen Internetanslutning krävs från domänkontrollanterna. Datorer som kör tjänsten Azure AD lösenord skydd proxy är endast datorer som kräver internet-anslutning.
* Inga nätverksportarna öppnas på domänkontrollanter.
* Det krävs inga ändringar för Active Directory-schemat.
   * Programmet använder befintliga Active Directory-behållare och serviceConnectionPoint schemaobjekt.
* Det finns inga Active Directory-domän eller skog funktionella nivån (DFL\FFL) minimikrav.
* Programvaran inte skapar eller kräver något konto i Active Directory-domäner som den skyddar.
* Stegvis distribution stöds med förhållandet att lösenord policyn verkställs bara där domain controller agenten är installerad.
* Azure AD-lösenordsskydd är inte en realtid principmodulen för programmet. Det kan finnas en fördröjning mellan en lösenordsändring princip för konfiguration och den tid det når och tillämpas på alla domänkontrollanter.

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises.md)