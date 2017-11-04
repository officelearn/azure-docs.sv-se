---
title: "Begrepp för utvecklare av data Catalog | Microsoft Docs"
description: Introduktion till viktiga begrepp i Azure Data Catalog konceptuella modellen som exponerade via REST-API-katalogen.
services: data-catalog
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
tags: 
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/15/2017
ms.author: spelluru
ms.openlocfilehash: e3c26c2358c15d18c71b82fe1f389c039ecbd97b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-catalog-developer-concepts"></a>Begrepp för utvecklare för Azure Data Catalog
Microsoft **Azure Data Catalog** är en helt hanterad molntjänst som tillhandahåller funktioner för upptäckt av datakälla och gemensamt skapade metadata från datakällan. Utvecklare kan använda tjänsten via dess REST-API: er. Förstå begrepp som implementerats i tjänsten är viktigt för utvecklare att integrera med **Azure Data Catalog**.

## <a name="key-concepts"></a>Viktiga begrepp
Den **Azure Data Catalog** konceptuella modellen baseras på fyra viktiga begrepp: den **katalog**, **användare**, **tillgångar**, och **anteckningar**.

![Begrepp][1]

*Bild 1 – Azure Data Catalog förenklad konceptuella modellen*

### <a name="catalog"></a>Katalogen
En **katalog** översta behållaren för alla metadata som lagras i en organisation. Det finns en **katalog** tillåts per Azure-konto. Kataloger som är knutna till en Azure-prenumeration, men endast ett **katalog** kan skapas för en viss Azure-konto, även om ett konto kan ha flera prenumerationer.

En katalog innehåller **användare** och **tillgångar**.

### <a name="users"></a>Användare
Användare kan säkerhetsobjekt som har behörighet att utföra åtgärder (söka i katalogen, lägga till, redigera eller ta bort objekt, etc.) i katalogen.

Det finns flera olika roller som en användare kan ha. Information om roller finns i avsnittet roller och auktorisering.

Enskilda användare och säkerhetsgrupper kan läggas till.

Azure Data Catalog använder Azure Active Directory för identitets- och åtkomsthantering. Varje användare i katalogen måste vara medlem i Active Directory för kontot.

### <a name="assets"></a>Tillgångar
En **katalogen** innehåller datatillgångar. **Tillgångar** är enheten granularitet som hanteras av katalogen.

Granulariteten för en tillgång varierar beroende på datakällan. En tillgång kan vara en tabell eller en vy för SQL Server eller Oracle-databas. En tillgång kan vara ett mått eller en Dimension nyckel prestanda indikator (KPI) för SQL Server Analysis Services. För SQL Server Reporting Services är en tillgång en rapport.

En **tillgången** är det som du lägger till eller ta bort från en katalog. Det är resultatet åter från enheten **Sök**.

En **tillgången** består från dess namn, plats och typ och anteckningar som beskriver den ytterligare.

### <a name="annotations"></a>Anteckningar
Anteckningar är objekt som representerar metadata om tillgångar.

Exempel på anteckningar är beskrivning, taggar, schemat, dokumentation och så vidare. En fullständig lista över resurstyper och anteckningens typer finns i avsnittet objekt för tillgångsinformation modellen.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Gemensamt skapade anteckningar och användarens synvinkel (multipliciteten för åsikt)
En viktig aspekt av Azure Data Catalog är hur den stöder gemensamt skapade metadata i systemet. I motsats till en wiki-strategi – där det finns endast en åsikt och de senaste skrivaren wins – Azure Data Catalog modellen tillåter flera åsikter till live sida vid sida i systemet.

Den här metoden avspeglar den verkliga världen företagsdata där olika användare kan ha olika perspektiv på en given tillgång:

* En databasadministratör kan innehålla information om servicenivåavtal eller fönstret tillgängliga bearbetning för ETL massåtgärder
* En data-steward kan ge information om affärsprocesser som gäller för tillgången eller klassificeringar som företaget har använts
* En ekonomi analytiker kan innehålla information om hur data ska användas vid slutet av perioden rapportuppgifter

