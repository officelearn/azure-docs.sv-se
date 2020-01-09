---
title: Skriva uttryck för mappningar av attribut i Azure AD
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
ms.openlocfilehash: 93b8387d4453a3d83bcce55c739548d914545f2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430070"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Skriva uttryck för attributmappningar i Azure Active Directory
När du konfigurerar etablering till ett SaaS-program, är en av typerna av attributmappningar som du kan ange mappningen för en uttryck. För dessa, måste du skriva ett skript-liknande uttryck som hjälper dig att omvandla dina användares data till format som kan användas mer för SaaS-program.

## <a name="syntax-overview"></a>Översikt över syntax
Syntaxen för uttryck för attributmappningar är påminner om Visual Basic för Applications (VBA).

* Hela uttrycket måste definieras när det gäller funktioner, som består av ett namn följt av argument inom parentes: <br>
  *FunctionName (`<<argument 1>>`,`<<argument N>>`)*
* Du kan kapsla funktioner i varandra. Ett exempel: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Du kan skicka tre olika typer av argument funktioner:
  
  1. Attribut måste omges av hakparenteser. Till exempel: [attributeName]
  2. Strängkonstanter måste vara inom dubbla citattecken. Till exempel: ”USA”
  3. Andra funktioner. Till exempel: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* För strängkonstanter, om du behöver ett omvänt snedstreck (\) eller citattecken (”) i strängen är måste den föregås symbolen omvänt snedstreck (\). Exempel: "företags namn: \\" contoso\\""

## <a name="list-of-functions"></a>Lista över funktioner
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Lägg till
**Funktionen:**<br> Append(Source, suffix)

**Beskrivning:**<br> Tar ett strängvärde för källa och lägger till suffixet i slutet av den.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String |Vanligtvis namnet på attributet från källobjektet. |
| **suffix** |Krävs |String |Den sträng som du vill lägga till i slutet av värdet för datakällan. |

---
### <a name="bitand"></a>BitAnd
**Funktionen:**<br> BitAnd (värde1, värde2)

**Beskrivning:**<br> Den här funktionen konverterar båda parametrarna till den binära representationen och anger en bit till:

0 – om en eller båda av motsvarande bitar i värde1 och värde2 är 0                                                  
1 – om båda motsvarande bitar är 1.                                    

Med andra ord returneras 0 i samtliga fall, förutom när motsvarande bitar i båda parametrarna är 1.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **value1** |Krävs |num |Numeriskt värde som ska AND'ed med värde2|
| **värde2** |Krävs |num |Numeriskt värde som ska AND'ed med värde1|

**Exempel:**<br>
BitAnd (& HF, & HF7)                                                                                
11110111 och 00000111 = 00000111 så BitAnd returnerar 7, det binära värdet 00000111

---
### <a name="cbool"></a>CBool
**Funktionen:**<br> CBool (uttryck)

**Beskrivning:**<br> CBool returnerar ett booleskt värde baserat på det utvärderade uttrycket. Om uttrycket utvärderas till ett värde som inte är noll returnerar CBool True, annars returneras FALSKT..

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uttryck** |Krävs | expression | Ett giltigt uttryck |

**Exempel:**<br>
CBool ([Attribute1] = [Attribute2])                                                                    
Returnerar true om båda attributen har samma värde.

---
### <a name="coalesce"></a>Coalesce
**Funktionen:**<br> Sammanslagning (source1, SOURCE2,..., defaultValue)

**Beskrivning:**<br> Returnerar det första käll värde som inte är NULL. Om alla argument är NULL och defaultValue finns returneras defaultValue. Om alla argument är NULL och defaultValue inte finns, returnerar sammanslagningen NULL.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källa1... källan** | Krävs | String |Obligatoriskt, variabel antal gånger. Vanligtvis namnet på attributet från källobjektet. |
| **Standardvärde** | Valfritt | String | Standardvärdet som ska användas när alla käll värden är NULL. Kan vara tom sträng (””).

---
### <a name="converttobase64"></a>ConvertToBase64
**Funktionen:**<br> ConvertToBase64 (källa)

