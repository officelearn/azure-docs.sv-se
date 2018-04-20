---
title: Skriva uttryck för attributmappning i Azure Active Directory | Microsoft Docs
description: Lär dig mer om att använda uttryck mappningar för att omvandla attributvärden till ett acceptabelt format vid automatisk etablering med SaaS-app i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: b13c51cd-1bea-4e5e-9791-5d951a518943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: f1cf83044eb4f001ba341cabd0771b267c3f996d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Skriva uttryck för attributmappning i Azure Active Directory
När du konfigurerar etablering till ett SaaS-program, är en av typerna av attributmappning som du kan ange mappningen för en uttryck. Du måste skriva ett skript-liknande uttryck som gör att du kan omvandla användarnas data i format som är mer godkänd för SaaS-program för dessa.

## <a name="syntax-overview"></a>Syntax: översikt
Syntax för uttryck för attributmappning är påminner om Visual Basic för Applications (VBA)-funktioner.

* Uttrycket måste definieras vad gäller funktioner, som består av ett namn följt av argument inom parentes: <br>
  *FunctionName (<< argument 1 >> <<argument N>>)*
* Du kan kapsla funktioner i varandra. Exempel: <br> *FunctionOne (FunctionTwo (<<argument1>>))*
* Du kan skicka tre olika typer av argument till funktioner:
  
  1. Attribut måste stå inom klamrar kvadratisk. Exempel: [attributeName]
  2. Sträng som måste omges av dubbla citattecken. Till exempel: ”USA”
  3. Andra funktioner. Till exempel: FunctionOne (<<argument1>>, FunctionTwo (<<argument2>>))
* För strängkonstanter, om du behöver ett omvänt snedstreck (\) eller citattecken (”) i en sträng, måste det föregås av omvänt snedstreck (\) symbolen. Till exempel ”: Företagsnamn: \"Contoso\"”

