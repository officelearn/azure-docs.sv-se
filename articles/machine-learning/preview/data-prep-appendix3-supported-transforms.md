---
title: "Använd data transformeringar för förberedelse av data i Azure Machine Learning | Microsoft Docs"
description: "Den här artikeln innehåller en fullständig lista över transformationer som är tillgängliga för Azure Machine Learning förberedelse av data."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 10/09/2017
ms.openlocfilehash: d0b62a63d381239e17b7dccceb89171bca15a68e
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Använd data transformeringar för förberedelse av data i Azure Machine Learning

En *transformera* i Azure Machine Learning förbrukar data i ett visst format, utför en åtgärd på data (till exempel ändra datatyp) och genererar data i det nya formatet. Varje transformering har egna gränssnittet och beteende. Du kan utföra komplexa och repeterbara omformningar på dina data med länkning flera transformeringar tillsammans via stegen i dataflödet. Det här är grundläggande funktioner för förberedelse av data.

Följande är transformeringar tillgängliga i Azure Machine Learning. 

## <a name="column-selection"></a>Kolumnen 
Många av transformeringar i den här listan fungerar på en kolumn eller många. Använd Ctrl-tangenten om du vill markera flera kolumner. Använd SKIFT-tangenten för att välja ett antal kolumner.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>Transformerar från huvudmenyn eller rutnät huvudet 
Åtkomst transformerar från alternativet transformeringar på huvudmenyn. Du kan också välja transformeringar genom att högerklicka på kolumnnamnet i datarutnätet. Om flera kolumner har valts, innehåller Högerklicka på någon av dem en transformeringar meny.

Snabbmenyn visar endast giltiga transformeringar för den datatyp som valts. Huvudmenyn ger alla transformeringar men inaktiverar de inte är relevanta för de markerade kolumnerna.

En liten del av kontextuella transformeringar är tillgänglig genom att högerklicka på en cell. Dessa transformeringar är kopia, Ersätt, och filtrera. Dessa är data typ-medveten, så alternativ för en kolumn med tal skiljer sig för en strängkolumn.

## <a name="derive-column-by-example"></a>Härledd kolumn efter exempel
Använd den här transformeringen för att skapa en ny kolumn som ett derivat av en eller flera befintliga kolumner. Transformera söker i indatakolumnerna (markerade) och exemplet och anger önskad utdata i den nya kolumnen. 

Välj en eller flera kolumner om du vill använda den här transformeringen. Lägg till en ny (tom) härledd kolumn efter exempel. Skriv ett exempel på vad du vill se i kolumnen härledda (förutsatt att den är härledd från andra kolumner) och ”av exemplet” teknik försöker att fylla i de andra cellerna i kolumnen. 

Komplicerade exempel kan du behöva ge mer än ett exempel. För att göra detta, Välj en annan cell och ange ett annat exempel.

”Med hjälp av exempel”-tekniken använder valda kolumner för att försöka fastställa innebörden av ett exempel. Om inga kolumner har valts när den här transformeringen har anropats, används alla celler för den aktuella raden. Att välja endast de obligatoriska kolumnerna leder till mer korrekta resultat.

Du kan markera kolumner innan du anropar transformeringen. När Redigeraren för transformering har öppnats, kryssrutorna längst upp i varje kolumn som anger vilka kolumner har valts som indata. Du kan lägga till eller ta bort kolumner från markeringen med hjälp av kryssrutorna i en kolumnrubrik.

