---
title: Kom igång med ditt Microsoft-kundavtal faktureringskonto - Azure
description: Förstå din Microsoft-kundavtal fakturering konto
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490739"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Kom igång med ditt Microsoft-kundavtal faktureringskonto

Ett faktureringskonto skapas när du registrerar dig att använda Azure. Du använder ditt faktureringskonto för att hantera fakturor, betalningar och spåra kostnader. Du kan ha åtkomst till flera konton för fakturering. Exempelvis kan kanske du har registrerat dig för Azure för dina personliga projekt. Du kan också ha åtkomst till Azure via din organisations Enterprise-avtal eller Microsoft kundavtal. För var och en av dessa scenarier behöver du en separat faktureringskonto.

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Ditt faktureringskonto

Ditt faktureringskonto för Microsoft-kundavtal innehåller en eller flera fakturering profiler som låter dig hantera dina fakturor och betalningsmetoder. Varje fakturering profil innehåller en eller flera faktura-avsnitt som kan du ordna kostnaderna i fakturering profilens fakturan.

Följande diagram visar relationen mellan en faktureringskonto och fakturering profiler faktura avsnitt.

![Diagram som visar hur Microsoft kundavtal fakturering hierarki](./media/billing-mca-overview/mca-billing-hierarchy.png)

Roller på faktureringskontot ha den högsta nivån av behörigheter. Som standard bara den användare som registrerat sig för Azure får åtkomst till faktureringskontot. Dessa roller ska tilldelas till användare som behöver visa fakturor och spåra kostnader för hela organisationen som finans- eller IT-chefer. Mer information finns i [fakturering kontoroller och uppgifter](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Fakturering profiler

Använda en profil för fakturering för att hantera din faktura och betalning metoder. En månadsfaktura genereras i början av månaden för varje fakturering profil i ditt konto. Fakturan innehåller respektive kostnaden för alla Azure-prenumerationer och andra inköp från föregående månad.

En profil för fakturering skapas automatiskt för ditt faktureringskonto. Den innehåller ett faktura avsnitt som standard. Du kan skapa ytterligare avsnitt om du vill spåra och organisera utifrån dina behov om är per projekt, avdelning eller utvecklingsmiljö. Dessa avsnitt visas på fakturering profilens faktura återger användningen av varje prenumeration och inköp som du har tilldelat till den.

Roller på fakturering profiler har behörighet att visa och hantera fakturor och betalningsmetoder. Tilldela dessa roller till användare som betalar fakturor som medlemmar i ett redovisningsteam i din organisation. Mer information finns i [fakturering profil roller och uppgifter](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Varje fakturering profil hämtar en månadsfaktura

En månadsfaktura genereras i början av månaden för varje fakturering profil. Fakturan innehåller alla kostnader från föregående månad.

Du kan visa fakturan, hämta dokument och ändrar inställningen för att hämta framtida fakturor via e-post i Azure-portalen. Mer information finns i [ladda ner fakturor för en Microsoft-kundavtal](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Faktura betalningsmetoder

Varje fakturering profil har sin egen betalningsmetoder som används för att betala sina fakturor. Följande betalningsmetoder stöds:

| Type             | Definition  |
|------------------|-------------|
|Azure-krediter    |  Krediter tillämpas automatiskt på berättigade avgifter på fakturan, vilket minskar den mängd som du behöver betala. Mer information finns i [spåra Azure saldot för din faktureringsinformation profil](billing-mca-check-azure-credits-balance.md). |
|Kontrollera/banköverföring överföring | Om ditt konto har godkänts för betalning via Kontrollera/banköverföring. Du kan betala på grund av för din faktura via Kontrollera/banköverföring. Instruktioner för betalning återfinns på fakturan |
|Kreditkort | Kunder som registrerar sig för Azure via Azure-webbplatsen kan betala med kreditkort. |

### <a name="apply-policies-to-control-purchases"></a>Tillämpa principer för att styra inköp

Tillämpa principer för åtkomstkontroll Azure Marketplace och Reservation inköp med en profil för fakturering. Du kan ange principer för att inaktivera köper Azure-reservationer och produkter för Marketplace. Prenumerationer som faktureras för fakturering profilen kan inte användas för att göra dessa inköp när principerna tillämpas.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure planer fastställa pris- och servicenivåavtal för prenumerationer

Azure planer avgöra priset och serviceavtal för Azure-prenumerationer. De aktiveras automatiskt när du skapar en profil för fakturering. Alla faktura avsnitt som är associerade med fakturering profilen kan använda dessa planer. Användare med åtkomst till faktura avsnitt använder planerna för att skapa Azure-prenumerationer. Följande Azure-prenumerationer stöds i fakturering konton för Microsoft kundavtal:

| Planera             | Definition  |
|------------------|-------------|
|Microsoft Azure Plan   | Tillåt användare att skapa prenumerationer som kan köra alla arbetsbelastningar. Mer information finns i [planera för Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Microsoft Azure-Plan för utveckling och testning | Tillåt Visual Studio-prenumeranter skapa prenumerationer som är begränsad för utveckling eller testning arbetsbelastningar. Dessa prenumerationer får förmåner, till exempel lägre pris och åtkomst till exklusiva avbildningar i Azure-portalen. Mer information finns i [Microsoft Azure planera för DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="invoice-sections"></a>Faktura-avsnitt

Skapa faktura avsnitt för att organisera kostnader på din faktura. Till exempel behöva en enda faktura för din organisation och sedan vill ordna kostnader per avdelning, team eller projekt. I det här scenariot har du en enskild faktureringen profil där du skapar en faktura avsnitt för varje avdelning, team eller projekt.

När en faktura-avsnittet har skapats kan ge du andra användare behörighet att skapa Azure-prenumerationer som faktureras till avsnitt. Alla avgifter för användning och inköp för prenumerationerna som faktureras sedan till avsnittet.

Roller i avsnittet faktura har behörighet att kontrollera vem som skapar Azure-prenumerationer. Tilldela dessa roller till användare som ställer in Azure-miljön för team i företaget som engineering leads och tekniska arkitekter. Mer information finns i [faktura avsnittet roller och uppgifter](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för att lära dig om ditt faktureringskonto:

- [Förstå Microsoft kundavtal administrativa roller i Azure](billing-understand-mca-roles.md)
- [Skapa en ytterligare Azure-prenumeration för Microsoft kundavtal](billing-mca-create-subscription.md)
- [Skapa avsnitt på din faktura för att organisera dina kostnader](billing-mca-section-invoice.md)