**Beskrivning:**<br> Funktionen ConvertToBase64 konverterar en sträng till en Unicode base64-sträng.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String |Sträng som ska konverteras till bas 64|

**Exempel:**<br>
ConvertToBase64 ("Hello World!")                                                                                                        
Returnerar "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funktionen:**<br> ConvertToUTF8Hex (källa)

**Beskrivning:**<br> Funktionen ConvertToUTF8Hex konverterar en sträng till ett hexadecimalt värde för UTF8-kodning.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String |Sträng som ska konverteras till UTF8 hex|

**Exempel:**<br>
ConvertToUTF8Hex ("Hello World!")                                                                                                         
Returnerar 48656C6C6F20776F726C6421

---
### <a name="count"></a>Antal
**Funktionen:**<br> Count (attribut)

**Beskrivning:**<br> Funktionen COUNT returnerar antalet element i ett multi-värde-attribut

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **basattributet** |Krävs |-attribut |Ett flervärdesattribut som innehåller element som räknas|

---
### <a name="cstr"></a>CStr
**Funktionen:**<br> CStr (värde)

**Beskrivning:**<br> Funktionen CStr konverterar ett värde till en sträng data typ.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **värde** |Krävs | numerisk, referens eller boolesk | Kan vara ett numeriskt värde, ett referens-eller Boolean-attribut. |

**Exempel:**<br>
CStr ([DN])                                                            
Returnerar "CN = Johan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Funktionen:**<br> DateFromNum (värde)

**Beskrivning:**<br> Funktionen DateFromNum konverterar ett värde i ADs datum format till en DateTime-typ.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **värde** |Krävs | Datum | AD-datum som ska konverteras till DateTime-typ |

**Exempel:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
Returnerar en DateTime som representerar 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>formatDateTime
**Funktionen:**<br> FormatDateTime (källa, inputFormat, outputFormat)

