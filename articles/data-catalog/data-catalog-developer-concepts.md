---
title: Utvecklarkoncept för Azure Data Catalog
description: Introduktion till de viktigaste begreppen i Azure Data Catalog konceptuell modell, som exponeras via Catalog REST API.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 80adc98255cfc9145d583ac775bbc490d599234e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976834"
---
# <a name="azure-data-catalog-developer-concepts"></a>Utvecklarkoncept för Azure Data Catalog
Microsoft **Azure Data Catalog** är en fullständigt hanterad molntjänst som tillhandahåller funktioner för identifiering av datakällor och för crowdsourcing datakällametadata. Utvecklare kan använda tjänsten via rest-API:erna. Att förstå de begrepp som implementeras i tjänsten är viktigt för utvecklare att framgångsrikt integrera med **Azure Data Catalog**.

## <a name="key-concepts"></a>Viktiga begrepp
Konceptuell modell för **Azure Data Catalog** baseras på fyra nyckelbegrepp: **Katalogen,** **Användare,** **Tillgångar** **och Anteckningar**.

![Konceptuell modellillustration för Azure Data Catalog](./media/data-catalog-developer-concepts/concept2.png)

*Bild 1 - Förenklad konceptuell modell för Azure Data Catalog*

### <a name="catalog"></a>Katalog
En **katalog** är behållaren på den översta nivån för alla metadata som en organisation lagrar. Det finns en **katalog** tillåts per Azure-konto. Kataloger är kopplade till en Azure-prenumeration, men endast en **katalog** kan skapas för ett visst Azure-konto, även om ett konto kan ha flera prenumerationer.

En katalog innehåller **användare** och **tillgångar**.

### <a name="users"></a>Användare
Användare är säkerhetsobjekt som har behörighet att utföra åtgärder (sök i katalogen, lägga till, redigera eller ta bort objekt osv.) i katalogen.

Det finns flera olika roller som en användare kan ha. Information om roller finns i avsnittet Roller och Auktorisering.

Enskilda användare och säkerhetsgrupper kan läggas till.

Azure Data Catalog använder Azure Active Directory för identitets- och åtkomsthantering. Varje kataloganvändare måste vara medlem i Active Directory för kontot.

### <a name="assets"></a>Tillgångar
En **katalog** innehåller datatillgångar. **Tillgångar** är den detaljenhet som hanteras av katalogen.

En tillgångs granularitet varierar beroende på datakälla. För SQL Server eller Oracle Database kan en tillgång vara en tabell eller en vy. För SQL Server Analysis Services kan en tillgång vara ett mått, en dimension eller en KPI (Key Performance Indicator). För SQL Server Reporting Services är en tillgång en rapport.

En **tillgång** är det du lägger till eller tar bort från en katalog. Det är den enhet av resultat du får tillbaka från **Sök**.

En **tillgång** består av namn, plats och typ och anteckningar som beskriver den ytterligare.

### <a name="annotations"></a>Anteckningar
Anteckningar är objekt som representerar metadata om tillgångar.

Exempel på anteckningar är beskrivning, taggar, schema, dokumentation, etc. En fullständig lista över tillgångstyper och anteckningstyper finns i avsnittet Tillgångsobjektmodell.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Crowdsourcing anteckningar och användarperspektiv (mångfald av åsikter)
En viktig aspekt av Azure Data Catalog är hur den stöder crowdsourcing av metadata i systemet. I motsats till en wiki-metod – där det bara finns en åsikt och den sista författaren vinner – tillåter Azure Data Catalog-modellen flera åsikter att leva sida vid sida i systemet.

Detta tillvägagångssätt återspeglar den verkliga världen av företagsdata där olika användare kan ha olika perspektiv på en viss tillgång:

* En databasadministratör kan tillhandahålla information om servicenivåavtal eller det tillgängliga bearbetningsfönstret för mass-ETL-åtgärder
* En dataansvarig kan tillhandahålla information om de affärsprocesser som tillgången tillämpar, eller de klassificeringar som företaget har tillämpat på den
* En ekonomianalytiker kan ge information om hur data används under rapporteringsuppgifter i slutet av perioden

