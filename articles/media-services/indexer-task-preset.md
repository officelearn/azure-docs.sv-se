---
title: "Uppgiften förinställningen Azure Media indexer"
description: "Det här avsnittet ger en översikt över aktivitet förinställningen Azure Media indexer."
services: media-services
documentationcenter: 
author: Asolanki
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: adsolank;juliako;
ms.openlocfilehash: ae6c4da189cd6637b4e1fa9274473b62f6664e51
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="task-preset-for-azure-media-indexer"></a>Uppgiften förinställningen Azure Media indexer

Azure Media Indexer är en Medieprocessor som används för att utföra följande uppgifter: Se mediefiler och innehåll sökbara, generera stängd textning spårar och nyckelord, index tillgångsfiler som ingår i din tillgång.

Det här avsnittet beskriver aktiviteten förinställningen du behöver för att skicka till indexerings-jobbet. Komplett exempel finns [indexering mediefiler med Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer konfigurations-XML

I följande tabell beskrivs element och attribut i XML-konfigurationen.

|Namn|Kräv|Beskrivning|
|---|---|---|
|Indata|SANT|Tillgångsinformation fil(er) som du vill index.<br/>Azure Media Indexer stöder följande media format: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Du kan ange filnamnet (s) i den **namn** eller **lista** attribut för den **inkommande** element (som visas nedan). Om du inte anger vilken tillgångsfil som till index hämtas den primära filen. Om inga primära resursfilen anges indexeras den första filen i den inkommande tillgången.<br/><br/>Gör att explicit ange filnamnet för tillgångsinformation:<br/>```<input name="TestFile.wmv" />```<br/><br/>Du kan även indexera flera tillgångsfiler på en gång (upp till 10). Gör så här:<br/>-Skapa en textfil (manifestfilen) och ge den ett .lst-tillägg.<br/>-Lägg till en lista över alla filnamn för tillgångsinformation i din inkommande tillgång till den här manifestfilen.<br/>-Lägg till () thanifest uppladdningsfilen tillgången.<br/>-Ange namnet på manifestfilen i angivna indata-attribut.<br/>```<input list="input.lst">```<br/><br/>**Obs:** om du lägger till fler än 10 filer manifestfilen indexering jobbet misslyckas med felkoden 2006.|
|Metadata|FALSKT|Metadata för den angivna resursen /-filerna.<br/>```<metadata key="..." value="..." />```<br/><br/>Du kan ange värden för fördefinierade nycklar. <br/><br/>För närvarande stöds följande nycklar:<br/><br/>**Rubrik** och **beskrivning** – används för att uppdatera modellen språk för att förbättra taligenkänningen.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**användarnamnet** och **lösenord** – används för autentisering när du hämtar Internetfiler via http eller https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Värden för användarnamn och lösenord gäller media URL: er i inkommande manifestet.|
|funktioner<br/><br/>Lägga till i version 1.2. Den enda funktionen som stöds är för närvarande taligenkänning (”ASR”).|FALSKT|Taligenkänning har följande inställningar för:<br/><br/>Språk:<br/>-Naturligt språk ska kunna identifieras i fil.<br/>-Engelska, spanska<br/><br/>CaptionFormats:<br/>-en semikolonavgränsad lista över önskade utgående bildtexten format (eventuella)<br/>-ttml; sami; webvtt<br/><br/><br/>GenerateAIB:<br/>-En boolesk flagga som anger huruvida en AIB fil krävs (för användning med SQL Server och kunden indexeraren IFilter). Mer information finns i använda AIB filer med Azure Media Indexer och SQL Server.<br/>-SANT. FALSKT<br/><br/>GenerateKeywords:<br/>-En boolesk flagga som anger huruvida en nyckelordet XML-fil måste anges.<br/>-SANT. FALSKT.|

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

Se [indexering mediefiler med Azure Media Indexer](media-services-index-content.md).