För att stödja det här exemplet, kan varje användare – DBA data steward och analysen – Lägg till en beskrivning för en tabell som har registrerats i katalogen. Alla beskrivningar underhålls i systemet och alla beskrivningar visas i Azure Data Catalog-portalen.

Det här mönstret tillämpas på de flesta objekt i objektmodellen, så objekttyper i JSON-nyttolast är ofta matriser för egenskaper där du kan förvänta dig en singleton.

Under tillgången är roten till exempel en matris med beskrivning objekt. Array-egenskap har namnet ”beskrivning”. En beskrivning av objektet har en egenskap - Beskrivning. Mönstret är att varje användare som beskrivning av typer hämtar en beskrivning av objektet som skapats för värdet som anges av användaren.

UX kan sedan välja hur du visar en kombination. Det finns tre olika mönster för visning.

* Det enklaste mönstret är ”visa alla”. I det här mönstret visas alla objekt i en listvy. Azure Data Catalog-portalen UX använder det här mönstret beskrivning.
* En annan mönstret är ”sammanfoga”. I det här mönstret samman alla värden från olika användare tillsammans med dubblett tas bort. Exempel på detta mönster i Azure Data Catalog-portalen UX är egenskaperna taggar och experter.
* En tredje mönstret är ”sista skrivare vinner”. I det här mönstret visas bara det senaste värdet som har skrivit in. friendlyName är ett exempel på detta mönster.

## <a name="asset-object-model"></a>Objektmodell för tillgångsinformation
Som det introducerats i avsnittet nyckelkoncept i **Azure Data Catalog** objektmodellen innehåller objekt som kan vara tillgångar eller anteckningar. Objekt har egenskaper som kan vara valfri eller nödvändig. Vissa egenskaper som gäller för alla objekt. Vissa egenskaper som gäller för alla tillgångar. Vissa egenskaper gäller endast för specifika resurstyper.

### <a name="system-properties"></a>Systemegenskaper
<table><tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr><tr><td>tidsstämpel</td><td>Datum och tid</td><td>Objektet ändrades senast. Det här fältet genereras av servern när ett objekt infogas och varje gång ett objekt uppdateras. Värdet för den här egenskapen ignoreras på indata för åtgärder.</td></tr><tr><td>id</td><td>URI: N</td><td>Absolut url för objektet (skrivskyddad). Det är unikt adresserbara URI för objektet.  Värdet för den här egenskapen ignoreras på indata för åtgärder.</td></tr><tr><td>typ</td><td>Sträng</td><td>Typ av tillgången (skrivskyddad).</td></tr><tr><td>ETag</td><td>Sträng</td><td>En sträng som motsvarar versionen för det objekt som kan användas för optimistisk samtidighetskontroll när du utför åtgärder som uppdatera objekt i katalogen. ”*” kan användas för att matcha alla värden.</td></tr></table>

### <a name="common-properties"></a>Gemensamma egenskaper
Dessa egenskaper gäller för alla typer av roten tillgång och alla anteckningstyper av.

<table>
<tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>
<tr><td>fromSourceSystem</td><td>Booleskt värde</td><td>Anger om objektets data är härledd från en källa (till exempel Sql Server-databas, Oracle-databas) eller som användaren.</td></tr>
</table>

### <a name="common-root-properties"></a>Allmänna egenskaper för rot
<p>
Dessa egenskaper gäller för alla typer av roten tillgången.

<table><tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr><tr><td>namn</td><td>Sträng</td><td>Ett namn som härleds från den platsinformationen om datakällan</td></tr><tr><td>DSL</td><td>DataSourceLocation</td><td>Unikt beskriver datakällan och är en identifierare för tillgången. (Se dubbla identity-avsnittet).  Strukturen för dsl varierar beroende på vilken protokoll och källa.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Mer information om vilken typ av tillgångsinformation.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Beskriver den användare som nyligen registrerats tillgången.  Innehåller båda unikt id för användare (upn) och ett namn (efternamn och förnamn).</td></tr><tr><td>behållar-ID</td><td>Sträng</td><td>ID för behållaren tillgången för datakällan. Den här egenskapen stöds inte för typ av behållare.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Allmänna egenskaper för icke-singleton-kommentar
Dessa egenskaper som gäller för alla anteckningstyper av icke-singleton (anteckningar, som kan vara flera per tillgång).

