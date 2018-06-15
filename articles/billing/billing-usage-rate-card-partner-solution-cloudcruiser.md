---
title: Molnet Cruiser och Microsoft Azure Billing API-Integration | Microsoft Docs
description: 'Ger ett unikt perspektiv från Microsoft Azure Billing partner molnet Cruiser på deras upplevelser integrera Azure Billing API: erna i sin produkt.  Detta är särskilt användbart för Azure och molnet Cruiser kunder som vill använda/försök molnet Cruiser för Microsoft Azure-paket.'
services: ''
documentationcenter: ''
author: tonguyen
manager: tonguyen
editor: ''
tags: billing
ms.assetid: b65128cf-5d4d-4cbd-b81e-d3dceab44271
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/09/2017
ms.author: mobandyo
ms.openlocfilehash: 487636ffb0efc35c282e14d835c6669ed9d47315
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
ms.locfileid: "32771505"
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Molnet Cruiser och Microsoft Azure Billing API-integrering
Den här artikeln beskrivs hur informationen som samlas in från den nya Microsoft Azure Billing API: er kan användas i molnet Cruiser för arbetsflödet kostnaden simulering och analys.

## <a name="azure-ratecard-api"></a>Azure RateCard API
RateCard API ger hastighet information från Azure. Efter autentisering med rätt autentiseringsuppgifter, kan du fråga API för att samla in metadata om tjänster som är tillgängliga på Azure, tillsammans med kostnader som är kopplade till ditt som erbjuder-ID.

Följande exempel svaret är från visar priserna för A0 API (Windows)-instans:

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

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Molnet Cruisers gränssnitt för Azure RateCard API
Molnet Cruiser kan använda RateCard API-informationen på olika sätt. I den här artikeln visar hur det kan användas för att göra IaaS arbetsbelastning kostnad simulering och analys.

Anta en arbetsbelastning med flera instanser som körs på Microsoft Azure-paket för att visa den här användningsfall. Målet är att simulera samma arbetsbelastningen på Azure och göra en uppskattning av kostnaderna för att göra sådana migrering. Det finns två huvudsakliga uppgifter som ska utföras för att skapa simuleringen:

1. **Import- och tjänstinformation som samlas in från RateCard-API.** Den här uppgiften utförs också på arbetsböcker, där utdrag ur RateCard API omvandlas och publiceras på en ny plan hastighet. Den här nya abonnemang används på simuleringarna för att beräkna priser för Azure.
2. **Normalisera WAP-tjänster och Azure IaaS-tjänster.** Som standard WAP tjänster baseras på enskilda resurser (CPU, minne, diskens storlek osv.) medan Azure services baseras på instansstorleken (A0, A1, A2 osv.). Uppgiften första kan utföras av molnet Cruiser ETL-motorn, kallas arbetsböcker, där resurserna kan ligga på instansen storlekar, analogt med Azure-instans-tjänster.

### <a name="import-data-from-the-ratecard-api"></a>Importera data från RateCard-API
Molnet Cruiser arbetsböcker ger en automatiserad metod för att samla in och bearbeta information från RateCard-API: et.  ETL (extract-transform-load) arbetsböcker kan du konfigurera samlingen, omvandling och publicering av data i molnet Cruiser-databasen.

Varje arbetsbok kan ha en eller flera samlingar, så att du kan korrelera information från olika källor att komplettera eller utöka användningsdata. Följande två skärmdumpar visar hur du skapar en ny *samling* i en befintlig arbetsbok och importera information till den *samling* från RateCard-API:

![Bild 1 - skapa en ny samling][1]

![Bild 2 - importera data från den nya samlingen][2]

När du har importerat data i arbetsboken är det möjligt att skapa flera steg och processer för omvandling, ändra och modeller för data. I det här exemplet eftersom det bara är intresserad av infrastruktur-som-en-tjänst (IaaS) vi kan använda omvandling steg för att ta bort onödiga rader eller poster rör tjänster än IaaS.

Följande skärmbild visar stegen för transformation som används för att bearbeta de data som samlas in från RateCard API:

