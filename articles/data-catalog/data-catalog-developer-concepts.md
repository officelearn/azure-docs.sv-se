---
title: Begrepp för utvecklare för Azure Data Catalog
description: Introduktion till viktiga begrepp i Azure Data Catalog konceptuell modell som exponerade via REST-API-katalogen.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 3cfd6bd453cd06be4676a806997697a71afb0b59
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727407"
---
# <a name="azure-data-catalog-developer-concepts"></a>Begrepp för utvecklare för Azure Data Catalog
Microsoft **Azure Data Catalog** är en fullständigt hanterad molntjänst som tillhandahåller funktioner för datakällsidentifiering och gemensamt skapade metadata från datakällan. Utvecklare kan använda tjänsten via dess REST-API: er. Förstå koncepten som är implementerade i tjänsten är viktigt för utvecklare att integrera har med **Azure Data Catalog**.

## <a name="key-concepts"></a>Viktiga begrepp
Den **Azure Data Catalog** konceptuell modell som baseras på fyra viktiga begrepp: Den **Catalog**, **användare**, **tillgångar**, och **anteckningar**.

![begrepp][1]

*Bild 1 – Azure Data Catalog förenklad konceptuell modell*

### <a name="catalog"></a>Katalog
En **Catalog** är behållaren på översta nivån för alla metadata som lagras i en organisation. Det finns en sådan **Catalog** tillåts per Azure-konto. Kataloger är knutna till en Azure-prenumeration, men endast en **Catalog** kan skapas för alla angivna Azure-konto, även om ett konto kan ha flera prenumerationer.

En katalog innehåller **användare** och **tillgångar**.

### <a name="users"></a>Användare
Användarna är säkerhetsobjekt som har behörighet att utföra åtgärder (söka i katalogen, lägga till, redigera eller ta bort objekt och så vidare) i katalogen.

Det finns flera olika roller som en användare kan ha. Information om roller finns i avsnittet roller och auktorisering.

Enskilda användare och säkerhetsgrupper kan läggas till.

Azure Data Catalog använder Azure Active Directory för identitets- och åtkomsthantering. Varje Catalog-användare måste vara medlem i Active Directory för kontot.

### <a name="assets"></a>Tillgångar
En **Catalog** innehåller datatillgångar. **Tillgångar** är enheterna för kornighet som hanteras av katalogen.

Granulariteten för en tillgång varierar beroende på datakällan. För SQL Server eller Oracle Database, kan en tillgång vara en tabell eller en vy. En tillgång kan vara ett mått, en Dimension eller en indikator KPI (Key Performance) för SQL Server Analysis Services. För SQL Server Reporting Services är en rapport i en tillgång.

En **tillgången** är det som du lägger till eller ta bort från en katalog. Det är Faktureringsenhet resultat som du kan få från **Search**.

En **tillgången** består från dess namn, plats, och typ och anteckningar som beskriver den ytterligare.

### <a name="annotations"></a>anteckningar
Anteckningar finns objekt som representerar metadata om tillgångar.

Exempel på anteckningar är beskrivning, taggar, schema, dokumentation, osv. En fullständig lista över resurstyper och anteckningstyper finns i tillgången objektet modell.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Gemensamt skapade anteckningar och användarperspektiv (multipliciteten för åsikt)
En viktig aspekt av Azure Data Catalog är hur den stöder gemensamt skapade metadata i systemet. I motsats till en wiki-metod – där det finns bara en åsikter och de senaste skrivaren wins – Azure Data Catalog-modellen kan flera yttranden att finnas sida vid sida i systemet.

Den här metoden visar den verkliga världen av företagsdata där olika användare kan ha olika perspektiv på en given tillgång:

* En databasadministratör kan innehålla information om serviceavtal eller fönstret tillgängliga bearbetning för ETL massåtgärder
* En data-steward kan innehålla information om affärsprocesser som gäller för tillgången eller de klassificeringar som företaget har tillämpas
* En ekonomi-analytiker kan ge information om hur data används vid slutet av perioden rapportuppgifter

