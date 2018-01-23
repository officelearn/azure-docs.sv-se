---
title: "Reguljära uttryck i Azure Log Analytics logga sökningar | Microsoft Docs"
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
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: 8915e0e35951871ff10fd84453d55bd5102e97df
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>Använda reguljära uttryck för att filtrera loggen söker i logganalys

>[!NOTE]
> Den här artikeln beskriver reguljära uttryck med äldre frågespråket i logganalys.  Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), läser du bör [reguljära uttryck i dokumentationen för språket](https://docs.loganalytics.io/docs/Language-Reference/References/Regular-Expressions-syntax).


[Logga sökningar](log-analytics-log-searches.md) gör att du kan extrahera information från logganalys-arbetsytan.  [Filtrera uttryck](log-analytics-search-reference.md#filter-expressions) kan du filtrera resultaten för sökningen efter specifika villkor.  Den **RegEx** nyckelord kan du ange ett reguljärt uttryck för det här filtret.  

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

## <a name="characters"></a>Karaktärer
Ange olika tecken.

| Tecken | Beskrivning | Exempel | Exempel matchar |
|:--|:--|:--|:--|
| a | En förekomst av tecknet. | Computer=RegEx("srv01.contoso.com") | srv01.contoso.com |
| . | Ett enskilt tecken. | Computer=RegEx("srv...contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| en? | Noll eller en förekomst av tecknet. | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>srv01.contoso.com |
| a* | Noll eller flera förekomster av tecken. | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | En eller flera förekomster av tecken. | Computer=RegEx("srv01+.contoso.com") | srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | Matcha valfritt enstaka tecken inom parentes | Computer=RegEx("srv0[123].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [*a*-*z*] | Matcha ett enskilt tecken i intervallet.  Kan innehålla flera adressintervall. | Computer=RegEx("srv0[1-3].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | Ingen av tecknen inom parentes | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^*a*-*z*] | Ingen tecken i intervallet. | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | Matcha en uppsättning numeriska tecken. | Computer=RegEx("srv[01-03].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| @ | Valfri sträng med tecken. | Computer=RegEx("srv@.contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>Flera förekomster av tecken
Ange flera förekomster av en viss tecken.

| Tecken | Beskrivning | Exempel | Exempel matchar |
|:--|:--|:--|:--|
| a{n} |  *n*förekomster av tecken. | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| {n} |  *n*eller flera förekomster av tecken. | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| {n, m} |  *n*att *m* förekomster av tecken. | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>Logiska uttryck
Välj flera värden.

| Tecken | Beskrivning | Exempel | Exempel matchar |
|:--|:--|:--|:--|
| &#124; | Logiskt OR.  Returnerar resultatet om matchar på något av uttrycken. | Type=Alert AlertSeverity=RegEx("Warning&#124;Error") | Varning<br>Fel |
| & | Logiskt och.  Returnerar resultatet om matchar på båda uttryck | EventData=regex("(Security.\*&.\*success.\*)") | Säkerhetsgranskning lyckades |


## <a name="literals"></a>Literaler
Konvertera specialtecken till strängtecken.  Det innehåller tecken som innehåller funktioner för reguljära uttryck som?-\*^\[\]{}\(\)+\|. &.

| Tecken | Beskrivning | Exempel | Exempel matchar |
|:--|:--|:--|:--|
| \\ | Konverterar ett specialtecken till en literal. | Status_CF=\\[Error\\]@<br>Status_CF=Error\\-@ | [Fel] Filen hittades inte.<br>Fel-filen hittades inte. |


## <a name="next-steps"></a>Nästa steg

* Bekanta dig med [logga sökningar](log-analytics-log-searches.md) att visa och analysera data i logganalys-arbetsytan.
