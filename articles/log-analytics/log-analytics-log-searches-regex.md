---
title: "Reguljära uttryck i OMS logganalys logga sökningar | Microsoft Docs"
description: "Du kan använda RegEx-nyckelord i logganalys loggen sökningar för filtret resultaten efter ett reguljärt uttryck.  Den här artikeln innehåller syntaxen för dessa uttryck med flera exempel."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.openlocfilehash: 9746170f157ed5065adc953a31687ff18bd73708
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>Använda reguljära uttryck för att filtrera loggen söker i logganalys

[Logga sökningar](log-analytics-log-searches.md) gör att du kan hämta information från logganalys-databasen.  [Filtrera uttryck](log-analytics-search-reference.md#filter-expressions) kan du filtrera resultaten för sökningen efter specifika villkor.  Den **RegEx** nyckelord kan du ange ett reguljärt uttryck för det här filtret.  

Den här artikeln innehåller information om syntax för reguljära uttryck som används av logganalys.

> [!NOTE]
> Du kan bara använda RegEx med sökbara fält.  Mer information om sökbara fält finns **fälttyp** i [söka efter data med hjälp av loggen sökningar i logganalys](log-analytics-log-searches.md#use-additional-filters).


## <a name="regex-keyword"></a>RegEx-nyckelord

Använd följande syntax för att använda den **RegEx** nyckelord i en sökning i loggen.  Du kan använda andra avsnitt i den här artikeln för att fastställa syntaxen för reguljära uttryck sig själv.

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

Till exempel för att använda ett reguljärt uttryck och avisering poster med en typ av *varning* eller *fel*, använder du följande loggen sökningen.

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>Delmatchningar
Observera att det reguljära uttrycket måste matcha egenskapen hela texten.  Delmatchningar returneras inte några poster.  Till exempel om du försökte returnera poster från en dator med namnet srv01.contoso.com följande loggen sökningen skulle **inte** returnerar några poster.

    Computer=RegEx("srv..")

Det beror på att den första delen av namnet matchar det reguljära uttrycket.  I följande två loggen genomsöks returnerar poster från den här datorn eftersom de matchar hela namnet.

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>Tecken
Ange olika tecken.

| Tecken | Beskrivning | Exempel | Exempel matchar |
|:--|:--|:--|:--|
| A | En förekomst av tecknet. | Computer=Regex("Srv01.contoso.com") | Srv01.contoso.com |
| . | Ett enskilt tecken. | Computer=Regex("SRV...contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| en? | Noll eller en förekomst av tecknet. | Datorn = RegEx (”srv01?. ”Contoso.com”) | srv0.contoso.com<br>Srv01.contoso.com |
| en * | Noll eller flera förekomster av tecken. | Computer=Regex("Srv01*.contoso.com") | srv0.contoso.com<br>Srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| en + | En eller flera förekomster av tecken. | Computer=Regex("Srv01+.contoso.com") | Srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | Matcha valfritt enstaka tecken inom parentes | Computer=Regex("srv0[123].contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| [*en*-*z*] | Matcha ett enskilt tecken i intervallet.  Kan innehålla flera adressintervall. | Computer=Regex("srv0[1-3].contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | Ingen av tecknen inom parentes | Computer=Regex("srv0[^123].contoso.com") | srv05.contoso.com<br>SRV06.contoso.com<br>srv07.contoso.com |
| [^*en*-*z*] | Ingen tecken i intervallet. | Computer=Regex("srv0[^1-3].contoso.com") | srv05.contoso.com<br>SRV06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | Matcha en uppsättning numeriska tecken. | Computer=Regex("SRV[01-03].contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| @ | Valfri sträng med tecken. | Datorn = RegEx (”srv@.contoso.com”) | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>Flera förekomster av tecken
Ange flera förekomster av en viss tecken.

| Tecken | Beskrivning | Exempel | Exempel matchar |
|:--|:--|:--|:--|
| {n} |  *n*förekomster av tecken. | Computer=Regex("BW-Win-sc01{3}.bwren.Lab") | BW-win-sc0111.bwren.lab |
| {n} |  *n*eller flera förekomster av tecken. | Computer=Regex("BW-Win-sc01{3,}.bwren.Lab") | BW-win-sc0111.bwren.lab<br>BW-win-sc01111.bwren.lab<br>BW-win-sc011111.bwren.lab<br>BW-win-sc0111111.bwren.lab |
| {n, m} |  *n*att *m* förekomster av tecken. | Computer=Regex("BW-Win-sc01{3,5}.bwren.Lab") | BW-win-sc0111.bwren.lab<br>BW-win-sc01111.bwren.lab<br>BW-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>Logiska uttryck
Välj flera värden.

| Tecken | Beskrivning | Exempel | Exempel matchar |
|:--|:--|:--|:--|
| &#124; | Logiskt OR.  Returnerar resultatet om matchar på något av uttrycken. | Typ = avisering AlertSeverity = RegEx (”varning &#124; Fel ”) | Varning<br>Fel |
| & | Logiskt och.  Returnerar resultatet om matchar på båda uttryck | EventData = regex (”(Security.\* &. \*lyckades. \*)") | Säkerhetsgranskning lyckades |


## <a name="literals"></a>Literaler
Konvertera specialtecken till strängtecken.  Det innehåller tecken som innehåller funktioner för reguljära uttryck som?-\*^\[\]{}\(\)+\|. &.

| Tecken | Beskrivning | Exempel | Exempel matchar |
|:--|:--|:--|:--|
| \\ | Konverterar ett specialtecken till en literal. | Status_CF =\\[fel\\] @<br>Status_CF = fel\\-@ | [Fel] Filen hittades inte.<br>Fel-filen hittades inte. |


## <a name="next-steps"></a>Nästa steg

* Bekanta dig med [logga sökningar](log-analytics-log-searches.md) att visa och analysera data i logganalys-databasen.
