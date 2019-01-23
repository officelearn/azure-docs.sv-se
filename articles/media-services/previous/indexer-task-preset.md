---
title: Uppgiften som förinställning för Azure Media Indexer
description: Det här avsnittet ger en översikt över aktivitet förinställning för Azure Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: adsolank;juliako;
ms.openlocfilehash: 65b4e2da2cb019c46ee566cd14f0a576c2376db2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463097"
---
# <a name="task-preset-for-azure-media-indexer"></a>Uppgiften som förinställning för Azure Media Indexer

Azure Media Indexer är en Mediebearbetare som används för att utföra följande uppgifter: gör mediefiler och innehåll sökbara, generera spårar för dold textning och nyckelord, index-tillgångsfiler som ingår i din tillgång.

Det här avsnittet beskriver aktiviteten förinställda att du måste skicka till ditt indexeringsjobb. Komplett exempel finns i [indexera mediefiler med Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer konfigurations-XML

I följande tabell beskriver elementen och attributen i XML-konfiguration.

|Namn|Kräv|Beskrivning|
|---|---|---|
|Indata|true|Tillgången fil(er) som du vill indexera.<br/>Azure Media Indexer stöder följande media format: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Du kan ange namnet på filen (s) i den **namn** eller **lista** attributet för den **inkommande** element (som visas nedan). Om du inte anger vilken tillgångsfil till index plockas den primära filen. Om ingen primär tillgångsfil anges indexeras den första filen i indatatillgången.<br/><br/>För att uttryckligen ange filnamnet tillgången, gör du:<br/>```<input name="TestFile.wmv" />```<br/><br/>Du kan också indexera flera tillgångsfiler på samma gång (upp till 10 filer). Gör så här:<br/>– Skapa en textfil (manifestfilen) och ge den ett .lst-tillägg.<br/>-Lägga till en lista över alla tillgångar filnamn i din inkommande tillgång till den här manifestfilen.<br/>-Lägga till () manifestet uppladdningsfilen till tillgången.<br/>– Ange namnet på manifestfilen i angivna indata-attribut.<br/>```<input list="input.lst">```<br/><br/>**Obs!** Om du lägger till fler än 10 filer att manifestfilen misslyckas indexering jobbet med felkoden 2006.|
|metadata|false|Metadata för den angivna resursen/filerna.<br/>```<metadata key="..." value="..." />```<br/><br/>Du kan ange värden för fördefinierade nycklar. <br/><br/>För närvarande stöds följande nycklar:<br/><br/>**Rubrik** och **beskrivning** – används för att uppdatera språkmodellen för att förbättra taligenkänningen.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**användarnamn** och **lösenord** – används för autentisering när du laddar ned Internetfiler via http eller https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Värden för användarnamn och lösenord gäller för alla media-URL: er i indata manifestet.|
|funktioner<br/><br/>Har lagts till i version 1.2. Den enda funktionen som stöds är för närvarande taligenkänning (”ASR”).|false|Funktionen har följande inställningar för nycklar:<br/><br/>Språk:<br/>-Naturligt språk ska identifieras i multimediefilen.<br/>-Engelska, spanska<br/><br/>CaptionFormats:<br/>-en semikolonavgränsad lista över önskade utdata texten formaterar (i förekommande fall)<br/>- ttml;sami;webvtt<br/><br/><br/>GenerateAIB:<br/>-En boolesk flagga som anger huruvida en AIB-fil krävs (för användning med SQL Server och kunden indexeraren IFilter). Mer information finns i använda AIB-filer med Azure Media Indexer och SQL Server.<br/>- True; False<br/><br/>GenerateKeywords:<br/>-En boolesk flagga som anger huruvida en XML-fil med nyckelord måste anges.<br/>- True; False.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Azure Media Indexer configuration XML-exempel

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Nästa steg

Se [indexera mediefiler med Azure Media Indexer](media-services-index-content.md).

