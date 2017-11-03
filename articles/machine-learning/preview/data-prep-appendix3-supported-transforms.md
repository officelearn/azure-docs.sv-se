---
title: "Använd data transformeringar för förberedelse av data i Azure Machine Learning | Microsoft Docs"
description: "Den här artikeln innehåller en fullständig lista över tillgängliga för Azure Machine Learning data prep transformationer"
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
ms.openlocfilehash: 4331bb4a16d56f027dd63a97868822fa6ecc92d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Använd data transformeringar för förberedelse av data i Azure Machine Learning

En *transformera* i Azure Machine Learning förbrukar data i ett visst format, utför en åtgärd på data (till exempel ändra datatyp) och genererar data i det nya formatet. Varje transformering har egna gränssnittet och beteende. Du kan länka flera transformeringar tillsammans via steg i dataströmmen så att du kan utföra komplexa och repeterbara omformningar på dina data. Det här är grundläggande funktioner för förberedelse av data.

Följande är en lista över transformeringar tillgängliga i Azure Machine Learning. 

## <a name="column-selection"></a>Kolumnen 
Många av följande listade transformeringar fungerar på en kolumn eller många. Markera flera kolumner med hjälp av **Ctrl** nyckeln, eller markera en uppsättning kolumner genom att använda den **SKIFT** nyckel.

## <a name="transforms-from-main-menu-andor-grid-header"></a>Transformerar från main-menyn och/eller rutnät huvud 
Transformeringar kan nås från alternativet transformeringar på huvudmenyn. Transformeringar kan också väljas genom att högerklicka på kolumnnamnet i datarutnätet. Om flera kolumner har valts, innehåller Högerklicka på någon av dem en transformeringar meny.

Endast erbjuds giltiga transformeringar för den datatyp som valts på snabbmenyn. Huvudmenyn ger alla transformeringar men inaktiverar transformeringar som inte är relevanta för de markerade kolumnerna.

En liten del av kontextuella transformeringar är tillgänglig genom att högerklicka på en cell. Dessa transformeringar är kopia, Ersätt, och filtrera. Dessa transformeringar är medveten om datatypen så alternativ för en kolumn med tal skiljer sig för en strängkolumn.

## <a name="derive-column-by-example"></a>Härledd kolumn efter exempel
Den här transformeringen kan skapas en ny kolumn som ett derivat av en eller flera befintliga kolumner. Transformeringar söker i indatakolumnerna (markerade) och exemplet och anger önskan utdata i den nya kolumnen. 

Välj en eller flera kolumner om du vill använda den här transformeringen. Lägg till en ny (tom) härledd kolumn efter exempel. Skriv ett exempel på vad du vill se i kolumnen härledda (förutsatt att den är härledd från andra kolumner) och ”av exemplet” teknik försöker att fylla i de andra cellerna i kolumnen. 

Komplicerade exempel kan det vara nödvändigt att ange mer än ett exempel. För att göra detta, Välj en annan cell och ange ett annat exempel.

”Med hjälp av exempel”-tekniken använder valda kolumner för att försöka fastställa innebörden av ett exempel. Om inga kolumner har valts när den här transformeringen har anropats, används alla celler för den aktuella raden. Att välja endast de obligatoriska kolumnerna leder till mer korrekta resultat.

Du kan markera kolumner innan du anropar transformeringen. När Redigeraren för transformering har startats kryssrutorna längst upp i varje kolumn som anger vilka kolumner har valts som indata. Du kan lägga till eller ta bort kolumner från markeringen med hjälp av kryssrutorna i en kolumnrubrik.

En mer detaljerad förklaring av **härledd kolumn av exempel** transformeringen tillsammans med flera sampel, se: [härledd kolumn efter exempel referens](data-prep-derive-column-by-example.md)  

## <a name="split-column-by-example"></a>Delad kolumn efter exempel
Den här transformeringen tar en befintlig kolumn och ”med hjälp av exempel”-motorn försöker att dela i kolumnen  *n*  andra kolumner. Det är möjligt att köra Auto-delning på efterföljande genererade kolumner.

En mer detaljerad förklaring av **dela kolumn med hjälp av exempel** transformeringen tillsammans med flera sampel, se: [delad kolumn av exempel referens](data-prep-split-column-by-example.md)

