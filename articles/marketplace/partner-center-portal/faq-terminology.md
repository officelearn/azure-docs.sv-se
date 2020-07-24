---
title: Vanliga frågor och svar om kommersiell Marketplace & terminologi – Partner Center
description: Få svar på vanliga frågor om den kommersiella Marketplace-analysen i Partner Center. Den här artikeln innehåller en data ord lista för analys terminologi.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f3d4050e51c5394edaa3c72213bc8da48ab568e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011846"
---
# <a name="commercial-marketplace-analytics-terminology-and-common-questions"></a>Terminologi för kommersiell marknads plats analys och vanliga frågor

Den här artikeln handlar om vanliga frågor om analys meddelanden i Partner Center och innehåller en ord lista med analys terminologi.

## <a name="common-questions"></a>Vanliga frågor

**Jag kan inte Visa mina analys data i Partner Center. När jag har åtkomst till dessa sidor visas meddelandet nedan. Varför är det här?**

![Inga data för dina erbjudanden än](./media/analytics-faq-no-data.png)

Varför du kan få det här meddelandet:

- Det finns för närvarande inga förvärv för dina publicerade erbjudanden i Marketplace. Detta kan betyda att dina erbjudanden är Live i Marketplace och att du får vyer från kunder på produktens visnings sidor, men kunder som inte har vidtagit åtgärder för att köpa och distribuera dem.
- Publiceringen av ditt erbjudande kan vara i gång och är inte i drift än. Endast Live-erbjudanden kan förvärvas av kunder. För att kontrol lera statusen för dina erbjudanden, se Översikt i [analys instrument panelen](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Mer information finns i [Översikt över instrument panelen i marknads plats analys](./summary-dashboard.md).
- Dina erbjudanden kan visas som **kontakta mig**, som endast är List erbjudanden och inte kan köpas av kunder på Marketplace. Även om dessa erbjudanden genererar leads och delas med dig, skapas inga beställningar för dessa erbjudanden eftersom de inte kan köpas. Om du vill kontrol lera typen av erbjudande lista går du till installations sidan.

**Jag vet att jag har analys data, men meddelandet nedan visas:**

![Inga data för angivet datum intervall](./media/analytics-faq-data-range.png)

Om du får det här meddelandet betyder det att du har analys data, men det finns inga data för det datum intervall som du har valt. Välj ett annat datum intervall eller anpassat datum intervall om du vill visa data sedan 2010. Mer information finns i avsnittet datum intervall i [sammanfattnings instrument panelen i kommersiell Marketplace-analys](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Ord lista med data termer

| Attributnamn | Rapporter | Definition|
|---|---|---|
| Typ av Azure-licens | Kund, order | Den typ av licens avtal som kunder använder för att köpa Azure. Kallas även för kanal |
| Azure-licens typ: Cloud Solution Provider | Kund, order | Slutanvändaren kan köpa Azure och ditt Marketplace-erbjudande via moln lösnings leverantören, som agerar som åter försäljare.|
| Azure-licens typ: Enterprise | Kund, order | Slutanvändaren kan köpa Azure och ditt Marketplace-erbjudande via en Enterprise-avtal, som loggas direkt med Microsoft.|
| Azure-licens typ: Enterprise genom åter försäljare  | Kund, order | Slutanvändaren säljer Azure och Marketplace-erbjudandet via en åter försäljare som fören klar sina Enterprise-avtal med Microsoft.|
| Azure-licens typ: betala per användning| Kund, order | Slutanvändaren debiterar Azure och Marketplace-erbjudandet genom ett "betala per användning"-avtal, undertecknat direkt med Microsoft.|
| Moln instans namn| Beställa| Den Microsoft Cloud där en VM-distribution ägdes.|
| Moln instans namn: Azure Global| Beställa| Det offentliga globala Microsoft-molnet.|
| Moln instans namn: Azure Government | Beställa| Myndighets Microsoft-moln för någon av följande myndigheter: Kina, Tyskland eller USA i Amerika.| |
| Kund ort| Kund| Orts namnet som tillhandahålls av kunden. Staden kan skilja sig från staden i en kunds Azure-prenumeration.|
| Kundens kommunikations språk  | Kund| Det språk som kunden föredrar för kommunikation.|
| Kund företags namn | Kund, order | Företags namnet som tillhandahålls av kunden. Namnet får inte vara detsamma som staden i en kunds Azure-prenumeration.|
| Kund land/-region | Kund, order | Landets/regionens namn som tillhandahålls av kunden. Land/region kan vara annorlunda än landet/regionen i en kunds Azure-prenumeration.|
| Kund-e-post| Kund| E-postadressen som tillhandahålls av slut kunden. E-postadressen kan skilja sig från e-postadressen i en kunds Azure-prenumeration.|
| Förnamn för kund| Kund| Det namn som tillhandahålls av kunden. Namnet får inte vara detsamma som det namn som anges i en kunds Azure-prenumeration.|
| Kund-ID | Kund, order | Den unika identifierare som tilldelats till en kund. En kund kan ha noll eller flera prenumerationer på Azure Marketplace.|
| Kund post nummer  | Kund| Post numret från kunden. Koden får inte vara samma som post numret i en kunds Azure-prenumeration.|
| Kund tillstånd| Kund| Den stat (adress) som tillhandahålls av kunden. Tillstånd kan skilja sig från det tillstånd som tillhandahölls i en kunds Azure-prenumeration.|
| Inköps datum| Kund| Det första datumet då kunden köpte ett erbjudande som publicerats av dig.|
| Datum förlorat| Kund| Det senaste datumet då kunden avbröt den sista av alla erbjudanden som tidigare har köpts.|
| Är ny kund  | Beställa| Värdet identifierar en ny kund som förvärvar ett eller flera av dina erbjudanden för första gången (eller inte). Värdet blir "Ja" om det är inom samma kalender månad för "inköps datum". Värdet blir "nej" om kunden har köpt något av dina erbjudanden före den kalender månad som rapporter ATS. |
| Är för hands version av SKU| Beställa| Värdet meddelar dig om du har taggat SKU som "Preview". Värdet är "Ja" om SKU: n har taggats enligt detta och bara Azure-prenumerationer som har auktoriserats av dig kan distribuera och använda den här avbildningen. Värdet är "nej" om SKU: n inte har identifierats som "för hands version". |
| Är kampanj kontakt deltagande| Kund| Värdet gör att du vet om kunden proaktivt har valt reklam kontakt från utgivare. För tillfället visar vi inte alternativet för kunder, så vi har angett "nej" på tavlan. När den här funktionen har distribuerats kommer vi att börja uppdatera enligt detta.|
| Marketplace-licens typ| Beställa| Fakturerings metoden för Marketplace-erbjudandet.|
| Marketplace-licens typ: debiteras via Azure| Beställa| Microsoft är din agent för det här Marketplace-erbjudandet och fakturerar kunder åt dig. (Antingen PAYG kredit kort eller företags faktura)|
| Marketplace-licens typ: ta med din egen licens | Beställa| Den virtuella datorn kräver en licens nyckel från kunden som ska distribueras. Microsoft fakturerar inte kunder för att lista sina erbjudanden på det här sättet via Marketplace.|
| Marketplace-licens typ: kostnads fri| Beställa| Erbjudandet är konfigurerat att vara kostnads fritt för alla användare. Microsoft fakturerar inte kunder för användningen av det här erbjudandet.|
| Marketplace-licens typ: Microsoft as åter försäljare  | Beställa| Microsoft är din åter försäljare för det här Marketplace-erbjudandet.|
| Prenumerations-ID för Marketplace | Kund, order | Den unika identifierare som är kopplad till Azure-prenumerationen som kunden använde för att köpa Marketplace-erbjudandet. ID var tidigare GUID för Azure-prenumerationen.|
| Erbjudandets namn  | Beställa| Namnet på Marketplace-erbjudandet.|
| Erbjudande typ  | Beställa| Typ av Microsoft Marketplace erbjudande.|
| Erbjudande typ: hanterat program  | Beställa | Använd Azure app: den hanterade appens erbjudande typ när följande villkor är uppfyllda: du distribuerar antingen en prenumerations baserad lösning för din kund med antingen en virtuell dator eller en hel IaaS lösning. Du eller din kund kräver att lösningen hanteras av en partner. |
| Erbjudande typ: Azure Application| Beställa | Använd mallen för Azure Application lösnings erbjudande när din lösning kräver ytterligare distribution och konfigurations automatisering utöver en enkel virtuell dator.|
| Erbjudande typ: konsult tjänst| Beställa| Konsult tjänster i Azure Marketplace hjälper till att ansluta kunder med tjänster som stöder och utökar användningen av Azure.|
| Erbjudande typ: container | Beställa| Använd containerns erbjudande typ när din lösning är en Docker-behållar avbildning som tillhandahålls som en Kubernetes-baserad Azure Container Service.|
| Erbjudande typ: Dynamics 365 Business Central| Beställa| Använd den här erbjudande typen när din lösning är integrerad med Dynamics 365 för finanser och åtgärder|
| Erbjudande typ: Dynamics 365 för kund engagemang | Beställa| Använd den här erbjudande typen när din lösning är integrerad med Dynamics 365 för kund engagemang.|
| Erbjudande typ: IoT Edge modul | Beställa| Azure IoT Edge moduler är de minsta beräknings enheter som hanteras av IoT Edge och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösnings-specifika kod. |
| Erbjudande typ: Power BI program | Beställa| Använd typen Power BI program erbjudande när du distribuerar ett program som är integrerat med Power BI.|
| Erbjudande typ: SaaS-program| Beställa| Använd SaaS-appens erbjudande typ för att göra det möjligt för kunden att köpa din SaaS-baserade, tekniska lösning som en prenumeration.||
| Erbjudande typ: virtuell dator | Beställa | Använd den virtuella datorns erbjudande typ när du distribuerar en virtuell installation till prenumerationen som är kopplad till din kund.|
| Erbjudande typ: tillägg för Visual Studio Marketplace  | Beställa| Erbjudande typ som tidigare var tillgänglig för Azure DevOps Extension-utvecklare. Att gå vidare med Azure DevOps Extension-utvecklare kan sälja sitt tillägg direkt till kunderna. Tilläggs erbjudanden kan konfigureras som betalda eller inklusive en utvärderings version. |
| Annullera datum för beställning| Beställa| Datumet då Marketplace-ordern annullerades.|
| Order-ID| Beställa| Den unika identifieraren för kund ordern för din Marketplace-tjänst. Användnings erbjudanden för virtuella datorer är inte kopplade till någon order.|
| Inköps datum för beställning| Beställa| Datumet då Marketplace-ordern skapades.|
| Orderstatus| Beställa| Status för en Marketplace-order vid den tidpunkt då data senast uppdaterades.|
| Order status: aktiv  | Beställa| Kunden har köpt en order och har inte annullerat sin beställning.|
| Order status: avbruten | Beställa| Kunden köpte tidigare en order och annullerade sedan sin beställning.|
| Providerns e-postadress| Kund| E-postadressen till leverantören som är involverad i relationen mellan Microsoft och slut kunden. Om kunden är ett företag genom åter försäljaren blir detta åter försäljaren. Om en moln lösnings leverantör (CSP) är involverad är detta CSP.|
| Providernamn| Kund| Namnet på leverantören som är involverad i relationen mellan Microsoft och slut kunden. Om kunden är ett företag genom åter försäljaren blir detta åter försäljaren. Om en moln lösnings leverantör (CSP) är involverad är detta CSP.|
| SKU| Beställa| SKU (nu kallat plan) namn som det definieras under publiceringen. Ett erbjudande kan ha många SKU: er (planer), men de kan bara associeras med ett enda erbjudande.|
| Slutdatum för utvärdering| Beställa| Det datum då utvärderings perioden för den här ordern upphör eller har avslut ATS.|
|||

## <a name="next-steps"></a>Nästa steg

- En översikt över analys rapporter som är tillgängliga i partner Centers kommersiella marknads platser finns i [analys för den kommersiella Marketplace i Partner Center](./analytics.md).
- För grafer, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för ditt erbjudande, se [översikts instrument panel i](./summary-dashboard.md)marknads plats analys.
- Information om dina beställningar i ett grafiskt och nedladdnings Bart format finns i [order instrument panel i kommersiell Marketplace-analys](./orders-dashboard.md).
- För virtuell dator (VM) erbjuder vi användnings-och mätnings mått i [användnings instrument panelen i den kommersiella Marketplace-analysen](./usage-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxt trender, finns [på kund instrument panel i affärs Marketplace-analys](./customer-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./downloads-dashboard.md).
- Om du vill se en sammanställd vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource, se [klassificering och granskning på instrument panelen i kommersiell Marketplace-analys](./ratings-reviews.md).
