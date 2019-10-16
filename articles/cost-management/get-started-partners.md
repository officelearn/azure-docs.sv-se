---
title: Kom igång med Azure Cost Management för partner
description: I den här artikeln förklaras hur partners använder Azure Cost Management funktioner och hur de ger Cost Management åtkomst till sina kunder.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 9d95e23cf92c7ee98291831d60088d610c3e5c52
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377699"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Kom igång med Azure Cost Management för partner

Azure Cost Management är internt tillgängligt för partner som har publicerat sina kunder i ett kund avtal från Microsoft. I den här artikeln förklaras hur partners använder [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) -funktioner. Det beskriver också hur partners ger Cost Management åtkomst till sina kunder. CSP-kunder kan använda Cost Management funktioner när de aktive ras av deras CSP-partner.

CSP-partner använder Cost Management för att:

- Förstå fakturerade kostnader och koppla kostnaderna till kunden, prenumerationerna, resurs grupperna och tjänsterna.
- Få en intuitiv vy över Azure-kostnader i [kostnads analys](quick-acm-cost-analysis.md) med funktioner för att analysera kostnader efter kund, prenumeration, resurs grupp, resurs, mätare, tjänst och många andra dimensioner.
- Visa resurs kostnader som har partner intjänad kredit (PEC) som tillämpas på kostnads analys.
- Konfigurera meddelanden och automatisering med hjälp av programmatiska [budgetar](tutorial-acm-create-budgets.md) och aviseringar när kostnaderna överstiger budgetarna.
- Aktivera den Azure Resource Manager principen som ger kunden åtkomst till Cost Management data. Kunderna kan sedan Visa förbruknings kostnads data för sina prenumerationer med [betala per användning-pris](https://azure.microsoft.com/pricing/calculator/).

Alla funktioner som är tillgängliga i Azure Cost Management finns också i REST-API: er. Använd API: erna för att automatisera kostnads hanterings uppgifter.

## <a name="prerequisites"></a>Krav

Azure Cost Management kräver Läs behörighet till ditt fakturerings konto eller prenumeration. Åtkomst kan beviljas på vilken nivå som helst ovanför dina resurser, från fakturerings kontot eller en hanterings grupp till enskilda resurs grupper där du hanterar dina appar. För att prenumerations användare ska kunna se priser och kostnader måste åtkomsten till Visa avgifter vara aktive rad för ditt fakturerings konto. Mer information om hur du aktiverar och tilldelar åtkomst till Azure Cost Management finns i [tilldela åtkomst till data](assign-access-acm-data.md). Om du vill visa en fullständig lista över vilka konto typer som stöds, se [förstå Cost Management data](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Hur Cost Management använder omfång

Med omfattningar kan du hantera fakturerings data, ha roller som är begränsade till betalningar, Visa fakturor och utföra allmän konto hantering. Fakturerings-och konto roller hanteras separat från omfattningar som används för resurs hantering, som använder RBAC. För att tydligt särskilja syftet med de separata omfattningarna, inklusive åtkomst kontroll skillnaderna, kallas de för fakturerings omfång respektive RBAC-omfång.

Om du vill förstå fakturerings omfattningar och RBAC-scope och hur kostnads hantering fungerar med omfattningar, se [förstå och arbeta med omfattningar](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Hantera kostnader med partner klient fakturerings omfång

När du har publicerat dina kunder till ett Microsoft-kundavtal är följande _fakturerings omfattningar_ tillgängliga i din klient organisation. Använd omfattningarna för att hantera kostnader i Cost Management.

### <a name="billing-account-scope"></a>Fakturerings konto omfång

Använd fakturerings konto omfånget för att Visa kostnader för moms i alla dina kunder och fakturerings profiler. Du kan också Visa faktura kostnader för konsumtions produkter för kunder i Microsofts kund avtal. Faktura kostnader visas också för inköpta produkter för kunder i Microsofts kund avtal och CSP-erbjudandet. För närvarande är standard valutan för att Visa kostnader i omfånget US dollar. Budgetarna som angetts för omfånget är också i USD.

Oavsett kundens fakturerade valuta använder partners omfattningen för att ställa in budgetar och hantera kostnader i USD över sina kunder, prenumerationer, resurser och resurs grupper.

Partner filtrerar också kostnader i en bestämd fakturerings valuta för kunderna i vyn kostnads analys. Välj listan **faktisk kostnad** om du vill visa kostnader i kund fakturerings valutor som stöds.

![Exempel som visar val av faktiska kostnader för valutor](./media/get-started-partners/actual-cost-selector.png)

Använd vyn förbrukad [kostnad](quick-acm-cost-analysis.md#customize-cost-views) i fakturerings omfånget om du vill visa reserverade kostnader för reserverade instanser över en reservations period.

### <a name="billing-profile-scope"></a>Fakturerings profil omfång

Använd fakturerings profil omfattningen för att Visa förskotts kostnader i fakturerings valutan för alla dina kunder för alla produkter och prenumerationer som ingår i en faktura. Du kan filtrera kostnader i en fakturerings profil för en speciell faktura med **InvoiceID** -filtret. Filtret visar förbruknings-och produkt inköps kostnader för en speciell faktura. Du kan också filtrera kostnaderna för en specifik kund på fakturan för att se moms kostnader.

När du har publicerat kunderna till ett Microsoft-kundavtal får du en kund faktura som visar avgifter för berättigade och köpta produkter som SaaS, Azure Marketplace och reservationer. Vid fakturering i samma fakturerings valuta, visar fakturan även kund avgifter som inte ingår i det nya Microsofts kund avtal.

För att hjälpa till med kund fakturan kan du se alla kostnader som påförs för en faktura för kunderna. Precis som fakturan visar omfattningen kostnader för varje kund i det nya Microsofts kund avtal. Omfattningen visar också alla kostnader för kund rättighets produkter som fortfarande finns i det aktuella CSP-erbjudandet.

Fakturerings profilen och fakturerings konto omfången är de enda som visar avgifter för rättighets-och inköps produkter.

I fakturerings profiler definieras de prenumerationer som ingår i en faktura. Fakturerings profiler är den funktionella motsvarigheten till en företags avtals registrering. En registrering är det omfång som fakturor genereras. På samma sätt är inköp som inte används, till exempel Azure Marketplace och reservationer, endast tillgängliga i fakturerings profil omfånget.

För närvarande är kundens fakturerings valuta standard valuta när du visar kostnader i fakturerings profil omfånget. Budgetarna som anges i fakturerings profil omfånget finns i fakturerings valutan.

Partner kan använda omfånget för att stämma av fakturor. Och använder de omfånget för att ställa in budgetar i fakturerings valutan för en:

- Speciell filtrerad faktura
- Kund
- Prenumeration
- Resursgrupp
- Resurs
- Azure-tjänst
- Meter
- ResellerMPNID

### <a name="customer-scope"></a>Kund omfång

Partner använder omfånget för att hantera kostnader som är kopplade till kunder som har publicerats till Microsofts kund avtal. Med hjälp av omfånget kan partners Visa moms kostnader för en specifik kund. Du kan också filtrera förskotts kostnader för en speciell prenumeration, resurs grupp eller resurs.

Kund omfånget omfattar inte kunder som använder det aktuella CSP-erbjudandet. Rättighets kostnader, inte Azure-användning för aktuella CSP-erbjudanden, är tillgängliga för kunder med fakturerings konto och fakturerings profil när du tillämpar kund filtret.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Partner åtkomst till fakturerings omfattningar i Cost Management

Endast användare med rollen **Global administratör** och **administratör** kan hantera och Visa kostnader för fakturerings konton, fakturerings profiler och kunder direkt i partnerns Azure-klient. Mer information om Partner Center-roller finns i [Tilldela användar roller och behörigheter](/partner-center/permissions-overview).

### <a name="enable-cost-management-in-the-customer-tenant"></a>Aktivera kostnads hantering i kund klienten

Partner kan ge till gång till Cost Management när kunder har publicerats till ett Microsoft-kundavtal. Sedan kan partner sedan aktivera en princip som gör det möjligt för kunderna att visa sina kostnader som beräknas enligt åter betalnings takten enligt principen betala per användning. Kostnaderna visas i kundens fakturerings valuta för förbrukad användning i RBAC-prenumeration och resurs grupps omfång.

När principen för kostnads synlighet har Aktiver ATS av partnern kan alla användare med Azure Resource Manager åtkomst till prenumerationen hantera och analysera kostnader enligt priser enligt principen betala per användning. Åter försäljare och kunder som har lämplig RBAC-åtkomst till Azure-prenumerationer kan se kostnad.

Oavsett principen kan partner också se kostnaderna om de har åtkomst till prenumerationen och resurs gruppen.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Aktivera principen för att Visa användnings kostnader för Azure

Partner använder följande information för att aktivera principen för att Visa Azures användnings kostnader för sina kunder.

Logga in på partner klienten i Azure Portal och klicka på **Cost Management + fakturering**. Välj ett fakturerings konto och klicka sedan på **kunder**. Listan över kunder är kopplad till fakturerings kontot.

I listan över kunder väljer du den kund som du vill tillåta för att Visa kostnader.

![Välj kunder i Cost Management](./media/get-started-partners/customer-list.png)

Under **Inställningar**klickar du på **principer**.

Den aktuella kostnads Synlighets principen visas för **Azure Usage** -avgifter som är kopplade till prenumerationerna för den valda kunden.
![Policy så att kunderna kan se avgifterna betala per användning @ no__t-1

När principen är inställd på **Nej**är Azure Cost Management inte tillgänglig för prenumerations användare som är kopplade till kunden. Om den inte aktive ras av en partner är principen för kostnads synlighet inaktive rad som standard för alla prenumerations användare.

När kostnads principen är inställd på **Ja**kan prenumerations användare som är kopplade till kund innehavaren se användnings avgifter enligt priserna för betala per användning.

När principen för kostnads synlighet är aktive rad visar alla tjänster som har prenumerations användningen kostnader enligt priserna för betala per användning. Reservations användningen visas med noll kostnader för faktiska och periodiserade kostnader. Köp och rättigheter är inte kopplade till en speciell prenumeration. Därför visas inte inköpen i prenumerations omfånget.

Om du vill visa kostnader för kund klienten öppnar du Cost Management + fakturering och klickar sedan på fakturerings konton. Klicka på ett fakturerings konto i listan över fakturerings konton.

![Välj ett fakturerings konto](./media/get-started-partners/select-billing-account.png)

Klicka på **Azure-prenumerationer**under **fakturering**och klicka sedan på en kund.

![Välj en Azure-prenumerations kund](./media/get-started-partners/subscriptions-select-customer.png)

Klicka på **kostnads analys** och påbörja granskning av kostnader.
Kostnads analys, budgetar och aviseringar är nu tillgängliga för en prenumeration och en resurs grupp för RBAC-baserade kostnader enligt principen betala per användning.

![Visa kostnads analys som en kund ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Amortera vyer och faktiska kostnader för reserverade instanser i RBAC-omfången visar noll avgifter. Reserverade instans kostnader visas bara i fakturerings omfånget där inköpen gjordes.

## <a name="analyze-costs-in-cost-analysis"></a>Analysera kostnader i kostnads analys

Partner kan utforska och analysera kostnader i kostnads analys över kunder för en specifik kund eller för en faktura. Med funktionerna filtrera och gruppera efter kan du analysera kostnader med flera fält, inklusive:

| **Fält** | **Beskrivning** | **Motsvarande kolumn i Partner Center** |
| --- | --- | --- |
| PartnerTenantID | Identifierare för partnerns Azure Active Directory klient | Partner Azure Active Directory TenantID anropas som partner-ID. I GUID-format. |
| PartnerName | Namnet på partner Azure Active Directory klient organisation | Partners namn |
| CustomerTenantID | Identifierare för den Azure Active Directory klienten för kundens prenumeration | Kundens organisations-ID. Till exempel kund Azure Active Directory TenantID. |
| CustomerName | Namnet på den Azure Active Directory klienten som innehåller kundens prenumeration | Kundens organisations namn, enligt rapporter i Partner Center. Viktigt för att stämma av fakturan med din system information. |
| ResellerMPNID | MPNID för den åter försäljare som är associerad med prenumerationen | MPN-ID för åter försäljaren av posten för prenumerationen. Inte tillgängligt för den aktuella aktiviteten. |
| subscription ID | Unik identifierare som skapats av Microsoft för Azure-prenumerationen | Gäller inte |
| subscriptionName | Namnet på Azure-prenumerationen | Gäller inte |
| billingProfileID | Identifierare för fakturerings profilen. Den grupperar kostnader mellan fakturor i en enda fakturerings valuta för kunderna. | MCAPI partner fakturerings grupp-ID. Används i API-begäranden, men ingår inte i svar. |
| invoiceID | Faktura-ID på fakturan där den angivna transaktionen visas | Faktura nummer där den angivna transaktionen visas. |
| resourceGroup | Namnet på Azure-resurs gruppen. Används för resurs livs cykel hantering. | Namnet på resurs gruppen. |
| partnerEarnedCreditRate | Diskonterings ränta som används om det finns en partner som har tilldelats partner administratörs åtkomst. | Kostnaden för intjänad kredit för partner (PEC). Till exempel 0% eller 15%. |
| partnerEarnedCreditApplied | Visar om den intjänade partner krediten har tillämpats. | Gäller inte |

I vyn [kostnads analys](quick-acm-cost-analysis.md) kan du också [Spara vyer](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) och exportera data till CSV- [och PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis) -filer.

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Visa resurs kostnader för partner intjänade kredit (PEC)

I Azure Cost Management kan partner använda kostnads analys för att Visa kostnader som har fått PEC-förmånerna.

Logga in på partner klienten i Azure Portal och välj **Cost Management + fakturering**. Under **Cost Management**klickar du på **kostnads analys**.

I vyn kostnads analys visas kostnader för partnerns fakturerings konto. Välj **omfånget** efter behov för partnern, en specifik kund eller en fakturerings profil för att stämma av fakturor.

I ett ring diagram klickar du på list rutan och väljer **PartnerEarnedCreditApplied** för att gå vidare till PEC-kostnader.

![Exempel som visar hur du visar partner intjänad kredit](./media/get-started-partners/cost-analysis-pec1.png)

När **PartnerEarnedCreditApplied** -egenskapen är _True_, har den tillhör ande kostnaden nytta av partnerns intjänade administratörs åtkomst.

När **PartnerEarnedCreditApplied** -egenskapen har _värdet false_har den tillhör ande kostnaden inte uppnått nödvändig behörighet för krediten. Eller så är den köpta tjänsten inte berättigad till partner intjänad kredit.

Tjänst användnings data tar normalt 8-24 timmar innan de visas i Cost Management. Mer information finns i [uppdaterings frekvensen för användnings data varierar](understand-cost-mgt-data.md#usage-data-update-frequency-varies). PEC-krediter visas inom 48 timmar från tiden för åtkomst i Azure Cost Management.


Du kan också gruppera och filtrera efter egenskapen **PartnerEarnedCreditApplied** med alternativen **Gruppera efter** . Använd alternativen för att undersöka kostnader som är och inte har PEC.

![Gruppera eller filtrera efter partner-intjänad kredit](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>Cost Management REST-API: er

Partner, indirekta leverantörer och kunder kan använda Cost Management API: er som beskrivs i följande avsnitt för vanliga uppgifter.

### <a name="azure-cost-management-apis-for-partners"></a>Azure Cost Management-API: er för partner

Partner och användare som har åtkomst till fakturerings omfång i en partner klient organisation kan använda följande API: er.

#### <a name="to-get-a-list-of-billing-accounts"></a>Så här hämtar du en lista över fakturerings konton

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Så här hämtar du en lista över kunder

```
armclient get "providers/Microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>Hämta en lista över prenumerationer

```
armclient get "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-create-new-subscription"></a>Så här skapar du en ny prenumeration

```
armclient post "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub.json -verbose
```

#### <a name="to-get-or-download-usage-for-azure-services"></a>Hämta eller hämta användning för Azure-tjänster

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-get-a-list-of-billing-profiles"></a>Hämta en lista över fakturerings profiler

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/billingProfiles?api-version=2019-10-01-preview
```

#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Hämta och hämta pris dokumentet för förbrukade Azure-tjänster

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/BillingProfiles/JUT6-EU3Q-BG7-TGB/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>För att få kund kostnader under de senaste två månaderna, sorterade efter månad

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>För att få prenumerations kostnader för Azure under de senaste två månaderna, sorterade efter månad

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>Så här hämtar du dagliga kostnader för den aktuella månaden

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Så här hämtar du principen för kunder att Visa kostnader

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Så här ställer du in principen för kunder för att Visa kostnader

```
armclient put "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview" @policy.json
```

### <a name="azure-cost-management-apis-for-indirect-providers"></a>Azure Cost Management-API: er för indirekta leverantörer

Indirekta leverantörer med åtkomst till RBAC-scope i en kund klient organisation kan använda följande API: er. Kom igång genom att logga in som användare eller med ett huvud namn för tjänsten.

#### <a name="to-get-the-billing-account-information"></a>Så här hämtar du information om fakturerings kontot

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Så här hämtar du en lista över kunder

```
armclient get "providers/Microsoft.billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-resellers-associated-with-the-customer"></a>Så här hämtar du en lista över åter försäljare som är associerade med kunden

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db?api-version=2019-10-01-preview&$expand=resellers
```

#### <a name="to-get-a-list-of-subscriptions-with-reseller-information"></a>Hämta en lista över prenumerationer med åter försäljarens information

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-create-a-subscription"></a>Så här skapar du en prenumeration

```
armclient post "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub_reseller.json
```

### <a name="azure-cost-management-apis-for-customers"></a>Azure Cost Management-API: er för kunder

Kunder kan använda följande information för att få åtkomst till API: erna. Kom igång genom att logga in som en användare.

#### <a name="to-get-or-download-azure-consumption-usage-information-with-retail-rates"></a>Hämta och hämta användnings information om Azure förbrukning med åter försäljare

```
armclient post /subscriptions/66bada28-271e-4b7a-aaf5-c0ead63923d7/providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

## <a name="next-steps"></a>Nästa steg
- [Börja analysera kostnader](quick-acm-cost-analysis.md) i Cost Management
- [Skapa och hantera budgetar](tutorial-acm-create-budgets.md) i Cost Management