## <a name="expand-json"></a>Expandera JSON

Den här transformeringen kan du lägga till flera kolumner genom att expandera en kolumn med giltig JSON-texten.

En mer detaljerad förklaring av **Expandera JSON** transformeringen tillsammans med flera sampel, se: [Expandera JSON-referens](data-prep-expand-json.md)


## <a name="combine-columns-by-example"></a>Kombinera kolumner efter exempel

Den här transformeringen kan du lägga till en ny kolumn genom att kombinera värden från flera kolumner. 

En mer detaljerad förklaring av **kombinera kolumner efter exempel** transformeringen tillsammans med flera sampel, se: [kombinera kolumner efter exempel referens](data-prep-combine-columns-by-example.md)


## <a name="duplicate-column"></a>Dubbletter av kolumnnamn
Den här transformeringen gör en exakt kopia av en eller flera av de markerade kolumnerna och ger varje ett nytt namn härleds från det ursprungliga kolumnnamnet.

## <a name="text-clustering"></a>Text-kluster 
Den här transformeringen är utformade för att dra inkonsekventa värden som ska vara samma och gruppera dem.  

När du använder den här transformeringen analyseras för likhet värdena i en kolumn och grupperade i kluster. Det finns ett kanoniskt värde, vilket är det värde som ersätter alla instanser i klustret och instansvärden för varje kluster. Fullständig kluster kan tas bort och kanoniskt värde kan redigeras. Instanser kan tas bort från ett kluster. Dessutom kan du ändra filtret likhet poäng tröskelvärdet som har använt till gruppen instanser i ett kluster.

Som standard ersätter den här transformeringen alla värden i klustret-instans med kanoniskt värde för klustret, skapa en ny kolumn som innehåller de nya värdena. Det är också möjligt att lägga till likhet poäng, för varje instans i en ny kolumn (som kan namnges) så att används vid ett senare tillfälle i dataflödet.

## <a name="replace-values"></a>Ersätt värden
Den här transformeringen kan en sträng som ska ersättas med en annan. Källsträngen kan vara en sträng som partiell eller fullständig cellen. ersätter kan tillämpas på en enda kolumn eller många. Strängen har stöd för sökning efter specialtecken samt vanliga tecken. 

## <a name="replace-na-values"></a>Ersätt värden som saknas
Den här transformeringen kan olika olika former av NA (saknas, NA, null eller NaN osv) eller vara tomma strängar som ska ersättas med ett värde så att de blir konsekventa. Den här transformeringen har stöd för en eller flera kolumner. Den här transformeringen visas bara när en kolumn är markerad och finns inte på menyn huvudsakliga Omforma när inga kolumner har valts.

## <a name="replace-missing-values"></a>Ersätt värden som saknas
Den här transformeringen ersätter data som saknas med ett enstaka värde. Den här transformeringen har stöd för en eller flera kolumner. Den här transformeringen visas bara när en kolumn är markerad och finns inte på menyn huvudsakliga Omforma när inga kolumner har valts.

## <a name="replace-error-values"></a>Ersätt felvärdena
Den här transformeringen ersätter fel med ett enstaka värde. Den här transformeringen har stöd för en eller flera kolumner. Den här transformeringen visas bara när en kolumn är markerad och finns inte på menyn huvudsakliga Omforma när inga kolumner har valts.

## <a name="trim-string"></a>Trim sträng
Den här transformeringen tar bort inledande och avslutande ”blanksteg” (innehåller blanksteg, tabbar *etc.*), från en eller flera kolumner.

## <a name="adjust-precision"></a>Justera Precision
Den här transformeringen kan du ange antalet decimaler för en numerisk kolumn.

## <a name="rename-column"></a>Byt namn på kolumnen
Den här transformationen ändrar namnet på den markerade kolumnen. Den här transformeringen kan också vara anropade infogad i kolumnrubriken genom att klicka på namnet på kolumnen.

## <a name="remove-column"></a>Ta bort kolumnen
Den här transformeringen tar bort de markerade kolumnerna. Den här transformeringen fungerar på en kolumn eller många. 

## <a name="keep-column"></a>Behåll kolumnen
Den här transformeringen ser bara de markerade kolumnerna. Den här transformeringen fungerar på en kolumn eller många.

