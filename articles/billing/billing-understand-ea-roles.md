---
title: Förstå administrativa roller för företag i Azure | Microsoft Docs
description: Läs mer om Enterprise-administratörsroller i Azure.
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
ms.openlocfilehash: 98ed28af8df246549fb521a81f1968e1f5c28cc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370721"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Förstå Azure Enterprise-avtal administrativa roller i Azure

Kunder med ett Enterprise Agreement (EA) kan tilldela fem olika administrativa roller för att hantera din organisations användning och utgifter, Azure:

- Företagsadministratör
- Företagsadministratör (skrivskyddat)
- Avdelningsadministratör
- Avdelning administratör (skrivskyddat)
- Kontoägare
 
Dessa roller är specifika för hantering av Azure Enterprise-avtal och måste vara uppfyllda utöver de inbyggda rollerna som Azure har för att styra åtkomsten till resurser. Mer information finns i [inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md).

I följande avsnitt beskrivs de begränsningar och funktionerna för varje roll.

## <a name="user-limit-for-admin-roles"></a>Gränsen för administratörsroller

|Roll| Användargräns|
|---|---|
|Företagsadministratör|Obegränsat|
|Företagsadministratör (skrivskyddat)|Obegränsat|
|Avdelningsadministratör|Obegränsat|
|Avdelning administratör (skrivskyddat)|Obegränsat|
|Kontoägare|1 per konto<sup>1</sup>|

<sup>1</sup> varje konto kräver ett unikt Microsoft-konto eller arbets-eller skolkonto.

## <a name="organization-structure-and-permissions-by-role"></a>Organisationsstruktur och behörigheter efter roll

|Uppgifter| Företagsadministratör|Företagsadministratör (skrivskyddat)|Avdelningsadministratör|Avdelning administratör (skrivskyddat)|Kontoägare|
|---|---|---|---|---|---|
|Visa Företagsadministratörer|✔|✔|✘|✘|✘|
|Lägg till eller ta bort Företagsadministratörer|✔|✘|✘|✘|✘|
|Visa meddelande kontakter<sup>2</sup> |✔|✔|✘|✘|✘|
|Lägga till eller ta bort meddelande kontakter<sup>2</sup> |✔|✘|✘|✘|✘|
|Skapa och hantera avdelningar |✔|✘|✘|✘|✘|
|Visa avdelning administratörer|✔|✔|✔|✔|✘|
|Lägga till eller ta bort avdelning administratörer|✔|✔|✔|✘|✘|
|Visa konton i registreringen |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Lägga till konton i registreringen och ändra ägare|✔|✘|✔<sup>3</sup>|✘|✘|
|Skapa och hantera prenumerationer och Prenumerationsbehörigheter som krävs|✘|✘|✘|✘|✔|

- <sup>2</sup> meddelande kontakter skickas e-postmeddelanden om Azure Enterprise-avtal.
- <sup>3</sup> uppgift är begränsad till konton i din avdelning.


## <a name="usage-and-costs-access-by-role"></a>Användning och kostnader för åtkomst av roll

|Uppgifter| Företagsadministratör|Företagsadministratör (skrivskyddat)|Avdelningsadministratör|Avdelning administratör (skrivskyddat) |Kontoägare|
|---|---|---|---|---|---|
|Visa kreditsaldo inklusive åtagandebelopp|✔|✔|✘|✘|✘|
|Visa avdelningens utgiftsgränser|✔|✔|✘|✘|✘|
|Ange avdelningens utgiftsgränser|✔|✘|✘|✘|✘|
|Visa organisations EA prisdokument|✔|✔|✘|✘|✘|
|Visa information om användning och kostnader|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Hantera resurser i Azure-portalen|✘|✘|✘|✘|✔|

- <sup>4</sup> kräver att företagsadministratören aktiverar **DA visa debiteringar** principen i Enterprise portal. Avdelning-administratören kan sedan se kostnadsinformation för avdelningen.
- <sup>5</sup> kräver att företagsadministratören aktiverar **AO visa debiteringar** principen i Enterprise portal. Ägare kan sedan se kostnadsinformation för kontot.


## <a name="pricing-in-azure-portal"></a>Priser på Azure-portalen

Du kan se olika priser på Azure-portalen beroende på din administrativ roll och hur Visa avgifter policys ställs in som företagsadministratör. De två principerna i Enterprise portal som påverkar priserna som visas i Azure-portalen är:

- DA visa debiteringar
- AO visa debiteringar

Information om hur du konfigurerar dessa principer finns i [hantera åtkomst till faktureringsinformation för Azure](billing-manage-access.md).

I följande tabell visar förhållandet mellan administratörsroller Enterprise-avtal, Visa avgifter princip, rollbaserad åtkomstkontroll (RBAC) rollen i Azure portal och de priser som visas i Azure-portalen. Företagsadministratören ser alltid användningsinformation baserat på organisationens EA-priser. Avdelning administratör och Kontoägare ser dock olika priser vyer baserat på Visa kostnad principer och deras RBAC-roll. Avdelning administratörsroll som visas i följande tabell refererar till både avdelning Admin och avdelning (skrivskyddat) administratörsroller.

|Enterprise Agreement-administratörsrollen|Visa avgifter princip för rollen|RBAC-roll|Priser för vyn|
|---|---|---|---|
|Ägare eller administratör för avdelning|✔ Aktiverat|Ägare|Organisations EA-priser|
|Ägare eller administratör för avdelning|✘ Inaktiverad|Ägare|Återförsäljarpriset|
|Ägare eller administratör för avdelning|✔ Aktiverat |inga|Ingen prissättning|
|Ägare eller administratör för avdelning|✘ Inaktiverad |inga|Ingen prissättning|
|Ingen|Inte tillämpligt |Ägare|Återförsäljarpriset|

Du ställer in administratörsrollen för Enterprise och visa debiterar principer i Enterprise portal. RBAC-roll kan uppdateras i Azure-portalen. Mer information finns i [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomst till faktureringsinformation för Azure](billing-manage-access.md)
- [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../role-based-access-control/role-assignments-portal.md)
- [Inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md)