För att stödja det här exemplet kan varje användare – DBA, dataansvarig och analytikern – lägga till en beskrivning i en enda tabell som har registrerats i katalogen. Alla beskrivningar underhålls i systemet och i Azure Data Catalog-portalen visas alla beskrivningar.

Det här mönstret används på de flesta objekt i objektmodellen, så objekttyper i JSON-nyttolasten är ofta matriser för egenskaper där du kan förvänta dig en singleton.

Under tillgångsroten finns till exempel en matris med beskrivningsobjekt. Matrisegenskapen heter "beskrivningar". Ett beskrivningsobjekt har en egenskap - beskrivning. Mönstret är att varje användare som skriver beskrivning får ett beskrivningsobjekt som skapats för det värde som tillhandahålls av användaren.

Användarupplevelsen kan sedan välja hur kombinationen ska visas. Det finns tre olika mönster för visning.

* Det enklaste mönstret är "Visa alla". I det här mönstret visas alla objekt i en listvy. Azure Data Catalog portal UX använder det här mönstret för beskrivning.
* Ett annat mönster är "Merge". I det här mönstret slås alla värden från de olika användarna samman, med dubblett borttagen. Exempel på det här mönstret i Azure Data Catalog portal UX är taggar och experter egenskaper.
* Ett tredje mönster är "sista författaren vinner". I det här mönstret visas bara det senaste värdet som skrivs in. friendlyName är ett exempel på det här mönstret.

## <a name="asset-object-model"></a>Tillgångsobjektmodell
Som introducerades i avsnittet Nyckelbegrepp innehåller **objektmodellen Azure Data Catalog** objekt, som kan vara tillgångar eller anteckningar. Artiklar har egenskaper som kan vara valfria eller obligatoriska. Vissa egenskaper gäller för alla artiklar. Vissa egenskaper gäller för alla tillgångar. Vissa egenskaper gäller endast för specifika tillgångstyper.

### <a name="system-properties"></a>Systemegenskaper
<table><tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentar</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>Senast objektet ändrades. Det här fältet genereras av servern när ett objekt infogas och varje gång ett objekt uppdateras. Värdet för den här egenskapen ignoreras vid indata från publiceringsåtgärder.</td></tr><tr><td>id</td><td>Uri</td><td>Absolut url för objektet (skrivskyddad). Det är den unika adresserbara URI för objektet.  Värdet för den här egenskapen ignoreras vid indata från publiceringsåtgärder.</td></tr><tr><td>typ</td><td>String</td><td>Typen av tillgång (skrivskyddad).</td></tr><tr><td>Etag</td><td>String</td><td>En sträng som motsvarar den version av artikeln som kan användas för optimistisk samtidighetskontroll när du utför åtgärder som uppdaterar objekt i katalogen. "*" kan användas för att matcha valfritt värde.</td></tr></table>

### <a name="common-properties"></a>Gemensamma egenskaper
Dessa egenskaper gäller för alla rottillgångstyper och alla anteckningstyper.

<table>
<tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentar</b></td></tr>
<tr><td>frånSourceSystem</td><td>Boolean</td><td>Anger om objektets data härleds från ett källsystem (som Sql Server Database, Oracle Database) eller som skapas av en användare.</td></tr>
</table>

### <a name="common-root-properties"></a>Vanliga rotegenskaper
<p>
Dessa egenskaper gäller för alla rottillgångstyper.

<table><tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentar</b></td></tr><tr><td>namn</td><td>String</td><td>Ett namn som härleds från information om datakällans plats</td></tr><tr><td>Dsl</td><td>DataKällaPlats</td><td>Beskriver datakällan unikt och är en av identifierarna för tillgången. (Se avsnittet med dubbel identitet).  DSL:s struktur varierar beroende på protokoll och källtyp.</td></tr><tr><td>Datasource</td><td>DataSourceInfo</td><td>Mer information om vilken typ av tillgång.</td></tr><tr><td>senastregistreradAv</td><td>SäkerhetPrincipal</td><td>Beskriver den användare som senast registrerade tillgången.  Innehåller både det unika id:t för användaren (upn) och ett visningsnamn (efternamn och förnamn).</td></tr><tr><td>containerId (containerId)</td><td>String</td><td>Id för behållartillgången för datakällan. Den här egenskapen stöds inte för typen Container.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Vanliga icke-enstaka anteckningsegenskaper
Dessa egenskaper gäller för alla icke-singleton-anteckningstyper (anteckningar, som tillät att vara flera per tillgång).