För att stödja det här exemplet, kan varje användare – DBA, data-steward och analytikern – lägga till en beskrivning för en tabell som har registrerats i katalogen. Alla beskrivningar bevaras i systemet och alla beskrivningar visas i Azure Data Catalog-portalen.

Det här mönstret tillämpas på de flesta objekt i objektmodellen, så att objekttyper i JSON-nyttolasten är ofta matriser för egenskaper där du kan förvänta dig en singleton.

Under tillgången är roten till exempel en matris med objekt beskrivning. Egenskapen matris har namnet ”beskrivning”. En beskrivning av-objektet har en egenskap - Beskrivning. Mönstret är att varje användare som beskrivning av typer hämtar en beskrivning av objektet som skapats för det värdet som anges av användaren.

UX-Gränssnittet kan sedan välja hur du vill visa kombinationen. Det finns tre olika mönster för visning.

* Den enklaste mönstret är ”visa alla”. I det här mönstret visas alla objekt i en listvy. Azure Data Catalog-portalen UX använder det här mönstret beskrivning.
* En annan mönstret är ”sammanfoga”. I det här mönstret samman alla värden från olika användare tillsammans med dubblett tas bort. Exempel på det här mönstret i Azure Data Catalog-portalen UX är egenskaperna taggar och experter.
* Ett tredje mönster är ”senaste skrivaren vinner”. I det här mönstret visas bara det senaste värdet som har skrivit in. friendlyName är ett exempel på det här mönstret.

## <a name="asset-object-model"></a>Objektmodell för tillgången
Som det introducerats i avsnittet nyckelkoncept i **Azure Data Catalog** objektmodellen innehåller objekt som kan vara tillgångar eller kommentarer. Objekt har egenskaper som kan vara valfritt eller krävs. Vissa egenskaper gäller för alla objekt. Vissa egenskaper gäller för alla tillgångar. Vissa egenskaper gäller endast för specifika resurstyper.

### <a name="system-properties"></a>Systemegenskaper
<table><tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr><tr><td>tidsstämpel</td><td>DateTime</td><td>Den senaste gången objektet har ändrats. Det här fältet genereras av servern när ett objekt har infogats och varje gång ett objekt uppdateras. Värdet för den här egenskapen ignoreras på indata av åtgärder.</td></tr><tr><td>id</td><td>URI</td><td>Absolut url för objektet (skrivskyddad). Det är unikt adresserbara URI: N för objektet.  Värdet för den här egenskapen ignoreras på indata av åtgärder.</td></tr><tr><td>typ</td><td>String</td><td>Typ av tillgången (skrivskyddad).</td></tr><tr><td>etag</td><td>String</td><td>En sträng som motsvarar versionen av det objekt som kan användas för optimistisk samtidighetskontroll när du utför åtgärder som uppdaterar objekt i katalogen. ”*” kan användas för att matcha alla värden.</td></tr></table>

### <a name="common-properties"></a>Gemensamma egenskaper
Dessa egenskaper gäller för alla typer av roten tillgång och alla anteckningstyper av.

<table>
<tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>
<tr><td>fromSourceSystem</td><td>Boolean</td><td>Anger om objektets data är härledd från ett källsystem (till exempel Sql Server-databas, Oracle Database) eller som användaren.</td></tr>
</table>

### <a name="common-root-properties"></a>Allmänna egenskaper för rot
<p>
Dessa egenskaper gäller för alla typer av roten tillgången.

