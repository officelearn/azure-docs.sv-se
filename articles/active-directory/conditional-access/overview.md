---
title: Vad är villkorlig åtkomst i Azure Active Directory?
description: Lär dig hur villkorlig åtkomst är kärnan i det nya identitets drivna kontroll planet.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 05/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: fb8687c091e8c34ad1fbae2a50981327b3994b6e
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "85051955"
---
# <a name="what-is-conditional-access"></a>Vad är villkorsstyrd åtkomst?

Modern säkerhetsperimeter sträcker sig nu utanför en organisations nätverk för att inkludera användar-och enhets identitet. Organisationer kan använda dessa identitets signaler som en del av besluten om åtkomst kontroll. 

Villkorlig åtkomst är det verktyg som används av Azure Active Directory för att ta fram signaler, fatta beslut och tillämpa organisations principer. Villkorlig åtkomst är kärnan i det nya identitets drivna kontroll planet.

![Konceptuell villkorlig signal plus beslut för att få tillämpning](./media/overview/conditional-access-signal-decision-enforcement.png)

Principer för villkorlig åtkomst på enklaste sätt är if-then-satser, om en användare vill komma åt en resurs, måste de slutföra en åtgärd. Exempel: en löne hanterare vill få åtkomst till löne programmet och krävs för att utföra Multi-Factor Authentication för att få åtkomst till den.

Administratörer är riktade mot två primära mål:

- Underlätta för användarna att vara produktiva oavsett tid och plats
- Skydda organisationens till gångar

Genom att använda principer för villkorlig åtkomst kan du tillämpa rätt åtkomst kontroller när det behövs för att hålla din organisation säker och hålla dig informerad när du inte behöver det.

![Koncept för process flöde för villkorlig åtkomst](./media/overview/conditional-access-overview-how-it-works.png)

Principer för villkorlig åtkomst tillämpas när den första faktorn har slutförts. Villkorlig åtkomst är inte avsedd som en organisations första försvars linje för scenarier som denial-of-service (DoS)-attacker, men kan använda signaler från dessa händelser för att fastställa åtkomst.

## <a name="common-signals"></a>Vanliga signaler

Vanliga signaler att villkorlig åtkomst kan ta med i kontot när du fattar ett princip beslut inkluderar följande signaler:

- Användar-eller grupp medlemskap
   - Principer kan riktas mot specifika användare och grupper som ger administratörer detaljerad kontroll över åtkomsten.
- Information om IP-plats
   - Organisationer kan skapa betrodda IP-adressintervall som kan användas när du fattar princip beslut. 
   - Administratörer kan ange hela länder/regioner med IP-intervall för att blockera eller tillåta trafik från.
- Enhet
   - Användare med enheter av vissa plattformar eller markerade med ett angivet tillstånd kan användas för att verkställa principer för villkorlig åtkomst.
- Program
   - Användare som försöker komma åt vissa program kan utlösa olika principer för villkorlig åtkomst. 
- Identifiering i real tid och beräknad risk
   - Signalerar integrering med Azure AD Identity Protection tillåter principer för villkorlig åtkomst för att identifiera riskfyllda inloggnings beteenden. Principer kan sedan göra det möjligt för användare att utföra lösen ords ändringar eller Multi-Factor Authentication för att minska risk nivån eller blockeras från åtkomst tills en administratör vidtar manuella åtgärder.
- Microsoft Cloud App Security (MCAS)
   - Gör att användarens program åtkomst och sessioner kan övervakas och kontrol leras i real tid, öka synlighet och kontroll över åtkomst till och aktiviteter som utförs i din moln miljö.

## <a name="common-decisions"></a>Vanliga beslut

- Blockera åtkomst
   - Mest restriktiva beslut
- Bevilja åtkomst
   - Minst restriktivt beslut kan fortfarande kräva ett eller flera av följande alternativ:
      - Kräv Multi-Factor Authentication
      - Kräv att enheten ska markeras som kompatibel
      - Kräv hybrid Azure AD-ansluten enhet
      - Kräv godkänd klientapp
      - Kräv app Protection-princip (förhands granskning)

## <a name="commonly-applied-policies"></a>Principer som tillämpas ofta

Många organisationer har [vanliga åtkomst frågor som principer för villkorlig åtkomst kan hjälpa](concept-conditional-access-policy-common.md) till, till exempel:

- Kräva Multi-Factor Authentication för användare med administrativa roller
- Kräver Multi-Factor Authentication för Azures hanterings uppgifter
- Blockera inloggningar för användare som försöker använda bakåtkompatibla autentiseringsprotokoll
- Kräva betrodda platser för registrering av Azure-Multi-Factor Authentication
- Blockera eller bevilja åtkomst från vissa platser
- Blockera riskfyllda inloggnings beteenden
- Kräva organisations hanterade enheter för vissa program

## <a name="customer-case-studies"></a>Kundfallstudier

Upptäck hur andra organisationer använder villkorlig åtkomst för Azure AD för att definiera och implementera beslut om automatisk åtkomst kontroll. Följande artiklar visar hur dessa kunder behöver uppfyllas.

* [Wipro driver mobil produktivitet med Microsofts moln säkerhets verktyg för att förbättra kund engagemang.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Principer för villkorlig åtkomst i Azure AD har aktiverat företaget för att dela dokument, resurser och program med betrodda utanför entiteter---som kan använda sina egna autentiseringsuppgifter---samtidigt som de behåller kontrollen över sina egna företags data.
* [Aramex Delivery Limited-global logistik och transport företag skapar ett moln anslutet kontor med identitets-och åtkomst hanterings lösning](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Att säkerställa säker åtkomst var särskilt svårt med Aramex fjär anställda. Företaget använder nu villkorlig åtkomst för att ge dessa anställda till gång till sina SaaS-program utanför nätverket. Regeln för villkorlig åtkomst avgör om du vill genomdriva Multi-Factor Authentication, vilket ger endast rätt personer rätt åtkomst.

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Kunder med [Microsoft 365 Business Premium-licenser](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) har också till gång till funktioner för villkorlig åtkomst. 

## <a name="next-steps"></a>Nästa steg

- [Skapa en princip för villkorlig åtkomst i stycken](concept-conditional-access-policies.md)
- [Planera distributionen av villkorsstyrd åtkomst](plan-conditional-access.md)
- [Lär dig mer om identitets skydd](../identity-protection/overview-v2.md)
- [Läs mer om Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)
- [Läs mer om Microsoft Intune](/intune/index)