## <a name="handle-path-column"></a>Hantera kolumnen sökväg
Vid import av en fil, sökväg är automatiskt lägga till en kolumn i datauppsättningen av guiden Lägg till datakälla. Den innehåller fullständigt kvalificerade filnamnet som utgör sökvägen till dataset. Den här transformeringen lägger till eller tar bort den extra kolumnen från datamängden.

## <a name="convert-field-type-to-numeric"></a>Omvandla fälttypen till numeriska
Den här transformeringen ändras kolumntypen till numeriska. Du kan ange avgränsaren om data inte är heltal. Den här transformeringen inte frågar för avgränsaren som standard använder den **redigera** menyalternativet att anropa redigeraren. Den här transformeringen fungerar på en kolumn eller många.

## <a name="convert-field-type-to-date"></a>Typ av fält att konvertera till Date
Den här transformeringen ändras kolumntypen till datum. En standardformatet för datum/tid används, men den kan åsidosättas med `strftime` direktiven. Du kan också lägga time-värden med ett fast datum.

Som standard den här transformeringen inte fråga efter formatet som används av **redigera** menyobjekt på det resulterande steget för att anropa redigeraren. Den här transformeringen fungerar på en kolumn eller många.

Endast datum mellan 9-22-1677 och 4-11-2262 kan konverteras till datatypen datum.

## <a name="convert-field-type-to-boolean"></a>Konvertera fälttypen till booleskt värde
Den här transformeringen ändras kolumntypen till SANT/FALSKT. Den här transformeringen har stöd för att mappa flera värden till true eller false och det är möjligt att redigera mappningarna. Det stöder också en konstant som du kan mappa värden som inte finns i tabellerna SANT/FALSKT mappning. Den här transformeringen fungerar på en kolumn eller många.

## <a name="convert-field-type-to-string"></a>Konvertera fälttypen till sträng
Den här transformeringen ändras kolumntypen till sträng. Den här transformeringen fungerar på en kolumn eller många.

## <a name="convert-unix-timestamp-to-datetime"></a>Konvertera Unix tidsstämpel till DateTime
Den här transformeringen förstår Unix tidstämpelformatet även om den representeras som en sträng i data och konverterar den till en datum i data prep korrekt.

## <a name="filter"></a>Filter
Den här transformeringen har stöd för filtrering av raderna baserat på värdena i en eller flera kolumner. Villkoren i filtret är beroende av datatypen för varje kolumn, gör det möjligt att filtrera sträng kolumner med ”innehåller” eller ”innehåller inte”. Numeriska kolumner kan filtreras efter ”större än” ”mindre än” villkor.

När filter transformeringen anropas från huvudmenyn och högerklicka på rubriken för en kolumn, finns alternativet att duplicera misslyckas rader i en annan dataflöde. Fortsätter det huvudsakliga dataflödet med filtrerade **i** rader och ett nytt dataflöde skapas som innehåller de rader som har filtrerats **ut**.

## <a name="use-first-row-as-headers"></a>Använd första raden som rubriker
Den här transformeringen flyttar upp den första raden i den datamängd som ska vara kolumnnamnen. Om vissa kolumner inte har data på den första raden, sedan genereras ett namn automatiskt. Med den här transformeringen innebär att importen av data börjar på rad 2 så fort som möjligt. Beroende på inställningen hoppa över rader startar importen även längre ned i datauppsättningen. Den kan också användas för att ta bort befordran och endast automatiskt genererade namn.

## <a name="join"></a>Slå ihop
Den här transformeringen används för att ansluta två dataflöden tillsammans. Du kan välja vilka utdata för anslutning till ska vara resultatet: lyckad rader från kopplingen ”vänster” misslyckas rader från kopplingen eller ”höger” misslyckas rader från kopplingen.

Guiden koppling startas från ett enskilt dataflöde och väljer att dataflödet som en sida i kopplingen. Sedan ombeds du ange en annan dataflödet för den andra sidan av kopplingen. När du har valt de två flödena kräver en enda kolumn på varje sida av gemensamma väljas att delta i guiden. Om kopplingen måste mer än en kolumn, skapar du en kolumn innan du startar guiden för att skapa en ny (sammanfogad) kolumn som ska användas för anslutning till. Guiden försöker föreslår koppling nycklar och generera om möjligt den härledda kolumnen automatiskt.

