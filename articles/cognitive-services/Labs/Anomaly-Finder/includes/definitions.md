---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 5ad589c4adb60369f81979e214935f73d9eb0755
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228924"
---
<a name="definitions"></a>
## <a name="definitions"></a>Definitioner

<a name="point"></a>
### <a name="point"></a>Återställningspunkt

|Name|Beskrivning|Schema|
|---|---|---|
|**Tidsstämpel**  <br>*optional*|Tidsstämpel för datapunkten. Kontrollera att den överensstämmer med midnatt och Använd en UTC tidsvärdet sträng, till exempel 2017-08-01T00:00:00Z.|sträng (datum / tid)|
|**Värde**  <br>*optional*|Ett datavärde mått.|tal (dubbelt)|


<a name="request"></a>
### <a name="request"></a>Begäran

|Name|Beskrivning|Schema|
|---|---|---|
|**Period**  <br>*optional*|Period för datapunkter. Om värdet är null eller inte utgör någon, avgör API: et perioden automatiskt.|tal (dubbelt)|
|**Punkter**  <br>*optional*|Time series-data pekar. Data ska sorteras efter tidsstämpel stigande för att matcha avvikelseidentifiering resultatet. Om att data inte sorteras korrekt eller om det finns duplicerade tidsstämpel, API: et identifierar avvikelser punkter korrekt, men du kan inte bra matcha de punkter som returneras med indata. I detta fall är läggs ett varningsmeddelande i svaret.|< [Peka](#point) > matris|


<a name="response"></a>
### <a name="response"></a>Svar

|Name|Beskrivning|Schema|
|---|---|---|
|**ExpectedValues**  <br>*optional*|Förutsagt värde genom utbildningsresurser-baserad modell. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade värdet och ursprungsvärdet.|< tal (dubbelt) > matris|
|**IsAnomaly**  <br>*optional*|Resultatet på om datapunkter är avvikelser eller inte i båda riktningarna (toppar och dalar). innebär att SANT punkten ligger avvikelseidentifiering, innebär att false det är icke-avvikelseidentifiering. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade värdet och ursprungsvärdet.|< boolean > matris|
|**IsAnomaly_Neg**  <br>*optional*|Resultatet på om datapunkter är avvikelser i negativ riktning (dips). SANT innebär riktning avvikelsen är negativt. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade värdet och ursprungsvärdet.|< boolean > matris|
|**IsAnomaly_Pos**  <br>*optional*|Resultatet på om datapunkter är avvikelser i positiva riktning (toppar). SANT innebär riktning avvikelsen är positiv. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade och det ursprungliga värdet.|< boolean > matris|
|**LowerMargin**  <br>*optional*|(ExpectedValue - LowerMargin) anger den nedre gränsen för datapunkt är fortfarande betraktas som vanligt. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade värdet och ursprungsvärdet.|< tal (dubbelt) > matris|
|**Period**  <br>*optional*|Den period som API: et används för att identifiera avvikelser punkter.|Antal (float)|
|**UpperMargin**  <br>*optional*|Summan av ExpectedValue och UpperMargin anger den övre gränsen som datapunkt är fortfarande betraktas som vanligt. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade värdet och ursprungsvärdet.|< tal (dubbelt) > matris|
|**WarningText**  <br>*optional*|Om de inkommande datapunkter som tillhandahålls inte följer den regel som kräver att API: et och data fortfarande kan identifieras genom API: et, API: et analyserar data och lägga till varningsinformation i det här fältet.|sträng|



