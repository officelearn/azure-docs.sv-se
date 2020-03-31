---
title: Undersöka risken azure Active Directory Identity Protection
description: Lär dig hur du undersöker riskfyllda användare, identifieringar och inloggningar i Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253486"
---
# <a name="how-to-investigate-risk"></a>Så här: Undersök risker

Identity Protection ger organisationer tre rapporter som de kan använda för att undersöka identitetsrisker i sin miljö. Dessa rapporter är **riskfyllda användare,** **riskfyllda inloggningar**och **riskidentifieringar**. Utredning av händelser är nyckeln till bättre förståelse och identifiera eventuella svaga punkter i din säkerhetsstrategi.

Alla tre rapporter gör det möjligt att ladda ner händelser i . CSV-format för vidare analys utanför Azure-portalen. De riskfyllda användarna och riskfyllda inloggningsrapporter gör det möjligt att hämta de senaste 2500 posterna, medan rapporten riskidentifiering gör det möjligt att hämta de senaste 5000 posterna.

Organisationer kan dra nytta av Microsoft Graph API-integreringar för att aggregera data med andra källor som de kan ha åtkomst till som organisation.

De tre rapporterna finns i **Azure Portal** > **Azure Active Directory** > **Security**.

## <a name="navigating-the-reports"></a>Navigera i rapporterna

Varje rapport startas med en lista över alla identifieringar för perioden som visas högst upp i rapporten. Varje rapport gör det möjligt att lägga till eller ta bort kolumner baserat på administratörsinställningar. Administratörer kan välja att hämta data i . CSV eller . JSON-format. Rapporter kan filtreras med hjälp av filtren överst i rapporten.

Om du väljer enskilda poster kan det aktiveras ytterligare poster högst upp i rapporten, till exempel möjligheten att bekräfta en inloggning som komprometterad eller säker, bekräfta en användare som komprometterad eller avvisa användarrisk.

Om du väljer enskilda poster expanderas ett informationsfönster under identifieringarna. Med informationsvyn kan administratörer undersöka och utföra åtgärder på varje identifiering. 

![Exempel på identitetsskyddsrapport som visar riskfyllda inloggningar och information](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Riskfyllda användare

Med den information som tillhandahålls av rapporten för riskfyllda användare kan administratörer hitta:

- Vilka användare är i riskzonen, har haft risk avhjälpt eller har haft risk avfärdas?
- Information om identifieringar
- Historik över alla riskfyllda inloggningar
- Riskhistorik
 
Administratörer kan sedan välja att vidta åtgärder för dessa händelser. Administratörer kan välja att:

- Återställa användarlösenordet
- Bekräfta en kompromettering av användare
- Avvisa användarrisk
- Blockera användare från att logga in
- Undersöka vidare med Azure ATP

## <a name="risky-sign-ins"></a>Riskfyllda inloggningar

Rapporten riskfyllda inloggningar innehåller filterbara data i upp till de senaste 30 dagarna (1 månad).

Med den information som tillhandahålls av rapporten riskfyllda inloggningar kan administratörer hitta:

- Vilka inloggningar klassificeras som i riskzonen, bekräftas äventyras, bekräftas säker, ogillas eller åtgärdas.
- Risknivåer i realtid och aggregerad i samband med inloggningsförsök.
- Identifieringstyper som utlöses
- Principer för villkorlig åtkomst tillämpas
- MFA-detaljer
- Enhetsinformation
- Programinformation
- Platsinformation

Administratörer kan sedan välja att vidta åtgärder för dessa händelser. Administratörer kan välja att:

- Bekräfta kompromettering av inloggning
- Bekräfta inloggningen säker

## <a name="risk-detections"></a>Riskidentifieringar

Riskidentifieringsrapporten innehåller filterbara data i upp till de senaste 90 dagarna (3 månader).

Med den information som tillhandahålls av rapporten om riskidentifieringar kan administratörer hitta:

- Information om varje riskidentifiering inklusive typ.
- Andra risker som utlöses samtidigt
- Plats för inloggningsförsök
- Länka ut till mer information från Microsoft Cloud App Security (MCAS).

Administratörer kan sedan välja att återgå till användarens risk- eller inloggningsrapport för att vidta åtgärder baserat på insamlad information.

## <a name="next-steps"></a>Nästa steg

- [Policyer tillgängliga för att minska riskerna](concept-identity-protection-policies.md)

- [Aktivera principer för inloggning och användarrisk](howto-identity-protection-configure-risk-policies.md)