<table>
<tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentar</b></td></tr>
<tr><td>key</td><td>String</td><td>En användarspecificerad nyckel som unikt identifierar anteckningen i den aktuella samlingen. Nyckellängden får inte överstiga 256 tecken.</td></tr>
</table>

### <a name="root-asset-types"></a>Rottillgångstyper
Rottillgångstyper är de typer som representerar de olika typer av datatillgångar som kan registreras i katalogen. För varje rottyp finns det en vy som beskriver tillgång och anteckningar som ingår i vyn. Visningsnamnet ska användas i motsvarande url-segment för {view_name} när en tillgång publiceras med REST API.

<table><tr><td><b>Tillgångstyp (visa namn)</b></td><td><b>Ytterligare egenskaper</b></td><td><b>Datatyp</b></td><td><b>Tillåtna anteckningar</b></td><td><b>Kommentar</b></td></tr><tr><td>Tabell ("tabeller")</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagga<p>Schema<p>ColumnDescription<p>ColumnTag (Kolumn)<p> Expert<p>Förhandsversion<p>TillträdeInstruktion<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentation<p></td><td>En tabell representerar alla tabelldata.  Till exempel: SQL Table, SQL View, Analysis Services Tabell, Analysis Services flerdimensionell dimension, Oracle Tabell, etc.   </td></tr><tr><td>Åtgärd ("åtgärder")</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagga<p>Expert<p>TillträdeInstruktion<p>Dokumentation<p></td><td>Den här typen representerar ett Analysis Services-mått.</td></tr><tr><td></td><td>Åtgärd</td><td>Kolumn</td><td></td><td>Metadata som beskriver måttet</td></tr><tr><td></td><td>ärBeräknad </td><td>Boolean</td><td></td><td>Anger om måttet är beräknat eller inte.</td></tr><tr><td></td><td>measureGroup (mätGroup)</td><td>String</td><td></td><td>Fysisk behållare för åtgärd</td></tr><td>KPI ("kpi:er")</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagga<p>Expert<p>TillträdeInstruktion<p>Dokumentation</td><td></td></tr><tr><td></td><td>measureGroup (mätGroup)</td><td>String</td><td></td><td>Fysisk behållare för åtgärd</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td></td><td>Ett MDX-numeriskt uttryck eller en beräkning som returnerar KPI:ns målvärde.</td></tr><tr><td></td><td>värdeUttryck</td><td>String</td><td></td><td>Ett MDX-numeriskt uttryck som returnerar KPI:ns faktiska värde.</td></tr><tr><td></td><td>statusUttryck</td><td>String</td><td></td><td>Ett MDX-uttryck som representerar KPI:ns tillstånd vid en angiven tidpunkt.</td></tr><tr><td></td><td>trendUttryck</td><td>String</td><td></td><td>Ett MDX-uttryck som utvärderar KPI:ns värde över tid. Trenden kan vara valfritt tidsbaserat kriterium som är användbart i ett visst affärssammanhang.</td>
<tr><td>Rapport ("rapporter")</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagga<p>Expert<p>TillträdeInstruktion<p>Dokumentation<p></td><td>Den här typen representerar en SQL Server Reporting Services-rapport </td></tr><tr><td></td><td>tillgångarSkaperadDat</td><td>String</td><td></td><td></td></tr><tr><td></td><td>tillgångarSkapetAv</td><td>String</td><td></td><td></td></tr><tr><td></td><td>tillgångModifiedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>tillgångModifiedBy</td><td>String</td><td></td><td></td></tr><tr><td>Behållare ("behållare")</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagga<p>Expert<p>TillträdeInstruktion<p>Dokumentation<p></td><td>Den här typen representerar en behållare med andra resurser, till exempel en SQL-databas, en Azure Blobs-behållare eller en Analysis Services-modell.</td></tr></table>