**Beskrivning:**<br> Tar en datumsträng från ett format och konverterar den till ett annat format.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String |Vanligtvis namnet på attributet från källobjektet. |
| **inputFormat** |Krävs |String |Förväntade format för värdet för datakällan. Format som stöds, se [ https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Krävs |String |Formatet för Utdatadatum. |

---
### <a name="guid"></a>GUID
**Funktionen:**<br> GUID ()

**Beskrivning:**<br> Funktions-GUID genererar ett nytt slumpmässigt GUID

---
### <a name="instr"></a>InStr
**Funktionen:**<br> InStr (värde1, värde2, start, compareType)

**Beskrivning:**<br> Funktionen InStr söker efter den första förekomsten av en del sträng i en sträng

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **value1** |Krävs |String |Sträng som ska genomsökas |
| **värde2** |Krävs |String |Sträng som ska hittas |
| **start** |Valfritt |Integer |Start position för att hitta del strängen|
| **compareType** |Valfritt |Enum |Kan vara vbTextCompare eller vbBinaryCompare |

**Exempel:**<br>
InStr ("Quick Jansson Fox", "snabb")                                                                             
Evalues till 5

InStr ("repeterad", "e", 3, vbBinaryCompare)                                                                                  
Utvärderas till 7

---
### <a name="isnull"></a>IsNull
**Funktionen:**<br> IsNull (uttryck)

**Beskrivning:**<br> Om uttrycket utvärderas till null returnerar funktionen IsNull True. För ett-attribut uttrycks ett null-värde av frånvaron av attributet.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uttryck** |Krävs |expression |Uttryck som ska utvärderas |

**Exempel:**<br>
IsNull ([displayName])                                                                                                
Returnerar sant om attributet inte finns

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funktionen:**<br> IsNullOrEmpty (uttryck)

**Beskrivning:**<br> Om uttrycket är null eller en tom sträng returnerar funktionen IsNullOrEmpty värdet true. För ett-attribut utvärderar detta till sant om attributet saknas eller finns, men är en tom sträng.
Inversen till den här funktionen heter IsPresent.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uttryck** |Krävs |expression |Uttryck som ska utvärderas |

**Exempel:**<br>
IsNullOrEmpty ([displayName])                                               
Returnerar sant om attributet inte finns eller är en tom sträng

---
### <a name="ispresent"></a>IsPresent
**Funktionen:**<br> IsNullOrEmpty (uttryck)

**Beskrivning:**<br> Om uttrycket utvärderas till en sträng som inte är null och inte är tomt, returnerar funktionen IsPresent True. Inversen till den här funktionen heter IsNullOrEmpty.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uttryck** |Krävs |expression |Uttryck som ska utvärderas |

**Exempel:**<br>
Switch (IsPresent ([directManager]), [directManager], IsPresent ([skiplevelManager]), [skiplevelManager], IsPresent ([Director]), [Director])

---
### <a name="isstring"></a>IsString
**Funktionen:**<br> IsString (uttryck)

**Beskrivning:**<br> Om uttrycket kan utvärderas till en sträng typ, utvärderar funktionen IsString till true.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uttryck** |Krävs |expression |Uttryck som ska utvärderas |

---
### <a name="item"></a>Objekt
**Funktionen:**<br> Objekt (attribut, index)

**Beskrivning:**<br> Funktionen item returnerar ett objekt från en multi-valueion String/Attribute.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **basattributet** |Krävs |Attribut |Multi-Value-attribut som ska genomsökas |
| **index** |Krävs |Integer | Index till ett objekt i en multi-Value-sträng|

**Exempel:**<br>
Objekt ([proxyAddresses], 1)

---
### <a name="join"></a>Slå ihop
**Funktionen:**<br> Ansluta till (avgränsare, källa1, källa2...)

**Beskrivning:**<br> JOIN() liknar Append(), förutom att det kan kombinera flera **källa** sträng värden till en sträng och varje värde skiljs åt av en **avgränsare** sträng.

Om ett av käll värdena är ett flervärdesattribut, kopplas alla värden i det attributet samman, avgränsade med avgränsning svärdet.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **avgränsare** |Krävs |String |Strängen används för att avgränsa källvärdena när de sammanfogas till en sträng. Kan vara ”” om det krävs ingen avgränsare. |
| **källa1... källan** |Krävs, variabeln antal gånger |String |Sträng värden kopplas. |

---
### <a name="left"></a>Vänster
**Funktionen:**<br> Left (sträng, NumChars)

**Beskrivning:**<br> Funktionen Left returnerar ett angivet antal tecken från vänster i en sträng. Om numChars = 0 returneras en tom sträng.
Om numChars < 0, returnerar du Indatasträngen.
Om strängen är null returneras en tom sträng.
Om strängen innehåller färre tecken än det tal som anges i numChars returneras en sträng som är identisk med sträng (dvs. med alla tecken i parameter 1).

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Sträng** |Krävs |Attribut | Strängen att returnera tecken från |
| **NumChars** |Krävs |Integer | Ett tal som identifierar antalet tecken som ska returneras från början (vänster) av sträng|

**Exempel:**<br>
Left ("John berg", 3)                                                            
Returnerar "Joh"

---
### <a name="mid"></a>Mid
**Funktionen:**<br> MID (källa, start, length)

**Beskrivning:**<br> Returnerar en understräng av värdet för datakällan. En understräng är en sträng som innehåller bara en del av tecken från Källsträngen.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String |Vanligtvis namnet på attributet. |
| **start** |Krävs |heltal |Index i den **källa** sträng som var delsträngen ska börja. Första tecknet i strängen har index 1, andra tecknet ska ha index 2 och så vidare. |
| **Längd** |Krävs |heltal |Delsträngens längd. Om längden slutar utanför den **källa** sträng, returnerar funktionen delsträngen från **starta** indexet till slutet av **källa** sträng. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funktionen:**<br> NormalizeDiacritics(source)

**Beskrivning:**<br> Kräver ett strängargument. Returnerar strängen, men med eventuella diakritiska tecken ersätts med motsvarande icke-diakritiska tecken. Normalt används för att konvertera förnamn och efternamn som innehåller diakritiska tecken (accenttecken) i juridiska värden som kan användas i olika användaridentifierare, t.ex användarhuvudnamn SAM-kontonamn och e-postadresser.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String | Vanligt vis attributet förnamn eller efter namn. |

---
### <a name="not"></a>Inte
**Funktionen:**<br> Not(Source)

**Beskrivning:**<br> Vänder booleskt värde för den **källa**. Om **källa** värdet är ”*SANT*”, returnerar ”*FALSKT*”. I annat fall returnerar ”*SANT*”.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |Booleska sträng |Förväntade **käll** värden är "true" eller "false". |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Funktionen:**<br> RemoveDuplicates (attribut)

**Beskrivning:**<br> Funktionen RemoveDuplicates använder en sträng med flera värden och ser till att varje värde är unikt.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **basattributet** |Krävs |Multi-Value-attribut |Multi-Value-attribut som ska ha dubbletter borttagna|

**Exempel:**<br>
RemoveDuplicates ([proxyAddresses])                                                                                                       
Returnerar ett sanerat proxyAddress-attribut där alla dubblettvärden har tagits bort

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
| **Källa** |Krävs |String |Vanligt vis namnet på attributet från **källobjektet** . |
| **oldValue** |Valfritt |String |Värdet som ska ersättas i **källa** eller **mallen**. |
| **regexPattern** |Valfritt |String |Regex-mönster för det värde som ska ersättas i **källa**. Eller, när **replacementPropertyName** används, mönster för att extrahera värdet från **replacementPropertyName**. |
| **regexGroupName** |Valfritt |String |Namnet på gruppen i **regexPattern**. Endast när **replacementPropertyName** används kommer vi att extrahera värdet för den här gruppen som **replacementValue** från **replacementPropertyName**. |
| **Ersättningsvärde** |Valfritt |String |Nytt värde som ersätter gamla med. |
| **replacementAttributeName** |Valfritt |String |Namnet på attributet som ska användas för ersättnings värde |
| **mall** |Valfritt |String |När ett **mallnamn** anges söker vi efter **OldValue** i mallen och ersätter det med **käll** värde. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funktionen:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Beskrivning:**<br> Kräver minst två argument, som är unikt värde Genereringsregler definieras med hjälp av uttryck. Funktionen utvärderar varje regel och sedan kontrollerar värdet som genererats för unikhet i appen/målkatalogen. Det första unika värdet att hitta som kommer att returneras. Om alla värden redan finns i målet, posten ska hämta escrowed och orsaken hämtar loggas i granskningsloggarna. Det finns ingen övre gräns för antalet argument som kan anges.

> [!NOTE]
> - Det här är en funktion på översta nivån, kan inte kapslas.
> - Den här funktionen kan inte tillämpas på attribut som har en matchande prioritet.  
> - Den här funktionen är endast avsedd att användas för skapande av posten. När du använder det med ett attribut, ange den **gäller mappning** egenskap **enbart vid objektskapande**.
> - Den här funktionen stöds för närvarande endast för "arbets dag för Active Directory användar etablering". Det kan inte användas med andra etablerings program. 


**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uniqueValueRule1... uniqueValueRuleN** |Minst är 2 krävs, inte övre gräns |String | Lista med regler för generering av unika värden som ska utvärderas. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funktionen:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beskrivning:**<br> Returnerar en enskild appRoleAssignment från listan över alla appRoleAssignments som har tilldelats till en användare för ett visst program. Den här funktionen krävs för att konvertera appRoleAssignments-objektet till en enda roll namn sträng. Observera att det bästa sättet är att se till att endast en appRoleAssignment är tilldelad en användare åt gången, och om flera roller tilldelas kan den returnerade roll strängen inte vara förutsägbar. 

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Krävs |String |**[appRoleAssignments]**  objekt. |

---
### <a name="split"></a>Dela
**Funktionen:**<br> Dela (källa, avgränsare)

**Beskrivning:**<br> Delar upp en sträng i en Multivärdes mat ris med hjälp av det angivna avgränsnings tecken.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String |**källan** värde att uppdatera. |
| **avgränsare** |Krävs |String |Anger det tecken som ska användas för att dela strängen (exempel: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Funktionen:**<br> StripSpaces(source)

**Beskrivning:**<br> Tar bort alla blanksteg (””) tecken från strängen källa.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String |**källan** värde att uppdatera. |

---
### <a name="switch"></a>Växel
**Funktionen:**<br> Växel (källa, standardvärde, key1, value1, key2, value2,...)

**Beskrivning:**<br> När **källa** värdet matchar en **nyckel**, returnerar **värdet** för som **nyckeln**. Om **källa** värdet matchar inte några nycklar, returnerar **defaultValue**.  **Nyckeln** och **värdet** parametrar måste komma i par. Funktionen förväntar alltid ett jämnt antal parametrar. Funktionen ska inte användas för referensbaserade attribut som chef. 

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String |**källan** värde att uppdatera. |
| **Standardvärde** |Valfritt |String |Standardvärde som ska användas när källan inte matchar några nycklar. Kan vara tom sträng (””). |
| **nyckel** |Krävs |String |**Nyckeln** att jämföra **källa** värde med. |
| **värde** |Krävs |String |Ersättningsvärdet för den **källa** matchar nyckeln. |

---
### <a name="tolower"></a>ToLower
**Funktionen:**<br> ToLower (källa, kultur)

**Beskrivning:**<br> Tar ett *käll* sträng värde och konverterar det till gemener med de angivna kultur reglerna. Om det inte finns någon angiven *kultur* information, används en invariant kultur.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String |Vanligtvis namnet på attributet från källobjektet |
| **substrat** |Valfritt |String |Formatet för kultur namnet baserat på RFC 4646 är *languagecode2-land/regioncode2*, där *languagecode2* är språk koden för två bokstäver och *land/regioncode2* är under kultur koden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver härledd från ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Funktionen:**<br> ToUpper (källa, kultur)

**Beskrivning:**<br> Tar ett *käll* sträng värde och konverterar det till versaler med de angivna kultur reglerna. Om det inte finns någon angiven *kultur* information, används en invariant kultur.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källa** |Krävs |String |Vanligtvis namnet på attributet från källobjektet. |
| **substrat** |Valfritt |String |Formatet för kultur namnet baserat på RFC 4646 är *languagecode2-land/regioncode2*, där *languagecode2* är språk koden för två bokstäver och *land/regioncode2* är under kultur koden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver härledd från ISO 639-2.|

---
### <a name="word"></a>Word
**Funktionen:**<br> Ord (sträng, WordNumber, avgränsare)

**Beskrivning:**<br> Funktionen ord returnerar ett ord som finns i en sträng, baserat på parametrar som beskriver avgränsarna som ska användas och ord numret som ska returneras. Varje sträng med tecken i strängen avgränsade med ett av tecknen i avgränsare identifieras som ord:

Om talet < 1 returneras en tom sträng.
Om strängen är null returnerar en tom sträng.
Om strängen innehåller färre än tal ord, eller om strängen inte innehåller ord som identifieras av avgränsare returneras en tom sträng.

**Parametrar:**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Sträng** |Krävs |Multi-Value-attribut |Sträng för att returnera ett ord från.|
| **WordNumber** |Krävs | Integer | Nummer som identifierar vilket ord nummer som ska returneras|
| **avgränsare** |Krävs |String| En sträng som representerar de avgränsare som ska användas för att identifiera ord|

**Exempel:**<br>
Word ("Quick Jansson Fox", 3, "")                                                                                       
Returnerar "brun"

Ordet ("detta, String! har & många avgränsare", 3, ",! & #")                                                                       
Returnerar "har"

---

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

* **INDATA** (givenName): ”John”
* **INDATA** (efternamn): ”Berg”
* **UTDATA**: ”JohDoe”

### <a name="remove-diacritics-from-a-string"></a>Ta bort diakritiska tecken från en sträng
Du måste ersätta tecken med accenter med motsvarande tecken som inte innehåller accenttecken.

**Uttryck:** <br>
NormalizeDiacritics([givenName])

**Exempel indata/utdata:** <br>

* **INDATA** (givenName): ”Zoë”
* **UTDATA**: ”Zoe”

### <a name="split-a-string-into-a-multi-valued-array"></a>Dela upp en sträng i en Multivärdes mat ris
Du måste ta en kommaavgränsad lista med strängar och dela upp dem i en matris som kan kopplas till ett flervärdesattribut som Salesforce: s PermissionSets-attribut. I det här exemplet har en lista över behörighets uppsättningar fyllts i extensionAttribute5 i Azure AD.

**Uttryck:** <br>
Dela ([extensionAttribute5], ",")

**Exempel indata/utdata:** <br>

* **Inmatade** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Utdatadatum som en sträng i ett visst format
Du vill skicka datum till ett SaaS-program i ett visst format. <br>
Exempelvis kan du formatera datum för ServiceNow.

**Uttryck:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Exempel indata/utdata:**

* **INDATA** (extensionAttribute1): ”20150123105347.1Z”
* **UTDATA**: ”2015-01-23”

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Ersätt ett värde baserat på fördefinierade uppsättning med alternativ

Du behöver definiera tidszonen för användaren baserat på delstatskod som lagras i Azure AD. <br>
Om delstatskod inte matchar någon av de fördefinierade alternativ, använder du standardvärdet ”Australien/Sydney”.

**Uttryck:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Exempel indata/utdata:**

* **INDATA** (tillstånd): ”QLD”
* **UTDATA**: ”Australien/Brisbane”

### <a name="replace-characters-using-a-regular-expression"></a>Ersätt tecken med ett reguljärt uttryck
Du måste hitta tecken som matchar ett reguljärt uttrycks värde och ta bort dem.

**Uttryck:** <br>

Ersätt ([smek namn],, "[a-zA-Z_] *",, "",,)

**Exempel indata/utdata:**

* **Inmatade** (smek namn: "john_doe72"
* **Utdata**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konvertera genererat userPrincipalName-värde (UPN) till gemener
I exemplet nedan genereras UPN-värdet genom att sammanfoga PreferredFirstName-och PreferredLastName-käll fälten och ToLower-funktionen fungerar på den genererade strängen för att konvertera alla tecken till gemener. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Exempel indata/utdata:**

* **INDATA** (PreferredFirstName): ”John”
* **INDATA** (PreferredLastName): ”Smith”
* **Utdata**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generera unikt värde för attributet userPrincipalName (UPN)
Baserat på användarens förnamn, mellannamn och efternamn, måste du generera ett värde för UPN-attributet och Sök efter dess unikhet i målkatalogen AD innan tilldelas värdet till UPN-attributet.

**Uttryck:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Exempel indata/utdata:**

* **INDATA** (PreferredFirstName): ”John”
* **INDATA** (PreferredLastName): ”Smith”
* **UTDATA**”:John.Smith@contoso.com” om UPN-värdet för John.Smith@contoso.com inte redan finns i katalogen
* **UTDATA**”:J.Smith@contoso.com” om UPN-värdet för John.Smith@contoso.com finns redan i katalogen
* **UTDATA**”:Jo.Smith@contoso.com” om ovanstående två UPN-värden som redan finns i katalogen

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Flödes post värde om det inte är NULL, annars Flow userPrincipalName
Du vill flöda e-postattributet om det finns. Om så inte är fallet, vill du flöda värdet för userPrincipalName i stället.

**Uttryck:** <br>
`Coalesce([mail],[userPrincipalName])`

**Exempel indata/utdata:** <br>

* **Inmatad** (e-post): null
* **Ininformation** (userPrincipalName): "John.Doe@contoso.com"
* **UTDATA**”:John.Doe@contoso.com”

## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera användaren etablering/avetablering för SaaS-appar](user-provisioning.md)
* [Anpassa attributmappningar för etableringen av användare](customize-application-attributes.md)
* [Omfångsfilter för etableringen av användare](define-conditional-rules-for-provisioning-user-accounts.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md)
* [Kontoetableringsmeddelanden](user-provisioning.md)
* [Lista över guider om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
