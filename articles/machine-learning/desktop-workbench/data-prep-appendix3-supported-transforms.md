---
title: Använd data transformeringar för förberedelse av data i Azure Machine Learning | Microsoft Docs
description: Den här artikeln innehåller en fullständig lista över transformationer som är tillgängliga för förberedelse av data i Azure Machine Learning.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 26674648217e01b66fbe722cd013d51d867e9ba9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984404"
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Använd data transformeringar för förberedelse av data i Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

En *transformera* i Azure Machine Learning använder data i ett visst format, utför en åtgärd på data (till exempel ändra datatypen) och sedan skapar data i det nya formatet. Varje transformering har sina egna gränssnitt och beteende. Du kan utföra komplexa och repeterbar transformationer på dina data genom att länkning flera transformeringar tillsammans via steg i dataflödet. Det här är kärnan i funktioner för förberedelse av data.

Följande är transformeringar som är tillgängliga i Azure Machine Learning. 

## <a name="column-selection"></a>Kolumnurval 
Många av transformeringar i den här listan fungerar på en enda kolumn eller många. Använd Ctrl-tangenten för att välja flera kolumner. Använd SKIFT-tangenten om du vill välja flera kolumner.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>Transformeringar från huvudmenyn eller grid-rubrik 
Åtkomst transformerar från alternativet transformeringar på huvudmenyn. Du kan också välja transformeringar genom att högerklicka på kolumnnamnet i datarutnätet. Om flera kolumner har valts, innehåller Högerklicka på någon av dem en transformeringar-meny.

Snabbmenyn visar bara giltiga transformeringar för den datatyp som valts. På huvudmenyn erbjuder alla transformeringar men inaktiverar de som inte är relevanta för de markerade kolumnerna.

En liten delmängd av sammanhangsbaserad transformeringar är tillgänglig genom att högerklicka på en cell. Dessa transformationer är kopia, Ersätt, och filtrera. Det här är data typ-medveten, så att alternativen för en talkolumn skiljer sig för en strängkolumn.

## <a name="derive-column-by-example"></a>Härled kolumn efter exempel
Använd den här transformeringen för att skapa en ny kolumn som ett derivat av en eller flera befintliga kolumner. Vi tittar på inkommande (valda) kolumner och exemplet och anger sedan önskade utdata i den nya kolumnen. 

Välj en eller flera kolumner om du vill använda den här transformeringen. Lägg till en ny (tom) härledd kolumn efter exempel. Ange ett exempel på vad du vill se i den härledda kolumnen (förutsatt att den är härledd från andra kolumner) och ”av exemplet” teknik försöker att fylla i alla andra celler i kolumnen. 

Komplicerade exempel kan du behöva ge flera exempel. Välj en annan cell och ange ett annat exempel gör du genom.

”Med hjälp av exempel”-teknik använder markerade kolumner för att försöka fastställa enligt ett exempel. Om inga kolumner har valts när den här transformeringen har anropats, används alla celler för den aktuella raden. Att välja endast de obligatoriska kolumnerna leder till mer exakta resultat.

Du kan välja kolumner innan du anropar transformeringen. När Redigeraren för transformering har öppnats, visar kryssrutorna högst upp i varje kolumn vilka kolumner har valts som indata. Du kan lägga till eller ta bort kolumner från markeringen med hjälp av kryssrutorna i en kolumnrubrik.