### <a name="annotation-types"></a>Anteckningstyper
Anteckningstyper representerar typer av metadata som kan tilldelas andra typer i katalogen.

<table>
<tr><td><b>Anteckningstyp (kapslad vynamn)</b></td><td><b>Ytterligare egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentar</b></td></tr>

<tr><td>Beskrivning ("beskrivningar")</td><td></td><td></td><td>Den här egenskapen innehåller en beskrivning för en tillgång. Varje användare av systemet kan lägga till sin egen beskrivning.  Endast den användaren kan redigera beskrivningsobjektet.  (Administratörer och tillgångsägare kan ta bort beskrivningsobjektet men inte redigera det). Systemet upprätthåller användarnas beskrivningar separat.  Det finns alltså en rad beskrivningar på varje tillgång (en för varje användare som har bidragit med sin kunskap om tillgången, förutom eventuellt en som innehåller information som härrör från datakällan).</td></tr>
<tr><td></td><td>description</td><td>sträng</td><td>En kort beskrivning (2-3 rader) av tillgången</td></tr>

<tr><td>Tagg ("taggar")</td><td></td><td></td><td>Den här egenskapen definierar en tagg för en tillgång. Varje användare av systemet kan lägga till flera taggar för en tillgång.  Endast den användare som skapade Taggobjekt kan redigera dem.  (Administratörer och tillgångsägare kan ta bort taggobjektet men inte redigera det). Systemet underhåller användarnas taggar separat.  Det finns alltså en matris med taggobjekt på varje tillgång.</td></tr>
<tr><td></td><td>tagg</td><td>sträng</td><td>En tagg som beskriver tillgången.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Den här egenskapen innehåller ett eget namn för en tillgång. FriendlyName är en singleton-anteckning – endast ett FriendlyName kan läggas till i en tillgång.  Endast den användare som skapade FriendlyName-objektet kan redigera det. (Administratörer och tillgångsägare kan ta bort FriendlyName-objektet men inte redigera det). Systemet underhåller användarnas egna namn separat.</td></tr>
<tr><td></td><td>friendlyName (eget namn)</td><td>sträng</td><td>Ett eget namn på tillgången.</td></tr>

<tr><td>Schema ("schema")</td><td></td><td></td><td>Schemat beskriver datastrukturen.  Den listar attribut (kolumn, attribut, fält, etc.) namn, typer samt andra metadata.  Den här informationen härleds från datakällan.  Schema är en singleton-anteckning - endast ett schema kan läggas till för en tillgång.</td></tr>
<tr><td></td><td>kolumner</td><td>Kolumn[]</td><td>En matris med kolumnobjekt. De beskriver kolumnen med information som härleds från datakällan.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Den här egenskapen innehåller en beskrivning av en kolumn.  Varje användare av systemet kan lägga till sina egna beskrivningar för flera kolumner (högst en per kolumn). Endast den användare som skapade ColumnDescription-objekt kan redigera dem.  (Administratörer och tillgångsägare kan ta bort ColumnDescription-objektet men inte redigera det). Systemet underhåller dessa användares kolumnbeskrivningar separat.  Det finns alltså en matris med ColumnDescription-objekt på varje tillgång (en per kolumn för varje användare som har bidragit med sin kunskap om kolumnen utöver eventuellt en som innehåller information som härletts från datakällan).  ColumnDescription är löst bunden till schemat så att det kan bli osynkroniserat. ColumnDescription kan beskriva en kolumn som inte längre finns i schemat.  Det är upp till författaren att hålla beskrivning och schema synkroniserat.  Datakällan kan också ha beskrivningsinformation för kolumner och de är ytterligare ColumnDescription-objekt som skulle skapas när verktyget körs.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>Namnet på den kolumn som den här beskrivningen refererar till.</td></tr>
<tr><td></td><td>description</td><td>String</td><td>en kort beskrivning (2-3 rader) i kolumnen.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Den här egenskapen innehåller en tagg för en kolumn. Varje användare av systemet kan lägga till flera taggar för en viss kolumn och kan lägga till taggar för flera kolumner. Endast den användare som skapade ColumnTag-objekt kan redigera dem. (Administratörer och tillgångsägare kan ta bort ColumnTag-objektet men inte redigera det). Systemet underhåller dessa användares kolumntaggar separat.  Det finns alltså en matris med ColumnTag-objekt på varje tillgång.  ColumnTag är löst bunden till schemat så att den kan komma ur synk. ColumnTag kan beskriva en kolumn som inte längre finns i schemat.  Det är upp till författaren att hålla kolumntaggen och schemat synkroniserat.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>Namnet på den kolumn som den här taggen refererar till.</td></tr>
<tr><td></td><td>tagg</td><td>String</td><td>En tagg som beskriver kolumnen.</td></tr>

