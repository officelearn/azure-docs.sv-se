---
title: Vanliga frågor och terminologi för den kommersiella marknadsplatsen analytics i Partner Center
description: Läs om hur du tar itu med vanliga frågor om kommersiell marknadsplatsanalys. Innehåller en dataordlista för analysterminologi.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: b7367e58de818c20723c02a6763b1bf1e3b18f24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251834"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>Vanliga frågor och terminologi för kommersiell marknadsplatsanalys

Den här artikeln tar upp vanliga frågor om analysmeddelanden i Partner Center och innehåller även en ordlista med analysterminologi.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Det här avsnittet innehåller svar på vanliga frågor om **meddelanden om analys ännu** i Partnercenter.

**Jag kan inte visa mina analysdata i Partner Center. När jag öppnar dessa sidor visas meddelandet nedan. Varför är det så här?**

![Inga data för dina erbjudanden ännu](./media/analytics-faq-no-data.png)

Varför du kanske får det här meddelandet:

- Det finns för närvarande inga förvärv för dina publicerade erbjudanden på marketplace. Det kan innebära att dina erbjudanden visas på marknadsplatsen och får visningar från kunder på produktvisningssidorna, men kunderna har ännu inte vidtagit åtgärder för att köpa och distribuera dem.
- Publiceringen av erbjudandet kan vara på gång och är inte live än. Endast liveerbjudanden kan förvärvas av kunder. Mer om du vill kontrollera status för dina erbjudanden finns i Översikt i [instrumentpanelen Analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Mer information finns [i Sammanfattningsinstrumentpanel i kommersiell marknadsplatsanalys](./summary-dashboard.md).
- Dina erbjudanden kan listas som **Kontakta mig**, som är list-only erbjudanden och kan inte köpas av kunder på marknaden. Även om dessa erbjudanden genererar leads och delas med dig, skapas inte order för dessa erbjudanden eftersom de inte kan köpas. Om du vill kontrollera typen av erbjudandeuppgifter går du till inställningssidan.

**Jag vet att jag har analysdata, men meddelandet nedan visas:**

![Inga data för givet datumintervall](./media/analytics-faq-data-range.png)

Om du får det här meddelandet betyder det att du har analysdata men det finns inte data för det datumintervall du valde. Välj ett annat datumintervall eller anpassat datumintervall om du vill visa data sedan 2010. Mer information finns i avsnittet Datumintervall [i sammanfattningsinstrumentpanelen i kommersiell marketplace-analys](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Ordlista med datatermer

| Attributnamn | Rapporter | Definition|
|---|---|---|
| Licenstyp för Azure | Kund, Order | Den typ av licensavtal som används av kunder för att köpa Azure. Kallas även Kanal |
| Azure-licenstyp: Molnlösningsprovider | Kund, Order | Slutkunden upphandlar Azure och ditt Marketplace-erbjudande via sin Cloud Solution Provider, som fungerar som din återförsäljare.|
| Azure-licenstyp: Företag | Kund, Order | Slutkunden upphandlar Azure och ditt Marketplace-erbjudande via ett Enterprise-avtal som har undertecknats direkt med Microsoft.|
| Azure-licenstyp: Företag via återförsäljare  | Kund, Order | Slutkunden upphandlar Azure och ditt Marketplace-erbjudande via en återförsäljare som underlättar deras Enterprise-avtal med Microsoft.|  |
| Azure-licenstyp: Betala allt eftersom| Kund, Order | Slutkunden upphandlar Azure och ditt Marketplace-erbjudande via ett "Pay as You Go"-avtal som har tecknats direkt med Microsoft.||
| Namn på molninstans| Beställa| Microsoft Cloud där en vm-distribution inträffade.||
| Namn på molninstans: Azure Global| Beställa| Det offentliga globala Microsoft-molnet.|| |
| Namn på molninstans: Azure Government | Beställa| Regeringsspecifika Microsoft-moln för en av följande regeringar: Kina, Tyskland eller USA.| |
| Kundstad| Kund| Stadens namn som tillhandahålls av kunden. City kan vara annorlunda än staden i en kunds Azure-prenumeration.||
| Språk för kundkommunikation  | Kund| Det språk som kunden föredrar för kommunikation.||
| Namn på kundföretag | Kund, Order | Företagets namn som tillhandahålls av kunden. Namnet kan vara annorlunda än staden i en kunds Azure-prenumeration.|  |
| Kundland | Kund, Order | Det landsnamn som kunden tillhandahåller. Land kan vara annorlunda än landet i en kunds Azure-prenumeration.|  |
| E-post till kunder| Kund| Den e-postadress som slutkunden har angett. E-post kan skilja sig från e-postadressen i en kunds Azure-prenumeration.||
| Kundens förnamn| Kund| Namnet som tillhandahålls av kunden. Namnet kan skilja sig från namnet som anges i en kunds Azure-prenumeration.| |
| Kund-ID | Kund, Order | Den unika identifierare som tilldelats en kund. En kund kan ha noll eller fler Azure Marketplace-prenumerationer.|  |
| Kundens postnummer  | Kund| Postnumret som tillhandahålls av kunden. Koden kan skilja sig från det postnummer som anges i en kunds Azure-prenumeration.| |
| Kundtillstånd| Kund| Tillståndet (adressen) som tillhandahålls av kunden. Tillståndet kan vara annorlunda än det tillstånd som anges i en kunds Azure-prenumeration.| |
| Inköpt datum| Kund| Det första datumet då kunden köpte ett erbjudande som du har publicerat.| |
| Förlorat datum| Kund| Det sista datumet då kunden annullerade det sista av alla köpta erbjudanden.||
| Är ny kund  | Beställa| Värdet identifierar en ny kund som förvärvar ett eller flera av dina erbjudanden för första gången (eller inte). Värdet blir "Ja" om det inom samma kalendermånad för "Inköpt datum". Värdet blir "Nej" om kunden har köpt något av dina erbjudanden före den rapporterade kalendermånaden. |
| Är Förhandsgranska SKU| Beställa| Värdet kommer att låta dig veta om du har taggat SKU som "förhandsvisning". Värdet blir "Ja" om SKU har taggats i enlighet med detta och endast Azure-prenumerationer som auktoriserats av du kan distribuera och använda den här avbildningen. Värdet blir "Nej" om SKU:n inte har identifierats som "förhandsversion".  |
| Är kampanjkontakt Opt In| Kund| Värdet meddelar dig om kunden proaktivt har valt att kontakta reklam från utgivare. Just nu presenterar vi inte alternativet för kunderna, så vi har angett "Nej" över hela linjen. När den här funktionen har distribuerats kommer vi att börja uppdatera därefter.|
| Licenstyp för Marketplace| Beställa| Faktureringsmetoden för Marketplace-erbjudandet.||
| Licenstyp för Marketplace: Faktureras via Azure| Beställa| Microsoft är din agent för detta Marketplace-erbjudande och fakturerar kunder för din räkning. (Antingen PAYG kreditkort eller Enterprise Faktura)||
| Marketplace-licenstyp: Ta med din egen licens | Beställa| Den virtuella datorn kräver en licensnyckel som tillhandahålls av kunden för att distribuera. Microsoft fakturerar inte kunder för att lista sina erbjudanden på detta sätt via marknadsplatsen.||
| Marketplace-licenstyp: Gratis| Beställa| Erbjudandet är konfigurerat för att vara gratis för alla användare. Microsoft fakturerar inte kunder för deras användning av detta erbjudande.||
| Licenstyp för Marketplace: Microsoft som återförsäljare  | Beställa| Microsoft är din återförsäljare för detta Marketplace-erbjudande.|  |
| Prenumerations-ID för Marketplace | Kund, Order | Den unika identifierare som är associerad med Azure-prenumerationen som kunden använde för att köpa ditt Marketplace-erbjudande. ID var tidigare Azure Subscription GUID.||
| Erbjudandets namn  | Beställa| Namnet på Marketplace-erbjudandet.|| |
| Typ av erbjudande  | Beställa| Den typ av Microsoft Marketplace-erbjudande.|||
| Typ av erbjudande: Hanterat program  | Ordning | Använd Azure-appen: hanterad app erbjudandetyp när följande villkor krävs: Du distribuerar antingen en prenumerationsbaserad lösning för din kund med antingen en virtuell dator eller en hel IaaS-baserad lösning. Du eller din kund kräver att lösningen hanteras av en partner. |
| Typ av erbjudande: Azure-program| Ordning | Använd erbjudandetypen för Azure Application-lösningsmallen när din lösning kräver ytterligare distribution och konfigurationsautomatisering utöver en enkel virtuell dator.||
| Typ av erbjudande: Konsulttjänster| Beställa| Konsulttjänster på Azure Marketplace hjälper till att ansluta kunder till tjänster för att stödja och utöka deras användning av Azure.| |
| Typ av erbjudande: Behållare | Beställa| Använd erbjudandetypen Behållare när din lösning är en Docker-behållaravbildning som etablerats som en Kubernetes-baserad Azure-behållartjänst.||
| Typ av erbjudande: Dynamics 365 Business Central| Beställa| Använd den här erbjudandetypen när din lösning är integrerad med Dynamics 365 for Finance and Operations| |
| Typ av erbjudande: Dynamics 365 för kundengagemang | Beställa| Använd den här erbjudandetypen när din lösning är integrerad med Dynamics 365 for Customer Engagement.||
| Typ av erbjudande: IoT Edge-modul | Beställa| Azure IoT Edge-moduler är de minsta beräkningsenheterna som hanteras av IoT Edge och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösningsspecifika kod. |
| Typ av erbjudande: Power BI-program | Beställa| Använd erbjudandetypen För Power BI-program när du distribuerar ett program som är integrerat med Power BI.|  |
| Typ av erbjudande: SaaS-program| Beställa| Använd erbjudandetypen SaaS-appen för att göra det möjligt för kunden att köpa din SaaS-baserade, tekniska lösning som prenumeration.||
| Typ av erbjudande: Virtuell dator | Beställa| Använd erbjudandetypen för den virtuella datorn när du distribuerar en virtuell installation till prenumerationen som är kopplad till kunden.||
| Erbjudandetyp: Tillägg för Visual Studio Marketplace  | Beställa| Erbjudandetyp som tidigare var tillgänglig för Azure DevOps-tilläggsutvecklare. Framöver kan Azure DevOps-tilläggsutvecklare sälja sina tillägg direkt till kunder. Tilläggserbjudanden kan konfigureras som betalda eller inklusive en utvärderingsversion. |
| Avbokningsdatum för order| Beställa| Datumet då Marketplace-ordern annullerades.||
| Order-ID| Beställa| Den unika identifieraren för kundordern för din Marketplace-tjänst. Användningsbaserade erbjudanden för virtuella datorer är inte associerade med en order.| |
| Inköpsdatum för order| Beställa| Datumet då Marketplace-ordern skapades.|||
| Orderstatus| Beställa| Statusen för en Marketplace-order vid den tidpunkt då data senast uppdaterades.|     |
| Orderstatus: Aktiv  | Beställa| Kunden har köpt en order och har inte annullerat sin beställning.|         |
| Orderstatus: annullerad | Beställa| Kunden har tidigare köpt en order och annullerade därefter sin beställning.||
| E-post för leverantör| Kund| E-postadressen till den leverantör som är involverad i relationen mellan Microsoft och slutkunden. Om kunden är en Enterprise through Reseller, kommer detta att vara återförsäljaren. Om en CSP (Cloud Solution Provider) är inblandad blir detta CSP.|
| Leverantörens namn| Kund| Namnet på den leverantör som är involverad i relationen mellan Microsoft och slutkunden. Om kunden är en Enterprise through Reseller, kommer detta att vara återförsäljaren. Om en CSP (Cloud Solution Provider) är inblandad blir detta CSP.|
| SKU| Beställa| SKU-namn som definierats under publiceringen. Ett erbjudande kan ha många SKU: er, men en SKU kan bara associeras med ett enda erbjudande.||
| Slutdatum för utvärderingsversionen| Beställa| Det datum då provperioden för den här ordern avslutas eller har avslutats.||

## <a name="next-steps"></a>Nästa steg

- En översikt över analysrapporter som är tillgängliga på den kommersiella marknaden för Partner Center finns i [Analytics för den kommersiella marknadsplatsen i Partner Center](./analytics.md).
- Diagram, trender och värden för aggregerade data som sammanfattar marketplace-aktivitet för ditt erbjudande finns [i Sammanfattningsinstrumentpanel i kommersiell marknadsplatsanalys](./summary-dashboard.md).
- Information om dina beställningar i ett grafiskt och nedladdningsbart format finns [i Order dashboard i kommersiell marknadsplatsanalys](./orders-dashboard.md).
- För virtuell dator (VM) erbjuder användnings- och datapriser för faktureringsmått, se [Användningsinstrumentpanel i kommersiell marknadsplatsanalys](./usage-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxttrender, finns [i Kundinstrumentpanelen i kommersiell marknadsplatsanalys](./customer-dashboard.md).
- En lista över dina hämtningsbegäranden under de senaste 30 dagarna finns [i Hämta instrumentpanel i kommersiell marknadsplatsanalys](./downloads-dashboard.md).
- Information om hur du ser en konsoliderad vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource finns [i instrumentpanelen Klassificeringar och recensioner i kommersiell marketplace-analys](./ratings-reviews.md).