<table>
<tr><td><b>Egenskapsnamn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>
<tr><td>key</td><td>Sträng</td><td>En användare ange nyckeln som unikt identifierar anteckningen i samlingen. Nyckellängden får inte överstiga 256 tecken.</td></tr>
</table>

### <a name="root-asset-types"></a>Roten resurstyper
Roten resurstyper är de typer som representerar de olika typerna av datatillgångar som kan registreras i katalogen. För varje typ av roten finns en vy som beskriver tillgången och anteckningar i vyn. Vynamn ska användas i det motsvarande {view_name} url-segmentet när du publicerar en tillgång med REST API.

<table><tr><td><b>Tillgångstypen (namn)</b></td><td><b>Ytterligare egenskaper</b></td><td><b>Datatyp</b></td><td><b>Tillåtna anteckningar</b></td><td><b>Kommentarer</b></td></tr><tr><td>Tabellen (”tabeller”)</td><td></td><td></td><td>Beskrivning<p>friendlyName<p>Tagga<p>Schemat<p>ColumnDescription<p>ColumnTag<p> Expert<p>Förhandsversion<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentation<p></td><td>En tabell representerar inga tabelldata.  Till exempel: SQL-tabell, SQL-vyn, Analysis Services Tabular tabell, Analysis Services flerdimensionella dimensions-, Oracle-tabell, osv.   </td></tr><tr><td>Mått (”mått”)</td><td></td><td></td><td>Beskrivning<p>friendlyName<p>Tagga<p>Expert<p>AccessInstruction<p>Dokumentation<p></td><td>Den här typen representerar ett Analysis Services-mått.</td></tr><tr><td></td><td>Mått</td><td>Kolumn</td><td></td><td>Metadata som beskriver åtgärden</td></tr><tr><td></td><td>isCalculated </td><td>Booleskt värde</td><td></td><td>Anger om måttet beräknas eller inte.</td></tr><tr><td></td><td>MeasureGroup</td><td>Sträng</td><td></td><td>Fysiska behållare för måttet</td></tr><td>KPI (”KPI”)</td><td></td><td></td><td>Beskrivning<p>friendlyName<p>Tagga<p>Expert<p>AccessInstruction<p>Dokumentation</td><td></td></tr><tr><td></td><td>MeasureGroup</td><td>Sträng</td><td></td><td>Fysiska behållare för måttet</td></tr><tr><td></td><td>goalExpression</td><td>Sträng</td><td></td><td>Ett numeriskt MDX-uttryck eller en beräkning som returnerar målvärdet för KPI: N.</td></tr><tr><td></td><td>valueExpression</td><td>Sträng</td><td></td><td>Ett MDX-numeriska uttryck som returnerar det faktiska värdet för KPI: N.</td></tr><tr><td></td><td>statusExpression</td><td>Sträng</td><td></td><td>Ett MDX-uttryck som representerar tillståndet för KPI vid en angiven tidpunkt.</td></tr><tr><td></td><td>trendExpression</td><td>Sträng</td><td></td><td>Ett MDX-uttryck som returnerar värdet för KPI över tid. Trend kan vara något tidsbaserade kriterium som används i en specifik affärskontexten.</td>
<tr><td>Rapporten (”rapporter”)</td><td></td><td></td><td>Beskrivning<p>friendlyName<p>Tagga<p>Expert<p>AccessInstruction<p>Dokumentation<p></td><td>Den här typen representerar en SQL Server Reporting Services-rapport </td></tr><tr><td></td><td>assetCreatedDate</td><td>Sträng</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Sträng</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Sträng</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Sträng</td><td></td><td></td></tr><tr><td>Behållare (”behållare”)</td><td></td><td></td><td>Beskrivning<p>friendlyName<p>Tagga<p>Expert<p>AccessInstruction<p>Dokumentation<p></td><td>Den här typen representerar en behållare för andra tillgångar, till exempel en SQL-databas, en Azure BLOB-behållare eller en Analysis Services-modell.</td></tr></table>