<tr><td>Expert ("experter")</td><td></td><td></td><td>Den här egenskapen innehåller en användare som betraktas som expert i datauppsättningen. Experternas åsikter (beskrivningar) bubbla till toppen av UX när notering beskrivningar. Varje användare kan ange sina egna experter. Endast den användaren kan redigera expertobjektet. (Administratörer och tillgångsägare kan ta bort expertobjekten men inte redigera dem).</td></tr>
<tr><td></td><td>Expert</td><td>SäkerhetPrincipal</td><td></td></tr>

<tr><td>Förhandsgranska ("förhandsvisningar")</td><td></td><td></td><td>Förhandsgranskningen innehåller en ögonblicksbild av de 20 översta raderna med data för tillgången. Förhandsversion är bara meningsfullt för vissa typer av tillgångar (det är vettigt för Tabell men inte för Åtgärd).</td></tr>
<tr><td></td><td>förhandsgranska</td><td>objekt[]</td><td>Matris med objekt som representerar en kolumn.  Varje objekt har en egenskapsmappning till en kolumn med ett värde för den kolumnen för raden.</td></tr>

<tr><td>TillträdeInstruktion ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Mimetype</td><td>sträng</td><td>Innehållets mimtyp.</td></tr>
<tr><td></td><td>innehåll</td><td>sträng</td><td>Instruktionerna för hur du får tillgång till den här datatillgången. Innehållet kan vara en webbadress, en e-postadress eller en uppsättning instruktioner.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Antalet rader i datauppsättningen</td></tr>
<tr><td></td><td>size</td><td>long</td><td>Storleken i byte för datauppsättningen.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>sträng</td><td>Senaste gången schemat ändrades</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>sträng</td><td>Senaste gången datauppsättningen ändrades (data lades till, ändrades eller togs bort)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>kolumner</td></td><td>ColumnDataProfile[]</td><td>En matris med kolumndataprofiler.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>Namnet på den kolumn som den här klassificeringen refererar till.</td></tr>
<tr><td></td><td>klassificering</td><td>String</td><td>Klassificeringen av data i den här kolumnen.</td></tr>

<tr><td>Dokumentation ("dokumentation")</td><td></td><td></td><td>En viss tillgång kan bara ha en dokumentation kopplad till sig.</td></tr>
<tr><td></td><td>Mimetype</td><td>sträng</td><td>Innehållets mimtyp.</td></tr>
<tr><td></td><td>innehåll</td><td>sträng</td><td>Dokumentationen innehåll.</td></tr>

</table>

### <a name="common-types"></a>Vanliga typer
Vanliga typer kan användas som typer för egenskaper, men är inte objekt.

<table>
<tr><td><b>Vanlig typ</b></td><td><b>Egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentar</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType (sourceType)</td><td>sträng</td><td>Beskriver typen av datakälla.  Till exempel: SQL Server, Oracle Database, etc.  </td></tr>
<tr><td></td><td>objectType (objekttyp)</td><td>sträng</td><td>Beskriver typen av objekt i datakällan. Till exempel: Tabell, Visa för SQL Server.</td></tr>