<table><tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr><tr><td>namn</td><td>String</td><td>Ett namn som härleds från informationen om datakällan plats</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>Unikt beskriver datakällan och är en identifierare för tillgången. (Se dubbla identitetsavsnittet).  Strukturen för dsl varierar beroende på vilken typ av protokoll och källa.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Mer information om typ av tillgång.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Beskriver den användare som nyligen registrerade den här tillgången.  Innehåller både unikt id för användaren (upn) och ett visningsnamn (lastName och firstName).</td></tr><tr><td>containerId</td><td>String</td><td>ID för behållaren tillgången för datakällan. Den här egenskapen stöds inte för typ av behållare.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Allmänna anteckningsegenskaper för icke-singleton
Dessa egenskaper gäller för alla anteckningstyper av icke-singleton (anteckningar, som kan vara flera per tillgång).

<table>
<tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>
<tr><td>key</td><td>String</td><td>Ett användardefinierat nyckeln, som unikt identifierar anteckningen i samlingen. Nyckellängden får inte överskrida 256 tecken.</td></tr>
</table>

### <a name="root-asset-types"></a>Rot-resurstyper
Rot-resurstyper finns dessa typer som representerar de olika typerna av datatillgångar som kan registreras i katalogen. För varje typ av roten finns en vy som beskriver tillgången och anteckningar som ingår i vyn. Vynamn ska användas i motsvarande {view_name} url-segmentet när du publicerar en tillgång med REST API.

<table><tr><td><b>Typ av tillgång (namn)</b></td><td><b>Ytterligare egenskaper</b></td><td><b>Datatyp</b></td><td><b>Tillåtna anteckningar</b></td><td><b>Kommentarer</b></td></tr><tr><td>Tabell (”tabeller”)</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagga<p>Schema<p>ColumnDescription<p>ColumnTag<p> Expert<p>Förhandsversion<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentation<p></td><td>En tabell representerar inga tabelldata.  Exempel: SQL-tabell, SQL-vy, Analysis Services Tabular-tabell, Analysis Services-flerdimensionella dimensions-, Oracle-tabell, osv.   </td></tr><tr><td>Mått (”mått”)</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagga<p>Expert<p>AccessInstruction<p>Dokumentation<p></td><td>Den här typen representerar ett Analysis Services-mått.</td></tr><tr><td></td><td>mått</td><td>Kolumn</td><td></td><td>Metadata som beskriver måttet</td></tr><tr><td></td><td>isCalculated </td><td>Boolean</td><td></td><td>Anger om måttet beräknas eller inte.</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>Fysiska behållare för mått</td></tr><td>KPI (”KPI: er”)</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagga<p>Expert<p>AccessInstruction<p>Dokumentation</td><td></td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>Fysiska behållare för mått</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td></td><td>Ett numeriskt MDX-uttryck eller en beräkning som returnerar målvärdet för KPI.</td></tr><tr><td></td><td>valueExpression</td><td>String</td><td></td><td>Ett MDX-numeriska uttryck som returnerar det faktiska värdet för KPI.</td></tr><tr><td></td><td>statusExpression</td><td>String</td><td></td><td>Ett MDX-uttryck som representerar tillståndet för KPI: N en viss tidpunkt.</td></tr><tr><td></td><td>trendExpression</td><td>String</td><td></td><td>Ett MDX-uttryck som utvärderas värdet för KPI över tid. Trend kan vara något tidsbaserade kriterium som är användbar för en specifik affärskontexten.</td>
<tr><td>Rapporten (”rapporter”)</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagga<p>Expert<p>AccessInstruction<p>Dokumentation<p></td><td>Den här typen representerar en SQL Server Reporting Services-rapport </td></tr><tr><td></td><td>assetCreatedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>String</td><td></td><td></td></tr><tr><td>Behållare (”behållare”)</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagga<p>Expert<p>AccessInstruction<p>Dokumentation<p></td><td>Den här typen representerar en behållare för andra tillgångar som till exempel en SQL-databas, en Azure BLOB-behållare eller en Analysis Services-modell.</td></tr></table>

### <a name="annotation-types"></a>Anteckningens typer
Anteckningens typer representerar typer av metadata som kan tilldelas till andra typer i katalogen.

<table>
<tr><td><b>Anteckningstypen (kapslade vynamn)</b></td><td><b>Ytterligare egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>