### <a name="annotation-types"></a>Anteckningstyper
Anteckningstyper representerar typer av metadata som kan tilldelas till andra typer i katalogen.

<table>
<tr><td><b>Anteckningstypen (kapslade vynamn)</b></td><td><b>Ytterligare egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>

<tr><td>Beskrivning (”beskrivningar”)</td><td></td><td></td><td>Den här egenskapen innehåller en beskrivning för en tillgång. Varje användare i systemet kan lägga till egna beskrivning.  Bara den användaren kan redigera objektet beskrivning.  (Administratörer och tillgångsinformation ägare kan ta bort objektet beskrivning, men inte redigera den). Systemet har användarnas beskrivningar separat.  Därför är en matris med beskrivningar på varje tillgång (en för varje användare som har bidragit sina kunskaper om tillgången, förutom eventuellt en som innehåller information som härrör från datakällan).</td></tr>
<tr><td></td><td>description</td><td>Sträng</td><td>En kort beskrivning (2-3 rader) av tillgången</td></tr>

<tr><td>Taggen (”-taggar”)</td><td></td><td></td><td>Den här egenskapen definierar en tagg för en tillgång. Varje användare i systemet kan lägga till flera taggar för en tillgång.  Bara den användare som skapade taggen objekt kan redigeras.  (I administratörer och tillgångsinformation ägare kan det ta bort taggen objektet men inte redigera den). Systemet har användarnas taggar separat.  Därför är en matris med taggen objekt på varje tillgång.</td></tr>
<tr><td></td><td>Taggen</td><td>Sträng</td><td>En tagg som beskriver tillgången.</td></tr>

<tr><td>FriendlyName (”friendlyName”)</td><td></td><td></td><td>Den här egenskapen innehåller ett eget namn för en tillgång. FriendlyName är en singleton-anteckning - endast en FriendlyName kan läggas till en tillgång.  Användaren som skapade FriendlyName objekt kan redigera den. (Administratörer och tillgångsinformation ägare kan ta bort objektet FriendlyName, men inte redigera den). Systemet har separat användarnas egna namn.</td></tr>
<tr><td></td><td>friendlyName</td><td>Sträng</td><td>Ett eget namn för tillgången.</td></tr>

<tr><td>Schemat (”schema”)</td><td></td><td></td><td>Schemat beskriver strukturen i data.  Den visar attributnamn (kolumn, attribut, fält, etc.), typer och andra metadata.  Den här informationen är härledd från datakällan.  Schemat är en singleton-anteckning - bara ett Schema kan läggas till för en tillgång.</td></tr>
<tr><td></td><td>Kolumner</td><td>Kolumnen]</td><td>En matris med kolumnen objekt. De beskriver kolumnen med information som härrör från datakällan.</td></tr>

<tr><td>ColumnDescription (”columnDescriptions”)</td><td></td><td></td><td>Den här egenskapen innehåller en beskrivning för en kolumn.  Varje användare i systemet kan lägga till egna beskrivningar på flera kolumner (högst en per kolumn). Användaren som skapade ColumnDescription objekt kan redigeras.  (Administratörer och tillgångsinformation ägare kan ta bort objektet ColumnDescription, men inte redigera den). Systemet har dessa användare kolumnbeskrivningar separat.  Därför är en matris med ColumnDescription objekt på varje tillgång (en per kolumn för varje användare som har bidragit sina kunskaper om kolumnen förutom eventuellt en som innehåller information som härrör från datakällan).  ColumnDescription är löst kopplat till schemat så att den kan hämta synkroniserat. ColumnDescription kanske det beskriver en kolumn som inte längre finns i schemat.  Det är skrivaren för att synkronisera beskrivning och schema.  Datakällan kanske också kolumner beskrivningsinformation och de ytterligare ColumnDescription-objekt som skapas när du kör verktyget.</td></tr>
<tr><td></td><td>columnName</td><td>Sträng</td><td>Namnet på den här beskrivningen refererar till kolumnen.</td></tr>
<tr><td></td><td>description</td><td>Sträng</td><td>en kort beskrivning (2-3 rader) i kolumnen.</td></tr>

