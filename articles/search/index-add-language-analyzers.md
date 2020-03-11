---
title: Lägga till språk analys verktyg i sträng fält
titleSuffix: Azure Cognitive Search
description: Flerspråkiga lexikala text analyser för icke-engelska frågor och index i Azure Kognitiv sökning.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
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
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379700"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Lägga till språk analys verktyg i sträng fält i ett Azure Kognitiv sökning-index

En *språk analys* är en specifik typ av [text analys](search-analyzers.md) som utför en lexikal analys med hjälp av språk reglerna för mål språket. Alla sökbara fält har en **analys** egenskap. Om indexet innehåller översatta strängar, t. ex. separata fält för engelsk och kinesisk text, kan du ange språk analys verktyg för varje fält för att få åtkomst till de omfattande språk funktionerna i dessa analys verktyg.  

Azure Kognitiv sökning har stöd för 35 analys verktyg som backas upp av Lucene och 50-analyser som backas upp av patentskyddad Microsoft Natural Language Processing Technology som används i Office och Bing.

## <a name="comparing-analyzers"></a>Jämföra analyserare

Vissa utvecklare kan föredra den mer välkända, enkla lösningen med öppen källkod för Lucene. Lucene-språkanalyser är snabbare, men Microsofts analys verktyg har avancerade funktioner, till exempel lemmatisering, ord desammansatt (på språk som tyska, danska, holländska, svenska, norska, estniska, finish, ungerska, slovakiska) och entitet igenkänning (URL: er, e-post, datum, siffror). Om möjligt bör du köra jämförelser av både Microsoft-och Lucene-analyserna för att avgöra vilken som passar bäst. 

Indexering med Microsoft-analyser är i genomsnitt två till tre gånger långsammare än deras Lucene-motsvarigheter, beroende på språket. Sök prestanda ska inte påverkas nämnvärt för genomsnittlig storleks frågor. 

### <a name="english-analyzers"></a>Engelska analys verktyg

Standard analys verktyget är standard Lucene, som fungerar bra för engelska, men kanske inte som Lucene, engelska Analyzer eller Microsofts engelska analys. 
 
+ Den engelska analysen i Lucene utökar standard analys verktyget. Den tar bort possessives (efterföljande) från ord, tillämpar det som följer av algoritmen för Porter och tar bort engelska stoppord.  

+ Microsofts engelska Analyzer utför lemmatisering i stället för att det finns. Det innebär att den kan hantera inflected och oregelbundna ord former mycket bättre, vilket resulterar i mer relevanta Sök Resultat 

## <a name="configuring-analyzers"></a>Konfigurera analys verktyg

Språk analys verktyg används i befintligt skick. För varje fält i index definitionen kan du ställa in **Analyzer** -egenskapen till ett analys namn som anger den språk-och språkstapel (Microsoft eller Lucene). Samma analys verktyg kommer att användas vid indexering och sökning efter fältet. Du kan till exempel ha separata fält för de engelska, franska och spanska hotell beskrivningar som finns sida vid sida i samma index.

> [!NOTE]
> Det går inte att använda en annan språk analys vid indexerings tiden än vid frågans tid för ett fält. Den funktionen är reserverad för [anpassade analys](index-add-custom-analyzers.md)verktyg. Av den anledningen, om du försöker ange **searchAnalyzer** -eller **indexAnalyzer** -egenskaperna till namnet på en språk analys, returnerar REST API ett fel svar. Du måste använda **Analyzer** -egenskapen i stället.

Använd Frågeparametern **searchFields** för att ange vilket språkspecifika fält som ska genomsökas i dina frågor. Du kan granska exempel på frågor som innehåller analys egenskapen i [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Mer information om index egenskaper finns i [create index &#40;Azure kognitiv sökning REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Mer information om analyser i Azure Kognitiv sökning finns i [analyserare i azure kognitiv sökning](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Språk analys lista 
 Nedan visas en lista över språk som stöds tillsammans med Lucene och Microsoft Analyzer-namn.  

|Språk|Microsoft Analyzer-namn|Namn på Lucene Analyzer|  
|--------------|-----------------------------|--------------------------|  
|Arabiska|ar.microsoft|ar. Lucene|  
|Armeniska||hy. Lucene|  
|Bangla|bn.microsoft||  
|Baskiska||eu.lucene|  
|Bulgariska|bg.microsoft|BG. Lucene|  
|Katalanska|ca.microsoft|ca. Lucene|  
|Kinesiska, förenklad|zh-Hans.microsoft|zh-Hans.lucene|  
|Traditionell kinesisk|zh-Hant.microsoft|zh-Hant. Lucene|  
|Kroatiska|hr.microsoft||  
|Tjeckiska|cs.microsoft|CS. Lucene|  
|Danska|da.microsoft|da. Lucene|  
|Nederländska|nl.microsoft|nl. Lucene|  
|Svenska|en.microsoft|en. Lucene|  
|Estniska|et.microsoft||  
|Finska|fi.microsoft|Fi. Lucene|  
|Franska|fr.microsoft|fr. Lucene|  
|Galiciska||GL. Lucene|  
|Tyska|de.microsoft|de. Lucene|  
|Grekiska|el.microsoft|El. Lucene|  
|Gujarati|gu.microsoft||  
|Hebreiska|he.microsoft||  
|Hindi|hi.microsoft|Hej. Lucene|  
|Ungerska|hu.microsoft|HU. Lucene|  
|Isländska|is.microsoft||  
|Indonesiska (Bahasa)|id.microsoft|ID. Lucene|  
|Iriska||ga. Lucene|  
|Italienska|it.microsoft|IT. Lucene|  
|Japanska|ja.microsoft|Ja. Lucene|  
|Kannada|kn.microsoft||  
|Koreanska|ko.microsoft|ko.lucene|  
|Lettiska|lv.microsoft|lv.lucene|  
|Litauiska|lt.microsoft||  
|Malayalam|ml.microsoft||  
|Malajiska (latinsk)|ms.microsoft||  
|Marathi|mr.microsoft||  
|Norska|nb.microsoft|Nej. Lucene|  
|Persiska||FA. Lucene|  
|Polska|pl.microsoft|pl. Lucene|  
|Portugisiska (Brasilien)|pt-Br.microsoft|pt-Br.lucene|  
|Portugisiska (Portugal)|pt-Pt.microsoft|pt-Pt.lucene|  
|Punjabi|pa.microsoft||  
|Rumänska|ro.microsoft|ro. Lucene|  
|Ryska|ru.microsoft|ru. Lucene|  
|Serbiska (kyrillisk)|sr-cyrillic.microsoft||  
|Serbiska (latinsk)|sr-latin.microsoft||  
|Slovakiska|sk.microsoft||  
|Slovenska|sl.microsoft||  
|Spanska|es.microsoft|ES. Lucene|  
|Svenska|sv.microsoft|sv.lucene|  
|Tamilska|ta.microsoft||  
|Telugu|te.microsoft||  
|Thailändska|th.microsoft|th. Lucene|  
|Turkiska|tr.microsoft|TR. Lucene|  
|Ukrainska|uk.microsoft||  
|Urdu|ur.microsoft||  
|Vietnamesiska|vi.microsoft||  

 Alla analyser med namn som är kommenterade med **Lucene** drivs av [Apache Lucenes språk analys verktyg](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Se även  

+ [Skapa index &#40;Azure kognitiv sökning REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