<tr><td>DataKällaPlats</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Protokollet</td><td>sträng</td><td>Krävs. Beskriver ett protokoll som används för att kommunicera med datakällan. Till exempel: "tds" för SQl Server, "oracle" för Oracle, etc. Se <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">referensspecifikation för datakälla - DSL-struktur</a> för listan över protokoll som stöds för närvarande.</td></tr>
<tr><td></td><td>adress</td><td>Ordlistesträng,&lt;objekt&gt;</td><td>Krävs. Adress är en uppsättning data som är specifika för protokollet och som används för att identifiera den datakälla som refereras. Adressdata som omfattas av ett visst protokoll, vilket innebär att det är meningslöst utan att känna till protokollet.</td></tr>
<tr><td></td><td>autentisering</td><td>sträng</td><td>Valfri. Autentiseringsschemat som används för att kommunicera med datakällan. Till exempel: fönster, oauth, etc.</td></tr>
<tr><td></td><td>anslutningEgenskaper</td><td>Ordlistesträng,&lt;objekt&gt;</td><td>Valfri. Ytterligare information om hur du ansluter till en datakälla.</td></tr>

<tr><td>SäkerhetPrincipal</td><td></td><td></td><td>Serverings-serverdan utför ingen validering av angivna egenskaper mot AAD under publiceringen.</td></tr>
<tr><td></td><td>Upn</td><td>sträng</td><td>Unik e-postadress till användaren. Måste anges om objectId inte tillhandahålls eller i samband med egenskapen "lastRegisteredBy", annars valfritt.</td></tr>
<tr><td></td><td>Objectid</td><td>GUID</td><td>AAD-identitet för användare eller säkerhetsgrupp. Valfri. Måste anges om upn inte tillhandahålls, annars valfritt.</td></tr>
<tr><td></td><td>firstName</td><td>sträng</td><td>Förnamn på användaren (för visning). Valfri. Gäller endast i samband med egenskapen "lastRegisteredBy". Det går inte att ange när säkerhetsobjekt för "roller", "behörigheter" och "experter" anges.</td></tr>
<tr><td></td><td>lastName</td><td>sträng</td><td>Efternamn på användaren (för visning). Valfri. Gäller endast i samband med egenskapen "lastRegisteredBy". Det går inte att ange när säkerhetsobjekt för "roller", "behörigheter" och "experter" anges.</td></tr>

<tr><td>Kolumn</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namn</td><td>sträng</td><td>Namn på kolumnen eller attributet.</td></tr>
<tr><td></td><td>typ</td><td>sträng</td><td>datatypen för kolumnen eller attributet. De tillåtna typerna beror på tillgångens datakällaTyp.  Endast en delmängd av typer stöds.</td></tr>
<tr><td></td><td>Maxlength</td><td>int</td><td>Den maximala tillåtna längden för kolumnen eller attributet. Härleds från datakällan. Gäller endast vissa källtyper.</td></tr>
<tr><td></td><td>Precision</td><td>byte</td><td>Precisionen för kolumnen eller attributet. Härleds från datakällan. Gäller endast vissa källtyper.</td></tr>
<tr><td></td><td>ärNullable</td><td>Boolean</td><td>Om kolumnen tillåts ha ett null-värde eller inte. Härleds från datakällan. Gäller endast vissa källtyper.</td></tr>
<tr><td></td><td>uttryck</td><td>sträng</td><td>Om värdet är en beräknad kolumn innehåller det här fältet uttrycket som uttrycker värdet. Härleds från datakällan. Gäller endast vissa källtyper.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>sträng</td><td>Namnet på kolumnen</td></tr>
<tr><td></td><td>typ </td><td>sträng</td><td>Typ av kolumn</td></tr>
<tr><td></td><td>min </td><td>sträng</td><td>Minimivärdet i datauppsättningen</td></tr>
<tr><td></td><td>Max </td><td>sträng</td><td>Det maximala värdet i datauppsättningen</td></tr>
<tr><td></td><td>Avg </td><td>double</td><td>Medelvärdet i datauppsättningen</td></tr>
<tr><td></td><td>Stdav </td><td>double</td><td>Standardavvikelsen för datauppsättningen</td></tr>
<tr><td></td><td>nullCount (nullCount) </td><td>int</td><td>Antalet null-värden i datauppsättningen</td></tr>
<tr><td></td><td>distinctCount (distinctCount)  </td><td>int</td><td>Antalet distinkta värden i datauppsättningen</td></tr>


</table>

