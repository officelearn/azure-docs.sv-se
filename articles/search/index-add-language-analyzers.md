---
title: Lägga till språkanalysatorer i strängfält
titleSuffix: Azure Cognitive Search
description: Flerspråkig lexikal textanalys för frågor och index som inte är engelska i Azure Cognitive Search.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283152"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Lägga till språkanalysatorer i strängfält i ett Azure Cognitive Search-index

En *språkanalysator* är en specifik typ av [textanalysator](search-analyzers.md) som utför lexikal analys med hjälp av målspråkets språkliga regler. Varje sökbart fält **analyzer** har en analysatoregenskap. Om indexet innehåller översatta strängar, till exempel separata fält för engelsk och kinesisk text, kan du ange språkanalysatorer i varje fält för att komma åt de omfattande språkliga funktionerna hos dessa analysatorer.  

Azure Cognitive Search stöder 35 analysatorer som backas upp av Lucene och 50 analysatorer som backas upp av microsofts processorteknik för naturligt språk som används i Office och Bing.

## <a name="comparing-analyzers"></a>Jämföra analysatorer

Vissa utvecklare kanske föredrar den mer välbekanta, enkla lösningen med öppen källkod för Lucene. Lucene språkanalysatorer är snabbare, men Microsoft-analysatorerna har avancerade funktioner, såsom lemmatisering, orddeproptering (på språk som tyska, danska, holländska, svenska, norska, estniska, finish, ungerska, slovakiska) och enhet erkännande (webbadresser, e-postmeddelanden, datum, siffror). Om möjligt bör du köra jämförelser av både Microsoft och Lucene analysatorer för att avgöra vilken som passar bättre. 

Indexering med Microsoft-analysatorer är i genomsnitt två till tre gånger långsammare än deras Lucene motsvarigheter, beroende på språk. Sökresultatet bör inte påverkas nämnvärt för frågor om genomsnittlig storlek. 

### <a name="english-analyzers"></a>Engelska analysatorer

Standardanalysatorn är Standard Lucene, som fungerar bra för engelska, men kanske inte lika bra som Lucenes engelska analysator eller Microsofts engelska analysator. 
 
