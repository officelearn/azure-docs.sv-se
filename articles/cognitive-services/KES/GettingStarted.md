---
title: Kom igång med tjänsten Knowledge utforskning | Microsoft Docs
description: Använd Knowledge utforskning Service (KES) för att skapa en motor för en interaktiv sökinställningar över academic publikationer i kognitiva Microsoft-tjänster.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 02dc9368eef02d6fa507335ef3171e923412acca
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352512"
---
<a name="getting-started"></a>
# <a name="get-started-with-the-knowledge-exploration-service"></a>Kom igång med tjänsten Knowledge undersökning
I den här genomgången använder Knowledge utforskning Service (KES) för att skapa motor för en interaktiv sökinställningar för academic publikationer. Du kan installera verktyget kommandoraden [ `kes.exe` ](CommandLine.md), och alla exempel filer från de [Knowledge utforskning Service SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

Academic publikationer exempel innehåller ett exempel på 1000 academic rapporter som har publicerats av forskare på Microsoft.  Varje dokumentet är associerad med en rubrik, år, författare och nyckelord. Varje författare representeras av ett ID, namn och anknytningen vid tidpunkten för publikationen. Varje nyckelord kan associeras med en uppsättning synonymer (till exempel den nyckelordet ”support vector machine” kan associeras med synonymen ”svm”).

<a name="defining-schema"></a>
## <a name="define-the-schema"></a>Definiera schemat
Schemat beskriver attributet strukturen för objekt i domänen. Den anger namn och datatyp för varje attribut i en JSON-format. I följande exempel är innehållet i filen *Academic.schema*.

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Här kan du definiera *rubrik*, *år*, och *nyckelordet* som en sträng, heltal och strängen attributet respektive. Eftersom författare representeras av ID, namn och anknytningen, definiera *författaren* som en sammansatt attribut med tre underordnade attribut: *Author.Id*, *Author.Name*, och *Author.Affiliation*.

Som standard attribut stöder alla åtgärder som är tillgängliga för datatypen, inklusive *är lika med*, *starts_with*, och *is_between*. Eftersom författare ID används bara internt som en identifierare, åsidosätta standardinställningen och ange *är lika med* som den enda åtgärden som indexeras.

För den *nyckelordet* attribut, tillåter synonymer att matcha nyckelordet kanoniska värden genom att ange filen synonymen *Keyword.syn* i attributdefinitionen. Den här filen innehåller en lista över kanoniska och synonymen värde-par:

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

Mer information om schemadefinitionen finns [schemaformat](SchemaFormat.md).

<a name="generating-data"></a>
## <a name="generate-data"></a>Generera data
Datafilen beskriver listan på publikationer att indexera med varje rad som anger attributvärden en papper i [JSON-formatet](http://json.org/).  Följande exempel är en rad från datafilen *Academic.data*, formaterad för att läsa:

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

I den här fragment, anger du den *rubrik* och *år* attributet dokumentet som en JSON-strängen och nummer, respektive. Flera värden representeras med hjälp av JSON-matriser. Eftersom *författare* är ett sammansatt attribut varje värde representeras med hjälp av ett JSON-objekt som består av dess underordnade attribut. Attribut med saknade värden som *nyckelordet* i det här fallet kan uteslutas från JSON-representation.

För att skilja sannolikheten för olika rapporter, ange den relativa logg sannolikheten med hjälp av inbyggt *logprob* attribut. Få en sannolikhet *p* mellan 0 och 1 du beräkna loggen sannolikheten som loggen (*p*), där log() är funktionen den naturliga logaritmen.

Mer information finns i [dataformat](DataFormat.md).

<a name="building-index"></a>
## <a name="build-a-compressed-binary-index"></a>Skapa en komprimerad binärt index
När du har en schemafilen och datafilen, du kan skapa en komprimerad binärt index för dataobjekt med hjälp av [ `kes.exe build_index` ](CommandLine.md#build_index-command). I det här exemplet skapar du indexfilen *Academic.index* från filen inmatningsschemat *Academic.schema* och datafil *Academic.data*. Ange följande kommando:

`kes.exe build_index Academic.schema Academic.data Academic.index`

För snabb prototyper utanför Azure, [ `kes.exe build_index` ](CommandLine.md#build_index-command) kan bygga lokalt, small index från filer som innehåller upp till 10 000 objekt. För större datafiler, kan du antingen köra kommandot inifrån en [Windows VM i Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), eller utföra en fjärransluten version i Azure. Mer information finns i [skala upp](#scaling-up).

<a name="authoring-grammar"></a>
## <a name="use-an-xml-grammar-specification"></a>Använda en XML-grammatik specifikation
Grammatiken anger vilken naturligt språk frågor som tjänsten kan tolka samt hur dessa frågor med naturligt språk översätts till semantisk fråga uttryck. I det här exemplet använder du grammatik som anges i *academic.xml*:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

Mer information om syntaxen som grammatik specifikationen finns [Grammar Format](GrammarFormat.md).

<a name="compiling-grammar"></a>
## <a name="compile-the-grammar"></a>Kompilera grammatik
När du har en XML-grammatik specifikation du kompilera den till en binär grammatik med [ `kes.exe build_grammar` ](CommandLine.md#build_grammar-command). Observera att om grammatiken importerar ett schema, schemafilen måste finnas i samma sökväg som grammatik XML. I det här exemplet skapar du filen binär grammatik *Academic.grammar* från XML-grammatik indatafilen *Academic.xml*. Ange följande kommando:

`kes.exe build_grammar Academic.xml Academic.grammar`

<a name="hosting-index"></a>
## <a name="host-the-grammar-and-index-in-a-web-service"></a>Värd för grammatik och index i en webbtjänst
För snabb prototyper, du kan vara värd för grammatik och index i en webbtjänst på den lokala datorn med hjälp av [ `kes.exe host_service` ](CommandLine.md#host_service-command). Du kan sedan komma åt tjänsten via [webb-API: er](WebAPI.md) att validera data är korrekt och grammatik design. I det här exemplet är värd för grammatikfilen *Academic.grammar* och indexfilen *Academic.index* på http://localhost:8000/. Ange följande kommando:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Detta startar en lokal instans av webbtjänsten. Du kan testa tjänsten interaktivt genom att besöka `http::localhost:<port>` från en webbläsare. Mer information finns i [testar tjänsten](#testing-service).

Du kan också direkt anropa olika [webb-API: er](WebAPI.md) att testa tolkning av naturligt språk, fråga slutförande, structured query utvärdering och histogram beräkning. Stoppa tjänsten genom att ange ”Avsluta” i den `kes.exe host_service` Kommandotolken och tryck på Ctrl + C. Här följer några exempel:

* [http://localhost:8000/interpret?query=papers av susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers susan t d & slutförd = 1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais') & attributes=Title,Year,Author.Name,Author.Id & count = 2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'), år > = 2013) & attribut = år, nyckelord och count = 4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Utanför Azure, [ `kes.exe host_service` ](CommandLine.md#host_service-command) är begränsad till index för upp till 10 000 objekt. Andra gränser är en API som 10 begäranden per sekund och totalt 1000 begäranden innan processen avslutas automatiskt. Om du vill kringgå dessa begränsningar, kör du kommandot inifrån en [Windows VM i Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), eller om du vill distribuera till en Azure-molntjänst med hjälp av den [ `kes.exe deploy_service` ](CommandLine.md#deploy_service-command) kommando. Mer information finns i [distribuera tjänsten](#deploying-service).

<a name="scaling-up"></a>
## <a name="scale-up-to-host-larger-indices"></a>Skala upp värden större indexen
När du kör `kes.exe` utanför Azure, indexet är begränsad till 10 000 objekt. Du kan skapa och vara värd för större index med hjälp av Azure. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). Om du prenumererar på Visual Studio eller MSDN, du kan också [aktivera prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Dessa erbjuder vissa Azure-krediter varje månad.

Att tillåta `kes.exe` åtkomst till ett Azure-konto [hämta filen Azure Publiceringsinställningar](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) från Azure-portalen. Om du uppmanas logga in på önskat Azure-konto. Spara filen som *AzurePublishSettings.xml* i arbetskatalogen varifrån `kes.exe` körs.

Det finns två sätt att skapa och stora index. Först är att förbereda schemat och datafiler i en Windows-dator i Azure. Kör sedan [ `kes.exe build_index` ](#building-index) och skapa index lokalt på den virtuella datorn, utan storleksbegränsningar. Det resulterande indexet kan finnas lokalt på den virtuella datorn med hjälp av [ `kes.exe host_service` ](#hosting-service) för snabb prototyper igen utan begränsningar. Detaljerade anvisningar finns i [Azure VM kursen](../../../articles/virtual-machines/windows/quick-create-portal.md).

Den andra metoden är att genomföra en fjärransluten Azure version med hjälp av [ `kes.exe build_index` ](CommandLine.md#build_index-command) med den `--remote` parameter. Anger en Azure VM-storlek. När den `--remote` anges för parametern, kommandot skapar en tillfällig Azure VM av storleken. Därefter skapar index på den virtuella datorn, överför index till mål-blob storage, och tar bort den virtuella datorn när åtgärden har slutförts. Din Azure-prenumeration debiteras för kostnaden för den virtuella datorn när indexet skapas.

Den här remote Azure build-funktionen kan [ `kes.exe build_index` ](CommandLine.md#build_index-command) ska köras i alla miljöer. När du utför en fjärransluten build indataargumenten schema och data kan vara lokala sökvägar eller [Azure-blobblagring](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) URL: er. Argumentet utdata index måste vara en blob storage-URL. För att skapa ett Azure storage-konto, se [om Azure storage-konton](../../storage/common/storage-create-storage-account.md). Att kopiera filer effektivt till och från blobblagring, använder du den [AzCopy](../../storage/common/storage-use-azcopy.md) verktyget.

I det här exemplet kan du anta att följande blobblagringsbehållare redan har skapats: http://&lt;*konto*&gt;.blob.core.windows.net/&lt;*behållare* &gt;/. Den innehåller schemat *Academic.schema*, filen refererade synonymen *Keywords.syn*, och filen fullskaliga *Academic.full.data*. Du kan skapa fullständiga indexet via fjärranslutning med hjälp av följande kommando:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Observera att det kan ta 5-10 minuter att etablera en temporay VM och skapa index. Snabb prototyper kan du:
- Utveckla med en mindre mängd data lokalt på en dator.
- Manuellt [skapa en Azure VM](../../../articles/virtual-machines/windows/quick-create-portal.md), [ansluta till den](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) via fjärrskrivbord, installera den [Knowledge utforskning Service SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488), och kör [ `kes.exe` ](CommandLine.md) från den virtuella datorn.

Växling minskar skapar. För att undvika sidindelning, använder du en virtuell dator med tre gånger mängden RAM-minne som indata filstorleken för skapande av index. Använda en virtuell dator med 1 GB mer RAM-minne än vad index för värd. En lista över tillgängliga storlekar på VM, se [storlekar för virtuella datorer](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

<a name="deploying-service"></a>
## <a name="deploy-the-service"></a>Distribuera tjänsten
När du har en grammatik och ett index är du redo att distribuera tjänsten till en Azure-molntjänst. Om du vill skapa en ny Azure-molntjänst finns [hur du skapar och distribuerar en tjänst i molnet](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). Ange inte ett distributionspaket nu.  

När du har skapat Molntjänsten kan du använda [ `kes.exe deploy_service` ](CommandLine.md#deploy_service-command) att distribuera tjänsten. En Azure-Molntjänsten har två distributionsplatser: produktions- och Förproduktion. För en tjänst som tar emot användartrafik live, bör du först distribuera till mellanlagringsplatsen. Vänta tills tjänsten startas och initieras. Du kan sedan skicka några begäranden om att verifiera distributionen och kontrollera att den skickar grundläggande tester.

[Växla](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) innehållet i mellanlagringsplatsen fack med produktionsplatsen, så att live trafik omdirigeras nu till den nyligen distribuerade tjänsten. Du kan upprepa processen när du distribuerar en uppdaterad version av tjänsten med nya data. Som med alla andra Azure-molntjänster, kan du eventuellt använda Azure-portalen för att konfigurera [automatisk skalning](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

I det här exemplet du distribuerar den *Academic* index till mellanlagringsplatsen för en befintlig molntjänst med *< vm_size >* virtuella datorer. Ange följande kommando:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

En lista över tillgängliga storlekar på VM, se [storlekar för virtuella datorer](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

När du distribuerar tjänsten, kan du anropa olika [webb-API: er](WebAPI.md) att testa tolkning av naturligt språk, fråga slutförande, structured query utvärdering och histogram beräkning.  

<a name="testing-service"></a>
## <a name="test-the-service"></a>Testa tjänsten
Bläddra till värddatorn från en webbläsare för att felsöka en live-tjänst. För en lokal tjänst som distribuerats [host_service](#hosting-service), besök `http://localhost:<port>/`.  För en Azure cloud service distribueras [deploy_service](#deploying-service), besök `http://<serviceName>.cloudapp.net/`.

Den här sidan innehåller en länk till information om grundläggande statistik för API-anrop, samt grammatik och index som finns på den här tjänsten. Den här sidan innehåller också ett interaktivt sökgränssnitt som visar hur du använder web API: er. Ange frågor i sökrutan för att se resultatet av den [tolka](interpretMethod.md), [utvärdera](evaluateMethod.md), och [calchistogram](calchistogramMethod.md) API-anrop. Den underliggande HTML-källan för den här sidan fungerar också som ett exempel på hur du integrerar web API: er i en app för att skapa en omfattande, interaktiva sökinställningar.