## <a name="asset-identity"></a>Identitet för tillgångar
Azure Data Catalog använder "protokoll" och identitetsegenskaper från egenskapspåsen "adress" för egenskapen DataSourceLocation "dsl" för att generera identiteten för tillgången, som används för att adressera tillgången i katalogen.
Till exempel har "tds"-protokollet identitetsegenskaper "server", "databas", "schema" och "objekt". Kombinationerna av protokollet och identitetsegenskaperna används för att generera identiteten för SQL Server Table Asset.
Azure Data Catalog innehåller flera inbyggda datakällprotokoll, som visas vid [referensspecifikation för datakälla - DSL-struktur](data-catalog-dsr.md).
Uppsättningen protokoll som stöds kan utökas programmässigt (Se REST API-referens för datakatalog). Administratörer i katalogen kan registrera anpassade datakällprotokoll. I följande tabell beskrivs de egenskaper som behövs för att registrera ett anpassat protokoll.

### <a name="custom-data-source-protocol-specification"></a>Specifikation av anpassat protokoll för datakälla
<table>
<tr><td><b>Typ</b></td><td><b>Egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentar</b></td></tr>

<tr><td>DataKällaProtokol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namnområde</td><td>sträng</td><td>Namnområdet för protokollet. Namnområdet måste vara mellan 1 och 255 tecken långt, innehålla en eller flera icke-tomma delar avgränsade med punkt (.). Varje del måste vara från 1 till 255 tecken lång, börja med en bokstav och innehåller endast bokstäver och siffror.</td></tr>
<tr><td></td><td>namn</td><td>sträng</td><td>Namnet på protokollet. Namnet måste vara mellan 1 och 255 tecken långt, börja med en bokstav och bara innehålla bokstäver, siffror och strecktecknet (-).</td></tr>
<tr><td></td><td>identitetEgenskaper</td><td>DataSourceProtocolIdentityProperty[]</td><td>Lista över identitetsegenskaper, måste innehålla minst en, men inte mer än 20 egenskaper. Till exempel: "server", "databas", "schema", "objekt" är identitetsegenskaper för "tds"-protokollet.</td></tr>
<tr><td></td><td>identitetSET</td><td>DataSourceProtocolIdentitySet[]</td><td>Lista över identitetsuppsättningar. Definierar uppsättningar av identitetsegenskaper som representerar den giltiga tillgångens identitet. Måste innehålla minst en, men inte mer än 20 uppsättningar. Till exempel: {"server", "databas", "schema" och "objekt"} är en identitetsuppsättning för "tds"-protokollet, som definierar identiteten för Sql Server Table-tillgången.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namn</td><td>sträng</td><td>Namnet på egenskapen. Namnet måste vara mellan 1 och 100 tecken långt, börja med en bokstav och kan bara innehålla bokstäver och siffror.</td></tr>
<tr><td></td><td>typ</td><td>sträng</td><td>Egenskapens typ. Värden som stöds: "bool", booleska", "byte", "guid", "int", "heltal", "long", "string", "url"</td></tr>
<tr><td></td><td>ignoreCase (ignoreraFall)</td><td>bool</td><td>Anger om ärendet ska ignoreras när egenskapens värde ska användas. Kan endast anges för egenskaper med "strängtyp". Standardvärdet är falskt.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool []</td><td>Anger om ärendet ska ignoreras för varje segment i url:ens sökväg. Kan endast anges för egenskaper med "url"-typ. Standardvärdet är [falskt].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namn</td><td>sträng</td><td>Namnet på identitetsuppsättningen.</td></tr>
<tr><td></td><td>properties</td><td>sträng[]</td><td>Listan över identitetsegenskaper som ingår i den här identitetsuppsättningen. Den får inte innehålla dubbletter. Varje egenskap som refereras av identitetsuppsättning måste definieras i listan över "identityProperties" i protokollet.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Roller och auktorisering
Microsoft Azure Data Catalog tillhandahåller auktoriseringsfunktioner för CRUD-åtgärder på tillgångar och anteckningar.

## <a name="key-concepts"></a>Viktiga begrepp
Azure Data Catalog använder två auktoriseringsmekanismer:

* Rollbaserad auktorisering
* Behörighetsbaserad auktorisering

### <a name="roles"></a>Roller
Det finns tre roller: **Administratör,** **Ägare**och **Deltagare**.  Varje roll har sin omfattning och sina rättigheter, som sammanfattas i följande tabell.

