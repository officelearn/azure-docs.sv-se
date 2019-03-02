---
title: Kom igång med ditt faktureringskonto för en Microsoft-kundavtal – Azure | Microsoft Docs
description: Förstå faktureringskonto för en Microsoft-kundavtal
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: c263669d7f74d8abebcd84e818ae2ccf2dcac0f0
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249247"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Kom igång med ditt faktureringskonto för en Microsoft-kundavtal

Ett faktureringskonto skapas för varje avtal som du loggar med Microsoft att använda Azure. Du kan använda ditt faktureringskonto för att hantera fakturerings- och spåra kostnader. Du kan ha åtkomst till flera konton för fakturering. Exempelvis kan kanske du har registrerat dig för Azure för dina personliga projekt. Du kan också ha åtkomst till Azure via din organisations Enterprise-avtal eller Microsoft kundavtal. För var och en av dessa scenarier behöver du en separat faktureringskonto.

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="understand-billing-account"></a>Förstå faktureringskonto

Ditt faktureringskonto för Microsoft-kundavtal innehåller en eller flera fakturering profiler. Varje fakturering profil har sin egen faktura och betalning metoder. Fakturering profilen innehåller ett eller flera faktura-avsnitt som kan du ordna kostnaderna i fakturering profilens fakturan.

Följande diagram visar relationen mellan en faktureringskonto, profiler för fakturering och faktura avsnitt.

![Diagram som visar fakturering hierarkin för Microsoft kundavtal](./media/billing-mca-overview/mca-billing-hierarchy.png)

Roller på faktureringskontot ha den högsta nivån av behörigheter. Tilldela dessa roller till användare som behöver visa fakturor och spåra kostnader för hela organisationen som finans- eller IT-chefer. Mer information finns i [fakturering kontoroller och uppgifter](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="understand-billing-profiles"></a>Förstå fakturering profiler

Använda en profil för fakturering för att hantera din faktura och betalning metoder. En månadsfaktura genereras för Azure-prenumerationer och andra produkter som har köpt med hjälp av fakturering profilen. Du kan använda metoderna betalningar för att betala fakturan.

En profil för fakturering skapas automatiskt för ditt faktureringskonto. Du kan skapa nya fakturering profiler för att ställa in ytterligare fakturor. Exempelvis kan du olika fakturor för varje avdelning eller projekt i din organisation.

Du kan också skapa faktura avsnitt för att organisera kostnader på en fakturering profil faktura. Avgifter för Azure-prenumerationer och produkter som har köpts för en faktura avsnittet som visas i avsnittet. Fakturering profilens faktura visar avgifter som för alla faktura avsnitt.

Roller på fakturering profiler har behörighet att visa och hantera fakturor och betalningsmetoder. Tilldela dessa roller till användare som betalar fakturor som medlemmar i ett redovisningsteam i din organisation. Mer information finns i [fakturering profil roller och uppgifter](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>Månadsfaktura genereras för varje fakturering profil

En månadsfaktura genereras på fakturadatum för varje fakturering profil. Fakturan innehåller alla avgifter för föregående månad.

Du kan visa fakturan, hämta dokument och ändrar inställningen för att hämta framtida fakturor via e-post i Azure-portalen. Mer information finns i [får fakturan i e-post](billing-download-azure-invoice-daily-usage-date.md#get-your-invoice-in-email-pdf).

### <a name="invoices-paid-through-payment-methods"></a>Fakturor betalda via betalningsmetoder

Varje fakturering profil har sin egen betalningsmetoder som används för att betala sina fakturor. Följande betalningsmetoder stöds:

| Type             | Definition  |
|------------------|-------------|
|Azure-krediter    |  Krediter tillämpas automatiskt på datamängd som faktureras på din faktura för att beräkna den mängd som du behöver betala. Mer information finns i [spåra Azure-saldot för din faktureringsinformation profil](billing-mca-check-azure-credits-balance.md). |
|Check eller banköverföring | Du kan betala det förfallna beloppet för din faktura överföra genom kontroll eller under överföring. Instruktioner för betalning återfinns på fakturan |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>Kontrollera Azure Marketplace och Reservation inköp genom att tillämpa principer

Använda principer för att styra köp som görs med hjälp av en profil för fakturering. Du kan ange principer för att inaktivera köper Azure-reservationer och produkter för Marketplace. Prenumerationer som skapats för faktura-avsnitt i profilen för fakturering kan inte användas för att köpa Azure-reservationer och produkter för Marketplace när principerna tillämpas.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Tillåt användare att skapa Azure-prenumerationer genom att aktivera Azure-planer

Azure planer aktiveras automatiskt när du skapar en profil för fakturering. Alla faktura-avsnitt i fakturering profilen får åtkomst till dessa prenumerationer. Användare med åtkomst till faktura avsnitt använder planerna för att skapa Azure-prenumerationer. De kan inte skapa Azure-prenumerationer, såvida inte en Azure-prenumeration aktiveras för fakturerings-profilen. Följande Azure-prenumerationer stöds i fakturering konton för Microsoft kundavtal:

| Planera             | Definition  |
|------------------|-------------|
|Microsoft Azure Plan   | Tillåt användare att skapa prenumerationer som kan köra alla arbetsbelastningar. Mer information finns i [planera för Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Microsoft Azure-Plan för utveckling och testning | Tillåt Visual Studio-prenumeranter skapa prenumerationer som är begränsad för utveckling eller testning arbetsbelastningar. Dessa prenumerationer får förmåner, till exempel lägre pris och åtkomst till exklusiva avbildningar i Azure-portalen. Mer information finns i [Microsoft Azure planera för DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)| <!--- TODO - Add the link to plan details page -->

## <a name="understand-invoice-sections"></a>Förstå fakturan avsnitt

Skapa faktura avsnitt för att organisera kostnader på en fakturering profil faktura. Till exempel behöva en enda faktura för din organisation och sedan vill ordna kostnader per avdelning, team eller projekt. I det här scenariot har du en enskild faktureringen profil där du skapar en faktura avsnitt för varje avdelning, team eller projekt.

När en faktura-avsnittet har skapats kan ge du andra användare behörighet att skapa Azure-prenumerationer för avsnittet. Eventuella avgifter för användning och inköp för prenumerationerna visas sedan i aktuellt avsnitt på fakturan.

Roller i avsnittet faktura har behörighet att kontrollera vem som skapar Azure-prenumerationer. Tilldela dessa roller till användare som ställer in Azure-miljön för team i företaget som engineering leads och tekniska arkitekter. Mer information finns i [faktura avsnittet roller och uppgifter](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för att lära dig om ditt faktureringskonto:

- [Förstå Microsoft kundavtal administrativa roller i Azure](billing-understand-mca-roles.md)
- [Skapa en Azure-prenumeration för ditt faktureringskonto för Microsoft kundavtal](billing-mca-create-subscription.md)
- [Organisera kostnaderna med faktura avsnitt](billing-mca-section-invoice.md)