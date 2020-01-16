---
title: Förstå administratörsroller för Enterprise i Azure | Microsoft Docs
description: Lär dig mer om administratörsroller för Enterprise i Azure.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2020
ms.author: banders
ms.openlocfilehash: ae33d320213cc526710845e78c23a83143a73771
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989909"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Förstå administrativa roller för Azure Enterprise-avtal i Azure

Azure-kunder med ett Enterprise-avtal (EA) kan tilldela fem olika administratörsroller för att hantera organisationens användning och utgifter:

- Företagsadministratörer
- Företagsadministratör (skrivskyddad)
- Avdelningsadministratör
- Avdelningsadministratör (skrivskyddad)
- Kontoägare
 
De här rollerna gäller särskilt för hantering av Azure Enterprise-avtal och används utöver de fördefinierade roller som används till att styra åtkomsten till resurser i Azure. Mer information finns i [Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

I följande avsnitt beskrivs begränsningarna och funktionerna för varje roll.

## <a name="user-limit-for-admin-roles"></a>Användargräns för administratörsroller

|Roll| Användargräns|
|---|---|
|Företagsadministratörer|Unlimited|
|Företagsadministratör (skrivskyddad)|Unlimited|
|Avdelningsadministratör|Unlimited|
|Avdelningsadministratör (skrivskyddad)|Unlimited|
|Kontoägare|1 per konto<sup>1</sup>|

<sup>1</sup> Varje konto måste ha ett unikt Microsoft-konto, arbetskonto eller skolkonto.

## <a name="organization-structure-and-permissions-by-role"></a>Organisationsstruktur och behörigheter per roll

|Aktiviteter| Företagsadministratörer|Företagsadministratör (skrivskyddad)|Avdelningsadministratör|Avdelningsadministratör (skrivskyddad)|Kontoägare|
|---|---|---|---|---|---|
|Visa företagsadministratörer|✔|✔|✘|✘|✘|
|Lägga till eller ta bort företagsadministratörer|✔|✘|✘|✘|✘|
|Visa meddelandekontakter<sup>2</sup> |✔|✔|✘|✘|✘|
|Lägga till eller ta bort meddelandekontakter<sup>2</sup> |✔|✘|✘|✘|✘|
|Skapa och hantera avdelningar |✔|✘|✘|✘|✘|
|Visa avdelningsadministratörer|✔|✔|✔|✔|✘|
|Lägga till eller ta bort avdelningsadministratörer|✔|✘|✔|✘|✘|
|Visa konton i registreringen |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Lägga till konton i registreringen och ändra kontoägare|✔|✘|✔<sup>3</sup>|✘|✘|
|Skapa och hantera prenumerationer och prenumerationsbehörigheter|✘|✘|✘|✘|✔|

- <sup>2</sup> Meddelandekontakter får e-postmeddelanden om Azure Enterprise-avtalet.
- <sup>3</sup> Uppgiften är begränsad till konton på din avdelning.


## <a name="usage-and-costs-access-by-role"></a>Åtkomst till användning och kostnader per roll

|Aktiviteter| Företagsadministratörer|Företagsadministratör (skrivskyddad)|Avdelningsadministratör|Avdelningsadministratör (skrivskyddad) |Kontoägare|
|---|---|---|---|---|---|
|Visa kreditsaldo inklusive ekonomiska åtaganden|✔|✔|✘|✘|✘|
|Visa avdelningens utgiftskvot|✔|✔|✘|✘|✘|
|Ange avdelningens utgiftskvot|✔|✘|✘|✘|✘|
|Visa organisationens EA-prisdokument|✔|✔|✘|✘|✘|
|Visa information om användning och kostnader|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Hantera resurser i Azure-portalen|✘|✘|✘|✘|✔|

- <sup>4</sup> Kräver att företagsadministratören aktiverar policyn **Visa avgifter för DA** i Enterprise-portalen. Avdelningsadministratören kan sedan se kostnadsinformation för avdelningen.
- <sup>5</sup> Kräver att företagsadministratören aktiverar policyn **Visa avgifter för AO** i Enterprise-portalen. Kontoägaren kan sedan se kostnadsinformation för kontot.


## <a name="pricing-in-azure-portal"></a>Priser i Azure-portalen

Du kan se olika priser i Azure-portalen beroende på din administrativa roll och hur företagsadministratören har ställt in policyerna för visning av avgifter. De två policyerna i Enterprise-portalen som påverkar prissättningen du ser i Azure-portalen är:

- Visa avgifter för DA
- Visa avgifter för AO

Information om hur du ställer in dessa policyer finns i [Hantera åtkomst till faktureringsinformation för Azure](manage-billing-access.md).

I följande tabell visas relationen mellan administratörsrollerna för Enterprise-avtal, policyn för visning av avgifter, RBAC-rollen i Azure-portalen och de priser du ser i Azure-portalen. Företagsadministratören ser alltid användningsinformation baserat på organisationens EA-prissättning. Avdelningsadministratörer och kontoägare ser däremot olika priser baserat på policyn för visning av avgifter och den tilldelade RBAC-rollen. Rollen som Avdelningsadministratör i följande tabell avser både rollen Avdelningsadministratör och Avdelningsadministratör (skrivskyddad).

|Administratörsroll för Enterprise-avtal|Princip om visning av avgifter för rollen|RBAC-roll|Prisvisning|
|---|---|---|---|
|Kontoägare ELLER Avdelningsadministratör|✔ Aktiverad|Ägare|Organisationens EA-prissättning|
|Kontoägare ELLER Avdelningsadministratör|✘ Inaktiverad|Ägare|Återförsäljarpris|
|Kontoägare ELLER Avdelningsadministratör|✔ Aktiverad |ingen|Inga priser|
|Kontoägare ELLER Avdelningsadministratör|✘ Inaktiverad |ingen|Inga priser|
|Inget|Inte aktuellt |Ägare|Återförsäljarpris|

Du ställer in administratörsroller för Enterprise och visar policyer för avgifter i Enterprise-portalen. RBAC-roller kan uppdateras i Azure-portalen. Mer information finns i [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomst till faktureringsinformation för Azure](manage-billing-access.md)
- [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md)
- [Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md)
