---
title: Kom igång med Azure Cost Management för partner
description: I den här artikeln förklaras hur partners använder Azure Cost Management funktioner och hur de ger Cost Management åtkomst till sina kunder.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/15/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: b7ae388488de32bb106ae29f975302953cfcb2e9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123021"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Kom igång med Azure Cost Management för partner

Azure Cost Management är internt tillgängligt för partner som har publicerat sina kunder på ett Microsoft-kundavtal och har [köpt en Azure-prenumeration](/partner-center/purchase-azure-plan). Den här artikeln förklarar hur partners använder [Azure Cost Management](index.yml) -funktioner för att Visa kostnader för prenumerationer i Azure-planen. Det beskriver också hur partners ger Cost Management åtkomst till sina kunder. Kunder kan använda Cost Management funktioner när de aktive ras av sin CSP-partner.

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

Partner kan utforska och analysera kostnader i kostnads analys över kunder för en specifik kund eller för en faktura. I vyn [kostnads analys](quick-acm-cost-analysis.md) kan du också [Spara vyer](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) och exportera data till CSV- [och PNG-filer](quick-acm-cost-analysis.md#automation-and-offline-analysis).

Du kan använda filtrera och gruppera efter funktioner i kostnads analys för att analysera kostnader med flera fält. Partner-/regionsspecifika fält visas i nästa avsnitt.

## <a name="data-fields"></a>Data fält

Följande data fält finns i användnings detalj filen och Cost Management API: er. Om det är tillgängligt visas information om Partner Center motsvarande. I följande fetstilta fält kan partner använda filter och gruppering efter funktioner i kostnads analys för att analysera kostnader med flera fält. Fetstilta fält gäller endast för Microsofts kund avtal som stöds av partner.

| **Fält namn** | **Beskrivning** | **Partner Center-motsvarighet** |
| --- | --- | --- |
| invoiceId | Faktura-ID visas på fakturan för den aktuella transaktionen. | Faktura nummer där transaktionen visas. |
| previousInvoiceID | Referens till en ursprunglig faktura det finns en åter betalning (negativ kostnad). Fylls bara i när det finns en åter betalning. | Saknas |
| billingAccountName | Namnet på det fakturerings konto som representerar partnern. Det påförs alla kostnader i de kunder som har registrerat sig för ett Microsoft-kundavtal och de CSP-kunder som har gjort rätt inköp som SaaS, Azure Marketplace och reservationer. | Saknas |
| billingAccountID | Identifierare för det fakturerings konto som representerar partnern. | Rot-ID för MCAPI-partner Commerce. Används i en begäran, men ingår inte i något svar.|
| billingProfileID | Identifierare för den fakturerings profil som grupperar kostnader över fakturor i en enda fakturerings valuta för de kunder som har registrerat sig för ett Microsoft-kundavtal och de CSP-kunder som har gjort rättighets köp som SaaS, Azure Marketplace och reservera. | MCAPI partner fakturerings grupp-ID. Används i en begäran, men ingår inte i något svar. |
| billingProfileName | Namnet på den fakturerings profil som grupperar kostnader över fakturor i en enda fakturerings valuta för de kunder som har registrerat sig för ett Microsoft-kundavtal och de CSP-kunder som har gjort rätt inköp som SaaS, Azure Marketplace och reservera. | Saknas |
| invoiceSectionName | Namnet på projektet som debiteras på fakturan. Ej tillämpligt för Microsofts kund avtal som har registrerats av partner. | Saknas |
| invoiceSectionID | Identifierare för projektet som debiteras på fakturan. Ej tillämpligt för Microsofts kund avtal som har registrerats av partner. | Saknas |
| **CustomerTenantID** | Identifierare för den Azure Active Directory klienten för kundens prenumeration. | Kundens organisations-ID – kundens organisations Azure Active Directory TenantID. |
| **CustomerName** | Namnet på Azure Active Directory klienten för kundens prenumeration. | Kundens organisations namn, som visas i Partner Center. Viktigt för att stämma av fakturan med din system information. |
| **CustomerTenantDomainName** | Domän namn för den Azure Active Directory klienten för kundens prenumeration. | Kund Azure Active Directory klient domän. |
| **PartnerTenantID** | Identifierare för partnerns Azure Active Directory klient. | Partner Azure Active Directory klient-ID som kallas partner-ID i GUID-format. |
| **PartnerName** | Namnet på partner Azure Active Directory klient organisationen. | Partner namn. |
| **ResellerMPNID** | MPNID för den åter försäljare som är associerad med prenumerationen. | MPN-ID för åter försäljarens post för prenumerationen. Inte tillgängligt för den aktuella aktiviteten. |
| costCenter | Kostnads ställe som är associerat med prenumerationen. | Saknas |
| billingPeriodStartDate | Fakturerings periodens start datum, som du ser på fakturan. | Saknas |
| billingPeriodEndDate | Fakturerings periodens slutdatum, som det visas på fakturan. | Saknas |
| servicePeriodStartDate | Start datum för klassificerings perioden när tjänste användningen har bedömts för avgifter. Priserna för Azure-tjänster bestäms för klassificerings perioden. | ChargeStartDate i Partner Center. Start datum för fakturerings cykel, förutom när du presenterar datum för tidigare avlastade latens användnings data från en tidigare fakturerings period. Tiden är alltid början på dagen, 0:00. |
| servicePeriodEndDate | Slutdatum för den period då tjänste användningen har bedömts för avgifter. Priserna för Azure-tjänster bestäms utifrån klassificerings perioden. | Saknas |
| datum | För Azures förbruknings data visas användnings datum som klassificerat. För reserverad instans visas inköps datum. För återkommande kostnader och engångs kostnader, till exempel Marketplace och support, visas inköps datumet. | Saknas |
| productID | Identifierare för den produkt som har upplupna kostnader per förbrukning eller inköp. Det är den sammansatta nyckeln productID och SKuID, som visas i Partner Center. | Produktens ID. |
| produkt | Namnet på den produkt som har upplupna kostnader per förbrukning eller inköp, som du ser på fakturan. | Produkt namnet i katalogen. |
| serviceFamily | Visar tjänst familjen för köpta eller debiterade produkter. Till exempel lagring eller beräkning. | Saknas |
| productOrderID | Identifieraren för det till gångs-eller Azure-Plans namn som prenumerationen tillhör. Till exempel Azure-plan. | Saknas |
| productOrderName | Namnet på den Azure-plan som prenumerationen tillhör. Till exempel Azure-plan. | Saknas|
| consumedService | Förbrukad tjänst (äldre taxonomi) som används i tidigare EA-användnings information. | Tjänsten visas i Partner Center. Till exempel Microsoft. Storage, Microsoft. Compute och Microsoft. operationalinsights. |
| meterID | Mätnings identifierare för uppmätt förbrukning. | ID för den använda mätaren. |
| meterName | Identifierar namnet på mätaren för uppmätt förbrukning. | Namnet på den förbrukade mätaren. |
| meterCategory | Identifierar den översta tjänsten för användning. | Tjänsten på den översta nivån för användning. |
| meterSubCategory | Definierar vilken typ eller under kategori av Azure-tjänst som kan påverka priset. | Den typ av Azure-tjänst som kan påverka priset.|
| meterRegion | Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret ligger. | Den regionala platsen för ett Data Center för tjänster, där det är tillämpligt och ifyllt. |
| subscription ID | Unikt Microsoft-genererad identifierare för Azure-prenumerationen. | Saknas |
| subscriptionName | Namnet på Azure-prenumerationen. | Saknas |
| Period | Visar giltighetstiden för erbjudandet. Reserverade instanser visar till exempel 12 månader av en årlig period på den reserverade instansen. Vid Engångs köp eller återkommande inköp visar termen en månad för SaaS, Azure Marketplace och support. Gäller inte för Azure-förbrukning. | Saknas |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Typ av utgivare som identifierar utgivaren som första part, åter försäljare av tredje part eller från tredje parts myndighet. | Saknas |
| partNumber | Del nummer för den oanvända reserverade instansen och Azure Marketplace-tjänster. | Saknas |
| publisherName | Namnet på utgivaren av tjänsten, inklusive Microsoft eller tredje parts utgivare. | Namnet på produktens utgivare.|
| reservationId | Identifierare för köpet av den reserverade instansen. | Saknas |
| reservationName | Namnet på den reserverade instansen. | Saknas |
| reservationOrderId | Ordernr för den reserverade instansen. | Saknas |
| frequency | Betalnings frekvens för en reserverad instans. | Saknas |
| resourceGroup | Namnet på den Azure-resurs grupp som används för livs cykel resurs hantering. | Namnet på resurs gruppen. |
| instanceID (eller) ResourceID | Identifierare för resurs instansen. | Visas som en ResourceURI som innehåller fullständiga resurs egenskaper. |
| resourceLocation | Namnet på resurs platsen. | Resursens plats. |
| Plats | Normaliserad plats för resursen. | Saknas |
| effectivePrice | Det effektiva enhets priset för tjänsten i pris valutan. Unikt för en produkt, tjänst familj, mätare och erbjudande. Används med priser i pris dokumentet för fakturerings kontot. När det finns ett pris på nivå eller en inkluderad kvantitet visas det blandade priset för förbrukning. | Enhets priset efter det att justeringarna har gjorts. |
| Kvantitet | Uppmätt kvantitet köpt eller förbrukad. Den Mät mängd som används under fakturerings perioden. | Antal enheter. Se till att den matchar informationen i fakturerings systemet under avstämningen. |
| unitOfMeasure | Identifierar enheten som tjänsten debiteras i. Till exempel GB och timmar. | Identifierar enheten som tjänsten debiteras i. Till exempel GB, timmar och 10, tal. |
| pricingCurrency | Den valuta som definierar enhets priset. | Valutan i pricelist.|
| billingCurrency | Den valuta som definierar den fakturerade kostnaden. | Valutan för kundens geografiska region. |
| chargeType | Definierar den typ av kostnad som kostnaden representerar i Azure Cost Management som inköp och åter betalning. | Typ av kostnad eller justering. Inte tillgängligt för den aktuella aktiviteten. |
| costinBillingCurrency | ExtendedCost eller blandade kostnader före skatt i den fakturerade valutan. | Saknas |
| costinPricingCurrency | ExtendedCost eller blandade kostnader före skatt i prissättnings valutan som motsvarar priserna. | Saknas |
| **costinUSD** | Beräknad ExtendedCost eller blandade kostnader före skatt i USD. | Saknas |
| **paygCostInBillingCurrency** | Visar kostnader om priserna är i detaljhandelspriser. Visar priser enligt principen betala per användning i fakturerings valutan. Endast tillgängligt i RBAC-scope. | Saknas |
| **paygCostInUSD** | Visar kostnader om priserna är i detaljhandelspriser. Visar priserna för betala per användning i USD. Endast tillgängligt i RBAC-scope. | Saknas |
| exchangeRate | Växelkurs som används för att konvertera från pris valutan till fakturerings valutan. | Kallas PCToBCExchangeRate i Partner Center. Pris valutan till fakturerings valutakursen.|
| exchangeRateDate | Datum för den växelkurs som används för att konvertera från pris valutan till fakturerings valutan. | Kallas PCToBCExchangeRateDat i Partner Center. Pris valutan till fakturerings valutans växelkurs datum.|
| isAzureCreditEligible | Anger om kostnaden är berättigad till betalning av Azure-krediter. | Saknas |
| serviceInfo1 | Äldre fält som fångar valfria tjänstspecifika metadata. | Interna Azure-tjänstemetadata. |
| serviceInfo2 | Äldre fält som fångar valfria tjänstspecifika metadata. | Tjänst information. Till exempel en avbildnings typ för en virtuell dator och ett ISP-namn för ExpressRoute.|
| additionalInfo | Tjänstspecifika metadata. Det kan till exempel vara en avbildningstyp för en virtuell dator. | Eventuell ytterligare information som inte omfattas i andra kolumner. Tjänstspecifika metadata. Det kan till exempel vara en avbildningstyp för en virtuell dator.|
| taggar | Tagg som du tilldelar mätaren. Använd taggar för att gruppera fakturerings poster. Du kan till exempel använda taggar för att distribuera kostnader på den avdelning som använder mätaren. | Taggar som lagts till av kunden.|
| **partnerEarnedCreditRate** | Rabatt som används om det finns en partner som har fått partner administratörs åtkomst. | Kostnaden för intjänad kredit för partner (PEC). Till exempel 0% eller 15%. |
| **partnerEarnedCreditApplied** | Anger om den partner som har intjänad kredit har tillämpats. | Saknas |

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
