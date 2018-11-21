---
title: Cloud Cruiser och Microsoft Azure Billing API-integrering | Microsoft Docs
description: 'Tillhandahåller ett unikt perspektiv från Microsoft Azure-fakturering partner Cloud Cruiser på deras upplevelser som integrerar Azure Billing API: erna i sin produkt.  Detta är särskilt användbart för Azure och Cloud Cruiser kunder som vill använda/försök Cloud Cruiser för Microsoft Azure-paket.'
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: b65128cf-5d4d-4cbd-b81e-d3dceab44271
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/09/2017
ms.author: erikre
ms.openlocfilehash: 79582e59d9ad9396acf29d6e35d640edcb20dca3
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275963"
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cloud Cruiser och Microsoft Azure Billing API-Integration
Den här artikeln beskrivs hur informationen som samlas in från den nya Microsoft Azure Billing API: er kan användas i Cloud Cruiser för arbetsflödet kostnaden simulering och analys.

## <a name="azure-ratecard-api"></a>Azure RateCard-API
RateCard-API: et innehåller priset information från Azure. När de har autentiserat med rätt autentiseringsuppgifter, kan du fråga API för att samla in metadata om tjänsterna som är tillgängliga på Azure, tillsammans med priserna som är associerade med ditt som erbjuder-ID.

Följande exempelsvar är som visar priset för A0-API (Windows)-instans:

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Cloud Cruisers gränssnitt för Azure RateCard API
Cloud Cruiser kan använda RateCard-API-informationen på olika sätt. I den här artikeln visar vi hur den kan användas för att göra IaaS arbetsbelastning kosta simulering och analys.

Föreställ dig en arbetsbelastning på flera instanser som körs på Microsoft Azure Pack (WAP) för att visa det här användningsfallet. Målet är att simulera det här samma arbetsbelastning på Azure och beräkna kostnaderna för att göra sådana migrering. Det finns två huvudsakliga uppgifter som ska utföras för att skapa simuleringen:

1. **Importera och bearbeta information om tjänstens som samlats in från RateCard-API.** Den här uppgiften utförs också på arbetsböcker, där extrahera från API: T för RateCard omvandlas och publiceras till en ny prislistor. Den här nya prislistor används på simuleringar för att beräkna priser för Azure.
2. **Normalisera WAP-tjänster och Azure-tjänster för IaaS.** Som standard WAP-tjänster baserat på enskilda resurser (CPU, minne, diskens storlek osv.) medan Azure services baseras på instansstorlek (A0, A1, A2, osv.). Den här första uppgiften kan utföras av Cloud Cruiser ETL-motorn, kallas arbetsböcker, där de här resurserna kan ligga på instansstorlekar som motsvarar instansen för Azure-tjänster.

### <a name="import-data-from-the-ratecard-api"></a>Importera data från RateCard-API
Cloud Cruiser arbetsböcker får en automatiserad metod för att samla in och bearbeta information från RateCard-API: et.  ETL (extrahering, transformering och laddning) arbetsböcker kan du konfigurera samlingen, omvandling och publicering av data i Cloud Cruiser-databasen.

Varje arbetsbok kan ha en eller flera samlingar, så att du kan kombinera information från olika källor för att komplettera eller förbättra användningsdata. Följande två skärmbilderna visar hur du skapar en ny *samling* i en befintlig arbetsbok och importera information till den *samling* RateCard-API:

![Bild 1 – skapa en ny samling][1]

![Bild 2 – importera data från den nya samlingen][2]

Det är möjligt att skapa flera steg och omvandling processer, ändra och modellera data när du har importerat data i arbetsboken. I det här exemplet eftersom vi bara är intresserad av infrastructure-as-a-Service (IaaS) som vi kan använda omvandling steg för att ta bort onödiga rader eller poster som rör tjänster än IaaS.

Följande skärmbild visar transformeringssteg som används för att bearbeta data som samlas in från RateCard-API:

![Bild 3 – transformeringssteg för att bearbeta insamlade data från API: T för RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Definiera nya tjänster och Rate-planer
Det finns olika sätt att definiera tjänster på Cloud Cruiser. Ett alternativ är att importera tjänsterna från användningsdata. Den här metoden används vanligtvis när du arbetar med offentliga moln, där tjänsten redan har definierats av providern.

En Rate-Plan är en uppsättning avgifter eller priser som kan tillämpas på olika tjänster, baserat på giltighetsdatum eller grupp av kunder, bland andra alternativ. Prislistor kan även användas på Cloud Cruiser för att skapa simulering eller ”vad händer om”-scenarier, att förstå hur ändringar i tjänster kan påverka den totala kostnaden för en arbetsbelastning.

I det här exemplet använder vi information om tjänstens RateCard-API för att definiera nya tjänster i Cloud Cruiser. På samma sätt kan vi använda priserna som är kopplade till tjänsterna för att skapa en ny Rate-Plan i Cloud Cruiser.

Det är möjligt att skapa ett nytt steg och publicera data från API: T för RateCard som nya tjänster och kostnader i slutet av processen omvandling.

![Bild 4 – publicera data från API: T för RateCard som nya tjänster och kostnader][4]

### <a name="verify-azure-services-and-rates"></a>Verifiera Azure-tjänster och kostnader
När du har publicerat tjänster och kostnader, kan du kontrollera listan över importerade tjänster i Cloud Cruiser *Services* fliken:

![Figur 5 – verifiera de nya tjänsterna][5]

På den *Rate planer* fliken som du kan kontrollera vilken nya prislista som kallas ”AzureSimulation” med priserna importeras från RateCard-API: et.

![Bild 6 – verifiera ny Rate-Plan och associerade kostnader][6]

### <a name="normalize-wap-and-azure-services"></a>Normalisera WAP och Azure-tjänster
Som standard innehåller WAP användningsinformation som baseras på användningen av beräkning, minne och nätverksresurser. Du kan definiera dina tjänster baserade i Cloud Cruiser direkt på allokering eller förbrukade användning av dessa resurser. Du kan till exempel ange en grundläggande hastighet för varje timme som CPU-användning eller debiterar GB minne som allokeras till en instans.

I det här exemplet för att jämföra kostnader mellan WAP och Azure, måste vi aggregera resursanvändning på WAP i paket, som sedan kan mappas till Azure-tjänster. Den här omvandlingen kan implementeras enkelt i arbetsböcker:

![Figur 7 – transformera WAP-data för att normalisera tjänster][7]

Det sista steget i arbetsboken är att publicera data till Cloud Cruiser-databasen. Det här steget användningsdata nu paketeras i tjänster (som mappar till Azure-tjänster) och knuten till standardkostnader skapa avgifterna.

När du har gått arbetsboken, kan du automatisera bearbetning av data, genom att lägga till en uppgift i Schemaläggaren och ange frekvens och tid för arbetsboken ska köras.

![Figur 8 – arbetsboken schemaläggning][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Skapa rapporter för arbetsbelastningen kostnad simulering analys
När användningen som samlas in och avgifter läses in i Cloud Cruiser databasen, kan vi använda Cloud Cruiser Insights-modulen för att skapa arbetsbelastningen kostnad simulering vi är intresserade.

Vi skapade följande rapport för att visa det här scenariot:

![Jämförelse av kostnader][9]

Övre diagrammet visar en jämförelse av en kostnad av tjänster, att jämföra priset för att köra arbetsbelastningen för varje specifik tjänst mellan WAP (mörkblå) och Azure (ljusblå).

Diagrammet längst ned visar samma data men uppdelade efter avdelning. Kostnaderna för varje avdelning att köra arbetsbelastningen på WAP och Azure, tillsammans med skillnaden mellan dem visas i fältet besparingar (grön).

## <a name="azure-usage-api"></a>API för Azure-användning
### <a name="introduction"></a>Introduktion
Microsoft introducerade nyligen Azure användning API, så att prenumeranter att programmässigt dra in användningsdata för att få insikter om deras användning. Cloud Cruiser-kunder kan dra nytta av bättre datauppsättningen tillgängliga via den här API: et.

Cloud Cruiser kan använda integreringen med API för användning i flera olika sätt. Precisionen (per timme användningsinformation) och resource metadatainformation som är tillgängliga via API: et tillhandahåller nödvändiga datauppsättningen för att stödja flexibla Showback eller Chargeback-modeller. 

I de här självstudierna presenterar vi ett exempel på hur Cloud Cruiser kan dra nytta av användning API-information. Mer specifikt kommer vi skapa en resursgrupp på Azure, associera taggar för kontostruktur och beskriver hur du hämtar och bearbetar tagginformation i Cloud Cruiser.

Slutgiltiga målet är att kunna skapa rapporter som det nedanstående och kunna analysera kostnaderna och förbrukning baserat på kontostrukturen har lagts till av taggarna.

![Bild 10 - rapport med uppdelningar med hjälp av taggar][10]

### <a name="microsoft-azure-tags"></a>Microsoft Azure-taggar
Data som är tillgängliga via API för användning i Azure innehåller information om förbrukningen utan även resursmetadata, inklusive alla taggar som är kopplade till den. Taggar är ett enkelt sätt att organisera dina resurser, men för att gälla, måste du se till att:

* Taggar tillämpas korrekt till resurserna på etablering
* Taggar används korrekt kostnadsrapporter/återbetalning processen för att knyta användningen av organisationens kontostruktur.

Båda dessa krav kan vara svårt, särskilt när det är en manuell process på tillhandahållande eller debitera sida. Felstavat, felaktig eller även saknas taggar är vanligt klagomål från kunder när med hjälp av taggar och de här felen kan underlätta på laddas sida svårt.

Med den nya API för Azure-användning, Cloud Cruiser hämta taggning resursinformationen och via avancerade ETL verktyget arbetsböcker, korrigera felen för vanliga Taggning. Genom omvandling med reguljära uttryck och korrelation av data, Cloud Cruiser identifiera felaktigt taggade resurser och lägga till rätt taggar att se till att rätt association av resurser med konsumenten.

På sidan Ladda Cloud Cruiser automatiserar processen kostnadsrapporter/återbetalning och Tagginformationen kan använda för att knyta användningen av lämplig konsumenten (avdelning, avdelning, projekt osv.). Det här automation ger en enorm förbättring och kan säkerställa en konsekvent och granskningsbar laddas process.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Skapa en resursgrupp med taggar på Microsoft Azure
Det första steget i den här självstudien är att skapa en resursgrupp i Azure portal, skapa nya taggar om du vill koppla till resurserna. I det här exemplet vi skapa följande taggar: avdelning, miljö, ägare, projekt.

Följande skärmbild visar ett urval resursgruppen med tillhörande taggar.

![Bild 11 - resursgrupp med tillhörande taggar på Azure portal][11]

Nästa steg är att hämta informationen från API för användning i Cloud Cruiser. Användning-API: et tillhandahåller för närvarande svar i JSON-format. Här är ett exempel på data som hämtats:

    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importera data från API för användning i Cloud Cruiser
Cloud Cruiser arbetsböcker får en automatiserad metod för att samla in och bearbeta information från API för användning. En arbetsbok för ETL (extrahering, transformering och laddning) kan du konfigurera samlingen, omvandling och publicering av data i Cloud Cruiser-databasen.

Varje arbetsbok kan ha en eller flera samlingar. På så sätt kan du kombinera information från olika källor för att komplettera eller förbättra användningsdata. I det här exemplet skapar vi ett nytt blad i arbetsboken Azure-mall (*UsageAPI)* och ange ett nytt *samling* att importera informationen från API för användning.

![Bild 3 – användnings-API-data som importerats till UsageAPI-blad][12]

Observera att den här arbetsboken redan har andra meddelanden att importera tjänster från Azure (*ImportServices*), och bearbeta förbrukning information från Billing-API (*PublishData*).

Nu ska vi använda API: et för användning för att fylla i *UsageAPI* blad och korrelera informationen med förbrukningsdata från Billing-API på den *PublishData* blad.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Bearbetning av tagginformation från API för användning
När du har importerat data i arbetsboken, vi skapar transformeringssteg i den *UsageAPI* blad för att bearbeta information från API: et. Första steget är att använda en ”JSON dela” processor att extrahera taggar från ett enda fält och sedan skapa fält för var och en av dem (avdelning, projekt, ägare och miljö).

![Bild 4 - skapa nya fält för Tagginformationen][13]

Lägg märke till tjänsten ”nätverk” saknar tagginformation (gul ruta), men vi kan kontrollera att det är en del av samma resursgrupp genom att titta på den *ResourceGroupName* fält. Eftersom vi har taggar för de övriga resurserna från den här resursgruppen kan använda vi den här informationen för att tillämpa saknas taggar till den här resursen senare i processen.

Nästa steg är att skapa en uppslagstabell som kopplar informationen från taggar till den *ResourceGroupName*. Den här uppslagstabell används i nästa steg för att utöka förbrukningsdata med tagginformation.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Att lägga till Tagginformationen i förbrukningsdata
Nu vi kan gå till den *PublishData* blad, som bearbetar informationen förbrukning från Billing-API och lägger till de fält som extraheras från taggarna. Den här processen utförs genom att titta på uppslagstabellen skapade i föregående steg, med hjälp av den *ResourceGroupName* som nyckel för sökningarna.

![Figur 5 – fylla kontostrukturen med information från sökningarna][14]

Observera att lämpligt konto struktur fält för tjänsten ”nätverk” har tillämpats, åtgärda problemet med taggarna saknas. Vi också fylls i fälten konto struktur för resurser än våra mål resursgruppen med ”annan” för att skilja dem i rapporterna.

Nu behöver vi bara att lägga till ett steg för att publicera användningsdata. Det här steget tillämpas blockblobavgifter för varje tjänst som definierats på vår priset har tänkt på användningsinformationen resulterande kostnad läses in i databasen.

Det bästa av allt är att du bara behöver gå igenom den här processen en gång. När arbetsboken är klar måste du lägga till den i scheduler och den körs varje timme eller varje dag vid den schemalagda tiden. Är det bara skapa nya rapporter och anpassa befintliga, för att analysera data för att få värdefull information från din molnanvändning.

### <a name="next-steps"></a>Nästa steg
* Detaljerade anvisningar om hur du skapar Cloud Cruiser arbetsböcker och rapporter finns i Cloud Cruiser online [dokumentation](http://docs.cloudcruiser.com/) (giltigt inloggning).  Mer information om Cloud Cruiser Kontakta [ info@cloudcruiser.com ](mailto:info@cloudcruiser.com).
* Se [insyn i din Microsoft Azure-resursförbrukning](billing-usage-rate-card-overview.md) för en översikt över Azure Resource-användning och RateCard APIs.
* Kolla in den [Azure Billing REST API-referens](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) mer information om båda API: er som är en del av en uppsättning API: er som tillhandahålls av Azure Resource Manager.
* Om du vill gå direkt till exempelkoden Kolla in våra Microsoft Azure Billing API-kodexempel på [kodexempel för Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Läs mer
* Läs mer om Azure Resource Manager i den [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) artikeln.

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Bild 1 – skapa en ny samling"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Bild 2 – importera data från den nya samlingen"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Bild 3 – transformeringssteg för att bearbeta insamlade data från API: T för RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Bild 4 – publicera data från API: T för RateCard som nya tjänster och kostnader"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figur 5 – verifiera de nya tjänsterna"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Bild 6 – verifiera ny Rate-Plan och associerade kostnader"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figur 7 – transformera WAP-data för att normalisera tjänster"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figur 8 – arbetsboken schemaläggning"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Bild 9 - exempelrapporten för scenariot med arbetsbelastningen kostnaden jämförelse"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Bild 10 - rapport med uppdelningar med hjälp av taggar"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Bild 11 - resursgrupp med tillhörande taggar på Azure portal"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figur 12 - användnings-API-data som importerats till UsageAPI-blad"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figur 13 - skapa nya fält för Tagginformationen"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figur 14 – fylla kontostrukturen med information från sökningarna"