<tr><td>Beskrivning (”beskrivningar”)</td><td></td><td></td><td>Den här egenskapen innehåller en beskrivning för en tillgång. Varje användare av systemet kan lägga till egna beskrivning.  Bara denna användaren kan redigera objektet beskrivning.  (Administratörer och tillgången ägare kan ta bort objektet beskrivning, men inte redigera den). Systemet har användarnas beskrivningar separat.  Det finns därför en matris med beskrivningar för varje tillgång (en för varje användare som har bidragit med sina kunskaper om tillgången, förutom eventuellt en som innehåller information som härleds från datakällan).</td></tr>
<tr><td></td><td>description</td><td>string</td><td>En kort beskrivning (2-3 rader) av tillgången</td></tr>

<tr><td>Taggen (”taggar”)</td><td></td><td></td><td>Den här egenskapen definierar en tagg för en tillgång. Varje användare av systemet kan lägga till flera taggar för en tillgång.  Endast den användare som skapade taggen objekt kan redigera dem.  (I administratörer och tillgången ägare kan det ta bort tagg-objektet men inte redigera den). Systemet har användarnas taggar separat.  Det finns därför en matris med taggen objekt på varje plats.</td></tr>
<tr><td></td><td>tagg</td><td>string</td><td>En tagg som beskriver tillgången.</td></tr>

<tr><td>FriendlyName (”friendlyName”)</td><td></td><td></td><td>Den här egenskapen innehåller ett eget namn för en tillgång. FriendlyName är en singleton-anteckning - endast en FriendlyName kan läggas till en tillgång.  Endast den användare som skapade FriendlyName objekt kan redigera den. (I administratörer och tillgången ägare kan det ta bort FriendlyName objektet men inte redigera den). Systemet har separat användarnas egna namn.</td></tr>
<tr><td></td><td>friendlyName</td><td>string</td><td>Ett eget namn för tillgången.</td></tr>

<tr><td>Schemat (”schema”)</td><td></td><td></td><td>Schemat beskriver strukturen för data.  Den visar en lista över attributnamn (kolumn, attribut, fält, etc.), typer och andra metadata.  Den här informationen hämtas från datakällan.  Schemat är en singleton-anteckning – endast ett Schema kan läggas till för en tillgång.</td></tr>
<tr><td></td><td>Kolumner</td><td>Column[]</td><td>En matris med objekt för kolumnen. De beskriver kolumnen med information som härleds från datakällan.</td></tr>

<tr><td>ColumnDescription (”columnDescriptions”)</td><td></td><td></td><td>Den här egenskapen innehåller en beskrivning för en kolumn.  Varje användare av systemet kan lägga till egna beskrivningar för flera kolumner (mest en per kolumn). Endast den användare som skapade ColumnDescription objekt kan redigera dem.  (I administratörer och tillgången ägare kan det ta bort ColumnDescription objektet men inte redigera den). Systemet har dessa användare kolumnbeskrivningar separat.  Det finns därför en matris med ColumnDescription objekt för varje tillgång (en per kolumn för varje användare som har bidragit med sina kunskaper om kolumnen utöver eventuellt en som innehåller information som härleds från datakällan).  ColumnDescription är löst kopplad till schemat så att det kan bli osynkroniserad. ColumnDescription kan beskriva en kolumn som inte längre finns i schemat.  Det är upp till skrivaren kan synkronisera beskrivning och schema.  Datakällan kan också ha kolumner beskrivningsinformation och de är ytterligare ColumnDescription-objekt som skapas när du kör verktyget.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>Namnet på den här beskrivningen refererar till kolumnen.</td></tr>
<tr><td></td><td>description</td><td>String</td><td>en kort beskrivning (2-3 rader) i kolumnen.</td></tr>

