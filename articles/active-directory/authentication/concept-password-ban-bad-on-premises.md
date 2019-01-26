---
title: Förhandsversionen protection Azure AD-lösenord
description: Förbjud svaga lösenord i den lokala Active Directory med hjälp av Azure AD lösenord protection preview
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: b99c1b99fe87c755d6092876ccd598d926289192
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077838"
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
* Det rekommenderas att installera DC-agenten på alla domänkontrollanter så tvingande för skydd av lösenord. 
* Azure AD-lösenordsskydd är inte en i realtid principmodulen för programmet. Det kan finnas en fördröjning under tiden mellan en lösenordsändring princip för konfiguration och hur lång tid det når och tillämpas på alla domänkontrollanter.

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