<tr><td>ColumnTag (”columnTags”)</td><td></td><td></td><td>Den här egenskapen innehåller en tagg för en kolumn. Varje användare i systemet kan lägga till flera taggar för en viss kolumn och kan lägga till taggar för flera kolumner. Användaren som skapade ColumnTag objekt kan redigeras. (Administratörer och tillgångsinformation ägare kan ta bort objektet ColumnTag, men inte redigera den). Systemet har dessa användare kolumnen taggar separat.  Därför är en matris med ColumnTag objekt på varje tillgång.  ColumnTag är löst kopplat till schemat så att den kan hämta synkroniserat. ColumnTag kanske det beskriver en kolumn som inte längre finns i schemat.  Det är skrivaren för att synkronisera kolumnen taggen och schema.</td></tr>
<tr><td></td><td>columnName</td><td>Sträng</td><td>Namnet på den här taggen refererar till kolumnen.</td></tr>
<tr><td></td><td>Taggen</td><td>Sträng</td><td>En tagg som beskriver kolumnen.</td></tr>

<tr><td>Expert (”experter”)</td><td></td><td></td><td>Den här egenskapen innehåller en användare anses vara en expert i datauppsättningen. De experter opinions(descriptions) bubblan överkant UX när med beskrivningar. Varje användare kan ange sina egna experter. Bara den användaren kan redigera objektet experter. (Administratörer och tillgångsinformation ägare kan ta bort Expert objekt, men inte redigera den).</td></tr>
<tr><td></td><td>Expert</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Förhandsgranska (”förhandsgranskningar”)</td><td></td><td></td><td>Förhandsgranskningen innehåller en ögonblicksbild av de översta 20 dataraderna för tillgången. Förhandsgranska endast vara meningsfullt för vissa typer av tillgångar (det klokt för tabellen men inte för mått).</td></tr>
<tr><td></td><td>Förhandsgranskning</td><td>objektet]</td><td>Matris med objekt som representerar en kolumn.  Varje objekt har en egenskapsmappning till en kolumn med ett värde för kolumnen för raden.</td></tr>

<tr><td>AccessInstruction (”accessInstructions”)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>Sträng</td><td>MIME-typ av innehållet.</td></tr>
<tr><td></td><td>Innehåll</td><td>Sträng</td><td>Instruktioner att få åtkomst till data tillgången. Innehållet kan vara en URL, en e-postadress eller en uppsättning instruktioner.</td></tr>

<tr><td>TableDataProfile (”tableDataProfiles”)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Antalet rader i datauppsättningen</td></tr>
<tr><td></td><td>Storlek</td><td>lång</td><td>Storlek i byte för datauppsättningen.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>Sträng</td><td>Schemat ändrades senast</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>Sträng</td><td>Datauppsättningen ändrades senast (data har lagts till, ändra, eller ta bort)</td></tr>

<tr><td>ColumnsDataProfile (”columnsDataProfiles”)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Kolumner</td></td><td>ColumnDataProfile]</td><td>En matris med kolumnen data profiler.</td></tr>

<tr><td>ColumnDataClassification (”columnDataClassifications”)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Sträng</td><td>Namnet på den här klassificeringen refererar till kolumnen.</td></tr>
<tr><td></td><td>Klassificering</td><td>Sträng</td><td>Klassificering av data i den här kolumnen.</td></tr>

<tr><td>Dokumentation (”dokumentation”)</td><td></td><td></td><td>En given tillgång kan ha endast en dokumentation som associeras med den.</td></tr>
<tr><td></td><td>mimeType</td><td>Sträng</td><td>MIME-typ av innehållet.</td></tr>
<tr><td></td><td>Innehåll</td><td>Sträng</td><td>Documentation-innehållet.</td></tr>

</table>

### <a name="common-types"></a>Vanliga
Vanliga datatyper som kan användas som typerna för egenskaper, men är inte objekt.

