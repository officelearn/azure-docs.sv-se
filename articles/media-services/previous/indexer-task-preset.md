---
title: Aktivitetsförinställning för Azure Media Indexer
description: Det här avsnittet innehåller en översikt över uppgiftsförinställningen för Azure Media Services Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 29753759af341f82429f12b6710ae9c32dcb4103
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896027"
---
# <a name="task-preset-for-azure-media-indexer"></a>Aktivitetsförinställning för Azure Media Indexer 

Azure Media Indexer är en medieprocessor som du använder för att utföra följande uppgifter: göra mediefiler och innehåll sökbart, generera spårar och nyckelord med dold textning, indexeringstillgångsfiler som ingår i din tillgång.

I det här avsnittet beskrivs den aktivitetsförinställning som du behöver skicka till ditt indexeringsjobb. Du kan till exempel se [Indexera mediefiler med Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>XML för konfiguration av Azure Media Indexer

I följande tabell beskrivs element och attribut för konfigurations-XML.

|Namn|Kräv|Beskrivning|
|---|---|---|
|Indata|true|Tillgångsfiler som du vill indexera.<br/>Azure Media Indexer stöder följande mediefilformat: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Du kan ange filnamnet (s) i **namnet** eller **listattributet** för **indataelementet** (som visas nedan). Om du inte anger vilken tillgångsfil som ska indexeras plockas den primära filen. Om ingen primär tillgångsfil har angetts indexeras den första filen i indatatillgången.<br/><br/>Om du uttryckligen vill ange namnet på tillgångsfilen gör du:<br/>```<input name="TestFile.wmv" />```<br/><br/>Du kan också indexera flera tillgångsfiler samtidigt (upp till 10 filer). Gör så här:<br/>- Skapa en textfil (manifestfil) och ge den ett LST-tillägg.<br/>- Lägg till en lista över alla tillgångsfilnamn i indatatillgången till den här manifestfilen.<br/>- Lägg till (ladda upp) manifestfilen till tillgången.<br/>- Ange namnet på manifestfilen i indatas listattribut.<br/>```<input list="input.lst">```<br/><br/>**Anm.:** Om du lägger till fler än 10 filer i manifestfilen misslyckas indexeringsjobbet med felkoden för 2006.|
|metadata|false|Metadata för de angivna tillgångsfilerna.<br/>```<metadata key="..." value="..." />```<br/><br/>Du kan ange värden för fördefinierade nycklar. <br/><br/>För närvarande stöds följande nycklar:<br/><br/>**titel** och **beskrivning** - används för att uppdatera språkmodellen för att förbättra taligenkänningsnoggrannheten.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**användarnamn** och **lösenord** - används för autentisering när du laddar ner internetfiler via http eller https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Användarnamns- och lösenordsvärdena gäller för alla medieadresser i indatamanifestet.|
|funktioner<br/><br/>Tillagd i version 1.2. För närvarande är den enda funktionen som stöds taligenkänning ("ASR").|false|Funktionen Taligenkänning har följande inställningstangenter:<br/><br/>Språk:<br/>- Det naturliga språk som ska kännas igen i multimediafilen.<br/>- Engelska, spanska<br/><br/>CaptionFormats:<br/>- En semikolonavseparerad lista över önskade utdatabildtextformat (om sådana finns)<br/>- ttml;webvtt<br/><br/><br/>GenerateKeywords:<br/>- En boolesk flagga som anger om en XML-nyckelordsfil krävs eller inte.<br/>- Det är sant. Falska.|

## <a name="azure-media-indexer-configuration-xml-example"></a>XML-exempel på Azure Media Indexer-konfiguration

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
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Nästa steg

Se [Indexera mediefiler med Azure Media Indexer](media-services-index-content.md).

