---
title: Skriva uttryck för attributmappningar i Azure Active Directory | Microsoft Docs
description: Lär dig hur du använder uttrycksmappningar för att omvandla attributvärden till ett acceptabelt format vid automatisk etablering av objekt för SaaS-app i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e741e8d4d68c9862aaabffaccb86740a3e1e9b8a
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694166"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Skriva uttryck för attributmappningar i Azure Active Directory
När du konfigurerar etablering till ett SaaS-program, är en av typerna av attributmappningar som du kan ange mappningen för en uttryck. För dessa, måste du skriva ett skript-liknande uttryck som hjälper dig att omvandla dina användares data till format som kan användas mer för SaaS-program.

## <a name="syntax-overview"></a>Översikt över syntax
Syntaxen för uttryck för attributmappningar är påminner om Visual Basic för Applications (VBA).

* Hela uttrycket måste definieras när det gäller funktioner, som består av ett namn följt av argument inom parentes: <br>
  *FunctionName (`<<argument 1>>`,`<<argument N>>`)*
* Du kan kapsla funktioner i varandra. Exempel: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Du kan skicka tre olika typer av argument funktioner:
  
  1. Attribut måste omges av hakparenteser. Till exempel: [attributeName]
  2. Strängkonstanter måste vara inom dubbla citattecken. Exempel: "USA"
  3. Andra funktioner. Exempel: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* För strängkonstanter, om du behöver ett omvänt snedstreck (\) eller citattecken (”) i strängen är måste den föregås symbolen omvänt snedstreck (\). Exempel: "Företags namn: \\"Contoso\\" "