<table>
<tr><td><b>Gemensam typ.</b></td><td><b>Egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>SourceType</td><td>Sträng</td><td>Beskriver typ av datakälla.  Till exempel: SQLServer, Oracle-databas, osv.  </td></tr>
<tr><td></td><td>Objekttyp</td><td>Sträng</td><td>Beskriver typ av objekt i datakällan. Till exempel: tabell, visa för SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Protokollet</td><td>Sträng</td><td>Krävs. Beskriver ett protokoll som används för att kommunicera med datakällan. Till exempel: ”tds” för SQl-servern ”oracle” för Oracle, osv. Referera till [Referensspecifikation - DSL-strukturen för datakälla](data-catalog-dsr.md) lista över protokoll som stöds.</td></tr>
<tr><td></td><td>Adress</td><td>Ordlista<string, object></td><td>Krävs. Adressen är en uppsättning data som är specifika för det protokoll som används för att identifiera refererar till datakällan. Dessa data omfattar ett visst protokoll, vilket innebär att den är meningslös utan att känna till protokollet.</td></tr>
<tr><td></td><td>Autentisering</td><td>Sträng</td><td>Valfri. Autentiseringsschema som används för att kommunicera med datakällan. Exempel: windows, oauth osv.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Ordlista<string, object></td><td>Valfri. Mer information om hur du ansluter till en datakälla.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Serverdelen utför inte någon validering av angivna egenskaper mot AAD vid publicering.</td></tr>
<tr><td></td><td>UPN</td><td>Sträng</td><td>Användarens unika e-postadress. Måste anges om objekt-ID inte har angetts eller i samband med ”lastRegisteredBy”-egenskap, annars valfritt.</td></tr>
<tr><td></td><td>objekt-ID</td><td>GUID</td><td>Användaren eller säkerhetsgruppen grupp AAD-identitet. Valfri. Måste anges om UPN-namnet inte anges, annars valfritt.</td></tr>
<tr><td></td><td>Förnamn</td><td>Sträng</td><td>Förnamn för användare (i visningssyfte). Valfri. Endast giltig i kontexten för egenskapen ”lastRegisteredBy”. Kan inte anges när säkerhetsobjekt för ”roller”, ”behörigheter” och ”experter”.</td></tr>
<tr><td></td><td>Efternamn</td><td>Sträng</td><td>Efternamn för användaren (för visning). Valfri. Endast giltig i kontexten för egenskapen ”lastRegisteredBy”. Kan inte anges när säkerhetsobjekt för ”roller”, ”behörigheter” och ”experter”.</td></tr>

<tr><td>Kolumn</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namn</td><td>Sträng</td><td>Namnet på den kolumn eller attribut.</td></tr>
<tr><td></td><td>typ</td><td>Sträng</td><td>datatypen för kolumnen eller attribut. De tillåtna typerna är beroende av data sourceType för tillgången.  Endast en delmängd av typer stöds.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>Den tillåtna maxlängden för kolumnen eller attribut. Härleds från datakällan. Gäller endast för vissa typer av datakällor.</td></tr>
<tr><td></td><td>Precision</td><td>Mottagna byte</td><td>Precisionen för kolumnen eller attribut. Härleds från datakällan. Gäller endast för vissa typer av datakällor.</td></tr>
<tr><td></td><td>isNullable</td><td>Booleskt värde</td><td>Om kolumnen kan ha värdet null eller inte. Härleds från datakällan. Gäller endast för vissa typer av datakällor.</td></tr>
<tr><td></td><td>uttryck</td><td>Sträng</td><td>Om värdet är en beräknad kolumn, innehåller uttryck som representerar värdet. Härleds från datakällan. Gäller endast för vissa typer av datakällor.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>Sträng</td><td>Namnet på kolumnen</td></tr>
<tr><td></td><td>typ </td><td>Sträng</td><td>Typen för kolumnen</td></tr>
<tr><td></td><td>min. </td><td>Sträng</td><td>Det lägsta värdet i datauppsättningen</td></tr>
<tr><td></td><td>Max </td><td>Sträng</td><td>Det maximala värdet i datauppsättningen</td></tr>
<tr><td></td><td>genomsn. </td><td>dubbla</td><td>Genomsnittligt värde i datauppsättningen</td></tr>
<tr><td></td><td>StDev </td><td>dubbla</td><td>Standardavvikelsen för datauppsättningen</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Antal null-värden i datauppsättningen</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Antalet distinkta värden i datauppsättningen</td></tr>