En mer detaljerad förklaring av de **härledd kolumn efter exempel** transformeringen tillsammans med flera sampel, se [härledd kolumn efter exempel referens](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Delad kolumn efter exempel
Den här transformeringen tar en befintlig kolumn och försöker med ”med hjälp av exempel”-motorn kan dela i kolumnen  *n*  andra kolumner. Du kan köra auto-delning på efterföljande genererade kolumner.

En mer detaljerad förklaring av de **delad kolumn efter exempel** transformeringen tillsammans med flera sampel, se [delad kolumn av exempel referens](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Expandera JSON

Den här transformeringen kan du lägga till flera kolumner genom att expandera en kolumn med giltig JSON-texten.

En mer detaljerad förklaring av de **Expandera JSON** transformeringen tillsammans med flera sampel, se [Expandera JSON-referens](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Kombinera kolumner efter exempel

Den här transformeringen lägger till en ny kolumn genom att kombinera värden från flera kolumner. 

En mer detaljerad förklaring av de **kombinera kolumner efter exempel** transformeringen tillsammans med flera sampel, se [kombinera kolumner efter exempel referens](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Dubbletter av kolumnnamn
Den här transformeringen gör en exakt kopia av en eller flera av de markerade kolumnerna och ger varje ett nytt namn härleds från det ursprungliga kolumnnamnet.

## <a name="text-clustering"></a>Text-kluster 
Den här transformeringen är utformade för att dra inkonsekventa värden som ska vara samma och gruppera dem.  

Med den här transformeringen analyseras för likhet och grupperade i kluster värdena i en kolumn. För varje kluster finns ett kanoniskt värde, vilket är det värde som ersätter alla instanser i klustret och instansvärden för alla. Fullständig kluster kan tas bort och kanoniskt värde redigeras. Instanser kan tas bort från ett kluster. Du kan ändra filtret likhet poäng tröskelvärdet som har använt till gruppen instanser i ett kluster.

Som standard ersätter den här transformeringen alla värden i klustret-instans med kanoniskt värde för klustret, skapa en ny kolumn som innehåller de nya värdena. Du kan också lägga till likhet poängen för varje instans till en ny kolumn (som kan namnges) för användning senare i dataflödet.

## <a name="replace-values"></a>Ersätt värden
Använd den här transformeringen för att ersätta en sträng med ett annat. Källsträngen kan vara en partiell sträng eller en fullständig cell och ersätter kan användas för en kolumn eller många. Strängen har stöd för sökning efter specialtecken samt för vanliga tecken. 

## <a name="replace-na-values"></a>Ersätt värden som saknas
Använd den här transformeringen för att ersätta olika typer av NA (saknas, NA, null eller NaN osv), eller ersätta tomma strängar med ett värde så att de blir konsekventa. Den här transformeringen har stöd för en eller flera kolumner och den visas bara när en kolumn har markerats. Det finns inte på menyn huvudsakliga transformeringen när inga kolumner har valts.

## <a name="replace-missing-values"></a>Ersätt värden som saknas
Den här transformeringen ersätter data som saknas med ett enskilt värde och den stöder en eller flera kolumner. Den här transformeringen visas bara när en kolumn har markerats. Det finns inte på menyn huvudsakliga transformeringen när inga kolumner har valts.

## <a name="replace-error-values"></a>Ersätt felvärdena
Den här transformeringen ersätter fel med ett enskilt värde och den stöder en eller flera kolumner. Den här transformeringen visas bara när en kolumn har markerats. Det finns inte på menyn huvudsakliga transformeringen när inga kolumner har valts.

## <a name="trim-string"></a>Trim sträng
Den här transformeringen tar bort inledande och avslutande ”” blanksteg (inklusive blanksteg, tabbar, etc.) från en eller flera kolumner.

## <a name="adjust-precision"></a>Justera Precision
Den här transformeringen anger antalet decimaler för en numerisk kolumn.

## <a name="rename-column"></a>Byt namn på kolumnen
Den här transformationen ändrar namnet på den markerade kolumnen. Du kan också anropa den infogad i kolumnrubriken genom att klicka på namnet på kolumnen.

## <a name="remove-column"></a>Ta bort kolumnen
Den här transformeringen tar bort de markerade kolumnerna och den fungerar på en kolumn eller många. 

## <a name="keep-column"></a>Behåll kolumnen
Den här transformeringen bevarar bara de markerade kolumnerna och den fungerar på en kolumn eller många.

## <a name="handle-path-column"></a>Hantera kolumnen sökväg
Vid import av en fil, sökväg är automatiskt lägga till en kolumn i datauppsättningen med den **Lägg till datakälla** funktion. Det innehåller det fullständigt kvalificerade filnamnet som utgör sökvägen till datamängden som. Den här transformeringen lägger till eller tar bort den extra kolumnen i datamängden.

## <a name="convert-field-type-to-numeric"></a>Omvandla fälttypen till numeriska
Den här transformeringen ändras kolumntypen till numeriska. Du kan ange avgränsaren för data som inte är heltal. Den här transformeringen inte frågar för avgränsaren som standard, så den **redigera** menyalternativet att anropa redigeraren. Den här transformeringen fungerar på en kolumn eller många.

## <a name="convert-field-type-to-date"></a>Typ av fält att konvertera till Date
Den här transformeringen ändras kolumntypen till datum. En standardformatet för datum/tid används, men den kan åsidosättas med hjälp av `strftime` direktiven. Du kan också lägga time-värden med ett fast datum.

Den här transformeringen inte frågar format som standard, så den **redigera** menyobjekt på det resulterande steget för att anropa redigeraren. Den här transformeringen fungerar på en kolumn eller många.

Endast datum mellan 9-22-1677 och 4-11-2262 kan konverteras till datatypen datum.

## <a name="convert-field-type-to-boolean"></a>Konvertera fälttypen till booleskt värde
Den här transformeringen ändras kolumntypen till SANT/FALSKT. Den stöder mappa flera värden till true eller false och du kan redigera mappningarna. Det stöder också en konstant som du kan mappa värden som inte finns i tabellerna SANT/FALSKT mappning. Den här transformeringen fungerar på en kolumn eller många.

## <a name="convert-field-type-to-string"></a>Konvertera fälttypen till sträng
Den här transformeringen ändras kolumntypen till sträng och den fungerar på en kolumn eller många.

## <a name="convert-unix-timestamp-to-datetime"></a>Konvertera Unix tidsstämpel till DateTime
Den här transformeringen förstår tidstämpelformatet Unix även om det visas som en sträng i data. Den konverterar tidsstämpeln korrekt till en datum i förberedelse av data.

## <a name="filter"></a>Filter
Den här transformeringen har stöd för filtrering av raderna baserat på värdena i en eller flera kolumner. Villkoren i filtret beror på datatypen för varje kolumn, så du kan filtrera kolumner strängen ”innehåller” eller ”innehåller inte”. Numeriska kolumner kan filtreras efter ”större än” eller ”mindre än” villkor.

När den **Filter** transformeringen anropas från huvudmenyn eller från att högerklicka på rubriken för en kolumn, finns alternativet att duplicera misslyckas rader i en annan dataflöde. Det huvudsakliga dataflödet fortsätter med filtrerade **i** rader och ett nytt dataflöde skapas som innehåller de rader som har filtrerats **ut**.

## <a name="use-first-row-as-headers"></a>Använd första raden som rubriker
Den här transformeringen flyttar upp den första raden i den datamängd som ska vara kolumnnamnen. Om vissa kolumner inte har data på den första raden, är ett namn automatiskt genererade. Med den här transformeringen innebär att importen av data börjar på rad 2 så fort som möjligt. Beroende på den **hoppa över rader** inställningen genom att importera starta även längre ned i datauppsättningen. Du kan också använda den att ta bort befordran och använda endast automatiskt genererade namn.

## <a name="join"></a>Slå ihop
Den här transformeringen används för att ansluta två dataflöden tillsammans. Du kan välja vilka utdata för anslutning till är resultatet: lyckad rader från kopplingen ”vänster” misslyckas rader från kopplingen eller ”höger” misslyckas rader från kopplingen.

Den **koppling** transformeringen startar från ett enskilt dataflöde och väljer att dataflödet som en sida i kopplingen. Sedan uppmanas du att välja en annan dataflödet för den andra sidan av kopplingen. När du har valt de två flödena, krävs en enda kolumn på varje sida i kopplingen väljas att sammanfoga för transformeringen. Om kopplingen behöver mer än en kolumn, kan du skapa en härledd kolumn innan du startar transformeringen om du vill skapa en ny, sammanfogad kolumn som ska användas endast för kopplingen. Transformera försöker föreslår koppling nycklar och, om möjligt generera härledda kolumnen automatiskt.

Kopplingen har slutförts, visas en Sankey diagramvy för anslutning till. Tjockleken på linjerna i förhållande till varandra representerar antalet rader som flytta via den del av join-flödet. Du kan välja lyckade rader, vänster inte rader eller höger misslyckas Rader enbart med hjälp av panelen till höger. Du kan också välja en enda filial.

## <a name="append-rows"></a>Lägg till rader
Den här transformeringen lägger till data från en annan dataflöde till den aktuella processen. Den mappar kolumner med möjlighet att lägga till nya rader i slutet.

## <a name="append-columns"></a>Lägga till kolumner
Den här transformeringen lägger till nya kolumner från en annan dataflöde till den aktuella. Nya kolumner läggs till höger. Den försöka inte ordna data i rader.

## <a name="summarize"></a>Sammanfatta
Den här transformeringen beräknar mängder för kombinationen av unika värden i en eller flera av de markerade kolumnerna. 

Aggregeringar som stöds är: antal, SUM, MIN, MAX, medelvärde, varians och standardavvikelsen. Listan med mängder för en viss kolumn har filtrerats till de mängder som gäller för datatypen. Mängder som inte gäller inaktiveras. T.ex, det går inte att beräkna medelvärdet för en strängkolumn, men det är möjligt att beräkna MIN och MAX.

När det finns redigeraren dra från kolumnrubriken till panelen längst upp till vänster, där kolumnerna som ska aggregeras visas. Den här panelen är hierarkiska, så du kan göra kapslade mängder. Panelen editor längst upp till höger används för att välja vilken mängd ska gälla för en kolumn. En kolumn kan sammanställas till en eller flera gånger. När minst en mängd har valts kommer förhandsgranskar rutnät längst ned rätt data i sin sammanställd form. 

Den här transformeringen är detsamma som en `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Ta bort dubbletter
Den här transformeringen tar bort hela raden när det finns dubblettvärden i en eller flera av de markerade kolumnerna. Om inga kolumner är valt, är endast rader som tas bort sådana där kolumnvärdena är samma.

## <a name="sort"></a>Sortera
Den här transformeringen sorterar data. Sorteringen kan göras av en enda kolumn eller många och varje kolumn kan sorteras stigande (standard) eller fallande (som kan ändras från redigeraren).

Den här transformeringen är detsamma som en `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Utgående transformeringar
Följande transformeringar utdata. Du kan ha flera write block i ett enda flöde för att skriva data vid olika tidpunkter.

### <a name="write-to-csv"></a>Skriva till CSV
Den här transformeringen skriver data i CSV-formatet från den aktuella punkten i dataflödet. Den styr plats (lokala eller fjärranslutna) och olika inställningar runt filen.

### <a name="write-to-parquet"></a>Skriva till parkettgolv
Den här transformeringen skriver data i formuläret parkettgolv från den aktuella punkten i dataflödet. Den styr plats (lokala eller fjärranslutna) och olika inställningar runt filen.

## <a name="script-based-transforms"></a>Skript-baserad transformeringar
Följande transformeringar använda skript (Python) för att utföra funktioner som saknas i huvudprodukten. 

>[!NOTE]
>Innan du använder någon av dessa transformeringar läsa [med Python utökningsbarhet](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Lägg till kolumn (skript)
Den här transformeringen lägger till en kolumn data med en Python-uttryck.
Mer information finns i [exempel Python-kod för att få fram nya kolumner](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Avancerade Filter (skript)
Använd den här transformeringen för att skriva ett nivå Python radfilter.
Mer information finns i [exempel filteruttryck](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transformera dataflöde (skript)
Den här transformeringen gäller Python hela datauppsättningen.
Mer information finns i [exempel transformeringen dataflöde transformationer](data-prep-appendix7-sample-transform-data-flow-python.md).

Den här transformeringen kan gäller även Python för en hel datapartition.
Mer information finns i [exempel transformeringen dataflöde transformationer](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Skriva dataflöde (skript)
Den här transformeringen använder Python för att skriva ut en hela datauppsättningen.
Mer information finns i [exempel Python för härledning av nya kolumner](data-prep-appendix9-sample-destination-connections-python.md).



