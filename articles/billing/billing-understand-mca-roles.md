---
title: Förstå fakturering administrativa roller för Microsoft-avtal för kund - Azure
description: Läs mer om fakturering roller för konton i Azure-fakturering för Microsoft kundens avtal.
author: amberbhargava
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 12e492fee95192d0af49ff7b6a5be3144398fef8
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490430"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Förstå Microsoft kundavtal administrativa roller i Azure

Hantera ditt faktureringskonto för en Microsoft-kundavtal med rollerna som beskrivs i följande avsnitt. Dessa roller måste vara uppfyllda utöver de inbyggda rollerna som Azure har för att styra åtkomsten till resurser. Mer information finns i [inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md).

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. Kontrollera om du har åtkomst till en Microsoft-kundavtal.

## <a name="billing-role-definitions"></a>Fakturering rolldefinitioner

I följande tabell beskrivs fakturering roller som du använder för att hantera ditt faktureringskonto fakturering profiler och fakturera avsnitt.

|Role|Beskrivning|
|---|---|
|Fakturering Kontoägare|Hantera allt för fakturering konto|
|Fakturerings-kontodeltagare|Hantera allt utom behörigheterna på faktureringskontot|
|Faktureringsläsare för konto|Skrivskyddad vy av allt på fakturering konto|
|Fakturering profilens ägare|Hantera allt för fakturering profil|
|Profil faktureringsdeltagare|Hantera allt utom behörigheterna på fakturering profilen|
|Faktureringsläsare profil|Skrivskyddad vy av allt på fakturering profil|
|Faktura manager|Visa och betala fakturor för fakturering profil|
|Faktura avsnittet ägare|Hantera allt på fakturan avsnittet|
|Faktura avsnittet deltagare|Hantera allt, men behörigheter i avsnittet faktura|
|Faktura avsnittet läsare|Skrivskyddad vy av allt i avsnittet faktura|
|Azure-prenumeration skapare|Skapa Azure-prenumerationer|

## <a name="billing-account-roles-and-tasks"></a>Fakturering kontoroller och uppgifter