<tr><td>ColumnTag (”columnTags”)</td><td></td><td></td><td>Den här egenskapen innehåller en tagg för en kolumn. Varje användare av systemet kan lägga till flera taggar för en viss kolumn och kan lägga till taggar för flera kolumner. Endast den användare som skapade ColumnTag objekt kan redigera dem. (I administratörer och tillgången ägare kan det ta bort ColumnTag objektet men inte redigera den). Systemet har dessa användare kolumnen taggar separat.  Det finns därför en matris med ColumnTag objekt på varje plats.  ColumnTag är löst kopplad till schemat så att det kan bli osynkroniserad. ColumnTag kan beskriva en kolumn som inte längre finns i schemat.  Det är upp till skrivaren kan synkronisera kolumnen tagg och schema.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>Namnet på den kolumn som den här taggen hänvisar till.</td></tr>
<tr><td></td><td>tagg</td><td>String</td><td>En tagg som beskriver kolumnen.</td></tr>

<tr><td>Expert (”experter”)</td><td></td><td></td><td>Den här egenskapen innehåller en användare som anses vara expert på datauppsättningen. Experternas opinions(descriptions) bubbla längst upp i UX-Gränssnittet när beskrivningar. Varje användare kan ange sina egna experter. Bara denna användaren kan redigera objektet experter. (I administratörer och tillgången ägare kan det ta bort experten objekt men inte redigera den).</td></tr>
<tr><td></td><td>expert</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Förhandsversion (”förhandsversioner”)</td><td></td><td></td><td>Förhandsversionen innehåller en ögonblicksbild av de främsta 20 datarader för tillgången. Förhandsversion är bara användbar för vissa typer av tillgångar som (det vara bra för tabell men inte för mått).</td></tr>
<tr><td></td><td>förhandsversion</td><td>object[]</td><td>Matris med objekt som representerar en kolumn.  Varje objekt har en egenskapsmappning till en kolumn med ett värde för kolumnen för raden.</td></tr>

<tr><td>AccessInstruction (”accessInstructions”)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>Mime-typ av innehållet.</td></tr>
<tr><td></td><td>innehåll</td><td>string</td><td>Anvisningar för hur du får åtkomst till den här datatillgången. Innehållet kan vara en URL, en e-postadress eller en uppsättning instruktioner.</td></tr>

<tr><td>TableDataProfile (”tableDataProfiles”)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Antalet rader i datauppsättningen</td></tr>
<tr><td></td><td>storlek</td><td>lång</td><td>Storlek i byte för datauppsättningen.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>string</td><td>Schemat ändrades senast</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>string</td><td>Datauppsättningen ändrades senast (data har lagts till, ändras, eller ta bort)</td></tr>

<tr><td>ColumnsDataProfile (”columnsDataProfiles”)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Kolumner</td></td><td>ColumnDataProfile[]</td><td>En matris med kolumnen data profiler.</td></tr>

<tr><td>ColumnDataClassification (”columnDataClassifications”)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>Namnet på den här klassificeringen refererar till kolumnen.</td></tr>
<tr><td></td><td>klassificering</td><td>String</td><td>Klassificering av data i den här kolumnen.</td></tr>

<tr><td>Dokumentation (”dokumentation”)</td><td></td><td></td><td>En given tillgång kan ha endast en dokumentation som är kopplade till den.</td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>Mime-typ av innehållet.</td></tr>
<tr><td></td><td>innehåll</td><td>string</td><td>Documentation-innehållet.</td></tr>

</table>

### <a name="common-types"></a>Vanliga typer
Vanliga typer kan användas som typerna för egenskaper, men det finns inga objekt.

