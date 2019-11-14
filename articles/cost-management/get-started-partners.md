---
title: Kom igång med Azure Cost Management för partner
description: I den här artikeln förklaras hur partners använder Azure Cost Management funktioner och hur de ger Cost Management åtkomst till sina kunder.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: cd3efbea7b194da54bc1d9bebd1cc77987bd9dea
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072341"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Kom igång med Azure Cost Management för partner

Azure Cost Management är internt tillgängligt för partner som har publicerat sina kunder på ett Microsoft-kundavtal och har köpt en Azure-prenumeration. I den här artikeln förklaras hur partners använder [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) -funktioner. Det beskriver också hur partners ger Cost Management åtkomst till sina kunder. Kunder kan använda Cost Management funktioner när de aktive ras av sin CSP-partner.

CSP-partner använder Cost Management för att:

- Förstå fakturerade kostnader och koppla kostnaderna till kunden, prenumerationerna, resurs grupperna och tjänsterna.
- Få en intuitiv vy över Azure-kostnader i [kostnads analys](quick-acm-cost-analysis.md) med funktioner för att analysera kostnader efter kund, prenumeration, resurs grupp, resurs, mätare, tjänst och många andra dimensioner.
- Visa resurs kostnader som har partner intjänad kredit (PEC) som tillämpas på kostnads analys.
- Konfigurera meddelanden och automatisering med hjälp av programmatiska [budgetar](tutorial-acm-create-budgets.md) och aviseringar när kostnaderna överstiger budgetarna.
- Aktivera den Azure Resource Manager principen som ger kunden åtkomst till Cost Management data. Kunderna kan sedan Visa förbruknings kostnads data för sina prenumerationer med [betala per användning-pris](https://azure.microsoft.com/pricing/calculator/).

Här är ett exempel som visar kostnader för alla kunder.
![exempel som visar kostnader för alla kunder](./media/get-started-partners/customer-costs1.png)

Här är ett exempel som visar kostnader för en enskild kund.
![exempel som visar kostnader för en enskild kund](./media/get-started-partners/customer-costs2.png)

Alla funktioner som är tillgängliga i Azure Cost Management finns också i REST-API: er. Använd API: erna för att automatisera kostnads hanterings uppgifter.

## <a name="prerequisites"></a>Krav

Azure Cost Management kräver Läs behörighet till ditt fakturerings konto eller prenumeration. Åtkomst kan beviljas på vilken nivå som helst ovanför dina resurser, från fakturerings kontot eller en hanterings grupp till enskilda resurs grupper där du hanterar dina appar. Mer information om hur du aktiverar och tilldelar åtkomst till Azure Cost Management för ett fakturerings konto finns i [Tilldela användar roller och behörigheter](/partner-center/permissions-overview). Rollen **Global administratör** och **admin-agenten** kan hantera kostnader för ett fakturerings konto.

Om du vill visa en fullständig lista över vilka konto typer som stöds, se [förstå Cost Management data](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Hur Cost Management använder omfång

Med omfattningar kan du hantera fakturerings data, ha roller som är begränsade till betalningar, Visa fakturor och utföra allmän konto hantering. Fakturerings-och konto roller hanteras separat från omfattningar som används för resurs hantering, som använder RBAC. För att tydligt särskilja syftet med de separata omfattningarna, inklusive åtkomst kontroll skillnaderna, kallas de för fakturerings omfång respektive RBAC-omfång.

Om du vill förstå fakturerings omfattningar och RBAC-scope och hur kostnads hantering fungerar med omfattningar, se [förstå och arbeta med omfattningar](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Hantera kostnader med partner klient fakturerings omfång

När du har publicerat dina kunder till ett Microsoft-kundavtal är följande _fakturerings omfattningar_ tillgängliga i din klient organisation. Använd omfattningarna för att hantera kostnader i Cost Management.

### <a name="billing-account-scope"></a>Fakturerings konto omfång

Använd fakturerings konto omfånget för att Visa kostnader för moms i alla dina kunder och fakturerings profiler. Faktura kostnader visas bara för kundens konsumtions produkter i Microsofts kund avtal. Faktura kostnader visas dock för inköpta produkter för kunder i både Microsofts kund avtal och CSP-erbjudandet. För närvarande är standard valutan för att Visa kostnader i omfånget US dollar. Budgetarna som angetts för omfånget är också i USD.

Oberoende av olika kund fakturerings bara valutor använder partners fakturerings konto omfånget för att ställa in budgetar och hantera kostnader i USD över sina kunder, prenumerationer, resurser och resurs grupper.

Partner filtrerar också kostnader i en bestämd fakturerings valuta för kunderna i vyn kostnads analys. Välj listan **faktisk kostnad** om du vill visa kostnader i kund fakturerings valutor som stöds.

![Exempel som visar val av faktiska kostnader för valutor](./media/get-started-partners/actual-cost-selector.png)

Använd vyn förbrukad [kostnad](quick-acm-cost-analysis.md#customize-cost-views) i fakturerings omfånget om du vill visa reserverade kostnader för reserverade instanser över en reservations period.

### <a name="billing-profile-scope"></a>Fakturerings profil omfång

Använd fakturerings profil omfattningen för att Visa förskotts kostnader i fakturerings valutan för alla dina kunder för alla produkter och prenumerationer som ingår i en faktura. Du kan filtrera kostnader i en fakturerings profil för en speciell faktura med **InvoiceID** -filtret. Filtret visar förbruknings-och produkt inköps kostnader för en speciell faktura. Du kan också filtrera kostnaderna för en specifik kund på fakturan för att se moms kostnader.

När du har publicerat kunderna till ett Microsoft-kundavtal får du en faktura som innehåller alla avgifter för alla produkter (förbrukning, inköp och rättigheter) för de här kunderna i Microsofts kund avtal. Vid fakturering i samma valuta inkluderar dessa fakturor även avgifter för rättighets-och inköpta produkter som SaaS, Azure Marketplace och reservationer för kunder som fortfarande ingår i CSP-erbjudandet.

För att hjälpa till med kund fakturan kan du se alla kostnader som påförs för en faktura för kunderna. Precis som fakturan visar omfattningen kostnader för varje kund i det nya Microsofts kund avtal. Omfattningen visar också alla kostnader för kund rättighets produkter som fortfarande finns i det aktuella CSP-erbjudandet.

Fakturerings profilen och fakturerings konto omfånget är de enda tillämpliga omfattningarna som visar avgifter för rättighets-och inköps produkter som Azure Marketplace och köp av reservationer.

I fakturerings profiler definieras de prenumerationer som ingår i en faktura. Fakturerings profiler är den funktionella motsvarigheten till en företags avtals registrering. En fakturerings profil är det omfång där fakturor genereras.

För närvarande är kundens fakturerings valuta standard valuta när du visar kostnader i fakturerings profil omfånget. Budgetarna som anges i fakturerings profil omfånget är i fakturerings valutan.

Partner kan använda omfånget för att stämma av fakturor. Och använder de omfånget för att ställa in budgetar i fakturerings valutan för följande objekt:

- Speciell filtrerad faktura
- Kund
- Prenumeration
- Resursgrupp
- Resurs
- Azure-tjänst
- Mätare
- ResellerMPNID

### <a name="customer-scope"></a>Kund omfång

Partner använder omfånget för att hantera kostnader som är kopplade till kunder som har publicerats till Microsofts kund avtal. Med hjälp av omfånget kan partners Visa moms kostnader för en specifik kund. Du kan också filtrera förskotts kostnader för en speciell prenumeration, resurs grupp eller resurs.

Kund omfånget omfattar inte kunder som använder det aktuella CSP-erbjudandet. Omfånget omfattar bara kunder som har ett kund avtal för Microsoft. Rättighets kostnader, inte Azure-användning för aktuella CSP-erbjudanden, är tillgängliga för kunder med fakturerings konto och fakturerings profil när du tillämpar kund filtret.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Partner åtkomst till fakturerings omfattningar i Cost Management

Endast användare med rollen **Global administratör** och **administratör** kan hantera och Visa kostnader för fakturerings konton, fakturerings profiler och kunder direkt i partnerns Azure-klient. Mer information om Partner Center-roller finns i [Tilldela användar roller och behörigheter](/partner-center/permissions-overview).

## <a name="enable-cost-management-in-the-customer-tenant"></a>Aktivera kostnads hantering i kund klienten

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
![principen så att kunderna kan se avgifterna betala per användning](./media/get-started-partners/cost-management-billing-policies.png)

När principen är inställd på **Nej**är Azure Cost Management inte tillgänglig för prenumerations användare som är kopplade till kunden. Om den inte aktive ras av en partner är principen för kostnads synlighet inaktive rad som standard för alla prenumerations användare.

När kostnads principen är inställd på **Ja**kan prenumerations användare som är kopplade till kund innehavaren se användnings avgifter enligt priserna för betala per användning.

När principen för kostnads synlighet är aktive rad visar alla tjänster som har prenumerations användningen kostnader enligt priserna för betala per användning. Reservations användningen visas med noll kostnader för faktiska och periodiserade kostnader. Köp och rättigheter är inte kopplade till en speciell prenumeration. Därför visas inte inköpen i prenumerations omfånget.

Om du vill visa kostnader för kund klienten öppnar du Cost Management + fakturering och klickar sedan på fakturerings konton. Klicka på ett fakturerings konto i listan över fakturerings konton.

![Välj ett fakturerings konto](./media/get-started-partners/select-billing-account.png)

Klicka på **Azure-prenumerationer**under **fakturering**och klicka sedan på en kund.

![Välj en Azure-prenumerations kund](./media/get-started-partners/subscriptions-select-customer.png)

Klicka på **kostnads analys** och påbörja granskning av kostnader.
Kostnads analys, budgetar och aviseringar är tillgängliga för en prenumeration och resurs grupp för RBAC-omkostnader enligt principen betala per användning.

![Visa kostnads analys som en kund ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Amortera vyer och faktiska kostnader för reserverade instanser i RBAC-omfången visar noll avgifter. Reserverade instans kostnader visas bara i fakturerings omfånget där inköpen gjordes.

## <a name="analyze-costs-in-cost-analysis"></a>Analysera kostnader i kostnads analys

Partner kan utforska och analysera kostnader i kostnads analys över kunder för en specifik kund eller för en faktura.

Följande fält finns i användnings detalj filen och Cost Management API: er. Du kan använda filtrera och gruppera efter funktioner i kostnads analys för att analysera kostnader med flera fält. Om du vill visa en fullständig lista över fält, se [Cost Management data fält](understand-cost-mgt-data.md#cost-management-data-fields).

| Fältnamn | Beskrivning |
| --- | --- |
| CustomerTenantID | Identifierare för den Azure Active Directory klienten för kunden&#39;s prenumeration. |
| CustomerName | Namnet på Azure Active Directory klient organisation för kund&#39;s-prenumerationen. |
| CustomerTenantDomainName | Domän namn för Azure Active Directory klient organisation för kund&#39;s-prenumerationen. |
| PartnerTenantID | Identifierare för partner&#39;s Azure Active Directory klient. |
| PartnerName | Namnet på partner Azure Active Directory klient organisationen. |
| ResellerMPNID | MPNID för den åter försäljare som är associerad med prenumerationen. |
| costinUSD | Beräknad ExtendedCost eller blandade kostnader före skatt i USD. |
| paygCostInBillingCurrency | Visar kostnader om priserna är i detaljhandelspriser. Visar priser enligt principen betala per användning i fakturerings valutan. Endast tillgängligt i RBAC-scope. |
| paygCostInUSD | Visar kostnader om priserna är i detaljhandelspriser. Visar priserna för betala per användning i USD. Endast tillgängligt i RBAC-scope. |
| partnerEarnedCreditRate | Rabatt som används om det finns en partner som har fått partner administratörs åtkomst. |
| partnerEarnedCreditApplied | Anger om den partner som har intjänad kredit har tillämpats. |

I vyn [kostnads analys](quick-acm-cost-analysis.md) kan du också [Spara vyer](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) och exportera data till CSV- [och PNG-filer](quick-acm-cost-analysis.md#automation-and-offline-analysis).

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

Partner och kunder kan använda Cost Management API: er som beskrivs i följande avsnitt för vanliga uppgifter.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management-API: er – direkta och indirekta leverantörer

Partner med åtkomst till fakturerings omfång i en partner klient organisation kan använda följande API: er för att Visa fakturerade kostnader.

API: er i prenumerations omfånget kan anropas av en partner oavsett kostnads princip om de har åtkomst till prenumerationen. Andra användare med åtkomst till prenumerationen, t. ex. kunden eller åter försäljaren, kan anropa API: erna endast efter att partnern har aktiverat kostnads principen för kund innehavaren.


#### <a name="to-get-a-list-of-billing-accounts"></a>Så här hämtar du en lista över fakturerings konton

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Så här hämtar du en lista över kunder

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Hämta en lista över prenumerationer

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Hämta en lista över fakturor under en tids period

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

API-anropet returnerar en matris med fakturor som innehåller element som liknar följande JSON-kod.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Använd föregående returnerade ID-fält-värde och Ersätt det i följande exempel som omfattning för att fråga efter användnings information.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

Exemplet returnerar de användnings poster som är associerade med den aktuella fakturan.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Så här hämtar du principen för kunder att Visa kostnader

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Så här ställer du in principen för kunder för att Visa kostnader

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Så här hämtar du Azure Service-användning för ett fakturerings konto

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Så här hämtar du en kunds Azure-tjänst användning

Följande GET-anrop är en asynkron åtgärd.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Anropa den `Location` URI som returnerades i svaret för att kontrol lera åtgärdens status. När statusen har *slutförts*innehåller egenskapen `downloadUrl` en länk som du kan använda för att hämta den genererade rapporten.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Hämta och hämta pris dokumentet för förbrukade Azure-tjänster

Använd först följande post.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Anropa sedan egenskap svärdet för den asynkrona åtgärden. Exempel:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
Föregående GET-anrop returnerar nedladdnings länken som innehåller pris dokumentet.


#### <a name="to-get-aggregated-costs"></a>För att få aggregerade kostnader

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Skapa en budget för en partner

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Skapa en budget för en kund

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Ta bort en budget

```
PUT
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Nästa steg
- [Börja analysera kostnader](quick-acm-cost-analysis.md) i Cost Management
- [Skapa och hantera budgetar](tutorial-acm-create-budgets.md) i Cost Management