![Bild 3 - Transformation steg för att bearbeta data som samlats in från RateCard API: et][3]

### <a name="defining-new-services-and-rate-plans"></a>Definiera nya tjänster och hastighet planer
Det finns olika sätt att definiera tjänster i molnet Cruiser. Ett alternativ är att importera tjänsterna från användningsdata. Den här metoden används ofta när du arbetar med offentliga moln, där tjänsten redan har definierats av providern.

Planera en hastighet är en uppsättning priser eller priser som kan tillämpas på olika tjänster, baserat på giltighetsdatum eller grupp av kunder, bland andra alternativ. Priser kan även användas på molnet Cruiser för att skapa simuleringen eller ”vad händer om”-scenarier att förstå hur ändringar i tjänster kan påverka den totala kostnaden för en arbetsbelastning.

I det här exemplet använder vi tjänstinformation från RateCard API för att definiera nya tjänster i molnet Cruiser. Vi kan använda de priser som är kopplade till tjänsterna på samma sätt för att skapa en ny hastighet planera på molnet Cruiser.

Det är möjligt att skapa ett nytt steg och publicera data från RateCard API som nya tjänster och priser i slutet av processen för omvandling.

![Bild 4 - publicera data från RateCard-API som nya tjänster och priser][4]

### <a name="verify-azure-services-and-rates"></a>Verifiera Azure-tjänster och priser
När du har publicerat tjänster och priser, kan du kontrollera listan över importerade tjänster i molnet Cruiser *Services* fliken:

![Bild 5 - verifiering av nya tjänster][5]

På den *hastighet planer* fliken kan du kontrollera den nya hastighet planen som kallas ”AzureSimulation” med de importeras från RateCard-API.

![Bild 6 - verifiera nya priser och associerade priser][6]

### <a name="normalize-wap-and-azure-services"></a>Normalisera WAP och Azure-tjänster
Som standard innehåller WAP användningsinformation baserat på användning av beräkningar, minne och nätverksresurser. I molnet Cruiser kan du definiera dina tjänster som baseras direkt på allokering eller förbrukade användning av dessa resurser. Du kan till exempel ange en grundläggande hastighet för varje timme av CPU-användning eller debiterar GB minne som allokerats till en instans.

I det här exemplet för att jämföra kostnader mellan WAP och Azure, behöver vi sammanställa resursanvändning på WAP i paket, som sedan kan mappas till Azure-tjänster. Den här omvandlingen kan enkelt implementeras i arbetsböckerna:

![Figur 7 – omvandla WAP-data för att normalisera tjänster][7]

Det sista steget i arbetsboken är att publicera data till molnet Cruiser-databasen. Det här steget användningsdata nu ihop till tjänster (som mappar till Azure-tjänster) och knuten till standard priser för att skapa avgifterna.

Du kan automatisera bearbetning av data, genom att lägga till en uppgift i Schemaläggaren och ange frekvens och tidpunkt för arbetsboken ska köras när arbetsboken är klar.

