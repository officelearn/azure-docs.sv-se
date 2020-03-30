---
title: Vad är villkorlig åtkomst i Azure Active Directory?
description: Lär dig hur villkorlig åtkomst är kärnan i det nya identitetsdrivna kontrollplanet.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b044a4fd4e29bfe35abff7a4b36e5bae783328b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240914"
---
# <a name="what-is-conditional-access"></a>Vad är villkorsstyrd åtkomst?

Den moderna säkerhetsperimetern sträcker sig nu utanför en organisations nätverk för att inkludera användar- och enhetsidentitet. Organisationer kan använda dessa identitetssignaler som en del av sina beslut om åtkomstkontroll. 

Villkorlig åtkomst är det verktyg som används av Azure Active Directory för att sammanföra signaler, fatta beslut och tillämpa organisationsprinciper. Villkorlig åtkomst är kärnan i det nya identitetsdrivna kontrollplanet.

![Konceptuell villkorlig signal plus beslut att få verkställighet](./media/overview/conditional-access-signal-decision-enforcement.png)

Principer för villkorlig åtkomst på sin enklaste är om-då-satser, om en användare vill komma åt en resurs, måste de slutföra en åtgärd. Exempel: En lönehanterare vill komma åt löneprogrammet och krävs för att utföra multifaktorautentisering för att komma åt det.

Administratörer står inför två primära mål:

- Underlätta för användarna att vara produktiva oavsett tid och plats
- Skydda organisationens tillgångar

Genom att använda principer för villkorlig åtkomst kan du använda rätt åtkomstkontroller när det behövs för att skydda din organisation och hålla dig borta från användarens väg när det inte behövs.

![Processflöde för konceptuell villkorlig åtkomst](./media/overview/conditional-access-overview-how-it-works.png)

Principer för villkorlig åtkomst tillämpas efter att den första faktorautentiseringen har slutförts. Villkorlig åtkomst är inte avsedd som en organisations första försvarslinje för scenarier som DoS-attacker (Denial-of-Service), men kan använda signaler från dessa händelser för att avgöra åtkomst.

## <a name="common-signals"></a>Vanliga signaler

Vanliga signaler som villkorlig åtkomst kan ta hänsyn till när ett politiskt beslut fattas är följande signaler:

- Medlemskap för användare eller grupper
   - Principer kan riktas till specifika användare och grupper som ger administratörer finkornig kontroll över åtkomst.
- INFORMATION OM IP-plats
   - Organisationer kan skapa betrodda IP-adressintervall som kan användas när du fattar politiska beslut. 
   - Administratörer kan ange hela länder IP-intervall för att blockera eller tillåta trafik från.
- Enhet
   - Användare med enheter av specifika plattformar eller markerade med ett visst tillstånd kan användas när principer för villkorlig åtkomst tillämpas.
- Program
   - Användare som försöker komma åt specifika program kan utlösa olika principer för villkorlig åtkomst. 
- Riskidentifiering i realtid och beräknad risk
   - Signaler integration med Azure AD Identity Protection tillåter principer för villkorlig åtkomst för att identifiera riskfyllda inloggningsbeteende. Principer kan sedan tvinga användare att utföra lösenordsändringar eller multifaktorautentisering för att minska sin risknivå eller blockeras från åtkomst tills en administratör vidtar manuella åtgärder.
- Microsoft Cloud App Security (MCAS)
   - Gör det möjligt att övervaka och kontrollera användarprogramåtkomst och sessioner i realtid, vilket ökar synligheten och kontrollen över åtkomst till och aktiviteter som utförs i molnmiljön.

## <a name="common-decisions"></a>Gemensamma beslut

- Blockera åtkomst
   - Mest restriktiva beslut
- Bevilja åtkomst
   - Minst restriktiva beslut, kan fortfarande kräva ett eller flera av följande alternativ:
      - Kräv multifaktorautentisering
      - Kräv att enheten ska markeras som kompatibel
      - Kräv Hybrid Azure AD-ansluten enhet
      - Kräv godkänd klientapp
      - Kräv appskyddsprincip (förhandsgranskning)

## <a name="commonly-applied-policies"></a>Vanliga principer

Många organisationer har vanliga åtkomstproblem som principer för villkorlig åtkomst kan hjälpa till med, till exempel:

- Kräver multifaktorautentisering för användare med administrativa roller
- Kräver multifaktorautentisering för Azure-hanteringsuppgifter
- Blockera inloggningar för användare som försöker använda äldre autentiseringsprotokoll
- Kräver betrodda platser för registrering av Azure Multi Factor-autentisering
- Blockera eller bevilja åtkomst från specifika platser
- Blockera riskfyllda inloggningsbeteenden
- Kräver organisationshanterade enheter för specifika program

## <a name="customer-case-studies"></a>Kundfallstudier

Upptäck hur andra organisationer använder Azure AD Villkorlig åtkomst för att definiera och implementera beslut om automatisk åtkomstkontroll. Följande presenterade berättelser visar hur dessa kundbehov tillgodoses.

* [Wipro ökar mobil produktiviteten med Microsofts molnsäkerhetsverktyg för att förbättra kundengagemanget.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Principerna för villkorlig åtkomst i Azure AD har gjort det möjligt för företaget att dela dokument, resurser och program med betrodda externa entiteter---som kan använda sina egna autentiseringsuppgifter--- samtidigt som de behåller kontrollen över sina egna företagsdata.
* [Aramex delivery limited - Globalt logistik- och transportföretag skapar molnanslutet kontor med identitets- och åtkomsthanteringslösning.](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en) Att säkerställa säker åtkomst var särskilt svårt med Aramexs fjärranställda. Företaget tillämpar nu villkorlig åtkomst för att låta dessa fjärranställda komma åt sina SaaS-program utanför nätverket. Regeln villkorlig åtkomst avgör om multifaktorautentisering ska tillämpas, vilket ger endast rätt personer rätt åtkomst.

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Kunder med [Microsoft 365 Business-licenser](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) har också tillgång till funktioner för villkorlig åtkomst. 

## <a name="next-steps"></a>Nästa steg

[Skapa en princip för villkorlig åtkomst bit för bit](concept-conditional-access-policies.md)

Mer information om hur du implementerar villkorlig åtkomst i din miljö finns i [Planera distributionen för villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md).

[Läs mer om identitetsskydd](../identity-protection/overview-v2.md)

[Läs mer om Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)

[Läs mer om Microsoft Intune](/intune/index)