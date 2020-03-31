---
title: Övervaka identitet och åtkomst i Azure Security Center | Microsoft Docs
description: Lär dig hur du använder identitets- och åtkomstfunktionerna i Azure Security Center för att övervaka användarnas åtkomstaktiviteter och identitetsrelaterade problem.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 152168bbadd5815659bc5f70c91bd2a28f5e049d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481967"
---
# <a name="monitor-identity-and-access"></a>Övervaka identitet och åtkomst

> [!TIP]
> Från och med mars 2020 ingår Azure Security Centers identitets- och åtkomstrekommendationer i alla prenumerationer på den kostnadsfria prisnivån. Om du har prenumerationer på den kostnadsfria nivån påverkas deras Secure Score eftersom de inte tidigare bedömts för sin identitets- och åtkomstsäkerhet. 

När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som hjälper dig att konfigurera de kontroller som behövs för att härda och skydda dina resurser.

Säkerhetsområdet har utvecklats från en nätverksperimeter till en identitetsperimeter. Säkerheten blir mindre om att försvara ditt nätverk och mer om att försvara dina data, samt hantera säkerheten för dina appar och användare. Nuförtiden, då mer data och fler appar flyttas till molnet, blir identiteten den nya perimetern.

Genom att övervaka identitetsaktiviteter kan du vidta proaktiva åtgärder innan en incident intar eller reaktiva åtgärder för att stoppa ett attackförsök. Säkerhetscenter kan till exempel flagga föråldrade konton (konton som inte längre behövs och blockeras från att logga in av Azure Active Directory) för borttagning. 

Exempel på rekommendationer som kan visas i avsnittet **Identitets- och åtkomstresurssäkerhet** i Azure Security Center är:

- MFA ska aktiveras på konton med ägarbehörighet för din prenumeration
- Högst 3 ägare bör utses för din prenumeration
- Externa konton med läsbehörighet bör tas bort från din prenumeration
- Inaktuella konton ska tas bort från din prenumeration

Mer information om dessa rekommendationer samt en fullständig lista över de rekommendationer som du kan se här finns i [rekommendationer om identitet och åtkomst](recommendations-reference.md#recs-identity).

> [!NOTE]
> Om din prenumeration har fler än 600 konton kan Security Center inte köra identitetsrekommendationerna mot din prenumeration. Rekommendationer som inte körs visas under "ej tillgängliga utvärderingar" nedan.
Security Center kan inte köra identitetsrekommendationerna mot en CSP-partners administratörsagenter (Cloud Solution Provider).
>


Alla identitets- och åtkomstrekommendationer finns i två säkerhetskontroller på sidan **Rekommendationer:**

- Hantera åtkomst och behörigheter 
- Aktivera MFA

![De två säkerhetskontrollerna med rekommendationer om identitet och åtkomst](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Aktivera MFA (Multifaktorautentisering)

Om du aktiverar MFA krävs [Azure Active Directory (AD) klientbehörigheter](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). 

- Om du har en premiumutgåva av AD aktiverar du MFA med [villkorad åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

- Användare av AD free edition kan aktivera **standardvärden** för säkerhet i Azure Active Directory enligt beskrivningen i [AD-dokumentationen,](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) men Rekommendationen Security Center för att aktivera MFA visas fortfarande.


## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra Azure-resurstyper finns i följande artiklar:

- [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
- [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
- [Skydda din Azure SQL-tjänst och dina Azure-data i Azure Security Center](security-center-sql-service-recommendations.md)