<table><tr><td><b>Roll</b></td><td><b>Omfång</b></td><td><b>Rättigheter</b></td></tr><tr><td>Administratör</td><td>Katalog (alla tillgångar/anteckningar i katalogen)</td><td>Läs Ta bort ViewRoles

Ändraownership ChangeVisibility ViewPermissions</td></tr><tr><td>Ägare</td><td>Varje tillgång (rotobjekt)</td><td>Läs Ta bort ViewRoles

Ändraownership ChangeVisibility ViewPermissions</td></tr><tr><td>Deltagare</td><td>Varje enskild tillgång och anteckning</td><td>Läs uppdatera ta bort ViewRolles Obs: alla rättigheter återkallas om läsrättigheten för objektet återkallas från deltagaren</td></tr></table>

> [!NOTE]
> **Läs**, **Uppdatera**, **Ta bort**, **ViewRoles-rättigheter** gäller för alla artiklar (tillgång eller anteckningar) medan **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** endast är tillämpliga på rottillgången.
> 
> **Borttagningsrätt** gäller för ett objekt och alla underobjekt eller ett enda objekt under det. Om du till exempel tar bort en tillgång tas även alla anteckningar för den tillgången bort.
> 
> 

### <a name="permissions"></a>Behörigheter
Behörighet är som lista över åtkomstkontrollposter. Varje åtkomstkontrollpost tilldelar en säkerhetsobjektuppsättning med behörighet. Behörigheter kan bara anges på en tillgång (det vill än rotobjekt) och gälla för tillgången och eventuella underobjekt.

Under förhandsversionen av **Azure Data Catalog** stöds endast **läsrätt** i behörighetslistan för att aktivera scenario för att begränsa synligheten för en tillgång.

Som standard har alla autentiserade användare **läsrätt** för alla objekt i katalogen om inte synligheten är begränsad till uppsättningen huvudnamn i behörigheterna.

## <a name="rest-api"></a>REST API
**POST-** och **POST-vyobjektbegäranden** kan användas för att styra roller och behörigheter: förutom artikelnyttolaster kan två systemegenskaper anges **roller** och **behörigheter**.

> [!NOTE]
> **behörigheter** som endast gäller för ett rotobjekt.
> 
> **Ägarrollen** gäller endast för ett rotobjekt.
> 
> Som standard när ett objekt skapas i katalogen är **deltagaren** inställd på den autentiserade användaren. Om objektet ska uppdateras av alla bör &lt; **Deltagaren** anges till Alla&gt; särskilda säkerhetsobjekt i **rollegenskapen** när objektet först publiceras (se följande exempel). **Deltagaren** kan inte ändras och förblir densamma under ett objekts livstid (inte ens **administratör** eller **ägare** har rätt att ändra **deltagaren).** Det enda värde som stöds för &lt;den&gt;explicita inställningen för **deltagaren** är &lt;&gt;Alla: **Deltagare** kan bara vara en användare som har skapat ett objekt eller Alla .
> 
> 

### <a name="examples"></a>Exempel
**Ange Deltagare &lt;&gt; till Alla när du publicerar ett objekt.**
Särskilda &lt;säkerhetsobjekt&gt; Alla har objectId "00000000-0000-0000-0000-00000000201".
  **POST** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Vissa HTTP-klientimplementeringar kan automatiskt återutfärda begäranden som svar på en 302 från servern, men vanligtvis ta bort auktoriseringshuvuden från begäran. Eftersom auktoriseringshuvudet krävs för att göra begäranden till Azure Data Catalog måste du se till att auktoriseringshuvudet fortfarande anges när en begäran omges till en omdirigeringsplats som anges av Azure Data Catalog. Följande exempelkod visar att den använder .NET HttpWebRequest-objektet.
> 
> 

**Brödtext**
```json
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
```

  **Tilldela ägare och begränsa synligheten för ett befintligt rotobjekt:** **PUT** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

```json
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
```

> [!NOTE]
> I PUT krävs det inte att ange en artikel nyttolast i brödtexten: PUT kan användas för att uppdatera bara roller och /eller behörigheter.
> 
