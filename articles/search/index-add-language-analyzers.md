---
title: Lägga till språkanalysverktyg – Azure Search
description: Flerspråkig lexikal textanalys för icke-engelska frågor och index i Azure Search.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 20a8d9f5b575fca5471916af0183257f2a43d5cb
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328307"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Lägga till språkanalysverktyg till ett Azure Search-index

En *språkanalysverktyg* är en viss typ av [text analyzer](search-analyzers.md) som utför lexikal analys med språkliga reglerna för språket som mål. Varje sökbara fält har en **analyzer** egenskapen. Om ditt index innehåller översatta strängar, till exempel olika fält för text på engelska och kinesiska, kan du ange språkanalysverktyg på varje fält för att få åtkomst till avancerade språkliga funktioner i dessa analysverktyg.  

Azure Search har stöd för 35 analysverktyg som backas upp av Lucene och 50 analysverktyg som backas upp av egna Microsoft naturligt språk videoteknik som används i Office och Bing.

## <a name="comparing-analyzers"></a>Jämföra analysverktyg

Vissa utvecklare kanske föredrar Lucene mer bekanta, enkla, open source-lösningen. Lucene språkanalysverktyg är snabbare, men Microsoft analysverktyg har avancerade funktioner, till exempel lemmatisering, word decompounding (på språk som tyska, danska, nederländska, svenska, norska, estniska, Slutför, ungerska, slovakiska) och entitet igenkänning av (URL: er, e-postmeddelanden, datum, siffror). Du bör om möjligt köra jämförelser av både Microsoft och Lucene analysverktyg för att bestämma vilken som passar bättre. 

Indexering med Microsoft analysverktyg är i genomsnitt två till tre gånger långsammare än motsvarigheterna Lucene beroende på språket. Sökningsprestanda bör inte påverkas avsevärt för genomsnittliga storleken frågor. 

### <a name="english-analyzers"></a>Engelska analysverktyg

Standard-analysatorn är Standard Lucene, vilket fungerar bra för engelska, men kanske inte samt engelska Lucene-analyzer eller Microsofts engelska analyzer. 
 
+ Engelska Lucene-analyzer utökar standard analysatorn. Det tar bort genitiv (avslutande) från ord, gäller ordstamsigenkänning enligt Porter ordstamsigenkänning algoritmen och tar bort engelska stoppord.  

+ Microsofts engelska analyzer utför lemmatisering i stället för ordstamsigenkänning. Det innebär att den kan hantera böjda och oregelbundna ordformer mycket bättre vad resulterar i mer relevanta sökresultat 

 > [!Tip]
 > Den [Sökanalysverktyg](https://alice.unearth.ai/) innehåller sida-vid-sida-jämförelse av resultat som skapas av analysverktyget från Lucene Lucenes engelska analyzer och Microsofts engelska naturligt språk processor. För varje sökinmatning anger du visas resultaten från varje analyzer i intilliggande fönster.

## <a name="configuring-analyzers"></a>Konfigurera analysverktyg

Språkanalysverktyg används som – är. För varje fält i indexdefinitionen, kan du ange den **analyzer** egenskapen till ett analyzer-namn som anger de språk och lingvistik stacken (Microsoft eller Lucene). Samma analysatorn tillämpas när indexering och sökning för det fältet. Du kan till exempel ha olika fält för engelska, franska och spanska hotell beskrivningar som finnas sida vid sida i samma index. Alternativt kan du i stället för **analyzer**, du kan använda **indexAnalyzer** och **searchAnalyzer** ha olika analysis regler vid indexering och frågetiden. 

Använd den **searchFields** frågeparameter för att ange vilket språkspecifika fält om du vill söka mot i dina frågor. Du kan granska frågan exempel som innehåller egenskapen analyzer i [söka efter dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Läs mer om egenskaper för frågeindex [Create Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Mer information om analys i Azure Search finns [analysverktyg i Azure Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Språklista analyzer 
 Nedan visas en lista över språk som stöds tillsammans med Lucene och Microsoft analyzer-namn.  

|Språk|Microsoft Analyzer Name|Lucene Analyzer namn|  
|--------------|-----------------------------|--------------------------|  
|Arabiska|ar.microsoft|ar.lucene|  
|Armeniska||hy.lucene|  
|Bangla|bn.microsoft||  
|Baskiska||eu.lucene|  
|Bulgariska|bg.microsoft|bg.lucene|  
|Katalanska|ca.microsoft|ca.lucene|  
|Kinesiska (förenklad)|zh-Hans.microsoft|zh-Hans.lucene|  
|Kinesiska (traditionell)|zh-Hant.microsoft|zh-Hant.lucene|  
|Kroatiska|hr.microsoft||  
|Tjeckiska|cs.microsoft|cs.lucene|  
|Danska|da.microsoft|da.lucene|  
|Nederländska|nl.microsoft|nl.lucene|  
|Svenska|en.microsoft|en.lucene|  
|Estniska|et.microsoft||  
|Finska|fi.microsoft|fi.lucene|  
|Franska|fr.microsoft|fr.lucene|  
|Galiciska||gl.lucene|  
|Tyska|de.microsoft|de.lucene|  
|Grekiska|el.microsoft|el.lucene|  
|Gujarati|gu.microsoft||  
|Hebreiska|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|Ungerska|hu.microsoft|hu.lucene|  
|Isländska|is.microsoft||  
|Indonesiska (Bahasa)|id.microsoft|id.lucene|  
|Iriska||ga.lucene|  
|Italienska|it.microsoft|it.lucene|  
|Japanska|ja.microsoft|ja.lucene|  
|Kannada|ka.microsoft||  
|Koreanska|ko.microsoft|ko.lucene|  
|Lettiska|lv.microsoft|lv.lucene|  
|Litauiska|lt.microsoft||  
|Malayalam|ml.microsoft||  
|Malajiska (latinsk)|ms.microsoft||  
|Marathi|mr.microsoft||  
|Norska|nb.microsoft|no.lucene|  
|Persiska||fa.lucene|  
|Polska|pl.microsoft|pl.lucene|  
|Portugisiska (Brasilien)|pt-Br.microsoft|pt-Br.lucene|  
|Portugisiska (Portugal)|pt-Pt.microsoft|pt-Pt.lucene|  
|Punjabi|pa.microsoft||  
|Rumänska|ro.microsoft|ro.lucene|  
|Ryska|ru.microsoft|ru.lucene|  
|Serbiska (kyrillisk)|sr-cyrillic.microsoft||  
|Serbiska (latinsk)|sr-latin.microsoft||  
|Slovakiska|sk.microsoft||  
|Slovenska|sl.microsoft||  
|Spanska|es.microsoft|es.lucene|  
|Svenska|sv.microsoft|sv.lucene|  
|Tamilska|ta.microsoft||  
|Telugu|te.microsoft||  
|Thai|th.microsoft|th.lucene|  
|Turkiska|tr.microsoft|tr.lucene|  
|Ukrainska|uk.microsoft||  
|Urdu|ur.microsoft||  
|Vietnamesiska|vi.microsoft||  

 Alla analysverktyg med namnen med **Lucene** drivs av [Apache Lucenes språkanalysverktyg](https://lucene.apache.org/core/4_9_0/core/overview-summary.html ).

## <a name="see-also"></a>Se också  
 [Skapa Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
 [AnalyzerName-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  
 [Video: modulen 7 i Azure Search MVA presentation](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07).  