## <a name="list-of-functions"></a>Lista över funktioner
[Lägg till](#append) &nbsp; &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; &nbsp; [ansluta](#join) &nbsp; &nbsp; &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; [inte](#not) &nbsp; &nbsp; &nbsp; &nbsp; [Ersätta](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [växel](#switch)

- - -
### <a name="append"></a>Lägg till
**Funktionen:**<br> Append(Source, suffix)

**Beskrivning:**<br> Tar ett strängvärde för källa och lägger till suffixet i slutet av den.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |Vanligtvis namnet på attributet från källobjektet |
| **suffix** |Krävs |Sträng |Strängen som du vill lägga till i slutet av värdet för källa. |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Funktionen:**<br> FormatDateTime (källa, inputFormat, outputFormat)

**Beskrivning:**<br> Tar en datumsträng från ett format och konverterar den till ett annat format.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |Vanligtvis namnet på attributet från källobjektet. |
| **inputFormat** |Krävs |Sträng |Förväntade format för källvärdet. Format som stöds, se [ http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Krävs |Sträng |Format för Utdatadatum. |

- - -
### <a name="join"></a>Slå ihop
**Funktionen:**<br> Ansluta till (avgränsare, källa1, källa2...)

**Beskrivning:**<br> JOIN() liknar Append(), förutom att den kan kombinera flera **källa** sträng värden till en sträng, och varje värde skiljs åt av en **avgränsare** sträng.

Om något av källvärden är ett attribut med flera värden och varje värde i attributet ska anslutas tillsammans, avgränsade värdet avgränsare.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **avgränsare** |Krävs |Sträng |Sträng som används för att avgränsa källvärden när de sammanfogas till en sträng. Kan vara ”” om ingen avgränsare krävs. |
| ** källa1... Källan ** |Obligatoriska variabeln antal gånger |Sträng |Strängen värden kopplas ihop. |

- - -
### <a name="mid"></a>Mid
**Funktionen:**<br> MID (källa, start, length)

**Beskrivning:**<br> Returnerar en understräng av källvärdet. En understräng är en sträng som innehåller endast en del av tecken från Källsträngen.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |Vanligtvis attributets namn. |
| **start** |Krävs |heltal |Index i den **källa** strängen där delsträngen ska starta. Första tecknet i strängen har index 1, andra tecknet ska ha index 2 och så vidare. |
| **Längd** |Krävs |heltal |Längden på delsträngen. Om längden slutar utanför den **källa** sträng, funktionen returnerar delsträngen från **starta** indexet till slutet av **källa** sträng. |

- - -
### <a name="not"></a>inte
**Funktionen:**<br> Not(Source)

**Beskrivning:**<br> Vänder booleskt värde för den **källa**. Om **källa** värdet är ”*SANT*”, returnerar ”*FALSKT*”. Annars returnerar ”*SANT*”.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Booleskt sträng |Förväntade **källa** värden är ”True” eller ”False”... |

- - -
### <a name="replace"></a>Ersätt
**Funktionen:**<br> Ersätt (källa, oldValue, regexPattern, regexGroupName, ersättningsvärde, replacementAttributeName, mall)

**Beskrivning:**<br>
Ersätter värden i en sträng. Den fungerar på olika sätt beroende på de angivna parametrarna:

* När **oldValue** och **ersättningsvärde** tillhandahålls:
  
  * Ersätter alla förekomster av oldValue i källan med ersättningsvärde
* När **oldValue** och **mallen** tillhandahålls:
  
  * Ersätter alla förekomster av de **oldValue** i den **mallen** med den **källa** värde
* När **regexPattern**, **regexGroupName**, **ersättningsvärde** tillhandahålls:
  
  * Ersätter alla värden som matchar oldValueRegexPattern i Källsträngen med ersättningsvärde
* När **regexPattern**, **regexGroupName**, **replacementPropertyName** tillhandahålls:
  
  * Om **källa** har inte något värde **källa** returneras
  * Om **källa** har ett värde, använder **regexPattern** och **regexGroupName** att extrahera ersättningsvärde från egenskapen med **replacementPropertyName** . Ersättningsvärde returneras som ett resultat

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |Vanligtvis namnet på attributet från källobjektet. |
| **oldValue** |Valfri |Sträng |Värdet som ska ersättas i **källa** eller **mallen**. |
| **regexPattern** |Valfri |Sträng |Regex-mönster för värdet som ska ersättas i **källa**. Eller, om replacementPropertyName används mönster för att hämta värdet från egendom. |
| **regexGroupName** |Valfri |Sträng |Namnet på gruppen i **regexPattern**. Endast när replacementPropertyName används, kommer vi extrahera värdet för den här gruppen som ersättningsvärde från egendom. |
| **Ersättningsvärde** |Valfri |Sträng |Nytt värde som ska ersätta gamla med. |
| **replacementAttributeName** |Valfri |Sträng |Namnet på attributet som ska användas för ersättningsvärde, när datakällan har inte något värde. |
| **mallen** |Valfri |Sträng |När **mallen** värde har angetts, kommer vi att leta efter **oldValue** i mallen och Ersätt den med källvärdet. |

- - -
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funktionen:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beskrivning:**<br> Returnerar en enda appRoleAssignment från listan över alla appRoleAssignments som tilldelats en användare för ett visst program. Den här funktionen krävs för att konvertera appRoleAssignments-objekt till en enda roll namnsträngen. Observera att det bästa sättet är att se till att endast en appRoleAssignment tilldelas en användare åt gången och om flera roller har tilldelats rollen-sträng returnerades inte förutsägbart.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Krävs |Sträng |**[appRoleAssignments]**  objekt. |

- - -
### <a name="stripspaces"></a>StripSpaces
**Funktionen:**<br> StripSpaces(source)

**Beskrivning:**<br> Tar bort alla blanksteg (””) tecken från Källsträngen.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |**Källan** värde att uppdatera. |

- - -
### <a name="switch"></a>Växel
**Funktionen:**<br> Växel (källa, defaultValue, key1, värde1, key2, värde2,...)

**Beskrivning:**<br> När **källa** värdet matchar en **nyckeln**, returnerar **värdet** för som **nyckeln**. Om **källa** värdet matchar inte några nycklar, returnerar **defaultValue**.  **Nyckeln** och **värdet** parametrar måste alltid komma parvis. Alltid förväntar ett jämnt antal parametrar.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |**Källan** värde att uppdatera. |
| **Standardvärde** |Valfri |Sträng |Standardvärde som ska användas när datakällan inte matchar några nycklar. Kan vara en tom sträng (””). |
| **Nyckel** |Krävs |Sträng |**Nyckeln** att jämföra **källa** värde med. |
| **värde** |Krävs |Sträng |Ersättningsvärde för den **källa** matchade nyckel. |

## <a name="examples"></a>Exempel
### <a name="strip-known-domain-name"></a>Remsans kända domännamn
Du behöver ett känt domännamn från en användares e-post för att erhålla ett användarnamn för remsans. <br>
Till exempel om domänen är ”contoso.com”, kan du använda följande uttryck:

**Uttryck:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Exempel på indata / utdata:** <br>

* **INDATA** (e) ”:john.doe@contoso.com”
* **UTDATA**: ”john.doe”

### <a name="append-constant-suffix-to-user-name"></a>Lägg till konstant suffix användarnamn
Om du använder en Salesforce Sandbox, kan du behöva lägga till ett ytterligare suffix till alla användare innan du synkroniserar dem.

**Uttryck:** <br>
`Append([userPrincipalName], ".test"))`

**I/o-exempel:** <br>

* **INDATA**: (userPrincipalName) ”:John.Doe@contoso.com”
* **UTDATA**”:John.Doe@contoso.com.test”

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Skapa användaralias genom att sammanbinda delar av för- och efternamn
Du måste skapa en användare alias genom att först 3 bokstäver i användarens förnamn och 5 första bokstäver i användarens efternamn.

**Uttryck:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**I/o-exempel:** <br>

* **INDATA** (givenName): ”John”
* **INDATA** (efternamn): ”Berg”
* **UTDATA**: ”JohDoe”

### <a name="remove-diacritics-from-a-string-and-convert-to-lowercase"></a>Ta bort diakritiska tecken från en sträng och konvertera till gemener
Du måste ta bort specialtecknen från en sträng och konvertera versaler till gemener.

**Uttryck:** <br>
`Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace([givenName], , "([Øø])", , "oe", , ), , "[Ææ]", , "ae", , ), , "([äãàâãåáąÄÃÀÂÃÅÁĄA])", , "a", , ), , "([B])", , "b", , ), , "([CçčćÇČĆ])", , "c", , ), , "([ďĎD])", , "d", , ), , "([ëèéêęěËÈÉÊĘĚE])", , "e", , ), , "([F])", , "f", , ), , "([G])", , "g", , ), , "([H])", , "h", , ), , "([ïîìíÏÎÌÍI])", , "i", , ), , "([J])", , "j", , ), , "([K])", , "k", , ), , "([ľłŁĽL])", , "l", , ), , "([M])", , "m", , ), , "([ñńňÑŃŇN])", , "n", , ), , "([öòőõôóÖÒŐÕÔÓO])", , "o", , ), , "([P])", , "p", , ), , "([Q])", , "q", , ), , "([řŘR])", , "r", , ), , "([ßšśŠŚS])", , "s", , ), , "([TŤť])", , "t", , ), , "([üùûúůűÜÙÛÚŮŰU])", , "u", , ), , "([V])", , "v", , ), , "([W])", , "w", , ), , "([ýÿýŸÝY])", , "y", , ), , "([źžżŹŽŻZ])", , "z", , ), " ", , , "", , )`

**I/o-exempel:** <br>

* **INDATA** (givenName): ”Zoë”
* **UTDATA**: ”zoe”

### <a name="output-date-as-a-string-in-a-certain-format"></a>Utdatadatum som en sträng i ett visst format
Vill du skicka datum till ett SaaS-program i ett visst format. <br>
Till exempel vill att formatera datum för ServiceNow.

**Uttryck:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**I/o-exempel:**

* **INDATA** (extensionAttribute1): ”20150123105347.1Z”
* **UTDATA**: ”2015-01-23”

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Ersätt ett värde baserat på fördefinierade uppsättning alternativ
Du måste definiera tidszonen för användaren baserat på statuskod som lagras i Azure AD. <br>
Om tillståndet koden inte matchar någon av de fördefinierade alternativ, använder du standardvärdet för ”Australien/Sydney”.

**Uttryck:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**I/o-exempel:**

* **INDATA** (tillstånd): ”QLD”
* **UTDATA**: ”Australien/Brisbane”

## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Automatisera användaren etablering/avetablering för SaaS-appar](active-directory-saas-app-provisioning.md)
* [Anpassa attributmappning för Användaretablering](active-directory-saas-customizing-attribute-mappings.md)
* [Omfångsfilter för Användaretablering](active-directory-saas-scoping-filters.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](active-directory-scim-provisioning.md)
* [Kontot etablering meddelanden](active-directory-saas-account-provisioning-notifications.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](active-directory-saas-tutorial-list.md)