![Figur 8 - arbetsbok schemaläggning][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Skapa rapporter för arbetsbelastningen kostnad simuleringen analys
När användningen samlas in och avgifter läses in i molnet Cruiser databasen kan använda vi modulen molnet Cruiser insikter för att skapa arbetsbelastningen kostnad simuleringen som vi vill ha.

Vi skapade följande rapport för att visa det här scenariot:

![Jämförelse av kostnader][9]

Övre diagrammet visar en kostnadsjämförelse av tjänster, jämföra priset på arbetsbelastningen för varje specifik tjänst mellan WAP (mörkt blå) och Azure (lätta blå) körs.

Längst ned diagrammet visar samma data men uppdelade efter avdelning. Kostnader för varje avdelning att köra arbetsbelastningen på WAP och Azure, tillsammans med skillnaden mellan dem visas i fältet besparingar (grön).

## <a name="azure-usage-api"></a>Användning av Azure API
### <a name="introduction"></a>Introduktion
Microsoft introducerade nyligen Azure användning API, vilket gör att prenumeranter att genom programmering dra in användningsdata och få insikter om deras användning. Molnet Cruiser kunder kan dra nytta av bättre dataset som är tillgängliga via den här API.

Molnet Cruiser kan använda integreringen med API för användning på flera sätt. Granularitet (varje timme användningsinformation) och metadata resursinformationen tillgänglig via API ger nödvändiga dataset för att stödja flexibla Showback eller återbetalning modeller. 

I den här kursen presenterar vi ett exempel på hur molnet Cruiser kan dra nytta av användning av API-information. Mer specifikt ska vi skapa en resursgrupp i Azure, associera taggar för konto och beskriver processen att dra och bearbetning av tagginformation till molnet Cruiser.

Sista målet är att kunna skapa rapporter som det nedanstående och kunna analysera kostnad och förbrukning av baserat på kontostrukturen som fylls i av taggarna.

![Bild 10 - rapport med uppdelning med hjälp av taggar][10]

### <a name="microsoft-azure-tags"></a>Microsoft Azure-taggar
Tillgängliga via API: et för Azure Usage data innehåller information om förbrukningen utan även resursmetadata, inklusive alla taggar som är kopplade till den. Taggar ger ett enkelt sätt att organisera dina resurser, men för att gälla, måste du se till att:

* Taggar tillämpas korrekt till resurserna på etablera tid
* Taggar används korrekt Showback/förbrukning processen för att koppla användningen till organisationens kontostrukturen.

Båda dessa villkor kan vara svårt, särskilt när det är en manuell process för att tillhandahålla eller debitering sida. Felstavat, felaktigt eller också saknas taggar är vanligt klagomål från kunder när med taggar och dessa fel kan underlätta på laddas sida svårt.

Med nya Azure användning API molnet Cruiser pull-märkning resursinformation och via avancerade ETL verktyget arbetsböcker, korrigera felen för vanliga märkning. Genom omvandling med reguljära uttryck och data korrelation, molnet Cruiser identifiera felaktigt formaterad resurser och tillämpa rätt taggar säkerställer rätt association av resurser med konsumenten.

På sidan laddas molnet Cruiser automatiserar processen Showback/förbrukning och Tagginformationen kan använda för att koppla användningen till lämplig konsumenten (avdelning, Division, projekt, etc.). Denna automatisering ger en enorm förbättring och säkerställa en konsekvent och granskningsbara laddas process.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Skapa en resursgrupp med taggar i Microsoft Azure
Det första steget i den här kursen är att skapa en resursgrupp i Azure portal, skapa nya etiketter ska associeras till resurserna. I det här exemplet vi skapa följande taggar: avdelning, miljö, ägare, projekt.

Följande skärmbild visar ett exempel på en resursgrupp med tillhörande taggar.

![Figur 11 - resursgrupp med tillhörande taggar på Azure-portalen][11]

Nästa steg är att hämta informationen från API: et för användning i molnet Cruiser. Användning-API tillhandahåller för närvarande svar i JSON-format. Här är ett exempel på data som hämtats:

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


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importera data från API: et för användning i molnet Cruiser
Molnet Cruiser arbetsböcker ger en automatiserad metod för att samla in och bearbeta information från API: et för användning. En arbetsbok för ETL (extract-transform-load) kan du konfigurera samlingen, omvandling och publicering av data i molnet Cruiser-databasen.

Varje arbetsbok kan ha en eller flera samlingar. På så sätt kan du matcha information från olika källor att komplettera eller utöka användningsdata. I det här exemplet skapar vi ett nytt blad i arbetsboken Azure-mall (*UsageAPI)* och ange en ny *samling* att importera informationen från API: et för användning.

![Bild 3 - användningsdata API som importeras till UsageAPI blad][12]

Observera att den här arbetsboken redan har andra blad att importera tjänster från Azure (*ImportServices*), den förbrukning information från fakturerings-API (*PublishData*).

Därefter vi använder användning API för att fylla i *UsageAPI* sheet och jämföra informationen med förbrukningsdata från fakturering API på den *PublishData* blad.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Bearbetning av tagginformation från API: et för användning
När du importerar data till arbetsboken kan vi skapa omvandling av stegen i den *UsageAPI* blad för att kunna bearbeta information från API: et. Första steget är att använda en ”JSON dela” processor extrahera taggarna från ett enda fält och sedan skapa fält för var och en av dem (avdelning, projekt, ägare och miljö).

![Bild 4 - skapa nya fält för Tagginformationen][13]

Lägg märke till tjänsten ”nätverk” saknar tagginformation (gul ruta), men vi kan verifiera att den är del av samma resursgrupp genom att titta på den *ResourceGroupName* fältet. Eftersom vi har taggar för resurser från den här resursgruppen kan använder vi informationen för att använda taggar som saknas på den här resursen senare i processen.

Nästa steg är att skapa en uppslagstabell associerar information från taggar till den *ResourceGroupName*. Den här uppslagstabellen används på nästa steg för att utöka förbrukningsdata med tagginformation.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Lägga till Tagginformationen i förbrukningsdata
Nu kan vi gå till den *PublishData* sheet, som bearbetar förbrukning information från fakturerings-API och Lägg till de fält som har extraherats från taggar. Den här processen utförs genom att titta på uppslagstabellen skapas på föregående steg, med hjälp av den *ResourceGroupName* som nyckel för sökningarna.

![Bild 5 - fylla kontostruktur med information från sökningarna][14]

Observera att lämpligt konto struktur fält för tjänsten ”nätverk” har tillämpats, åtgärda problemet med taggar som saknas. Vi också fylls i fälten konto struktur för resurser än våra mål resursgrupp med ”andra”, för att skilja dem i rapporter.

Vi behöver nu bara lägga till ett steg för att publicera användningsdata. Det här steget tillämpas lämpliga priser för varje tjänst som definierats på våra priser på användningsinformationen med resulterande tillägget läses in i databasen.

Det bästa är att du bara behöver gå igenom den här processen en gång. När arbetsboken är klar måste du lägga till den i Schemaläggaren och det körs varje timme eller varje dag vid den schemalagda tiden. Är det bara gäller att skapa nya rapporter och anpassa befintliga, för att analysera data för att få meningsfull information från din molnanvändning.

### <a name="next-steps"></a>Nästa steg
* Detaljerade anvisningar om hur du skapar moln Cruiser arbetsböcker och rapporter finns i molnet Cruiser online [dokumentationen](http://docs.cloudcruiser.com/) (giltig inloggning krävs).  Mer information om molnet Cruiser [ info@cloudcruiser.com ](mailto:info@cloudcruiser.com).
* Se [få insikter om dina Microsoft Azure-resursförbrukning](billing-usage-rate-card-overview.md) för en översikt över Azure Resursanvändning och RateCard APIs.
* Kolla in den [Azure Billing REST API-referens](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) mer information om både API: er som är en del av en uppsättning API: er som tillhandahålls av Azure Resource Manager.
* Om du vill sätta igång direkt i exempelkoden checka ut våra Microsoft Azure Billing API kodexempel på [Azure kodexempel](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Läs mer
* Läs mer om Azure Resource Manager i den [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) artikel.

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Bild 1 - skapa en ny samling"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Bild 2 - importera data från den nya samlingen"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Bild 3 - Transformation steg för att bearbeta data som samlats in från RateCard API: et"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Bild 4 - publicera data från RateCard-API som nya tjänster och priser"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Bild 5 - verifiering av nya tjänster"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Bild 6 - verifiera nya priser och associerade priser"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figur 7 – omvandla WAP-data för att normalisera tjänster"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figur 8 - arbetsbok schemaläggning"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Bild 9 - exempelrapporten för arbetsbelastningen kostnaden jämförelse scenario"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Bild 10 - rapport med uppdelning med hjälp av taggar"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figur 11 - resursgrupp med tillhörande taggar på Azure-portalen"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figur 12 - användningsdata API som importeras till UsageAPI blad"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figur 13 - skapa nya fält för Tagginformationen"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figur 14 - fylla kontostruktur med information från sökningarna"