</table>

## <a name="asset-identity"></a>Identitet för tillgångsinformation
Azure Data Catalog använder ”protokoll” och identitet egenskaper från egenskapsuppsättningen ”adress” för egenskapen DataSourceLocation ”dsl” för att generera identiteten för tillgången, som används för att hantera objekt i katalogen.
”Tds”-protokollet har till exempel identitet egenskaper ”server”, ”databas”, ”schema” och ”objekt”. Kombinationer av protokollet och identitetsegenskaperna används för att generera identiteten för SQL Server tabellen tillgången.
Azure Data Catalog innehåller flera inbyggda data käll-protokoll som nämns i [Referensspecifikation - DSL-strukturen för datakälla](data-catalog-dsr.md).
En uppsättning protokoll som stöds kan utökas genom programmering (finns i Data Catalog-REST API-referensen). Administratörer av katalogen kan registrera anpassade datakälla protokoll. I följande tabell beskrivs de egenskaper som behövs för att registrera ett anpassat protokoll.

### <a name="custom-data-source-protocol-specification"></a>Anpassade Protokollspecifikationen för datakälla
<table>
<tr><td><b>Typ</b></td><td><b>Egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namnområde</td><td>Sträng</td><td>Namnområdet för protokollet. Namespace måste vara mellan 1 och 255 tecken långt, innehåller en eller flera icke-tom delar avgränsade med punkt (.). Varje del måste vara mellan 1 och 255 tecken långt, börja med en bokstav och endast innehålla bokstäver och siffror.</td></tr>
<tr><td></td><td>namn</td><td>Sträng</td><td>Namnet på protokollen. Namnet måste vara mellan 1 och 255 tecken långt, börja med en bokstav och innehålla endast bokstäver, siffror och streck (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty]</td><td>Lista över identitetsegenskaper, måste innehålla minst en, men inga fler än 20 egenskaper. Till exempel: ”server”, ”databas”, ”schema”, ”objektet” är identitetsegenskaper för ”tds”-protokollet.</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet]</td><td>Lista över identitet anger. Definierar uppsättningar av identitetsegenskaper som representerar giltig tillgången identitet. Måste innehålla minst en, men inga fler än 20 uppsättningar. Exempel: {”server”, ”databas”, ”schema” och ”objekt”} är en identitet för ”tds”-protokollet som definierar identiteten för Sql Server-tabell tillgången.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namn</td><td>Sträng</td><td>Namnet på egenskapen. Namnet måste vara mellan 1 och 100 tecken, börja med en bokstav och får bara innehålla bokstäver och siffror.</td></tr>
<tr><td></td><td>typ</td><td>Sträng</td><td>Typ av egenskapen. Värden som stöds: ”bool”, boolean ”,” byte ”,” guid ”,” int ”,” heltal ”,” långa ”,” sträng ”,” url ”</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Anger om fallet ska ignoreras när du använder egenskapens värde. Kan bara anges för egenskaper av typen ”sträng”. Standardvärdet är false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool]</td><td>Anger om fallet ska ignoreras för varje del av den url-sökväg. Kan bara anges för egenskaper av typen ”url”. Standardvärdet är [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namn</td><td>Sträng</td><td>Ange namnet på identitet.</td></tr>
<tr><td></td><td>properties</td><td>String]</td><td>Listan över identitetsegenskaper inkluderas i den här identiteten anges. Det får inte innehålla dubbletter. Varje egenskap som refererar till identitet uppsättningen måste definieras i listan över ”identityProperties” av protokollet.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Roller och auktorisering
Microsoft Azure Data Catalog innehåller funktioner för auktorisering för CRUD-åtgärder på tillgångar och anteckningar.