+ Lucenes engelska analysator förlänger standardanalysatorn. Det tar bort possessiva (avslutande 's) från ord, gäller som härrör enligt Porter Stemming algoritm, och tar bort engelska stoppord.  

+ Microsofts engelska analysator utför lemmatisering i stället för att hejda. Detta innebär att den kan hantera inflected och oregelbundna ordformer mycket bättre vilket resulterar i mer relevanta sökresultat 

## <a name="configuring-analyzers"></a>Konfigurera analysatorer

Språkanalysatorer används som de är. För varje fält i indexdefinitionen **analyzer** kan du ange analysatoregenskapen till ett analysatornamn som anger språk- och språkstapeln (Microsoft eller Lucene). Samma analysator används när du indexerar och söker efter det fältet. Du kan till exempel ha separata fält för engelska, franska och spanska hotellbeskrivningar som finns sida vid sida i samma index.

> [!NOTE]
> Det går inte att använda en annan språkanalysator vid indexeringstiden än vid frågetillfället för ett fält. Den funktionen är reserverad för [anpassade analysatorer](index-add-custom-analyzers.md). Om du försöker ställa in egenskaperna **searchAnalyzer** eller **indexAnalyzer** till namnet på en språkanalysator returnerar REST API därför ett felsvar. Du måste använda egenskapen **analyzer** i stället.

Använd **sökfältsfrågeparametern** för att ange vilket språkspecifikt fält som ska sökas mot i dina frågor. Du kan granska frågeexempel som innehåller egenskapen analysator i [Sökdokument](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Mer information om indexegenskaper finns i [Skapa index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Mer information om analys i Azure Cognitive Search finns [i Analysatorer i Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lista över språkanalysator 
 Nedan finns en lista över språk som stöds tillsammans med Lucene och Microsoft analyzer namn.  

|Språk|Namn på Microsoft Analyzer|Lucene Analyzer Namn|  
|--------------|-----------------------------|--------------------------|  
|Arabiska|ar.microsoft (på/ )|ar.lucene (ar.lucene)|  
|Armeniska||hy.lucene (hy.lucene)|  
|Bangla|bn.microsoft (på andra)||  
|Baskiska||eu.lucene (eu.lucene)|  
|Bulgariska|bg.microsoft (på andra)|bg.lucene (på andra sätt)|  
|Katalanska|ca.microsoft (på ca.microsoft)|ca.lucene (ca.lucene)|  
|Kinesiska, förenklad|zh-Hans.microsoft|zh-Hans.lucene|  
|Kinesiska, traditionell|zh-Hant.microsoft|zh-Hant.lucene|  
|Kroatiska|hr.microsoft (på/ för mycket)||  
|Tjeckiska|cs.microsoft (på andra)|cs.lucene (på andra)|  
|Danska|da.microsoft (på andra sätt)|da.lucene (da.lucene)|  
|Nederländska|nl.microsoft (på andra)|nl.lucene (på andra sätt)|  
|Svenska|sv.microsoft (på en.microsoft)|sv.lucene (på väg mot lucene)|  
|Estniska|et.microsoft (på andra)||  
|Finska|fi.microsoft (på andra sätt)|fi.lucene (på andra)|  
|Franska|fr.microsoft (på andra)|fr.lucene (fr.lucene)|  
|Galiciska||gl.lucene (40000)|  
|Tyska|de.microsoft (på andra)|de.lucene (på andra sätt)|  
|Grekiska|el.microsoft (på andra)|el.lucene (på en)|  
|Gujarati|gu.microsoft (på andra)||  
|Hebreiska|he.microsoft (på/ )||  
|Hindi|hej.microsoft|hej.lucene|  
|Ungerska|hu.microsoft (på hu.microsoft)|hu.lucene (hu.lucene)|  
|Isländska|is.microsoft (på/ och från)||  
|Indonesiska (Bahasa)|id.microsoft|id.lucene|  
|Iriska||ga.lucene (|  
|Italienska|it.microsoft (på andra)|det.lucene|  
|Japanska|ja.microsoft (på/ och med andra)|ja.lucene (på andra sätt)|  
|Kannada|kn.microsoft||  
|Koreansk|ko.microsoft (på andra)|ko.lucene (ko.lucene)|  
|Lettiska|lv.microsoft (på andra sätt)|lv.lucene (lv.lucene)|  
|Litauiska|lt.microsoft (på andra)||  
|Malayalam|ml.microsoft (på andra sätt)||  
|Malajiska (latin)|ms.microsoft (på andra)||  
|Marathi|mr.microsoft (på andra sätt)||  
|Norska|nb.microsoft (på andra sätt)|no.lucene (nr.lucene)|  
|Persiska||fa.lucene (på andra sätt)|  
|Polska|pl.microsoft (på andra)|pl.lucene (på en)|  
|Portugisiska (Brasilien)|pt-Br.microsoft|pt-Br.lucene|  
|Portugisiska (Portugal)|pt-Pt.microsoft|pt-Pt.lucene|  
|Punjabi|pa.microsoft (på andra sätt)||  
|Rumänska|ro.microsoft|ro.lucene (ro.lucene)|  
|Ryska|ru.microsoft (på andra)|ru.lucene (ru.lucene)|  
|Serbiska (kyrillisk)|sr-cyrillic.microsoft||  
|Serbiska (latinsk) (Serbien)|sr-latin.microsoft||  
|Slovakiska|sk.microsoft (på andra sätt)||  
|Slovenska|sl.microsoft (på andra)||  
|Spanska|es.microsoft (på andra sätt)|es.lucene (es.lucene)|  
|Svenska|sv.microsoft (svenska)|sv.lucene|  
|Tamilska|ta.microsoft (på andra)||  
|Telugu|te.microsoft (te.microsoft)||  
|Thailändska|th.microsoft (på andra plats)|th.lucene (på)th.lucene|  
|Turkiska|tr.microsoft (på andra)|tr.lucene (tr.lucene)|  
|Ukrainska|uk.microsoft (svenska)||  
|Urdu|your.microsoft||  
|Vietnamesiska|vi.microsoft||  

 Alla analysatorer med namn kommenterade med **Lucene** drivs av [Apache Lucene språkanalysatorer](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Se även  

+ [Skapa index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Klassen AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