<table>
<tr><td><b>Gemensam typ.</b></td><td><b>Egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>string</td><td>Beskriver typ av datakälla.  Exempel: SQL Server, Oracle Database, etc.  </td></tr>
<tr><td></td><td>Objekttyp</td><td>string</td><td>Beskriver typ av objekt i datakällan. Exempel: Tabell, vy för SQLServer.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protokoll</td><td>string</td><td>Krävs. Beskriver ett protokoll som används för att kommunicera med datakällan. Till exempel: ”tds” för SQl Server, ”oracle” för Oracle, osv. Referera till <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">datakällans referens-specifikationen - DSL struktur</a> lista över protokoll som stöds.</td></tr>
<tr><td></td><td>adress</td><td>Ordlista&lt;sträng, objekt&gt;</td><td>Krävs. Adressen är en uppsättning data som är specifika för det protokoll som används för att identifiera datakällan som refereras. Dessa data omfattar ett visst protokoll, vilket innebär att det är meningslöst utan att känna till protokollet.</td></tr>
<tr><td></td><td>autentisering</td><td>string</td><td>Valfri. Schema för autentiseringsmetoder som används för att kommunicera med datakällan. Till exempel: windows, oauth, osv.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Ordlista&lt;sträng, objekt&gt;</td><td>Valfri. Mer information om hur du ansluter till en datakälla.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Serverdelen utför inte någon validering av angivna egenskaper mot AAD vid publicering.</td></tr>
<tr><td></td><td>UPN</td><td>string</td><td>Unikt e-postadressen för användaren. Du måste ange om objectId inte har angetts eller i samband med ”lastRegisteredBy”-egenskapen, annars valfritt.</td></tr>
<tr><td></td><td>objekt-ID</td><td>Guid</td><td>Användare eller säkerhetsgrupp grupp AAD-identitet. Valfri. Du måste ange om upn inte anges, annars valfritt.</td></tr>
<tr><td></td><td>firstName</td><td>string</td><td>Förnamn för användare (i visningssyfte). Valfri. Endast giltigt i samband med ”lastRegisteredBy”-egenskap. Kan inte anges när du anger säkerhetsobjektet för ”roller”, ”behörigheter” och ”experter”.</td></tr>
<tr><td></td><td>lastName</td><td>string</td><td>Efternamn för användaren (för visning). Valfri. Endast giltigt i samband med ”lastRegisteredBy”-egenskap. Kan inte anges när du anger säkerhetsobjektet för ”roller”, ”behörigheter” och ”experter”.</td></tr>

<tr><td>Kolumn</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namn</td><td>string</td><td>Namn på kolumnen eller attribut.</td></tr>
<tr><td></td><td>typ</td><td>string</td><td>datatypen för kolumnen eller attribut. De tillåtna typerna är beroende av data sourceType av tillgången.  Endast en delmängd av typer stöds.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>Den tillåtna maxlängden för kolumnen eller attribut. Hämtad från datakällan. Gäller endast för vissa typer av datakällor.</td></tr>
<tr><td></td><td>precision</td><td>byte</td><td>Precisionen för kolumnen eller attribut. Hämtad från datakällan. Gäller endast för vissa typer av datakällor.</td></tr>
<tr><td></td><td>isNullable</td><td>Boolean</td><td>Om kolumnen får ha ett null-värde eller inte. Hämtad från datakällan. Gäller endast för vissa typer av datakällor.</td></tr>
<tr><td></td><td>uttryck</td><td>string</td><td>Om värdet är en beräknad kolumn, innehåller uttryck som representerar värdet. Hämtad från datakällan. Gäller endast för vissa typer av datakällor.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>string</td><td>Namnet på kolumnen</td></tr>
<tr><td></td><td>typ </td><td>string</td><td>Typ av kolumn</td></tr>
<tr><td></td><td>min. </td><td>string</td><td>Det minsta värdet i datauppsättningen</td></tr>
<tr><td></td><td>max </td><td>string</td><td>Det maximala värdet i datauppsättningen</td></tr>
<tr><td></td><td>medel </td><td>double</td><td>Genomsnittligt värde i datauppsättningen</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>Standardavvikelse för datauppsättningen</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Antal null-värden i datauppsättningen</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Antal distinkta värden i datauppsättningen</td></tr>


</table>

