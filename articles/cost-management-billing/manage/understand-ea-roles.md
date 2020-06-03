---
title: Förstå administratörsroller för företag i Azure
description: Lär dig mer om administratörsroller för Enterprise i Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/01/2020
ms.author: banders
ms.openlocfilehash: 15c686e0d33d7341d16097f32e1c69077c319a12
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84295311"
---
# <a name="managing-azure-enterprise-roles"></a>Hantera Azure Enterprise-roller

Azure-kunder med ett Enterprise-avtal (EA) kan tilldela fem olika administratörsroller för att hantera organisationens användning och utgifter:

- Företagsadministratör
- Företagsadministratör (skrivskyddad)<sup>1</sup>
- Avdelningsadministratör
- Avdelningsadministratör (skrivskyddad)
- Kontoinnehavare<sup>2</sup>

<sup>1</sup> Faktureringskontakten för EA-kontraktet har den här rollen.

<sup>2</sup> Det går inte att lägga till eller ändra faktureringskontakten på Azure EA-portalen. Den läggs till i EA-registreringen baserat på användaren som anges som faktureringskontakt på avtalsnivå. Om du vill ändra faktureringskontakten måste en begäran göras via en partner eller programvarurådgivare till det regionala driftcentret (ROC, Regional Operations Center).

De här rollerna gäller särskilt för hantering av Azure Enterprise-avtal och används utöver de fördefinierade roller som används till att styra åtkomsten till resurser i Azure. Mer information finns i [Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

I följande avsnitt beskrivs begränsningarna och funktionerna för varje roll.

## <a name="user-limit-for-admin-roles"></a>Användargräns för administratörsroller

|Roll| Användargräns|
|---|---|
|Företagsadministratör|Obegränsat|
|Företagsadministratör (skrivskyddad)|Obegränsat|
|Avdelningsadministratör|Obegränsat|
|Avdelningsadministratör (skrivskyddad)|Obegränsat|
|Kontoägare|1 per konto<sup>3</sup>|

<sup>3</sup> Varje konto måste ha ett unikt Microsoft-konto, arbetskonto eller skolkonto.

## <a name="organization-structure-and-permissions-by-role"></a>Organisationsstruktur och behörigheter per roll

|Aktiviteter| Företagsadministratör|Företagsadministratör (skrivskyddad)|Avdelningsadministratör|Avdelningsadministratör (skrivskyddad)|Kontoägare| Partner|
|---|---|---|---|---|---|---|
|Visa företagsadministratörer|✔|✔|✘|✘|✘|✔|
|Lägga till eller ta bort företagsadministratörer|✔|✘|✘|✘|✘|✘|
|Visa meddelandekontakter<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Lägga till eller ta bort meddelandekontakter<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Skapa och hantera avdelningar |✔|✘|✘|✘|✘|✘|
|Visa avdelningsadministratörer|✔|✔|✔|✔|✘|✔|
|Lägga till eller ta bort avdelningsadministratörer|✔|✘|✔|✘|✘|✘|
|Visa konton i registreringen |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Lägga till konton i registreringen och ändra kontoägare|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Skapa och hantera prenumerationer och prenumerationsbehörigheter|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Meddelandekontakter får e-postmeddelanden om Azure Enterprise-avtalet.
- <sup>5</sup> Uppgiften är begränsad till konton på din avdelning.

## <a name="add-a-new-enterprise-administrator"></a>Lägga till en ny företagsadministratör

Företagsadministratörer har fullständig behörighet i hanteringen av en Azure EA-registrering. Den första Azure EA-administratören skapades när EA-avtalet tecknades. Du kan dock när som helst lägga till och ta bort administratörer. Det är bara befintliga administratörer som kan lägga till nya administratörer. Du kan läsa mer om att lägga till fler företagsadministratörer i [Skapa en ny företagsadministratör](ea-portal-get-started.md#create-another-enterprise-administrator). Mer information om roller och uppgifter för faktureringsprofiler finns i [Roller och uppgifter för faktureringsprofiler](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>Uppdatera kontoinnehavares status från väntande till aktiv

När nya kontoinnehavare först läggs till i en Azure EA-registrering är deras status _väntande_. När en ny kontoinnehavare får sitt aktiveringsmail kan kontoinnehavaren logga in och aktivera sitt konto. När kontoinnehavaren aktiverar sitt konto uppdateras kontostatusen från _väntande_ till _aktiv_. Kontoinnehavaren måste läsa varningsmeddelandet och välja **Fortsätt**. Nya användare kan uppmanas att ange sitt för- och efternamn och skapa ett handelskonto. I så fall måste personen ange den nödvändiga informationen innan kontot aktiveras.

## <a name="add-a-department-admin"></a>Lägga till en avdelningsadministratör

När en Azure EA-administratör skapar en avdelning så kan Azure-företagsadministratören lägga till avdelningsadministratörer och koppla dem till en avdelning. En avdelningsadministratör kan skapa nya konton. Nya konton behövs till att skapa Azure EA-prenumerationer.

Du kan läsa mer om att lägga till en avdelningsadministratör under [Skapa en Azure EA-avdelningsadministratör](ea-portal-get-started.md#add-a-department-administrator).

## <a name="usage-and-costs-access-by-role"></a>Åtkomst till användning och kostnader per roll

|Aktiviteter| Företagsadministratör|Företagsadministratör (skrivskyddad)|Avdelningsadministratör|Avdelningsadministratör (skrivskyddad) |Kontoägare| Partner|
|---|---|---|---|---|---|---|
|Visa kreditsaldo inklusive ekonomiska åtaganden|✔|✔|✘|✘|✘|✔|
|Visa avdelningens utgiftskvot|✔|✔|✘|✘|✘|✔|
|Ange avdelningens utgiftskvot|✔|✘|✘|✘|✘|✘|
|Visa organisationens EA-prisdokument|✔|✔|✘|✘|✘|✔|
|Visa information om användning och kostnader|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Hantera resurser i Azure-portalen|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> Kräver att företagsadministratören aktiverar policyn **Visa avgifter för DA** på Enterprise-portalen. Avdelningsadministratören kan sedan se kostnadsinformation för avdelningen.
- <sup>7</sup> Kräver att företagsadministratören aktiverar policyn **Visa avgifter för AO** på Enterprise-portalen. Kontoägaren kan sedan se kostnadsinformation för kontot.

## <a name="see-pricing-for-different-user-roles"></a>Visa prissättning för olika användarroller

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
|Ingen|Inte tillämpligt |Ägare|Återförsäljarpris|

Du ställer in administratörsroller för Enterprise och visar policyer för avgifter i Enterprise-portalen. RBAC-roller kan uppdateras i Azure-portalen. Mer information finns i [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md).



## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomst till faktureringsinformation för Azure](manage-billing-access.md)
- [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md)
- [Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md)
