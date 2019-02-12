---
title: 'Exempel: Komma igång – API för tjänst för kunskapsutveckling'
titlesuffix: Azure Cognitive Services
description: Använd tjänsten för kunskapsutveckling (KES) API för att skapa en motor för en interaktiv sökupplevelse av akademiska publikationer.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: sample
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: e2bb5550cfe07064d595151305955d87f9c61050
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819543"
---
# <a name="get-started-with-the-knowledge-exploration-service"></a>Kom igång med tjänsten för kunskapsutveckling

I den här genomgången får du använda tjänsten för kunskapsutveckling (KES) API för att skapa en motor för en interaktiv sökupplevelse av akademiska publikationer. Du kan installera kommandoradsverktyget, [`kes.exe`](CommandLine.md), och alla exempel filer från [tjänsten för kunskapsutvecklings SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

Exemplet på akademiska publikationer innehåller ett exempel på 1 000 akademiska rapporter som har publicerats av forskare på Microsoft.  Varje dokumentet är associerad med en rubrik, år, författare och nyckelord. Varje författare representeras av ett ID, namn och anknytning vid tidpunkten för publiceringen. Varje nyckelord kan associeras med en uppsättning synonymer (till exempel nyckelordet ”dator för vektorstöd” kan vara associerat med synonymen ”svm”).

## <a name="define-the-schema"></a>Definiera schemat

Schemat beskriver attributstrukturen för objekt i domänen. Det anger namn och datatyp för varje attribut i en JSON-fil. I följande exempel är innehållet i filen *Academic.schema*.

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

Här kan du definiera *rubrik*, *år* och *nyckelordet* som sträng, heltal respektive strängattributet. Eftersom författare representeras av ID, namn och anknytning, definierar du *författare* som ett sammansatt attribut med tre underordnade attribut: *Author.Id*, *Author.Name* och *Author.Affiliation*.

Som standard har attribut stöd för alla åtgärder som är tillgängliga för datatypen, inklusive *equals*, *starts_with* och *is_between*. Eftersom författar-ID endast används internt som identifierare, åsidosätts standardinställningen och *är lika med* anges som den enda åtgärden som indexeras.

För attributet *Nyckelord* kan synonymer matcha godkända nyckelordsvärden genom att ange synonymenfilen *Keyword.syn* i attributdefinitionen. Den här filen innehåller en lista över godkända och synonymenvärde-par:

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

## <a name="generate-data"></a>Skapa data

Datafilen beskriver listan över publikationer som ska indexera, där varje rad anger attributvärden för en artikel i [JSON-format](http://json.org/).  I följande exempel är en enda rad från datafilen *Academic.data*, formaterad för läsbarhet:

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

I det här kodfragmentet anger du attributen *Rubrik* och *År* för dokumentet som en JSON-sträng respektive en siffra. Flera värden representeras med hjälp av JSON-matriser. Eftersom *författare* är ett sammansatt attribut visas varje värde med hjälp av ett JSON-objekt som består av dess underordnade attribut. Attribut med saknade värden, till exempel *Nyckelord* kan i det här fallet uteslutas från JSON-representationen.

För att skilja sannolikheten för olika artiklar, anger du den relativa logaritmiska sannolikheten med hjälp av det inbyggda attributet *logprob*. För en sannolikhet *p* mellan 0 och 1 kan du beräkna den logaritmiska sannolikheten som log (*p*), där log() är den naturliga logaritmen.

Mer information finns i [Dataformat](DataFormat.md).

## <a name="build-a-compressed-binary-index"></a>Skapa ett komprimerat binärt index

När du har en schemafil och datafil kan du skapa ett komprimerat binärt index för dataobjekt med hjälp av [`kes.exe build_index`](CommandLine.md#build_index-command). I det här exemplet skapar du indexfilen *Academic.index* från filinmatningsschemat *Academic.schema* och datafilen *Academic.data*. Ange följande kommando:

`kes.exe build_index Academic.schema Academic.data Academic.index`

För snabba prototyper utanför Azure kan [`kes.exe build_index`](CommandLine.md#build_index-command) skapa lokala, små index från filer som innehåller upp till 10 000 objekt. För större filer, kan du antingen köra kommandot inifrån en [Windows-dator i Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) eller utföra en fjärransluten kompilering i Azure. Mer information finns i Skala upp.

## <a name="use-an-xml-grammar-specification"></a>Använd en specifikation med XML-grammatik

Grammatik anger uppsättning frågor med naturligt språk som tjänsten kan tolka samt hur dessa frågor med naturligt språk översätts till semantiska frågeuttryck. I det här exemplet använder du grammatik enligt *academic.xml*:

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

Mer information om vilken grammatiksyntaxen finns i [Grammatikformat](GrammarFormat.md).

## <a name="compile-the-grammar"></a>Kompilera grammatik

När du har en XML-grammatik-specifikation kan du kompilera den till en binär grammatik med [`kes.exe build_grammar`](CommandLine.md#build_grammar-command). Observera att om grammatik importerar ett schema måste schemafilen finnas i samma sökväg som grammatik-XML. I det här exemplet skapar du den binära grammatikfilen *Academic.grammar* från indatafilen för XML-grammatik *Academic.xml*. Ange följande kommando:

`kes.exe build_grammar Academic.xml Academic.grammar`

## <a name="host-the-grammar-and-index-in-a-web-service"></a>Värd för grammatik och index i en webbtjänst

För snabba prototyper kan du lagra grammatik och index i en webbtjänst på den lokala datorn med hjälp av [`kes.exe host_service`](CommandLine.md#host_service-command). Du kan sedan komma åt tjänsten via [webb-API:er](WebAPI.md) för att bekräfta att dina data är korrekta och med korrekt grammatik. I det här exemplet kan du lagra grammatikfilen *Academic.grammar* och indexeringsfilen *Academic.index* på http://localhost:8000/. Ange följande kommando:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Detta startar en lokal instans av webbtjänsten. Du kan testa tjänsten interaktivt genom att besöka `http::localhost:<port>` från en webbläsare. Mer information finns i Testa tjänsten.

Du kan också anropa olika [webb-API: er](WebAPI.md) direkt för att testa tolkning av naturligt språk, frågeslutföring, strukturerad frågeutvärdering och histogramberäkning. Stoppa tjänsten genom att ange ”Avsluta” i kommandotolken för `kes.exe host_service` eller trycka på Ctrl + C. Här följer några exempel:

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers susan t d & fullständig = 1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Utanför Azure, [`kes.exe host_service`](CommandLine.md#host_service-command) är begränsad till index för upp till 10 000 objekt. Andra begränsningar inkluderar en API-hastighet på 10 förfrågningar per sekund och totalt 1000 begäranden innan processen avslutas automatiskt. Om du vill kringgå dessa begränsningar, kör du kommandot inifrån en [Windows-dator i Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) eller distribuerar till en Azure-molntjänst med hjälp av kommandot [`kes.exe deploy_service`](CommandLine.md#deploy_service-command). Mer information finns i Distribuera tjänsten.

## <a name="scale-up-to-host-larger-indices"></a>Skala upp till större index

När du kör `kes.exe` utanför Azure är indexet begränsat till 10 000 objekt. Du kan skapa och vara värd för större index med hjälp av Azure. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). Om du prenumererar på Visual Studio eller MSDN kan du också [aktivera prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Dessa erbjuder Azure-krediter varje månad.

För att tillåta `kes.exe` åtkomst till en Azure-konto, [hämta filen med Azure-publiceringsinställningar](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) från Azure-portalen. Om du uppmanas, logga in på det önskade Azure-kontot. Spara filen som *AzurePublishSettings.xml* i arbetskatalogen varifrån `kes.exe` körs.

Det finns två sätt att bygga och hantera stora index. Först är att förbereda schema- och datafilerna i en Windows-dator i Azure. Kör sedan [`kes.exe build_index`](#building-index) och skapa index lokalt på den virtuella datorn, utan storleksbegränsningar. Det resulterande indexet kan lagras lokalt på den virtuella datorn med hjälp av [`kes.exe host_service`](#hosting-service) för snabba prototyper, igen utan begränsningar. Detaljerade anvisningar finns i självstudien om [Virtuella Azure-datorer](../../../articles/virtual-machines/windows/quick-create-portal.md).

Den andra metoden är att utföra en fjärransluten Azure-kompilering med hjälp av [`kes.exe build_index`](CommandLine.md#build_index-command) med parametern `--remote`. Detta anger en Azure VM-storlek. När parametern `--remote` har angetts skapar kommandot en temporär virtuell Azure-dator med den storleken. Det indexet kompileras sedan på den virtuella datorn som överför indexet till målbloblagringen och tar bort den virtuella datorn när detta har slutförts. Din Azure-prenumeration debiteras för kostnaden för den virtuella datorn medan index skapas.

Den här fjärrkompileringsversionen för Azure kan köra [`kes.exe build_index`](CommandLine.md#build_index-command) i alla miljöer. När du utför en fjärrkompilering kan argumenten för indataschema och data vara lokala sökvägar eller URL:er till [Azure blob-lagring](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Indexargument för utdata måste vara URL:er till blob-lagring. Om du vill skapa ett Azure Storage-konto läser du [Om Azure Storage-konton](../../storage/common/storage-create-storage-account.md). För att kopiera filer effektivt till och från blob-lagring använder du verktyget [AzCopy](../../storage/common/storage-use-azcopy.md).

I det här exemplet kan du anta att följande blob storage-behållaren redan har skapats: http://&lt;*konto*&gt;.blob.core.windows.net/&lt;*behållare* &gt;/. Den innehåller schemat *Academic.schema*, den refererade synonymfilen *Keywords.syn* och den fullskaliga datafilen *Academic.full.data*. Du kan skapa fullständiga indexet via en fjärranslutning med hjälp av följande kommando:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Observera att det kan ta 5 – 10 minuter att etablera en tillfällig virtuell dator för att skapa indexet. För snabba prototyper kan du:
- Utveckla med en mindre mängd data lokalt på en dator.
- [Skapa en virtuell Azure-dator](../../../articles/virtual-machines/windows/quick-create-portal.md) manuellt, [anslut till den](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) via fjärrskrivbord, installera [SDK för tjänsten för kunskapsutveckling](https://www.microsoft.com/en-us/download/details.aspx?id=51488) och kör [`kes.exe`](CommandLine.md) från den virtuella datorn.

Växling gör skapandeprocessen långsammare. Om du vill undvika växling använder du en virtuell dator med tre gånger större RAM-minne än indatafilen som används för att kompilera indexet. Använd en virtuell dator med 1 GB mer RAM-minne än indexstorleken. En lista över tillgängliga storlekar på virtuella datorer finns i [storlekar på virtuella datorer](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

## <a name="deploy-the-service"></a>Distribuera tjänsten

När du har grammatik och index är du redo att distribuera tjänsten till en Azure-molntjänst. Se [Skapa och distribuera en tjänst i molnet](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md) om du vill skapa en ny Azure-molntjänst. Ange inte ett distributionspaket nu.  

När du har skapat molntjänsten kan du använda [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) för att distribuera tjänsten. En Azure-molntjänst har två distributionsplatser: produktion och mellanlagring. För en tjänst som tar emot direkt användartrafik bör du först distribuera mellanlagringen. Vänta medan tjänsten startas och initieras. Du kan sedan skicka några begäranden för att verifiera distributionen och kontrollera att den med några grundläggande tester.

[Växla](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) innehållet i mellanlagringsfacket med produktionsplatsen så att live-trafik omdirigeras till den nyligen distribuerade tjänsten. Du kan upprepa den här processen när du distribuerar en uppdaterad version av tjänsten med nya data. Som med alla andra Azure-molntjänster kan du också använda Azure-portalen för att konfigurera [autoskalning](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

I det här exemplet distribuerar du indexet *Akademisk* till en mellanlagringsplats för en befintlig molntjänst med *< vm_size >* virtuella datorer. Ange följande kommando:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

En lista över tillgängliga storlekar på virtuella datorer finns i [storlekar på virtuella datorer](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

När du har distribuerat tjänsten kan du anropa olika [webb-API: er](WebAPI.md) direkt för att testa tolkning av naturligt språk, frågeslutföring, strukturerad frågeutvärdering och histogramberäkning.  

## <a name="test-the-service"></a>Testa tjänsten

Bläddra till värddatorn från en webbläsare för att felsöka en live-tjänst. För en lokal tjänst som distribueras via host_service går du till `http://localhost:<port>/`.  För en Azure-molntjänst som distribueras via deploy_service går du till `http://<serviceName>.cloudapp.net/`.

Den här sidan innehåller en länk till information om grundläggande statistik för API-anrop, samt grammatik och index som finns på den här tjänsten. Den här sidan innehåller också ett gränssnitt för interaktiv sökning som visar hur du använder webb-API:er. Ange frågor i sökrutan för att se resultaten av API-anropen [interpret](interpretMethod.md), [evaluate](evaluateMethod.md), och [calchistogram](calchistogramMethod.md). Den underliggande HTML-källan för den här sidan fungerar också som ett exempel på hur du integrerar webb-API:er i en app för att skapa en omfattande och interaktiv sökfunktion.