Ett faktureringskonto kan du hantera faktureringen för din organisation. Du kan använda faktureringskonto för att organisera kostnader, övervaka kostnader och fakturor och fakturering åtkomstkontroll för din organisation. Mer information finns i [förstå faktureringskonto](billing-mca-overview.md#your-billing-account).

Följande tabeller visar vilken roll som du behöver utföra uppgifter i samband med faktureringskontot.

### <a name="manage-billing-account-permissions-and-properties"></a>Hantera behörighet för fakturering och egenskaper

|Aktivitet|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto|
|---|---|---|---|
|Visa befintliga behörigheter för fakturering konto|✔|✔|✔|
|Ge andra användare behörighet att visa och hantera faktureringskontot|✔|✘|✘|
|Visa fakturering Kontoegenskaper som företagets namn och adress|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Hantera fakturering profiler för fakturering konto

|Aktivitet|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto|
|---|---|---|---|
|Visa alla fakturering profiler i kontot|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Hantera fakturor för fakturering konto

|Aktivitet|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto|
|---|---|---|---|
|Visa alla fakturor i kontot|✔|✔|✔|
|Ladda ned fakturor, Azure files för användning och avgifter, prisdokument och skatt dokument i kontot|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Hantera faktura avsnitt för faktureringskonto

|Aktivitet|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto|
|---|---|---|---|
|Visa alla faktura-avsnitt i kontot|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Hantera transaktioner för fakturering konto

|Aktivitet|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto|
|---|---|---|---|
|Visa alla fakturering transaktioner för kontot|✔|✔|✔|
|Visa alla produkter som har köpt för kontot|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Hantera prenumerationer för fakturering konto

|Aktivitet|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto|
|---|---|---|---|
|Visa alla Azure-prenumerationer i fakturerings-konto|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Fakturering profil roller och uppgifter

En profil för fakturering kan du hantera dina fakturor och betalningsmetoder. En månadsfaktura genereras för Azure-prenumerationer och andra produkter som har köpt med hjälp av fakturering profilen. Du kan använda metoderna betalningar för att betala fakturan. Mer information finns i [förstå fakturering profiler](billing-mca-overview.md#billing-profiles).

Följande tabeller visar vilken roll som du behöver utföra uppgifter i samband med fakturering profilen.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Hantera fakturering behörighet, egenskaper och principer

|Aktivitet|Fakturering profilens ägare|Profil faktureringsdeltagare|Faktureringsläsare profil|Faktura Manager|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto
|---|---|---|---|---|---|---|---|
|Visa befintliga behörigheter för fakturerings-profilen|✔|✔|✔|✔|✔|✔|✔|
|Ge andra användare behörighet att visa och hantera faktureringen profilen|✔|✘|✘|✘|✘|✘|✘|
|Visa fakturering profilegenskaper som PO tal, faktura till inställningar för e-post och mycket mer|✔|✔|✔|✔|✔|✔|✔|
|Uppdatera fakturering profilegenskaper |✔|✔|✘|✘|✘|✘|✘|
|Visa principer som tillämpas på fakturering profilen som aktiverar Azure reservation inköp, aktivera Azure marketplace-köp och mycket mer|✔|✔|✔|✔|✔|✔|✔|
|Tillämpa principer på fakturering profilen |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Hantera fakturor för fakturering profil

|Aktivitet|Fakturering profilens ägare|Profil faktureringsdeltagare|Faktureringsläsare profil|Faktura Manager|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto
|---|---|---|---|---|---|---|---|
|Visa alla fakturor för fakturerings-profilen|✔|✔|✔|✔|✔|✔|✔|
|Ladda ned fakturor, Azure files för användning och avgifter, prisdokument och skatt dokument för fakturerings-profilen|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Hantera faktura avsnitt för fakturering profilen

|Aktivitet|Fakturering profilens ägare|Profil faktureringsdeltagare|Faktureringsläsare profil|Faktura Manager|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto
|---|---|---|---|---|---|---|---|
|Visa alla faktura avsnitt för fakturerings-profilen|✔|✔|✔|✔|✔|✔|✔|
|Skapa nytt faktura avsnitt för fakturerings-profilen|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Hantera transaktioner för fakturering profil

|Aktivitet|Fakturering profilens ägare|Profil faktureringsdeltagare|Faktureringsläsare profil|Faktura Manager|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto
|---|---|---|---|---|---|---|---|
|Visa alla fakturering transaktioner för fakturerings-profilen|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Hantera betalningsmetoder för fakturering profil

|Aktivitet|Fakturering profilens ägare|Profil faktureringsdeltagare|Faktureringsläsare profil|Faktura Manager|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto
|---|---|---|---|---|---|---|---|
|Visa betalningsmetoder för fakturerings-profilen|✔|✔|✔|✔|✔|✔|✔|
|Spåra Azure-krediter saldo för fakturerings-profilen|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Hantera prenumerationer för fakturering profil

|Aktivitet|Fakturering profilens ägare|Profil faktureringsdeltagare|Faktureringsläsare profil|Faktura Manager|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto
|---|---|---|---|---|---|---|---|
|Visa alla Azure-prenumerationer för fakturerings-profilen|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Faktura avsnittet roller och uppgifter

En faktura-avsnittet kan du organisera kostnader på din faktura. Du kan skapa ett avsnitt för att organisera dina kostnader per avdelning, utvecklingsmiljö, eller baserat på organisationens behov. Ge andra användare behörighet att skapa Azure-prenumerationer för avsnittet. Alla avgifter för användning och köp i prenumerationer därefter visa på avsnittet i fakturan. Mer information finns i [förstå fakturan avsnittet](billing-mca-overview.md#invoice-sections).

Följande tabeller visar vilken roll som du behöver utföra uppgifter i samband med faktura avsnitt.

### <a name="manage-invoice-section-permissions-and-properties"></a>Hantera behörigheter för faktura-avsnittet och egenskaper

|Uppgifter|Faktura avsnittet ägare|Faktura avsnittet deltagare|Faktura avsnittet läsare|Azure-prenumeration skapare|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto | |
|---|---|---|---|---|---|---|---|---|
|Visa alla behörigheter på fakturan avsnittet|✔|✔|✔|✔|✔|✔|✔| |
|Ge andra användare behörighet att visa och hantera avsnittet faktura|✔|✘|✘|✘|✘|✘|✘| |
|Visa faktura Avsnittsegenskaper|✔|✔|✔|✔|✔|✔|✔| |
|Uppdatera egenskaper för faktura-avsnitt|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Hantera produkter för faktura-avsnitt

|Uppgifter|Faktura avsnittet ägare|Faktura avsnittet deltagare|Faktura avsnittet läsare|Azure-prenumeration skapare|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto
|---|---|---|---|---|---|---|---|
|Visa alla produkter som har köpt i avsnittet faktura|✔|✔|✔|✘|✔|✔|✔|
|Hantera faktureringen för produkter för faktura-avsnitt som Avbryt, inaktivera automatisk förnyelse och mycket mer|✔|✔|✘|✘|✘|✘|✘|
|Ändra faktura avsnittet för produkterna|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Hantera prenumerationer för faktura-avsnitt

|Uppgifter|Faktura avsnittet ägare|Faktura avsnittet deltagare|Faktura avsnittet läsare|Azure-prenumeration skapare|Fakturering Kontoägare|Fakturerings-kontodeltagare|Faktureringsläsare för konto
|---|---|---|---|---|---|---|---|
|Visa alla Azure-prenumerationer för faktura-avsnitt|✔|✔|✔|✘|✔|✔|✔|
|Ändra faktura avsnittet för prenumerationer|✔|✔|✘|✘|✘|✘|✘|
|Begära faktureringsägarskapet för prenumerationer från användare i andra fakturering konton|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Prenumeration fakturering roller och uppgifter

I följande tabell visar vilken roll som du behöver utföra uppgifter i samband med en prenumeration.

|Uppgifter|Faktura avsnittet ägare|Faktura avsnittet deltagare|Faktura avsnittet läsare|Azure-prenumeration skapare|
|---|---|---|---|---|
|Skapa Azure-prenumerationer|✔|✔|✘|✔|
|Uppdatera kostnadsställe för prenumerationen|✔|✔|✘|✘|
|Ändra faktura avsnittet för prenumerationen|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Hantera fakturering roller i Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. Välj **åtkomstkontroll (IAM)** i ett omfång som faktureringskonto, fakturering profil eller faktura avsnitt, där du vill ge åtkomst.

4. Sidan åtkomst för åtkomstkontroll (IAM) visar en lista över användare och grupper som är kopplade till varje roll för detta omfång.

   ![Skärmbild som visar listan över administratörer för faktureringskonto](./media/billing-understand-mca-roles/billing-list-admins.png)

5. Om du vill ge åtkomst till en användare, Välj **Lägg till** högst upp på sidan. Välj en roll i listrutan roll. Ange e-postadressen för användaren som du vill ge åtkomst. Välj **spara** du tilldela rollen.

   ![Skärmbild som visar att lägga till en administratör i en faktureringskonto](./media/billing-understand-mca-roles/billing-add-admin.png)

6. Välj användaren med den rolltilldelning som du vill ta bort om du vill ta bort åtkomst för en användare. Välj Ta bort.

   ![Skärmbild som visar borttagning av en administratör från ett faktureringskonto](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten
Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för att lära dig om ditt faktureringskonto:

- [Kom igång med ditt faktureringskonto för Microsoft kundavtal](billing-mca-overview.md)
- [Skapa en Azure-prenumeration för ditt faktureringskonto för Microsoft kundavtal](billing-mca-create-subscription.md)
