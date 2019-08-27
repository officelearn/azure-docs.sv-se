---
title: Förstå administratörs roller för företag i Azure | Microsoft Docs
description: Lär dig mer om företags administratörs roller i Azure.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: banders
ms.openlocfilehash: 39526defb8f41ddacb0a26d7ad852f820ca6ea77
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034533"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Förstå Azure Enterprise-avtal administrativa roller i Azure

För att hjälpa till att hantera organisationens användning och utgifter kan Azure-kunder med en Enterprise-avtal (EA) tilldela fem olika administrativa roller:

- Företagsadministratör
- Företags administratör (skrivskyddad)
- Avdelningsadministratör
- Avdelnings administratör (skrivskyddad)
- Kontoägare
 
De här rollerna är speciella för att hantera Azure Enterprise-avtal och är utöver de inbyggda rollerna som Azure har för att kontrol lera åtkomsten till resurser. Mer information finns i [inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md).

I följande avsnitt beskrivs begränsningar och funktioner för varje roll.

## <a name="user-limit-for-admin-roles"></a>Användar gräns för administratörs roller

|Role| Användar gräns|
|---|---|
|Företagsadministratör|Obegränsat|
|Företags administratör (skrivskyddad)|Obegränsat|
|Avdelningsadministratör|Obegränsat|
|Avdelnings administratör (skrivskyddad)|Obegränsat|
|Kontoägare|1 per konto<sup>1</sup>|

<sup>1</sup> varje konto kräver ett unikt Microsoft-konto-eller arbets-eller skol konto.

## <a name="organization-structure-and-permissions-by-role"></a>Organisations struktur och behörigheter efter roll

|Aktiviteter| Företagsadministratör|Företags administratör (skrivskyddad)|Avdelningsadministratör|Avdelnings administratör (skrivskyddad)|Kontoägare|
|---|---|---|---|---|---|
|Visa företags administratörer|✔|✔|✘|✘|✘|
|Lägga till eller ta bort företags administratörer|✔|✘|✘|✘|✘|
|Visa meddelande kontakter<sup>2</sup> |✔|✔|✘|✘|✘|
|Lägg till eller ta bort aviserings kontakter<sup>2</sup> |✔|✘|✘|✘|✘|
|Skapa och hantera avdelningar |✔|✘|✘|✘|✘|
|Visa avdelnings administratörer|✔|✔|✔|✔|✘|
|Lägga till eller ta bort avdelnings administratörer|✔|✘|✔|✘|✘|
|Visa konton i registreringen |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Lägg till konton i registreringen och ändra kontots ägare|✔|✘|✔<sup>3</sup>|✘|✘|
|Skapa och hantera prenumerationer och prenumerations behörigheter|✘|✘|✘|✘|✔|

- <sup>2</sup> meddelande kontakter skickas via e-post om Azure-Enterprise-avtal.
- <sup>3</sup> uppgiften är begränsad till konton på din avdelning.


## <a name="usage-and-costs-access-by-role"></a>Användning och kostnader åtkomst per roll

|Aktiviteter| Företagsadministratör|Företags administratör (skrivskyddad)|Avdelningsadministratör|Avdelnings administratör (skrivskyddad) |Kontoägare|
|---|---|---|---|---|---|
|Visa kredit balans inklusive betalnings åtagande|✔|✔|✘|✘|✘|
|Visa utgifts kvoter för avdelning|✔|✔|✘|✘|✘|
|Ange avdelnings utgifts kvoter|✔|✘|✘|✘|✘|
|Visa organisationens EA pris dokument|✔|✔|✘|✘|✘|
|Visa information om användning och kostnad|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Hantera resurser i Azure Portal|✘|✘|✘|✘|✔|

- <sup>4</sup> kräver att företags administratören aktiverar tilläggs principen för **da View** i Enterprise Portal. Avdelnings administratören kan sedan se kostnads information för avdelningen.
- <sup>5</sup> kräver att företags administratören aktiverar **Ao View charges** policy i Enterprise Portal. Konto ägaren kan sedan se kostnads information för kontot.


## <a name="pricing-in-azure-portal"></a>Priser i Azure Portal

Du kan se olika priser i Azure Portal beroende på din administrativa roll och hur avgifts principerna för vyn ställs in av företags administratören. De två principerna i Enterprise Portal som påverkar den prissättning som visas i Azure Portal är:

- Visa avgifter för DA
- AO Visa avgifter

Information om hur du ställer in dessa principer finns i [Hantera åtkomst till fakturerings information för Azure](billing-manage-access.md).

I följande tabell visas relationen mellan Enterprise-avtal admin-roller, rollen Visa avgift, rollen rollbaserad åtkomst kontroll (RBAC) i Azure Portal och den prissättning som visas i Azure Portal. Företags administratören ser alltid användnings information baserat på organisationens EA-prissättning. Men avdelnings administratören och konto ägaren ser olika prisvyer baserat på vyn avgifts princip och dess RBAC-roll. Rollen avdelnings administratör som anges i följande tabell refererar till rollerna både avdelnings administratör och avdelnings administratör (skrivskyddad).

|Enterprise-avtal administratörs roll|Visa avgifts princip för roll|RBAC-roll|Pris visning|
|---|---|---|---|
|Konto ägare eller avdelnings administratör|✔ Aktive rad|Ägare|Organisationens EA-prissättning|
|Konto ägare eller avdelnings administratör|✘ Inaktiverat|Ägare|Detalj handels pris|
|Konto ägare eller avdelnings administratör|✔ Aktive rad |inga|Ingen prissättning|
|Konto ägare eller avdelnings administratör|✘ Inaktiverat |inga|Ingen prissättning|
|Inga|Inte tillämpligt |Ägare|Detalj handels pris|

Du ställer in rollen företags administratör och visar debiterings principer i Enterprise Portal. RBAC-rollen kan uppdateras i Azure Portal. Mer information finns i [Hantera åtkomst med RBAC och Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomst till faktureringsinformation för Azure](billing-manage-access.md)
- [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../role-based-access-control/role-assignments-portal.md)
- [Inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md)