När kopplingen har slutförts, visas en Sankey diagramvy för anslutning till. Tjockleken på linjerna i förhållande till varandra representerar antalet rader som flytta via den del av join-flödet. Panelen till höger kan du markera lyckade rader, misslyckas vänster eller höger misslyckas exklusivt. Det är också möjligt att välja endast en gren.

## <a name="append-rows"></a>Lägg till rader
Den här transformeringen lägger till data från en annan dataflöde till den aktuella processen. Den mappar kolumner med möjlighet att lägga till nya rader i slutet.

## <a name="append-columns"></a>Lägga till kolumner
Den här transformeringen lägger till nya kolumner från en annan dataflöde till den aktuella. Nya kolumner läggs till höger. den försöka inte ordna data i rader.

## <a name="summarize"></a>Sammanfatta
Den här transformeringen beräknar mängder för kombinationen av unika värden i en eller flera av de markerade kolumnerna. 

Aggregeringar som stöds är: antal, SUM, MIN, MAX, medelvärde, varians och standardavvikelsen. Listan med mängder för en viss kolumn har filtrerats till endast de som gäller för datatypen. Mängder som inte gäller inaktiveras. T.ex, det går inte att beräkna medelvärdet för en strängkolumn, men det är möjligt att beräkna min och max.

När Redigeraren för är tillgänglig, dra från kolumnrubriken till panelen på upp till vänster. där visas kolumnerna som ska aggregeras. Den här panelen är hierarkisk så att det är möjligt att göra kapslade mängder. Panelen editor längst upp till höger används för att välja vilken mängd ska gälla för en kolumn. En kolumn kan sammanställas till en eller flera gånger. När minst en mängd har valts kommer förhandsgranskar data i sin sammanställd form i rutnätet i nederkant högra hörnet. 

Den här transformeringen är detsamma som en `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Ta bort dubbletter
Den här transformeringen tar du bort hela raden där det finns dubblettvärden i en eller flera markerade kolumnerna. Om inga kolumner är valt, och sedan endast rader som tas bort är sådana där kolumnvärdena är samma.

## <a name="sort"></a>Sortera
Den här transformeringen sorterar data. Sorteringen kan göras av en enda kolumn eller många och varje kolumn kan sorteras i stigande (standard) eller fallande (kan ändras från redigeraren).

Den här transformeringen är detsamma som en `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Utgående transformeringar
Följande transformeringar utdata. Det är möjligt att ha flera write block i ett enda flöde för att kunna skriva ut data vid olika tidpunkter.

### <a name="write-to-csv"></a>Skriva till CSV
Den här transformeringen skriver data i CSV-formatet från den aktuella punkten i dataflödet. Kontroll av platsen (lokala eller fjärranslutna) och olika inställningar kan runt filen.

### <a name="write-to-parquet"></a>Skriva till parkettgolv
Den här transformeringen skriver data i formuläret parkettgolv från den aktuella punkten i dataflödet. Kontroll av platsen (lokala eller fjärranslutna) och olika inställningar kan runt filen.

## <a name="script-based-transforms"></a>Skriptbaserade transformeringar
Följande transformeringar använda skript (Python) för att utföra funktioner som saknas i huvudprodukten. Innan du använder någon av dessa transformeringar läsa [med Python utökningsbarhet](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Lägg till kolumn (skript)
Den här transformeringen kan en kolumn som ska läggas till data med en Python-uttryck.
Mer information finns i [exempel Python-kod för att få fram nya kolumner](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Avancerade Filter (skript)
Den här transformeringen kan en Python nivån radfilter ska skrivas.
Mer information finns i [exempel filteruttryck](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transformera dataflöde (skript)
Den här transformeringen kan Python ska tillämpas på hela datauppsättningen.
Mer information finns i [exempel transformeringen dataflöde transformationer](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="transform-dataflow-script"></a>Transformera dataflöde (skript)
Den här transformeringen kan Python ska tillämpas på en datapartition i hela.
Mer information finns i [exempel transformeringen dataflöde transformationer](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Skriva dataflöde (skript)
Den här transformeringen kan Python ska användas för att skriva och hela datauppsättningen.
Mer information finns i [exempel Python för härledning av nya kolumner](data-prep-appendix9-sample-destination-connections-python.md).



