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
ms.openlocfilehash: 042780c313c444062fd512ab0d9f38aaeb6cf170
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894566"
---
# <a name="monitor-identity-and-access"></a>Övervaka identitet och åtkomst

> [!TIP]
> Från mars 2020 ingår Azure Security Centers identitets-och åtkomst rekommendationer i alla prenumerationer på den kostnads fria pris nivån. Om du har prenumerationer på den kostnads fria nivån kommer deras säkra poäng att påverkas eftersom de inte tidigare har utvärderats för identitets-och åtkomst säkerhet. 

När Security Center identifierar potentiella säkerhets risker skapar den rekommendationer som vägleder dig genom processen att konfigurera de nödvändiga kontrollerna för att skärp och skydda dina resurser.

Säkerhetsperimetern har utvecklats från en nätverks gräns till en identitets perimeter. Säkerheten blir mindre säker på skyddet av ditt nätverk och mer om att skydda dina data, samt att hantera säkerheten för dina appar och användare. Nuförtiden, då mer data och fler appar flyttas till molnet, blir identiteten den nya perimetern.

Genom att övervaka identitets aktiviteter kan du vidta proaktiva åtgärder innan en incident inträffar eller åtgärder som aktive ras för att stoppa ett angrepps försök. Security Center kan till exempel flagga föråldrade konton (konton som inte längre behövs och som blockeras från att logga in med Azure Active Directory) för borttagning. 

Exempel på rekommendationer som du kan se i avsnittet **identitets-och åtkomst** resurs säkerhet i Azure Security Center:

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- Högst 3 ägare bör anges för din prenumeration
- Externa konton med Läs behörighet bör tas bort från din prenumeration
- Föråldrade konton bör tas bort från din prenumeration

Mer information om dessa rekommendationer och en fullständig lista över rekommendationer som du kan se här finns i [rekommendationer för identitet och åtkomst](recommendations-reference.md#recs-identity).

> [!NOTE]
> Om din prenumeration har fler än 600 konton kan Security Center inte köra identitets rekommendationerna mot din prenumeration. Rekommendationer som inte körs visas under "otillgängliga utvärderingar" nedan.
Security Center kan inte köra identitets rekommendationer mot en CSP-partners (Cloud Solution Provider) administratörs agenter.
>


Alla rekommendationer för identitet och åtkomst finns i två säkerhets kontroller på sidan **rekommendationer** :

- Hantera åtkomst och behörigheter 
- Aktivera MFA

![De två säkerhets kontrollerna med rekommendationerna för identitet och åtkomst](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Aktivera Multi-Factor Authentication (MFA)

För att aktivera MFA krävs [Azure Active Directory (AD) klient behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). 

- Om du har en Premium-version av AD aktiverar du MFA med [villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md).

- Om du använder en kostnads fri version av AD, aktiverar du **säkerhets inställningar** i Azure Active Directory enligt beskrivningen i [AD-dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande artiklar:

- [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)