## <a name="asset-identity"></a>Tillgången identitet
Azure Data Catalog använder ”protokoll” och identitet egenskaper från egenskapsuppsättningen ”adress” för egenskapen DataSourceLocation ”dsl” för att generera identiteten för den tillgång som används för att adressera tillgång i katalogen.
Till exempel har ”tds”-protokollet identitet egenskaper ”server”, ”databas”, ”schema” och ”objekt”. Kombinationer av protokollet och identitetsegenskaperna används för att generera identiteten för SQL Server-tabell tillgången.
Azure Data Catalog innehåller flera inbyggda datakällprotokoll, vilket visas i [datakällans referens-specifikationen - DSL struktur](data-catalog-dsr.md).
Uppsättningen protokoll som stöds kan utökas programmässigt (finns i Data Catalog REST API-referens). Administratörer av katalogen kan registrera anpassade datakällprotokoll. I följande tabell beskriver de egenskaper som behövs för att registrera ett anpassat protokoll.

### <a name="custom-data-source-protocol-specification"></a>Anpassade protokollspecifikation för datakälla
<table>
<tr><td><b>Typ</b></td><td><b>Egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namnområde</td><td>string</td><td>Namnområdet för protokollet. Namespace måste vara mellan 1 och 255 tecken långt, innehåller en eller flera icke-tomma delar avgränsade med punkt (.). Varje del måste vara mellan 1 och 255 tecken långt, börja med en bokstav och endast innehålla bokstäver och siffror.</td></tr>
<tr><td></td><td>namn</td><td>string</td><td>Namnet på protokollet. Namnet måste vara mellan 1 och 255 tecken långt, börja med en bokstav och innehålla endast bokstäver, siffror och bindestreck (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Lista över identitetsegenskaper, måste innehålla minst en, men inga fler än 20 egenskaper. Till exempel: ”server”, ”databas”, ”schema”, ”objekt” är identitetsegenskaper av ”tds”-protokollet.</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Lista över identitetsuppsättningar. Definierar uppsättningar identitetsegenskaper som representerar giltig tillgång identitet. Måste innehålla minst en, men inga fler än 20 uppsättningar. Till exempel: {”server”, ”databas”, ”schema” och ”objekt”} är en identitet som angetts för ”tds”-protokollet som definierar identiteten för Sql Server-tabell tillgången.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namn</td><td>string</td><td>Namnet på egenskapen. Namnet måste vara mellan 1 och 100 tecken långt, börja med en bokstav och får bara innehålla bokstäver och siffror.</td></tr>
<tr><td></td><td>typ</td><td>string</td><td>Typ av egenskapen. Värden som stöds: ”bool”, boolean ”,” byte ”,” guid ”,” int ”,” heltal ”,” länge ”,” string ”,” url ”</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Anger om fall ska ignoreras när du använder egenskapens värde. Kan bara anges för egenskaper med typen ”string”. Standardvärdet är FALSKT.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>Anger om fall ska ignoreras för varje segment i den url-sökväg. Kan bara anges för egenskaper med typen ”url”. Standardvärdet är [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namn</td><td>string</td><td>Ange namnet på identiteten.</td></tr>
<tr><td></td><td>properties</td><td>String]</td><td>Listan över identitetsegenskaper som ingår i den här identiteten anges. Det får inte innehålla dubbletter. Varje egenskap som refereras av identitetsuppsättningen måste definieras i listan över ”identityProperties” av protokollet.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Roller och auktorisering
Microsoft Azure Data Catalog innehåller funktioner för auktorisering för CRUD-åtgärder på resurser och anteckningar.

## <a name="key-concepts"></a>Viktiga begrepp
Azure Data Catalog använder två auktoriseringsmekanismer:

* Rollbaserad auktorisering
* Tillstånd-baserad auktorisering

### <a name="roles"></a>Roller
Det finns tre roller: **Administratören**, **ägare**, och **deltagare**.  Varje roll har sitt omfång och rättigheter, som sammanfattas i tabellen nedan.

<table><tr><td><b>Roll</b></td><td><b>Omfång</b></td><td><b>Rättigheter</b></td></tr><tr><td>Administratör</td><td>Katalog (alla tillgångar/anteckningar i katalogen)</td><td>Läs Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Ägare</td><td>Varje tillgång (rotobjekt)</td><td>Läs Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Deltagare</td><td>Varje enskild tillgång och anteckning</td><td>Läs Update Delete ViewRoles Obs: alla rättigheter som har återkallats, Läs direkt på objektet har återkallats från deltagaren</td></tr></table>

> [!NOTE]
> **Läs**, **uppdatering**, **ta bort**, **ViewRoles** rättigheter kan användas för alla objekt (tillgång eller anteckning) vid **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** gäller endast för rot-tillgången.
> 
> **Ta bort** rättigheten för ett objekt och alla underobjekt eller enskilda objekt under den. Till exempel bort tar en tillgång även alla anteckningar för tillgången.
> 
> 

### <a name="permissions"></a>Behörigheter
Behörigheten är som lista med poster för åtkomstkontroll. Varje åtkomstkontrollpost tilldelar uppsättning rättigheter till ett säkerhetsobjekt. Behörigheter kan bara anges för en tillgång (det vill säga rotobjekt) och gäller för tillgången och eventuella undermappar.

Under den **Azure Data Catalog** förhandsgranska endast **Läs** höger stöds i listan med behörigheter att aktivera scenariot att begränsa synligheten för en tillgång.

Alla autentiserade användare har som standard **Läs** direkt för alla objekt i katalogen, såvida inte synlighet är begränsad till vilken uppsättning av säkerhetsobjekt i hur behörigheterna.

## <a name="rest-api"></a>REST-API
**PLACERA** och **POST** visa objekt-begäranden kan användas till att styra roller och behörigheter: förutom objekt nyttolast två Systemegenskaper kan anges **roller** och  **behörigheter**.

> [!NOTE]
> **behörigheter** gäller endast för ett rotobjekt.
> 
> **Ägare** rollen gäller endast för ett rotobjekt.
> 
> Som standard när ett objekt skapas i katalogen dess **deltagare** är inställd på den för närvarande autentiserade användaren. Om objektet ska uppdateras av alla, **deltagare** ska vara inställd på &lt;alla&gt; särskilda säkerhetsobjekt i den **roller** egenskapen när objektet först publiceras (se informationen i följande exempel). **Deltagare** kan inte ändras och förblir densamma under livslängden för ett objekt (även **administratör** eller **ägare** inte har behörighet att ändra den **deltagare**). Det enda värde som stöds för den explicita inställningen av den **deltagare** är &lt;alla&gt;: **Deltagare** kan bara vara en användare som skapade ett objekt eller &lt;alla&gt;.
> 
> 

### <a name="examples"></a>Exempel
**Ange deltagare &lt;alla&gt; när du publicerar ett objekt.**
Särskilda säkerhetsobjekt &lt;alla&gt; har objectId ”00000000-0000-0000-0000-000000000201”.
  **POST** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Vissa http-klient-implementeringar kan automatiskt återutfärda begäranden som svar på en 302 från servern, men vanligtvis remsans auktorisering rubriker i begäran. Eftersom auktoriseringsrubriken krävs för att göra begäranden till Azure Data Catalog, måste du kontrollera auktoriseringsrubriken tillhandahålls fortfarande när återutfärda en begäran till en omdirigerings-plats som anges av Azure Data Catalog. Följande exempelkod visar den med hjälp av .NET i HttpWebRequest-objektet.
> 
> 

**Brödtext**

    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }

  **Tilldela ägare och begränsa synligheten för ett befintligt objekt för roten**: **PLACERA** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }

> [!NOTE]
> Det är inte nödvändigt för att ange en nyttolast för objekt i brödtexten för i PUT: PUT kan användas för att uppdatera bara roller och/eller behörigheter.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png
