---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a49df0c18ef9db9d0d41ca2e714474e6386ae7d1
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904471"
---
<a name="definitions"></a>
## <a name="definitions"></a>Definitioner

<a name="point"></a>
### <a name="point"></a>Återställningspunkt

|Namn|Beskrivning|Schema|
|---|---|---|
|**Tidsstämpel**  <br>*Valfritt*|Tidsstämpel för datapunkten. Kontrollera att den överensstämmer med midnatt och Använd en UTC tidsvärdet sträng, till exempel 2017-08-01T00:00:00Z.|sträng (datum / tid)|
|**Värde**  <br>*Valfritt*|Ett datavärde mått.|tal (dubbelt)|


<a name="request"></a>
### <a name="request"></a>Förfrågan

|Namn|Beskrivning|Schema|
|---|---|---|
|**Period**  <br>*Valfritt*|Period för datapunkter. Om värdet är null eller inte utgör någon, avgör API: et perioden automatiskt.|tal (dubbelt)|
|**Punkter**  <br>*Valfritt*|Time series-data pekar. Data ska sorteras efter tidsstämpel stigande för att matcha avvikelseidentifiering resultatet. Om att data inte sorteras korrekt eller om det finns duplicerade tidsstämpel, API: et identifierar avvikelser punkter korrekt, men du kan inte bra matcha de punkter som returneras med indata. I detta fall är läggs ett varningsmeddelande i svaret.|< [Peka](#point) > matris|


<a name="response"></a>
### <a name="response"></a>Svar

|Namn|Beskrivning|Schema|
|---|---|---|
|**ExpectedValues**  <br>*Valfritt*|Förutsagt värde genom utbildningsresurser-baserad modell. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade värdet och ursprungsvärdet.|< tal (dubbelt) > matris|
|**IsAnomaly**  <br>*Valfritt*|Resultatet på om datapunkter är avvikelser eller inte i båda riktningarna (toppar och dalar). innebär att SANT punkten ligger avvikelseidentifiering, innebär att false det är icke-avvikelseidentifiering. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade värdet och ursprungsvärdet.|< boolean > matris|
|**IsAnomaly_Neg**  <br>*Valfritt*|Resultatet på om datapunkter är avvikelser i negativ riktning (dips). SANT innebär riktning avvikelsen är negativt. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade värdet och ursprungsvärdet.|< boolean > matris|
|**IsAnomaly_Pos**  <br>*Valfritt*|Resultatet på om datapunkter är avvikelser i positiva riktning (toppar). SANT innebär riktning avvikelsen är positiv. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade och det ursprungliga värdet.|< boolean > matris|
|**LowerMargin**  <br>*Valfritt*|(ExpectedValue - LowerMargin) anger den nedre gränsen för datapunkt är fortfarande betraktas som vanligt. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade värdet och ursprungsvärdet.|< tal (dubbelt) > matris|
|**Period**  <br>*Valfritt*|Den period som API: et används för att identifiera avvikelser punkter.|Antal (float)|
|**UpperMargin**  <br>*Valfritt*|Summan av ExpectedValue och UpperMargin anger den övre gränsen som datapunkt är fortfarande betraktas som vanligt. Om inkommande datapunkter sorteras efter tidsstämpel stigande, användas index för matrisen för att mappa det förväntade värdet och ursprungsvärdet.|< tal (dubbelt) > matris|
|**WarningText**  <br>*Valfritt*|Om de inkommande datapunkter som tillhandahålls inte följer den regel som kräver att API: et och data fortfarande kan identifieras genom API: et, API: et analyserar data och lägga till varningsinformation i det här fältet.|sträng|



