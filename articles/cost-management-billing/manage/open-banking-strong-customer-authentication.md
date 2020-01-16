---
title: Open Banking (PSD2) och Strong Customer Authentication (SCA) för Azure-kunder
description: I den här artikeln förklaras varför multifaktorautentisering krävs för vissa Azure-köp och hur du genomför autentiseringen.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: aff5e1e707980c9a63988c22653fa5fba0fc236b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75996045"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Open Banking (PSD2) och Strong Customer Authentication (SCA) för Azure-kunder

Från den 14 september 2019 måste alla banker i de 31 länder som ingår i det [Europeiska ekonomiska samarbetsområdet](https://en.wikipedia.org/wiki/European_Economic_Area) verifiera identiteten av en person som utför ett köp via internet innan betalningen kan bearbetas. Den här verifieringen kräver multifaktorautentisering för att se till att dina köp via internet är säkra och skyddade. Datumet då det här verifieringskravet införs kommer att senareläggas i vissa länder. Mer information finns i [Microsofts vanliga frågor och svar om PSD2](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>Vad PSD2 innebär för Azure-kunder

Om du betalar för Azure med ett kreditkort som utfärdats av en bank inom det [Europeiska ekonomiska samarbetsområdet](https://en.wikipedia.org/wiki/European_Economic_Area) kan du behöva använda multifaktorautentisering för att betala med ditt konto. Du uppmanas att använda multifaktorautentisering när du registrerar dig för ett Azure-konto eller uppgraderar ditt Azure-konto, även om du inte utför ett köp när du gör detta. Du kan också uppmanas att använda multifaktorautentisering när du ändrar betalningsmetoden för ditt Azure-konto, tar bort din utgiftsgräns eller utför en betalning från Microsoft Azure-portalen, till exempel om du kvitterar utestående saldon eller köper Azure-krediter.

Om din bank avvisar dina månatliga Azure-debiteringar får du ett e-postmeddelande om att förfallodatumet har passerat från Azure med instruktioner för att åtgärda problemet. Du kan använda multifaktorautentisering för att betala dina utestående debiteringar i Microsoft Azure-portalen.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Slutför multifaktorautentisering i Microsoft Azure-portalen

Följande avsnitt beskriver hur du använder multifaktorautentisering i Microsoft Azure-portalen. Följ instruktionerna som gäller dig.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Ändra aktiv betalningsmetod för ditt Azure-konto

Du kan ändra den aktiva betalningsmetoden för ditt Azure-konto genom att genomföra följande steg:

1. Logga in på [Microsoft Azure-portalen](https://portal.azure.com) som kontoadministratör och gå till **Kostnadshantering och fakturering**.
2. På sidan **Översikt** väljer du prenumeration från rutnätet **Mina prenumerationer**.
3. Under ”Fakturering” väljer du **Betalningsmetoder**. Du kan lägga till ett nytt kreditkort eller ange ett befintligt kort som den aktiva betalningsmetoden för prenumerationen. Om din bank kräver multifaktorautentisering uppmanas du att slutföra en autentisering under processen.

Mer information hittar du i [Lägga till, uppdatera eller ta bort ett kreditkort för Azure](change-credit-card.md).

### <a name="settle-outstanding-charges-for-azure-services"></a>Betala utestående debiteringar för Azure-tjänster

Om din bank avvisar debiteringarna ändras statusen för ditt Azure-konto till **Förfallodatum har passerat** i Microsoft Azure-portalen. Du kan kontrollera ditt kontos status genom att utföra följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som kontoadministratör.
2. Sök efter **Kostnadshantering och fakturering.**
3. På sidan **Cost Management + fakturerings** **Översikt** granskar du kolumnen Status i rutnätet **Mina prenumerationer** .
4. Om din prenumeration har statusen **Förfallodatum har passerat** klickar du på **Reglera saldo**. Du uppmanas att använda multifaktorautentisering under processen.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Betala utestående avgifter för Marketplace och reservationsinköp

Köp på Marketplace och reservationsinköp faktureras separat från Azure-tjänster. Om din bank avvisar debiteringarna för Marketplace eller dina reservationsinköp ändras din faktureringsstatus till **Förfallodatum har passerat** i Microsoft Azure-portalen. Du kan kontrollera status för dina Marketplace- och reservationsfakturor genom att utföra följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som kontoadministratör.
2. Sök efter **Kostnadshantering och fakturering.**
3. Under ”Fakturering” väljer du **Fakturor**.
4. Klicka på fliken **Azure Marketplace och reservationer** till höger.
5. Välj prenumeration.
6. Granska statuskolumnen i rutnätet Fakturor. Om status för fakturan är **Ska betalas** eller **Förfallodatum har passerat** klickar du på **Betala nu.** Du uppmanas att använda multifaktorautentisering under processen.

## <a name="next-steps"></a>Nästa steg
- Läs [Lös passerat betalningsdatum för din Azure-prenumeration](resolve-past-due-balance.md) om du behöver betala en Azure-faktura.