## <a name="list-of-functions"></a>Lista över funktioner
[Lägg till](#append) &nbsp; [](#formatdatetime) [](#join) FormatDateTime -anslutning&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [](#mid) Mid &nbsp; [](#normalizediacritics) [](#not) NormalizeDiacritics inte &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ErsättSelectUniqueValue&nbsp; [](#replace) &nbsp; &nbsp; [](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ SingleAppRoleAssignment](#singleapproleassignment) &nbsp; delaStripSpaces&nbsp; [](#stripspaces) [](#split)&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; &nbsp; &nbsp; VäxlaToLower&nbsp; [](#tolower) [](#switch) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper)

---
### <a name="append"></a>Lägg till
**Funktionen:**<br> Append(Source, suffix)

**Beskrivning:**<br> Tar ett strängvärde för källa och lägger till suffixet i slutet av den.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |Vanligtvis namnet på attributet från källobjektet. |
| **suffix** |Krävs |Sträng |Den sträng som du vill lägga till i slutet av värdet för datakällan. |

---
### <a name="formatdatetime"></a>formatDateTime
**Funktionen:**<br> FormatDateTime (källa, inputFormat, outputFormat)

**Beskrivning:**<br> Tar en datumsträng från ett format och konverterar den till ett annat format.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |Vanligtvis namnet på attributet från källobjektet. |
| **inputFormat** |Krävs |Sträng |Förväntade format för värdet för datakällan. Format som stöds, se [ https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Krävs |Sträng |Formatet för Utdatadatum. |

---
### <a name="join"></a>Slå ihop
**Funktionen:**<br> Ansluta till (avgränsare, källa1, källa2...)

**Beskrivning:**<br> JOIN() liknar Append(), förutom att det kan kombinera flera **källa** sträng värden till en sträng och varje värde skiljs åt av en **avgränsare** sträng.

Om ett av käll värdena är ett flervärdesattribut, kopplas alla värden i det attributet samman, avgränsade med avgränsning svärdet.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **avgränsare** |Krävs |Sträng |Strängen används för att avgränsa källvärdena när de sammanfogas till en sträng. Kan vara ”” om det krävs ingen avgränsare. |
| **källa1... källan** |Krävs, variabeln antal gånger |Sträng |Sträng värden kopplas. |

---
### <a name="mid"></a>Mid
**Funktionen:**<br> MID (källa, start, length)

**Beskrivning:**<br> Returnerar en understräng av värdet för datakällan. En understräng är en sträng som innehåller bara en del av tecken från Källsträngen.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |Vanligtvis namnet på attributet. |
| **start** |Krävs |heltal |Index i den **källa** sträng som var delsträngen ska börja. Första tecknet i strängen har index 1, andra tecknet ska ha index 2 och så vidare. |
| **Längd** |Krävs |heltal |Delsträngens längd. Om längden slutar utanför den **källa** sträng, returnerar funktionen delsträngen från **starta** indexet till slutet av **källa** sträng. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funktionen:**<br> NormalizeDiacritics(source)

**Beskrivning:**<br> Kräver ett strängargument. Returnerar strängen, men med eventuella diakritiska tecken ersätts med motsvarande icke-diakritiska tecken. Normalt används för att konvertera förnamn och efternamn som innehåller diakritiska tecken (accenttecken) i juridiska värden som kan användas i olika användaridentifierare, t.ex användarhuvudnamn SAM-kontonamn och e-postadresser.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng | Vanligt vis attributet förnamn eller efter namn. |

---
### <a name="not"></a>inte
**Funktionen:**<br> Not(Source)

**Beskrivning:**<br> Vänder booleskt värde för den **källa**. Om **källa** värdet är ”*SANT*”, returnerar ”*FALSKT*”. I annat fall returnerar ”*SANT*”.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Booleska sträng |Förväntade **käll** värden är "true" eller "false". |

---
### <a name="replace"></a>Ersätt
**Funktionen:**<br> Ersätt (källa, oldValue, regexPattern, regexGroupName, ersättningsvärde, replacementAttributeName, mall)

**Beskrivning:**<br>
Ersätter värden i en sträng. Den fungerar på olika sätt beroende på parametrarna som anges:

* När **oldValue** och **ersättningsvärde** tillhandahålls:
  
  * Ersätter alla förekomster av **OldValue** i **källan** med **replacementValue**
* När **oldValue** och **mall** tillhandahålls:
  
  * Ersätter alla förekomster av den **oldValue** i den **mall** med den **källa** värde
* När **regexPattern** och **replacementValue** anges:

  * Funktionen tillämpar **regexPattern** på **käll** strängen och du kan använda regex-gruppens namn för att skapa strängen för **replacementValue**
* När **regexPattern**, **regexGroupName**, **ersättningsvärde** tillhandahålls:
  
  * Funktionen tillämpar **regexPattern** på **käll** strängen och ersätter alla värden som matchar **regexGroupName** med **replacementValue**
* När **regexPattern**, **regexGroupName**, **replacementAttributeName** anges:
  
  * Om **källa** har inte något värde **källa** returneras
  * Om **källan** har ett värde tillämpar funktionen **regexPattern** på **käll** strängen och ersätter alla värden som matchar **RegexGroupName** med värdet som är kopplat till **replacementAttributeName**

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |Vanligt vis namnet på attributet från **källobjektet** . |
| **oldValue** |Valfri |Sträng |Värdet som ska ersättas i **källa** eller **mallen**. |
| **regexPattern** |Valfri |Sträng |Regex-mönster för det värde som ska ersättas i **källa**. Eller, när **replacementPropertyName** används, mönster för att extrahera värdet från **replacementPropertyName**. |
| **regexGroupName** |Valfri |Sträng |Namnet på gruppen i **regexPattern**. Endast när **replacementPropertyName** används kommer vi att extrahera värdet för den här gruppen som **replacementValue** från **replacementPropertyName**. |
| **Ersättningsvärde** |Valfri |Sträng |Nytt värde som ersätter gamla med. |
| **replacementAttributeName** |Valfri |Sträng |Namnet på attributet som ska användas för ersättnings värde |
| **mall** |Valfri |Sträng |När ett **mallnamn** anges söker vi efter **OldValue** i mallen och ersätter det med **käll** värde. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funktionen:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Beskrivning:**<br> Kräver minst två argument, som är unikt värde Genereringsregler definieras med hjälp av uttryck. Funktionen utvärderar varje regel och sedan kontrollerar värdet som genererats för unikhet i appen/målkatalogen. Det första unika värdet att hitta som kommer att returneras. Om alla värden redan finns i målet, posten ska hämta escrowed och orsaken hämtar loggas i granskningsloggarna. Det finns ingen övre gräns för antalet argument som kan anges.

> [!NOTE]
>1. Det här är en funktion på översta nivån, kan inte kapslas.
>2. Den här funktionen är endast avsedd att användas för skapande av posten. När du använder det med ett attribut, ange den **gäller mappning** egenskap **enbart vid objektskapande**.


**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uniqueValueRule1... uniqueValueRuleN** |Minst är 2 krävs, inte övre gräns |Sträng | Lista med regler för generering av unika värden som ska utvärderas. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funktionen:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beskrivning:**<br> Returnerar en enskild appRoleAssignment från listan över alla appRoleAssignments som har tilldelats till en användare för ett visst program. Den här funktionen krävs för att konvertera appRoleAssignments-objektet till en enda roll namn sträng. Observera att det bästa sättet är att se till att endast en appRoleAssignment är tilldelad en användare åt gången, och om flera roller tilldelas kan den returnerade roll strängen inte vara förutsägbar. 

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Krävs |Sträng |**[appRoleAssignments]**  objekt. |

---
### <a name="split"></a>Delat
**Funktionen:**<br> Dela (källa, avgränsare)

**Beskrivning:**<br> Delar upp en sträng i en mulit-matris med det angivna avgränsnings tecken.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |**källan** värde att uppdatera. |
| **avgränsare** |Obligatorisk |Sträng |Anger det tecken som ska användas för att dela strängen (exempel: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Funktionen:**<br> StripSpaces(source)

**Beskrivning:**<br> Tar bort alla blanksteg (””) tecken från strängen källa.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |**källan** värde att uppdatera. |

---
### <a name="switch"></a>Växel
**Funktionen:**<br> Växel (källa, standardvärde, key1, value1, key2, value2,...)

**Beskrivning:**<br> När **källa** värdet matchar en **nyckel**, returnerar **värdet** för som **nyckeln**. Om **källa** värdet matchar inte några nycklar, returnerar **defaultValue**.  **Nyckeln** och **värdet** parametrar måste komma i par. Funktionen förväntar alltid ett jämnt antal parametrar.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |**källan** värde att uppdatera. |
| **Standardvärde** |Valfri |Sträng |Standardvärde som ska användas när källan inte matchar några nycklar. Kan vara tom sträng (””). |
| **nyckel** |Krävs |Sträng |**Nyckeln** att jämföra **källa** värde med. |
| **värde** |Krävs |Sträng |Ersättningsvärdet för den **källa** matchar nyckeln. |

---
### <a name="tolower"></a>ToLower
**Funktionen:**<br> ToLower (källa, kultur)

**Beskrivning:**<br> Tar ett *käll* sträng värde och konverterar det till gemener med de angivna kultur reglerna. Om det inte finns någon angiven *kultur* information, används en invariant kultur.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |Vanligtvis namnet på attributet från källobjektet |
| **substrat** |Valfritt |Sträng |Formatet för kultur namnet baserat på RFC 4646 är *languagecode2-land/regioncode2*, där *languagecode2* är språk koden för två bokstäver och *land/regioncode2* är under kultur koden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver härledd från ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Funktionen:**<br> ToUpper (källa, kultur)

**Beskrivning:**<br> Tar ett *käll* sträng värde och konverterar det till versaler med de angivna kultur reglerna. Om det inte finns någon angiven *kultur* information, används en invariant kultur.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Sträng |Vanligtvis namnet på attributet från källobjektet. |
| **substrat** |Valfritt |Sträng |Formatet för kultur namnet baserat på RFC 4646 är *languagecode2-land/regioncode2*, där *languagecode2* är språk koden för två bokstäver och *land/regioncode2* är under kultur koden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver härledd från ISO 639-2.|

## <a name="examples"></a>Exempel
### <a name="strip-known-domain-name"></a>Remsans kända domännamn
Du måste ta bort ett känt domännamn från en användares e-post att hämta ett användarnamn. <br>
Till exempel om domänen är ”contoso.com”, kan du använda följande uttryck:

**Uttryck:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Exempel på indata / utdata:** <br>

* **INDATA** (e-post) ”:john.doe@contoso.com”
* **UTDATA**: ”john.doe”

### <a name="append-constant-suffix-to-user-name"></a>Lägg till konstant suffix till användarnamn
Om du använder en Salesforce-Sandbox kan du behöva lägga till en ytterligare suffix till alla användare innan du synkroniserar dem.

**Uttryck:** <br>
`Append([userPrincipalName], ".test")`

**Exempel indata/utdata:** <br>

* **INDATA**: (userPrincipalName) ”:John.Doe@contoso.com”
* **UTDATA**”:John.Doe@contoso.com.test”

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generera användaralias genom att sammanfoga delar av förnamn, efternamn
Du måste du generera en användare alias genom att först 3 bokstäverna i användarens förnamn och 5 första bokstäverna i användarens efternamn.

**Uttryck:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Exempel indata/utdata:** <br>

* **Mata in** (givenName): Anders
* **Mata in** (efter namn): Andersson
* **OUTPUT**:  "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Ta bort diakritiska tecken från en sträng
Du måste ersätta tecken med accenter med motsvarande tecken som inte innehåller accenttecken.

**Uttryck:** <br>
NormalizeDiacritics([givenName])

**Exempel indata/utdata:** <br>

* **Mata in** (givenName): "Zoë"
* **OUTPUT**:  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dela upp en sträng i en Multivärdes mat ris
Du måste ta en kommaavgränsad lista med strängar och dela upp dem i en matris som kan kopplas till ett flervärdesattribut som Salesforce: s PermissionSets-attribut. I det här exemplet har en lista över behörighets uppsättningar fyllts i extensionAttribute5 i Azure AD.

**Uttryck:** <br>
Dela ([extensionAttribute5], ",")

**Exempel indata/utdata:** <br>

* **Mata in** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Utdatadatum som en sträng i ett visst format
Du vill skicka datum till ett SaaS-program i ett visst format. <br>
Exempelvis kan du formatera datum för ServiceNow.

**Uttryck:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Exempel indata/utdata:**

* **Mata in** (extensionAttribute1): "20150123105347.1Z"
* **OUTPUT**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Ersätt ett värde baserat på fördefinierade uppsättning med alternativ

Du behöver definiera tidszonen för användaren baserat på delstatskod som lagras i Azure AD. <br>
Om delstatskod inte matchar någon av de fördefinierade alternativ, använder du standardvärdet ”Australien/Sydney”.

**Uttryck:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Exempel indata/utdata:**

* **Mata in** (tillstånd): "QLD"
* **OUTPUT**: "Australien/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Ersätt tecken med ett reguljärt uttryck
Du måste hitta tecken som matchar ett reguljärt uttrycks värde och ta bort dem.

**Uttryck:** <br>

Ersätt ([smek namn],, "[a-zA-Z_] *",, "",,)

**Exempel indata/utdata:**

* **Mata in** (smek namn: "john_doe72"
* **OUTPUT**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konvertera genererat userPrincipalName-värde (UPN) till gemener
I exemplet nedan genereras UPN-värdet genom att sammanfoga PreferredFirstName-och PreferredLastName-käll fälten och ToLower-funktionen fungerar på den genererade strängen för att konvertera alla tecken till gemener. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Exempel indata/utdata:**

* **Mata in** (PreferredFirstName): Anders
* **Mata in** (PreferredLastName): Son
* **UTDATA**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generera unikt värde för attributet userPrincipalName (UPN)
Baserat på användarens förnamn, mellannamn och efternamn, måste du generera ett värde för UPN-attributet och Sök efter dess unikhet i målkatalogen AD innan tilldelas värdet till UPN-attributet.

**Uttryck:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com")
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Exempel indata/utdata:**

* **Mata in** (PreferredFirstName): Anders
* **Mata in** (PreferredLastName): Son
* **UTDATA**”:John.Smith@contoso.com” om UPN-värdet för John.Smith@contoso.com inte redan finns i katalogen
* **UTDATA**”:J.Smith@contoso.com” om UPN-värdet för John.Smith@contoso.com finns redan i katalogen
* **UTDATA**”:Jo.Smith@contoso.com” om ovanstående två UPN-värden som redan finns i katalogen

## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera användaren etablering/avetablering för SaaS-appar](user-provisioning.md)
* [Anpassa attributmappningar för etableringen av användare](customize-application-attributes.md)
* [Omfångsfilter för etableringen av användare](define-conditional-rules-for-provisioning-user-accounts.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md)
* [Kontoetableringsmeddelanden](user-provisioning.md)
* [Lista över guider om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