En mer detaljerad förklaring av den **härledd kolumn efter exempel** transformering, tillsammans med fler exempel finns i [Härled kolumner med exempel referens](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Dela upp kolumn efter exempel
Den här transformeringen tar en befintlig kolumn och genom att använda ”av exempel”-motorn, försöker att dela upp kolumnen i *n* andra kolumner. Du kan köra automatisk-delningen på de efterföljande genererade kolumnerna.

En mer detaljerad förklaring av den **dela kolumner med exempel** transformering, tillsammans med fler exempel finns i [dela upp kolumn efter exempel referens](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Expandera JSON

Den här transformeringen kan du lägga till flera kolumner genom att expandera en kolumn med giltig JSON-text.

En mer detaljerad förklaring av den **Expandera JSON** transformering, tillsammans med fler exempel finns i [Expandera JSON referens](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Kombinera kolumner med exempel

Den här transformeringen lägger till en ny kolumn genom att kombinera värden från flera kolumner. 

En mer detaljerad förklaring av den **kombinera kolumner med exempel** transformering, tillsammans med fler exempel finns i [kombinera kolumner med exempel referens](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Duplicera kolumn
Den här transformeringen gör en exakt kopia av en eller flera valda kolumner och ger varje ett nytt namn härleds från det ursprungliga kolumnnamnet.

## <a name="text-clustering"></a>Text som klustring 
Den här transformeringen har utformats för att ta inkonsekventa värden som ska vara samma och gruppera dem tillsammans.  

Med den här transformeringen är värdena i en enda kolumn analyseras för likheter och grupperade i kluster. För varje kluster finns ett canonical värde, som är det värde som ersätter alla instanser i klustret och alla instansvärden. Fullständig kluster kan tas bort och canonical värdet redigeras. Instanser kan tas bort från ett kluster. Du kan ändra filtret likheter poäng tröskelvärdet som har använt grupp-instanser i ett kluster.

Som standard ersätter den här transformeringen alla värden för kluster-instans med canonical värdet för klustret, skapa en ny kolumn så att den innehåller de nya värdena. Du kan också lägga till likhet poängen för varje instans till en ny kolumn (som kan ha namnet) för användning senare i dataflödet.

## <a name="replace-values"></a>Ersätt värden
Använd den här transformeringen för att ersätta en sträng med ett annat. Källsträngen kan vara en partiell sträng eller en fullständig cell och ersättningen kan tillämpa i en enda kolumn eller många. Strängen har stöd för sökning efter specialtecken samt för vanliga tecken. 

## <a name="replace-na-values"></a>Ersätt NA värden
Använd den här transformeringen för att ersätta de olika formerna av NA (ej tillämpligt, NA, null, NaN osv), eller att ersätta tomma strängar med ett enda värde så att de blir konsekventa. Den här transformeringen har stöd för en eller flera kolumner och visas endast när en kolumn har markerats. Det finns inte på menyn huvudsakliga transformeringen när inga kolumner har valts.

## <a name="replace-missing-values"></a>Ersätt värden som saknas
Den här transformeringen ersätter saknade data med ett enda värde. den stöder en eller flera kolumner. Den här transformeringen visas endast när en kolumn har markerats. Det finns inte på menyn huvudsakliga transformeringen när inga kolumner har valts.

## <a name="replace-error-values"></a>Ersätta felvärdena
Den här transformeringen ersätter fel med ett enda värde. den stöder en eller flera kolumner. Den här transformeringen visas endast när en kolumn har markerats. Det finns inte på menyn huvudsakliga transformeringen när inga kolumner har valts.

## <a name="trim-string"></a>Trim sträng
Den här transformeringen tar bort inledande och avslutande ”” tecken som blanksteg (inklusive blanksteg, tabbar, etc.) från en eller flera kolumner.

## <a name="adjust-precision"></a>Justera Precision
Den här transformeringen anger antalet decimaler för en numerisk kolumn.

## <a name="rename-column"></a>Byt namn på kolumn
Den här transformeringen ändrar namnet på den markerade kolumnen. Du kan också anropa den infogad i kolumnrubriken genom att klicka på namnet på kolumnen.

## <a name="remove-column"></a>Ta bort kolumn
Den här transformeringen tar bort de markerade kolumnerna och den fungerar på en enda kolumn eller många. 

## <a name="keep-column"></a>Behåll kolumn
Den här transformeringen ser till att endast de markerade kolumnerna och den fungerar på en enda kolumn eller många.

## <a name="handle-path-column"></a>Hantera Sökvägskolumnen
Vid import av en fil, en sökvägskolumnen automatiskt läggas till i data av den **Lägg till datakälla** funktionen. Den innehåller det fullständigt kvalificerade filnamnet som utgör sökvägen till datauppsättningen. Den här transformeringen lägger till eller tar bort extra kolumnen från datauppsättningen.

## <a name="convert-field-type-to-numeric"></a>Konvertera fälttyp till numerisk
Den här transformeringen ändrar kolumntypen till numerisk. Du kan ange avgränsare för data som inte är heltal. Som standard inte den här transformeringen efterfrågar avgränsare, så Använd den **redigera** menyalternativet att anropa redigeraren. Den här transformeringen fungerar på en enda kolumn eller många.

## <a name="convert-field-type-to-date"></a>Konvertera fälttyp till datum
Den här transformeringen ändras kolumntypen till datum. Ett standardformat för datum/tid används, men den kan åsidosättas med hjälp av `strftime` direktiv. Du kan också åtkomstgruppen tidsvärden med ett fast datum.

Som standard inte den här transformeringen efterfrågar formatet, så Använd den **redigera** menyobjekt på det resulterande steget för att anropa redigeraren. Den här transformeringen fungerar på en enda kolumn eller många.

Endast datum mellan 9-22-1677 och 4-11-2262 kan konverteras till datatypen datum.

## <a name="convert-field-type-to-boolean"></a>Konvertera fälttyp till booleskt värde
Den här transformeringen ändras kolumntypen till SANT/FALSKT. Den stöder mappa flera värden till true eller false, och du kan redigera dessa mappningar. Det stöder även en konstant som du kan mappa värden som inte finns i tabellerna SANT/FALSKT-mappning. Den här transformeringen fungerar på en enda kolumn eller många.

## <a name="convert-field-type-to-string"></a>Konvertera fälttyp till sträng
Den här transformeringen ändrar kolumntypen till sträng och den fungerar på en enda kolumn eller många.

## <a name="convert-unix-timestamp-to-datetime"></a>Konvertera Unix-tidsstämpel till DateTime
Den här transformeringen förstår Unix timestamp format, även om det representeras som en sträng i data. Den konverterar tidsstämpeln korrekt till en datumdatatyp i förberedelse av data.

## <a name="filter"></a>Filter
Den här transformeringen har stöd för filtrering av rader baserat på värdena i en eller flera kolumner. Villkoren i filtret beror på vilken data för varje kolumn, så att du kan filtrera strängkolumner efter ”innehåller” eller ”innehåller inte”. Numeriska kolumner kan filtreras efter ”större” eller ”mindre än” villkor.

När den **Filter** transformeringen anropas på huvudmenyn eller från att högerklicka på rubriken för en kolumn, alternativet att Förgrena misslyckas rader i en annan dataflöde är tillgängligt. Det huvudsakliga dataflödet fortsätter med filtrerade **i** rader och ett nytt dataflöde skapas som innehåller alla rader som har filtrerats **ut**.

## <a name="use-first-row-as-headers"></a>Använd första raden som rubriker
Den här transformeringen främjar den första raden från datauppsättningen ska vara kolumnnamnen. Om vissa kolumner inte har data i den första raden, genereras ett namn automatiskt. Med den här transformeringen innebär att import av data börjar på rad 2 så snart som möjligt. Beroende på den **hoppa över rader** inställningen kan importen starta ännu längre ned i datauppsättningen. Du kan också använda det på att ta bort befordran och använda endast automatiskt genererade namn.

## <a name="join"></a>Slå ihop
Den här transformeringen används för att ansluta två dataflöden tillsammans. Du kan välja vilka utdata i kopplingen är resultatet: lyckad raderna från kopplingen den ”vänstra” misslyckas rader från kopplingen eller ”höger” misslyckas rader från kopplingen.

Den **Join** transform startar från ett enda dataflöde och väljer det dataflödet som en sida i kopplingen. Sedan uppmanas du att välja en annan dataflödet för den andra sidan av kopplingen. När du har valt de två flödena, krävs en kolumn på varje sida i kopplingen som ska väljas att delta i för transformeringen. Om kopplingen behöver mer än en kolumn, kan du skapa en härledd kolumn innan du startar vi skapa en ny, sammanlänkad kolumn som ska användas endast för kopplingen. Vi försöker att föreslå join nycklar och, om möjligt generera den härledda kolumnen automatiskt.

När kopplingen har slutförts, visas en Sankey diagramvy i kopplingen. Bredden på raderna i förhållande till varandra representerar antalet rader som flytta via den delen av join-flödet. Du kan välja lyckad rader, vänster misslyckas Rader eller höger misslyckas Rader exklusivt med hjälp av panelen till höger. Du kan också välja en enskild gren.

## <a name="append-rows"></a>Lägga till rader
Den här transformeringen lägger till data från en annan dataflödet till den aktuella artikeln. Det mappas kolumner efter position att lägga till nya rader i slutet.

## <a name="append-columns"></a>Lägg till kolumner
Den här transformeringen lägger till nya kolumner från en annan dataflödet till den aktuella artikeln. Det lägger till de nya kolumnerna till höger. Inga försök görs att ordna data i rader.

## <a name="summarize"></a>Sammanfatta
Den här transformeringen beräknar-mängder för en kombination av unika värden i en eller flera av de markerade kolumnerna. 

Aggregeringar som stöds är: antal, SUM, MIN, MAX, medelvärde, varians och standardavvikelsen. Listan med aggregeringar för en viss kolumn filtreras för de mängder som gäller för datatypen. Aggregeringar som inte gäller är inaktiverade. Exempel: Det går inte att beräkna medelvärdet av en strängkolumn, men det är möjligt att beräkna det minsta värdet och det största värdet.

Om det redigeraren dra från kolumnrubriken till panelen längst upp till vänster, där kolumnerna som ska aggregeras visas. Den här panelen är hierarkiska, så du kan göra kapslade mängder. Panelen redigeraren längst upp till höger används för att välja vilken mängd ska tillämpas på en kolumn. En kolumn kan aggregeras en eller flera gånger. När minst en aggregering har valts, förhandsgranskar rutnätet längst ned rätt data i sin sammanställd form. 

Den här transformeringen är detsamma som en `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Ta bort dubbletter
Den här transformeringen tar bort hela raden när det finns duplicerade värden i en eller flera av de markerade kolumnerna. Om inga kolumner väljs är endast raderna tas bort sådana där alla kolumnvärdena är desamma.

## <a name="sort"></a>Sortera
Den här transformeringen sorterar data. Sorteringen kan göras av en enda kolumn eller många och varje kolumn kan sorteras stigande (standard) eller fallande (som kan ändras från redigeringsprogrammet).

Den här transformeringen är detsamma som en `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Utdata-transformeringar
Följande transformeringar utdata. Du kan ha flera write block i ett enda flöde för att skriva data vid olika tidpunkter.

### <a name="write-to-csv"></a>Skriva till CSV
Den här transformeringen skriver ut data i CSV-format från den aktuella punkten i dataflödet. Det styr plats (lokal eller fjärransluten) och olika inställningar runt filen.

### <a name="write-to-parquet"></a>Skriva till Parquet
Den här transformeringen skriver data i Parquet-formatet från den aktuella punkten i dataflödet. Det styr plats (lokal eller fjärransluten) och olika inställningar runt filen.

## <a name="script-based-transforms"></a>Skriptbaserade transformeringar
Följande transformeringar använda skript (Python) för att utföra funktioner som saknas i huvudprodukten. 

>[!NOTE]
>Innan du använder någon av dessa transformeringar läser [med hjälp av Python extensibility](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Lägg till kolumn (skript)
Den här transformeringen lägger till en kolumn till den med ett Python-uttryck.
Mer information finns i [exemplet Python-kod för att få fram nya kolumner](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Avancerat Filter (skript)
Använd den här transformeringen för att skriva ett nivå radfilter för Python.
Mer information finns i [exempel filteruttryck](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transformera dataflöde (skript)
Den här transformeringen gäller Python att hela datauppsättningen.
Mer information finns i [exempel transformera dataflöde transformationer](data-prep-appendix7-sample-transform-data-flow-python.md).

Den här transformeringen kan också använda Python för en partition för alla data.
Mer information finns i [exempel transformera dataflöde transformationer](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Skriva dataflöde (skript)
Den här transformeringen använder Python för att skriva ut en hela datauppsättningen.
Mer information finns i [exemplet Python för att få fram nya kolumner](data-prep-appendix9-sample-destination-connections-python.md).



