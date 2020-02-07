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
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: a01f7f48dd93983edf4be4b797f62afede273c66
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066673"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Skriva uttryck för attributmappningar i Azure Active Directory
När du konfigurerar etablering till ett SaaS-program, är en av typerna av attributmappningar som du kan ange mappningen för en uttryck. För dessa, måste du skriva ett skript-liknande uttryck som hjälper dig att omvandla dina användares data till format som kan användas mer för SaaS-program.

## <a name="syntax-overview"></a>Översikt över syntax
Syntaxen för uttryck för attributmappningar är påminner om Visual Basic för Applications (VBA).

* Hela uttrycket måste definieras när det gäller funktioner, som består av ett namn följt av argument inom parentes: <br>
  *FunctionName (`<<argument 1>>`,`<<argument N>>`)*
* Du kan kapsla funktioner i varandra. Exempel: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* Du kan skicka tre olika typer av argument funktioner:
  
  1. Attribut måste omges av hakparenteser. Till exempel: [attributeName]
  2. Strängkonstanter måste vara inom dubbla citattecken. Till exempel: ”USA”
  3. Andra funktioner. Till exempel: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* För strängkonstanter, om du behöver ett omvänt snedstreck (\) eller citattecken (”) i strängen är måste den föregås symbolen omvänt snedstreck (\). Exempel: "företags namn: \\" contoso\\""