## <a name="key-concepts"></a>Viktiga begrepp
Azure Data Catalog använder två mekanismer för godkännande:

* Rollbaserad auktorisering
* Behörighet-baserad auktorisering

### <a name="roles"></a>Roller
Det finns tre roller: **administratör**, **ägare**, och **deltagare**.  Varje roll har sitt omfång och rättigheter som sammanfattas i följande tabell.

<table><tr><td><b>Roll</b></td><td><b>Omfång</b></td><td><b>Rättigheter</b></td></tr><tr><td>Administratör</td><td>Katalogen (alla tillgångar/anteckningar i katalogen)</td><td>Läs Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Ägare</td><td>Varje enskilt objekt (rotobjekt)</td><td>Läs Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Deltagare</td><td>Varje enskild tillgång och anteckningar</td><td>Läs Update Delete ViewRoles Obs: alla rättigheter som har återkallats, Läs åt höger på objektet har återkallats från deltagaren</td></tr></table>

> [!NOTE]
> **Läs**, **uppdatering**, **ta bort**, **ViewRoles** rättigheter gäller för alla objekt (tillgång eller anteckningen) när **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** gäller endast roten tillgången.
> 
> **Ta bort** rättigheten för ett objekt och subitems eller ett objekt under den. Till exempel tar en tillgång också du bort alla anteckningar för tillgången.
> 
> 

### <a name="permissions"></a>Behörigheter
Behörigheten är lista med poster. Varje åtkomstkontrollpost tilldelar rättigheter till ett säkerhetsobjekt. Behörigheter kan bara anges för en tillgång (det vill säga rotobjekt) och gäller för tillgången och alla undermappar.

Under den **Azure Data Catalog** förhandsgranska endast **Läs** höger stöds i listan med behörigheter att aktivera scenariot för att begränsa synligheten för en tillgång.

Alla autentiserade användare har som standard **Läs** Högerklicka för alla objekt i katalogen om synlighet är begränsad till vilken uppsättning av säkerhetsobjekt i området behörigheter.

## <a name="rest-api"></a>REST API
**PLACERA** och **POST** visa objekt begär kan användas till att styra roller och behörigheter: förutom objektet nyttolast två egenskaper kan anges **roller** och **behörigheter**.

> [!NOTE]
> **behörigheter** gäller endast för ett rotobjekt.
> 
> **Ägare** gäller endast för ett rotobjekt roll.
> 
> Som standard när ett objekt skapas i katalogen dess **deltagare** är inställd på den för närvarande autentiserade användaren. Om objektet ska vara uppdateringsbar av alla, **deltagare** ska anges till &lt;alla&gt; särskilda säkerhetsobjekt i den **roller** egenskapen när artikeln publiceras (se följande exempel). **Deltagare** kan inte ändras och förblir densamma under livslängden för ett objekt (även **administratör** eller **ägare** har inte behörighet att ändra den **deltagare**). Det enda värde som stöds för den explicita inställningen av den **deltagare** är &lt;alla&gt;: **deltagare** endast kan en användare som skapade ett objekt eller &lt;alla&gt;.
> 
> 

### <a name="examples"></a>Exempel
**Ange deltagare till &lt;alla&gt; när du publicerar ett objekt.**
Särskilda säkerhetsobjekt &lt;alla&gt; har objectId ”00000000-0000-0000-0000-000000000201”.
  **POST** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Vissa http-klient-implementeringar kan automatiskt återutfärda begäranden som svar på en 302 från servern, men vanligtvis remsans auktorisering huvuden i begäran. Eftersom Authorization-huvud krävs för att göra förfrågningar till Azure Data Catalog, måste du kontrollera Authorization-huvud tillhandahålls fortfarande när återutfärda en begäran till en omdirigerings-plats som anges av Azure Data Catalog. Följande exempelkod visar den med hjälp av .NET HttpWebRequest-objektet.
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

  **Tilldela ägare och begränsar synligheten för en befintlig rotobjekt**: **PLACERA** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

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
> I PUT det inte krävs för att ange nyttolast för ett objekt i brödtext: PUT kan användas för att uppdatera bara roller och/eller behörigheter.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png
