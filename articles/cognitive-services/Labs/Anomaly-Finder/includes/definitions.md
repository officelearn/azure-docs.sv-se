---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: ff36202b67f6262b7ba67fe48ef37f2b656b91fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353352"
---
<a name="definitions"></a>
## <a name="definitions"></a>Definitioner

<a name="point"></a>
### <a name="point"></a>Punkt

|Namn|Beskrivning|Schema|
|---|---|---|
|**Tidsstämpel**  <br>*Valfria*|Tidsstämpel för datapunkten. Kontrollera att den överensstämmer med midnatt och Använd UTC tidsvärdet sträng, till exempel 2017-08-01T00:00:00Z.|sträng (tid)|
|**Värde**  <br>*Valfria*|Måttet värdet.|tal (dubbelt)|


<a name="request"></a>
### <a name="request"></a>Förfrågan

|Namn|Beskrivning|Schema|
|---|---|---|
|**Period**  <br>*Valfria*|Period för datapunkter. Om värdet är null eller inte finns, avgör API: et perioden automatiskt.|tal (dubbelt)|
|**Punkter**  <br>*Valfria*|Den tid seriens datapunkter. Data ska sorteras efter tidsstämpel stigande för att matcha avvikelseidentifiering resultatet. Om data sorteras inte korrekt eller så finns det duplicerade tidsstämpel API: et identifierar avvikelseidentifiering pekar på rätt sätt, men du kan inte också matchar de punkter som returneras med indata. I så fall läggs ett varningsmeddelande i svaret.|< [Peka](#point) > matris|


<a name="response"></a>
### <a name="response"></a>Svar

|Namn|Beskrivning|Schema|
|---|---|---|
|**ExpectedValues**  <br>*Valfria*|Förutsägelsevärdet med inlärning baserat modellen. Om inkommande datapunkter sorteras efter tidsstämpel stigande användas index för matrisen för att mappa det förväntade värdet och ursprungliga värdet.|< tal (dubbelt) > matris|
|**IsAnomaly**  <br>*Valfria*|Resultatet om datapunkter är avvikelser eller inte i båda riktningarna (toppar eller korta). innebär sant att punkten avvikelseidentifiering, innebär false att det är icke-avvikelseidentifiering. Om inkommande datapunkter sorteras efter tidsstämpel stigande användas index för matrisen för att mappa det förväntade värdet och ursprungliga värdet.|< boolean > matris|
|**IsAnomaly_Neg**  <br>*Valfria*|Resultatet om datapunkter är avvikelser i negativ riktning (korta). SANT innebär att riktningen för avvikelseidentifiering är negativt. Om inkommande datapunkter sorteras efter tidsstämpel stigande användas index för matrisen för att mappa det förväntade värdet och ursprungliga värdet.|< boolean > matris|
|**IsAnomaly_Pos**  <br>*Valfria*|Resultatet om datapunkter är avvikelser i positiv riktning (toppar). SANT innebär att riktningen för avvikelseidentifiering är positivt. Om inkommande datapunkter sorteras efter tidsstämpel stigande användas index för matrisen för att mappa det förväntade och det ursprungliga värdet.|< boolean > matris|
|**LowerMargin**  <br>*Valfria*|(ExpectedValue - LowerMargin) anger den nedre gränsen för datapunkt kan fortfarande anses som vanligt. Om inkommande datapunkter sorteras efter tidsstämpel stigande användas index för matrisen för att mappa det förväntade värdet och ursprungliga värdet.|< tal (dubbelt) > matris|
|**Period**  <br>*Valfria*|Den period som API: et används för att identifiera avvikelseidentifiering punkter.|Antal (float)|
|**UpperMargin**  <br>*Valfria*|Summan av ExpectedValue och UpperMargin anger den övre gränsen för datapunkt kan fortfarande anses som vanligt. Om inkommande datapunkter sorteras efter tidsstämpel stigande användas index för matrisen för att mappa det förväntade värdet och ursprungliga värdet.|< tal (dubbelt) > matris|
|**WarningText**  <br>*Valfria*|Om inkommande datapunkter som inte följer den regel som kräver API: et och data kan fortfarande upptäckas av API: et, kommer API: et analysera data och Lägg till varning informationen i det här fältet.|sträng|