## <a name="list-of-functions"></a>Lista över funktioner
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp;[RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Lägg till
**Funktioner**<br> Append(Source, suffix)

**Beteckning**<br> Tar ett strängvärde för källa och lägger till suffixet i slutet av den.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |Vanligtvis namnet på attributet från källobjektet. |
| **huvudnamnssuffix** |Krävs |String |Den sträng som du vill lägga till i slutet av värdet för datakällan. |

---
### <a name="bitand"></a>BitAnd
**Funktioner**<br> BitAnd (värde1, värde2)

**Beteckning**<br> Den här funktionen konverterar båda parametrarna till den binära representationen och anger en bit till:

0 – om en eller båda av motsvarande bitar i värde1 och värde2 är 0                                                  
1 – om båda motsvarande bitar är 1.                                    

Med andra ord returneras 0 i samtliga fall, förutom när motsvarande bitar i båda parametrarna är 1.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **value1** |Krävs |NUM |Numeriskt värde som ska AND'ed med värde2|
| **värde2** |Krävs |NUM |Numeriskt värde som ska AND'ed med värde1|

**Exempel:**<br>
BitAnd (& HF, & HF7)                                                                                
11110111 och 00000111 = 00000111 så BitAnd returnerar 7, det binära värdet 00000111

---
### <a name="cbool"></a>CBool
**Funktioner**<br> CBool (uttryck)

**Beteckning**<br> CBool returnerar ett booleskt värde baserat på det utvärderade uttrycket. Om uttrycket utvärderas till ett värde som inte är noll returnerar CBool True, annars returneras FALSKT..

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uttryck** |Krävs | expression | Ett giltigt uttryck |

**Exempel:**<br>
CBool ([Attribute1] = [Attribute2])                                                                    
Returnerar true om båda attributen har samma värde.

---
### <a name="coalesce"></a>coalesce
**Funktioner**<br> Sammanslagning (source1, SOURCE2,..., defaultValue)

**Beteckning**<br> Returnerar det första käll värde som inte är NULL. Om alla argument är NULL och defaultValue finns returneras defaultValue. Om alla argument är NULL och defaultValue inte finns, returnerar sammanslagningen NULL.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **source1 ... Källa** | Krävs | String |Obligatoriskt, variabel antal gånger. Vanligtvis namnet på attributet från källobjektet. |
| **Standar** | Valfri | String | Standardvärdet som ska användas när alla käll värden är NULL. Kan vara tom sträng (””).

---
### <a name="converttobase64"></a>ConvertToBase64
**Funktioner**<br> ConvertToBase64 (källa)

**Beteckning**<br> Funktionen ConvertToBase64 konverterar en sträng till en Unicode base64-sträng.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |Sträng som ska konverteras till bas 64|

**Exempel:**<br>
ConvertToBase64 ("Hello World!")                                                                                                        
Returnerar "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funktioner**<br> ConvertToUTF8Hex (källa)

**Beteckning**<br> Funktionen ConvertToUTF8Hex konverterar en sträng till ett hexadecimalt värde för UTF8-kodning.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |Sträng som ska konverteras till UTF8 hex|

**Exempel:**<br>
ConvertToUTF8Hex ("Hello World!")                                                                                                         
Returnerar 48656C6C6F20776F726C6421

---
### <a name="count"></a>Antal
**Funktioner**<br> Count (attribut)

**Beteckning**<br> Funktionen COUNT returnerar antalet element i ett multi-värde-attribut

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **basattributet** |Krävs |basattributet |Ett flervärdesattribut som innehåller element som räknas|

---
### <a name="cstr"></a>CStr
**Funktioner**<br> CStr (värde)

**Beteckning**<br> Funktionen CStr konverterar ett värde till en sträng data typ.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **värde** |Krävs | numerisk, referens eller boolesk | Kan vara ett numeriskt värde, ett referens-eller Boolean-attribut. |

**Exempel:**<br>
CStr ([DN])                                                            
Returnerar "CN = Johan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Funktioner**<br> DateFromNum (värde)

**Beteckning**<br> Funktionen DateFromNum konverterar ett värde i ADs datum format till en DateTime-typ.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **värde** |Krävs | Datum | AD-datum som ska konverteras till DateTime-typ |

**Exempel:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
Returnerar en DateTime som representerar 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>formatDateTime
**Funktioner**<br> FormatDateTime (källa, inputFormat, outputFormat)

**Beteckning**<br> Tar en datumsträng från ett format och konverterar den till ett annat format.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |Vanligtvis namnet på attributet från källobjektet. |
| **inputFormat** |Krävs |String |Förväntade format för värdet för datakällan. För format som stöds, se [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Krävs |String |Formatet för Utdatadatum. |

---
### <a name="guid"></a>Guid
**Funktioner**<br> GUID ()

**Beteckning**<br> Funktions-GUID genererar ett nytt slumpmässigt GUID

---
### <a name="instr"></a>InStr
**Funktioner**<br> InStr (värde1, värde2, start, compareType)

**Beteckning**<br> Funktionen InStr söker efter den första förekomsten av en del sträng i en sträng

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **value1** |Krävs |String |Sträng som ska genomsökas |
| **värde2** |Krävs |String |Sträng som ska hittas |
| **start** |Valfri |Integer |Start position för att hitta del strängen|
| **compareType** |Valfri |Enum |Kan vara vbTextCompare eller vbBinaryCompare |

**Exempel:**<br>
InStr ("Quick Jansson Fox", "snabb")                                                                             
Evalues till 5

InStr ("repeterad", "e", 3, vbBinaryCompare)                                                                                  
Utvärderas till 7

---
### <a name="isnull"></a>IsNull
**Funktioner**<br> IsNull (uttryck)

**Beteckning**<br> Om uttrycket utvärderas till null returnerar funktionen IsNull True. För ett-attribut uttrycks ett null-värde av frånvaron av attributet.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uttryck** |Krävs |expression |Uttryck som ska utvärderas |

**Exempel:**<br>
IsNull ([displayName])                                                                                                
Returnerar sant om attributet inte finns

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funktioner**<br> IsNullOrEmpty (uttryck)

**Beteckning**<br> Om uttrycket är null eller en tom sträng returnerar funktionen IsNullOrEmpty värdet true. För ett-attribut utvärderar detta till sant om attributet saknas eller finns, men är en tom sträng.
Inversen till den här funktionen heter IsPresent.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uttryck** |Krävs |expression |Uttryck som ska utvärderas |

**Exempel:**<br>
IsNullOrEmpty ([displayName])                                               
Returnerar sant om attributet inte finns eller är en tom sträng

---
### <a name="ispresent"></a>IsPresent
**Funktioner**<br> IsPresent (uttryck)

**Beteckning**<br> Om uttrycket utvärderas till en sträng som inte är null och inte är tomt, returnerar funktionen IsPresent True. Inversen till den här funktionen heter IsNullOrEmpty.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uttryck** |Krävs |expression |Uttryck som ska utvärderas |

**Exempel:**<br>
Switch (IsPresent ([directManager]), [directManager], IsPresent ([skiplevelManager]), [skiplevelManager], IsPresent ([Director]), [Director])

---
### <a name="isstring"></a>IsString
**Funktioner**<br> IsString (uttryck)

**Beteckning**<br> Om uttrycket kan utvärderas till en sträng typ, utvärderar funktionen IsString till true.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uttryck** |Krävs |expression |Uttryck som ska utvärderas |

---
### <a name="item"></a>Objekt
**Funktioner**<br> Objekt (attribut, index)

**Beteckning**<br> Funktionen item returnerar ett objekt från en multi-valueion String/Attribute.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **basattributet** |Krävs |Attribut |Multi-Value-attribut som ska genomsökas |
| **Tabbindex** |Krävs |Integer | Index till ett objekt i en multi-Value-sträng|

**Exempel:**<br>
Objekt ([proxyAddresses], 1)

---
### <a name="join"></a>Slå ihop
**Funktioner**<br> Ansluta till (avgränsare, källa1, källa2...)

**Beteckning**<br> JOIN () liknar append (), förutom att det kan kombinera flera **käll** sträng värden till en enda sträng, och varje värde skiljs åt av en **avgränsnings** sträng.

Om ett av käll värdena är ett flervärdesattribut, kopplas alla värden i det attributet samman, avgränsade med avgränsning svärdet.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **brytning** |Krävs |String |Strängen används för att avgränsa källvärdena när de sammanfogas till en sträng. Kan vara ”” om det krävs ingen avgränsare. |
| **source1 ... Källa** |Krävs, variabeln antal gånger |String |Sträng värden kopplas. |

---
### <a name="left"></a>Från
**Funktioner**<br> Left (sträng, NumChars)

**Beteckning**<br> Funktionen Left returnerar ett angivet antal tecken från vänster i en sträng. Om numChars = 0 returneras en tom sträng.
Om numChars < 0, returnerar du Indatasträngen.
Om strängen är null returneras en tom sträng.
Om strängen innehåller färre tecken än det tal som anges i numChars returneras en sträng som är identisk med sträng (dvs. med alla tecken i parameter 1).

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Sträng** |Krävs |Attribut | Strängen att returnera tecken från |
| **NumChars** |Krävs |Integer | Ett tal som identifierar antalet tecken som ska returneras från början (vänster) av sträng|

**Exempel:**<br>
Left ("John berg", 3)                                                            
Returnerar "Joh"

---
### <a name="mid"></a>Mid
**Funktioner**<br> MID (källa, start, length)

**Beteckning**<br> Returnerar en understräng av värdet för datakällan. En understräng är en sträng som innehåller bara en del av tecken från Källsträngen.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |Vanligtvis namnet på attributet. |
| **start** |Krävs |heltal |Index i **käll** strängen där under strängen ska starta. Första tecknet i strängen har index 1, andra tecknet ska ha index 2 och så vidare. |
| **krävande** |Krävs |heltal |Delsträngens längd. Om längden slutar utanför **käll** strängen returnerar funktionen del sträng från **Start** index till slutet av **käll** strängen. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funktioner**<br> NormalizeDiacritics(source)

**Beteckning**<br> Kräver ett strängargument. Returnerar strängen, men med eventuella diakritiska tecken ersätts med motsvarande icke-diakritiska tecken. Normalt används för att konvertera förnamn och efternamn som innehåller diakritiska tecken (accenttecken) i juridiska värden som kan användas i olika användaridentifierare, t.ex användarhuvudnamn SAM-kontonamn och e-postadresser.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String | Vanligt vis attributet förnamn eller efter namn. |

---
### <a name="not"></a>Inte
**Funktioner**<br> Not(Source)

**Beteckning**<br> Vänder det booleska värdet för **källan**. Om **källobjektet** är "*True*" returnerar "*false*". Annars returnerar "*True*".

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |Booleska sträng |Förväntade **käll** värden är "true" eller "false". |

---
### <a name="numfromdate"></a>NumFromDate
**Funktioner**<br> NumFromDate (värde)

**Beteckning**<br> Funktionen NumFromDate konverterar ett DateTime-värde till Active Directory format som krävs för att ange attribut som [accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Använd den här funktionen för att konvertera DateTime-värden som tagits emot från moln HR-appar som Workday och SuccessFactors till motsvarande AD-representation. 

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **värde** |Krävs | String | Datum/tid-sträng i formatet stöds. För format som stöds, se https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Exempel:**<br>
* Exempel på arbets dagar <br>
  Förutsatt att du vill mappa attributet *ContractEndDate* från Workday som är i fältet format *2020-12-31-08:00* till *accountExpires* i AD, så kan du använda den här funktionen och ändra tids förskjutnings förskjutningen så att den matchar dina nationella inställningar. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors-exempel <br>
  Förutsatt att du vill mappa attributet *EndDate* från SuccessFactors som finns i formatet *M/d/ÅÅÅÅ HH: mm: ss tt* till *accountExpires* i AD, så kan du använda den här funktionen och ändra tids zons förskjutningen så att den matchar dina nationella inställningar.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Funktioner**<br> RemoveDuplicates (attribut)

**Beteckning**<br> Funktionen RemoveDuplicates använder en sträng med flera värden och ser till att varje värde är unikt.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **basattributet** |Krävs |Multi-Value-attribut |Multi-Value-attribut som ska ha dubbletter borttagna|

**Exempel:**<br>
RemoveDuplicates ([proxyAddresses])                                                                                                       
Returnerar ett sanerat proxyAddress-attribut där alla dubblettvärden har tagits bort

---
### <a name="replace"></a>Ersätt
**Funktioner**<br> Ersätt (källa, oldValue, regexPattern, regexGroupName, ersättningsvärde, replacementAttributeName, mall)

**Beteckning**<br>
Ersätter värden i en sträng. Den fungerar på olika sätt beroende på parametrarna som anges:

* När **OldValue** och **replacementValue** anges:
  
  * Ersätter alla förekomster av **OldValue** i **källan** med **replacementValue**
* När **OldValue** och **mall** tillhandahålls:
  
  * Ersätter alla förekomster av **OldValue** i **mallen** med **käll** värde
* När **regexPattern** och **replacementValue** anges:

  * Funktionen tillämpar **regexPattern** på **käll** strängen och du kan använda regex-gruppens namn för att skapa strängen för **replacementValue**
* När **regexPattern**, **regexGroupName**, **replacementValue** anges:
  
  * Funktionen tillämpar **regexPattern** på **käll** strängen och ersätter alla värden som matchar **regexGroupName** med **replacementValue**
* När **regexPattern**, **regexGroupName**, **replacementAttributeName** anges:
  
  * Om **källan** inte har något värde returneras **källan**
  * Om **källan** har ett värde tillämpar funktionen **regexPattern** på **käll** strängen och ersätter alla värden som matchar **RegexGroupName** med värdet som är kopplat till **replacementAttributeName**

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |Vanligt vis namnet på attributet från **källobjektet** . |
| **Gammalt** |Valfri |String |Värdet som ska ersättas i **källa** eller **mall**. |
| **regexPattern** |Valfri |String |Regex-mönster för värdet som ska ersättas i **källan**. Eller, när **replacementPropertyName** används, mönster för att extrahera värdet från **replacementPropertyName**. |
| **regexGroupName** |Valfri |String |Namnet på gruppen inuti **regexPattern**. Endast när **replacementPropertyName** används kommer vi att extrahera värdet för den här gruppen som **replacementValue** från **replacementPropertyName**. |
| **replacementValue** |Valfri |String |Nytt värde som ersätter gamla med. |
| **replacementAttributeName** |Valfri |String |Namnet på attributet som ska användas för ersättnings värde |
| **webbplatsmall** |Valfri |String |När ett **mallnamn** anges söker vi efter **OldValue** i mallen och ersätter det med **käll** värde. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funktioner**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Beteckning**<br> Kräver minst två argument, som är unikt värde Genereringsregler definieras med hjälp av uttryck. Funktionen utvärderar varje regel och sedan kontrollerar värdet som genererats för unikhet i appen/målkatalogen. Det första unika värdet att hitta som kommer att returneras. Om alla värden redan finns i målet, posten ska hämta escrowed och orsaken hämtar loggas i granskningsloggarna. Det finns ingen övre gräns för antalet argument som kan anges.

> [!NOTE]
> - Det här är en funktion på översta nivån, kan inte kapslas.
> - Den här funktionen kan inte tillämpas på attribut som har en matchande prioritet.  
> - Den här funktionen är endast avsedd att användas för skapande av posten. När du använder det med ett-attribut ställer du in egenskapen **tillämpa mappning** på **endast när objekt skapas**.
> - Den här funktionen stöds för närvarande endast för "arbets dag för Active Directory användar etablering". Det kan inte användas med andra etablerings program. 


**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Minst är 2 krävs, inte övre gräns |String | Lista med regler för generering av unika värden som ska utvärderas. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funktioner**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beteckning**<br> Returnerar en enskild appRoleAssignment från listan över alla appRoleAssignments som har tilldelats till en användare för ett visst program. Den här funktionen krävs för att konvertera appRoleAssignments-objektet till en enda roll namn sträng. Observera att det bästa sättet är att se till att endast en appRoleAssignment är tilldelad en användare åt gången, och om flera roller tilldelas kan den returnerade roll strängen inte vara förutsägbar. 

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Krävs |String |**[appRoleAssignments]** -objekt. |

---
### <a name="split"></a>Dela
**Funktioner**<br> Dela (källa, avgränsare)

**Beteckning**<br> Delar upp en sträng i en Multivärdes mat ris med hjälp av det angivna avgränsnings tecken.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |**käll** värde att uppdatera. |
| **avgränsare** |Krävs |String |Anger det tecken som ska användas för att dela strängen (exempel: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Funktioner**<br> StripSpaces(source)

**Beteckning**<br> Tar bort alla blanksteg (””) tecken från strängen källa.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |**käll** värde att uppdatera. |

---
### <a name="switch"></a>Växel
**Funktioner**<br> Växel (källa, standardvärde, key1, value1, key2, value2,...)

**Beteckning**<br> Returnerar **värdet** för den **nyckeln**när **källobjektet** matchar en **nyckel**. Om **käll** värde inte matchar några nycklar returnerar **DefaultValue**.  **Nyckel** -och **värde** parametrar måste alltid komma in i par. Funktionen förväntar alltid ett jämnt antal parametrar. Funktionen ska inte användas för referensbaserade attribut som chef. 

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |**Käll** värde att uppdatera. |
| **Standar** |Valfri |String |Standardvärde som ska användas när källan inte matchar några nycklar. Kan vara tom sträng (””). |
| **nyckel** |Krävs |String |**Nyckel** att jämföra **käll** värde med. |
| **värde** |Krävs |String |Ersättnings värde för den **källa** som matchar nyckeln. |

---
### <a name="tolower"></a>ToLower
**Funktioner**<br> ToLower (källa, kultur)

**Beteckning**<br> Tar ett *käll* sträng värde och konverterar det till gemener med de angivna kultur reglerna. Om det inte finns någon angiven *kultur* information, används en invariant kultur.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |Vanligtvis namnet på attributet från källobjektet |
| **substrat** |Valfri |String |Formatet för kultur namnet baserat på RFC 4646 är *languagecode2-land/regioncode2*, där *languagecode2* är språk koden för två bokstäver och *land/regioncode2* är under kultur koden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver härledd från ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Funktioner**<br> ToUpper (källa, kultur)

**Beteckning**<br> Tar ett *käll* sträng värde och konverterar det till versaler med de angivna kultur reglerna. Om det inte finns någon angiven *kultur* information, används en invariant kultur.

**Komponentparametrar**<br> 

| Namn | Obligatoriskt / upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källicensservern** |Krävs |String |Vanligtvis namnet på attributet från källobjektet. |
| **substrat** |Valfri |String |Formatet för kultur namnet baserat på RFC 4646 är *languagecode2-land/regioncode2*, där *languagecode2* är språk koden för två bokstäver och *land/regioncode2* är under kultur koden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver härledd från ISO 639-2.|

---
### <a name="word"></a>Word
**Funktioner**<br> Ord (sträng, WordNumber, avgränsare)

**Beteckning**<br> Funktionen ord returnerar ett ord som finns i en sträng, baserat på parametrar som beskriver avgränsarna som ska användas och ord numret som ska returneras. Varje sträng med tecken i strängen avgränsade med ett av tecknen i avgränsare identifieras som ord:

Om talet < 1 returneras en tom sträng.
Om strängen är null returnerar en tom sträng.
Om strängen innehåller färre än tal ord, eller om strängen inte innehåller ord som identifieras av avgränsare returneras en tom sträng.

**Komponentparametrar**<br> 

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

**Uttryck** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Exempel på indata/utdata:** <br>

* **Inmatad** (e-post): "john.doe@contoso.com"
* **Utdata**: "John. berg"

### <a name="append-constant-suffix-to-user-name"></a>Lägg till konstant suffix till användarnamn
Om du använder en Salesforce-Sandbox kan du behöva lägga till en ytterligare suffix till alla användare innan du synkroniserar dem.

**Uttryck** <br>
`Append([userPrincipalName], ".test")`

**Exempel på indata/utdata:** <br>

* **Inmatade**: (userPrincipalName): "John.Doe@contoso.com"
* **Utdata**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generera användaralias genom att sammanfoga delar av förnamn, efternamn
Du måste du generera en användare alias genom att först 3 bokstäverna i användarens förnamn och 5 första bokstäverna i användarens efternamn.

**Uttryck** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Exempel på indata/utdata:** <br>

* **Inmatade** (givenName): "John"
* **Inmatad** (efter namn): "berg"
* **Utdata**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Ta bort diakritiska tecken från en sträng
Du måste ersätta tecken med accenter med motsvarande tecken som inte innehåller accenttecken.

**Uttryck** <br>
NormalizeDiacritics([givenName])

**Exempel på indata/utdata:** <br>

* **Inmatade** (givenName): "Zoë"
* **Utdata**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dela upp en sträng i en Multivärdes mat ris
Du måste ta en kommaavgränsad lista med strängar och dela upp dem i en matris som kan kopplas till ett flervärdesattribut som Salesforce: s PermissionSets-attribut. I det här exemplet har en lista över behörighets uppsättningar fyllts i extensionAttribute5 i Azure AD.

**Uttryck** <br>
Dela ([extensionAttribute5], ",")

**Exempel på indata/utdata:** <br>

* **Inmatade** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Utdatadatum som en sträng i ett visst format
Du vill skicka datum till ett SaaS-program i ett visst format. <br>
Exempelvis kan du formatera datum för ServiceNow.

**Uttryck** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Exempel på indata/utdata:**

* **Inmatade** (extensionAttribute1): "20150123105347.1 z"
* **Utdata**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Ersätt ett värde baserat på fördefinierade uppsättning med alternativ

Du behöver definiera tidszonen för användaren baserat på delstatskod som lagras i Azure AD. <br>
Om delstatskod inte matchar någon av de fördefinierade alternativ, använder du standardvärdet ”Australien/Sydney”.

**Uttryck** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Exempel på indata/utdata:**

* **Inmatade** (State): "QLD"
* **Utdata**: "Australien/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Ersätt tecken med ett reguljärt uttryck
Du måste hitta tecken som matchar ett reguljärt uttrycks värde och ta bort dem.

**Uttryck** <br>

Ersätt ([smek namn],, "[a-zA-Z_] *",, "",,)

**Exempel på indata/utdata:**

* **Inmatade** (smek namn: "john_doe72"
* **Utdata**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konvertera genererat userPrincipalName-värde (UPN) till gemener
I exemplet nedan genereras UPN-värdet genom att sammanfoga PreferredFirstName-och PreferredLastName-käll fälten och ToLower-funktionen fungerar på den genererade strängen för att konvertera alla tecken till gemener. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Exempel på indata/utdata:**

* **Inmatade** (PreferredFirstName): "John"
* **Inmatade** (PreferredLastName): "Svensson"
* **Utdata**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generera unikt värde för attributet userPrincipalName (UPN)
Baserat på användarens förnamn, mellannamn och efternamn, måste du generera ett värde för UPN-attributet och Sök efter dess unikhet i målkatalogen AD innan tilldelas värdet till UPN-attributet.

**Uttryck** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Exempel på indata/utdata:**

* **Inmatade** (PreferredFirstName): "John"
* **Inmatade** (PreferredLastName): "Svensson"
* **Output**: "John.Smith@contoso.com" om UPN-värdet John.Smith@contoso.com inte redan finns i katalogen
* **Output**: "J.Smith@contoso.com" om UPN-värdet för John.Smith@contoso.com redan finns i katalogen
* **Utdata**: "Jo.Smith@contoso.com" om ovanstående två UPN-värden redan finns i katalogen

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Flödes post värde om det inte är NULL, annars Flow userPrincipalName
Du vill flöda e-postattributet om det finns. Om så inte är fallet, vill du flöda värdet för userPrincipalName i stället.

**Uttryck** <br>
`Coalesce([mail],[userPrincipalName])`

**Exempel på indata/utdata:** <br>

* **Inmatad** (e-post): null
* **Ininformation** (userPrincipalName): "John.Doe@contoso.com"
* **Utdata**: "John.Doe@contoso.com"

## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera användar etablering/avetablering för SaaS-appar](../app-provisioning/user-provisioning.md)
* [Anpassa mappningar av attribut för användar etablering](../app-provisioning/customize-application-attributes.md)
* [Omfångs filter för användar etablering](define-conditional-rules-for-provisioning-user-accounts.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Meddelanden om konto etablering](../app-provisioning/user-provisioning.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
