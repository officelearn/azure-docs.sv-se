---
title: Skriva uttryck för attributmappningar i Azure AD
description: Lär dig hur du använder uttrycksmappningar för att omvandla attributvärden till ett acceptabelt format under automatisk etablering av SaaS-appobjekt i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28e591234e28770a90bed827e4d36c6342661dd1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866591"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Skriva uttryck för attributmappningar i Azure Active Directory
När du konfigurerar etablering till ett SaaS-program är en av de typer av attributmappningar som du kan ange en uttrycksmappning. För dessa måste du skriva ett skriptliknande uttryck som gör att du kan omvandla användarnas data till format som är mer acceptabla för SaaS-programmet.

## <a name="syntax-overview"></a>Syntaxöversikt
Syntaxen för Uttryck för attributmappningar påminner om VBA-funktioner (Visual Basic for Applications).

* Hela uttrycket måste definieras i termer av funktioner, som består av ett namn följt av argument inom parentes: <br>
  *`<<argument 1>>`Funktionsnamn(`<<argument N>>`, )*
* Ni kan kapsla funktioner inom varandra. Ett exempel: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Du kan skicka tre olika typer av argument till funktioner:
  
  1. Attribut, som måste vara inneslutna i hakparenteser. Till exempel: [attributeName]
  2. Strängkonstanter, som måste omges av dubbla citattecken. Till exempel: "USA"
  3. Andra funktioner. Till exempel: FunctionOne(`<<argument1>>`,`<<argument2>>`FunctionTwo( ))
* Om du behöver ett omvänt snedstreck ( \ ) eller citattecken ( " ) i strängen måste det komma ut med omvänt snedstreck ( \ ). Till exempel: "Företagsnamn: \\"Contoso\\""

## <a name="list-of-functions"></a>Lista över funktioner
&nbsp; [Item](#item) &nbsp; [Left](#left) &nbsp; [Not](#not) &nbsp; [Count](#count) &nbsp; &nbsp; [Mid](#mid) &nbsp; [Join](#join) [Append](#append) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [BitAnd](#bitand) &nbsp; &nbsp; &nbsp; &nbsp; [CStr](#cstr) &nbsp; &nbsp; [Guid](#guid) &nbsp; &nbsp; &nbsp; [IsPresent](#ispresent) &nbsp; &nbsp; [IIF](#iif) &nbsp; &nbsp; &nbsp; [Replace](#replace) [DateFromNum](#datefromnum) &nbsp; &nbsp; [Coalesce](#coalesce) &nbsp; &nbsp; &nbsp; &nbsp; [IsString](#isstring) &nbsp; [CBool](#cbool) &nbsp; &nbsp; &nbsp; [InStr](#instr) &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; [ConvertToBase64](#converttobase64) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) [IsNull](#isnull) &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [RemoveDuplicates](#removeduplicates) [ConvertToUTF8Hex](#converttoutf8hex) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp; Lägg till bitoch &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; CBool &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Coalesce &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; Count &nbsp; &nbsp; CStr DatumFrånNum FormatDateTime Guid IIF InStr IsNull IsNull IsNullOrEmpty IsPresent IsString &nbsp; Item Join Left Mid NormalizeDiacritics Not RemoveDuplicates Replace SelectUniqueValue &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) &nbsp; [Word](#word) [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; SingleAppRoleAssignment&nbsp;Split StripSpaces &nbsp; Växla&nbsp; &nbsp; tillLower&nbsp; [ToUpper](#toupper) ToUpper Word&nbsp; &nbsp; &nbsp;

---
### <a name="append"></a>Lägg till
**Funktion:**<br> Lägg till(källa, suffix)

**Beskrivning:**<br> Tar ett källsträngvärde och lägger till suffixet i slutet av det.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |Vanligtvis namnet på attributet från källobjektet. |
| **Suffix** |Krävs |Sträng |Strängen som du vill lägga till i slutet av källvärdet. |

---
### <a name="bitand"></a>BitAnd ()
**Funktion:**<br> BitAnd(värde1, värde2)

**Beskrivning:**<br> Den här funktionen konverterar båda parametrarna till den binära representationen och ställer in lite till:

0 - om en eller båda av motsvarande bitar i värde1 och värde2 är 0                                                  
1 - om båda motsvarande bitar är 1.                                    

Med andra ord returnerar den 0 i alla fall utom när motsvarande bitar av båda parametrarna är 1.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **värde1** |Krävs |num |Numeriskt värde som ska VARA AND'ed med värde2|
| **värde2** |Krävs |num |Numeriskt värde som ska VARA AND'ed med värde1|

**Exempel:**<br>
BitAnd(&HF, &HF7)                                                                                
11110111 OCH 00000111 = 00000111 så BitAnd returnerar 7, det binära värdet 00000111

---
### <a name="cbool"></a>CBool (en)
**Funktion:**<br> CBool(uttryck)

**Beskrivning:**<br> CBool returnerar en boolesk baserat på det utvärderade uttrycket. Om uttrycket utvärderas till ett värde som inte är noll returnerar CBool True, annars returneras False..

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Uttryck** |Krävs | uttryck | Alla giltiga uttryck |

**Exempel:**<br>
CBool([attribute1] = [attribut2])                                                                    
Returnerar Sant om båda attributen har samma värde.

---
### <a name="coalesce"></a>Coalesce
**Funktion:**<br> Sammanslagning(source1, source2, ..., defaultValue)

**Beskrivning:**<br> Returnerar det första källvärdet som inte är NULL. Om alla argument är NULL och standardVärde finns returneras standardvärdevärdet. Om alla argument är NULL och standardVärde inte finns returnerar Coalesce NULL.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **källa1 ... källaN** | Krävs | Sträng |Obligatoriskt, variabelt antal gånger. Vanligtvis namnet på attributet från källobjektet. |
| **Standardvärde** | Valfri | Sträng | Standardvärde som ska användas när alla källvärden är NULL. Kan vara tom sträng ("").

---
### <a name="converttobase64"></a>Konvertera tillBase64
**Funktion:**<br> ConvertToBase64(källa)

**Beskrivning:**<br> Funktionen ConvertToBase64 konverterar en sträng till en Unicode base64-sträng.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |Sträng som ska konverteras till bas 64|

**Exempel:**<br>
ConvertToBase64("Hello world!")                                                                                                        
Returnerar "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>KonverteratoUTF8Hex
**Funktion:**<br> ConvertToUTF8Hex(källa)

**Beskrivning:**<br> Funktionen ConvertToUTF8Hex konverterar en sträng till ett UTF8 Hex-kodat värde.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |Sträng som ska konverteras till UTF8 Hex|

**Exempel:**<br>
ConvertToUTF8Hex ("Hello world!")                                                                                                         
Returnerar 48656C6C6F20776F726C6421

---
### <a name="count"></a>Antal
**Funktion:**<br> Count(attribute)

**Beskrivning:**<br> Funktionen Antal returnerar antalet element i ett attribut med flera värden

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Attributet** |Krävs |attribut |Attribut med flera värden som har element som räknas|

---
### <a name="cstr"></a>CStr (CStr)
**Funktion:**<br> CStr(värde)

**Beskrivning:**<br> Funktionen CStr konverterar ett värde till en strängdatatyp.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Värde** |Krävs | numeriska, referens- eller booleska | Kan vara ett numeriskt värde, referensattribut eller booleskt. |

**Exempel:**<br>
CStr([dn])                                                            
Returnerar "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DatumFrånNum
**Funktion:**<br> DatumFrånNum(värde)

**Beskrivning:**<br> Funktionen DateFromNum konverterar ett värde i AD:s datumformat till en DateTime-typ.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Värde** |Krävs | Date | AD-datum som ska konverteras till DateTime-typ |

**Exempel:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DatumFrånNum(1296993240000000000)                                                            
Returnerar en DateTime som representerar 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>FormatDateTime
**Funktion:**<br> FormatDateTime(källa, inputFormat, outputFormat)

**Beskrivning:**<br> Tar en datumsträng från ett format och konverterar den till ett annat format.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |Vanligtvis namnet på attributet från källobjektet. |
| **inputFormat** |Krävs |Sträng |Förväntat format för källvärdet. Format som stöds [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)finns i . |
| **outputFormat** |Krävs |Sträng |Format för utdatadatumet. |

---
### <a name="guid"></a>GUID
**Funktion:**<br> Guid()

**Beskrivning:**<br> Funktionen Guid genererar ett nytt slumpmässigt GUID

---
### <a name="iif"></a>Iif
**Funktion:**<br> IIF(villkor,värdeIfTrue,valueIfFalse)

**Beskrivning:**<br> Funktionen IIF returnerar en av en uppsättning möjliga värden baserat på ett angivet villkor.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Villkor** |Krävs |Variabel eller uttryck |Alla värden eller uttryck som kan utvärderas till sant eller falskt. |
| **värdeIfTrue** |Krävs |Variabel eller sträng | Om villkoret utvärderas till true, det returnerade värdet. |
| **värdeIfFalse** |Krävs |Variabel eller sträng |Om villkoret utvärderas till falskt, det returnerade värdet.|

**Exempel:**<br>
IIF([land]="USA",[land],[avdelning])

---
### <a name="instr"></a>Instr
**Funktion:**<br> InStr(värde1,värde2,start,compareType)

**Beskrivning:**<br> Funktionen InStr hittar den första förekomsten av en delsträng i en sträng

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **värde1** |Krävs |Sträng |Sträng som ska sökas |
| **värde2** |Krävs |Sträng |Sträng som ska hittas |
| **start** |Valfri |Integer |Startposition för att hitta delsträngen|
| **compareType (jämförTyp)** |Valfri |Enum |Kan vara vbTextCompare eller vbBinaryCompare |

**Exempel:**<br>
InStr("Den snabba bruna räven","snabb")                                                                             
Utvärderar till 5

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
Utvärderar till 7

---
### <a name="isnull"></a>Ärnull
**Funktion:**<br> Isnull(uttryck)

**Beskrivning:**<br> Om uttrycket utvärderas till Null returneras true. För ett attribut uttrycks en Null av att attributet inte finns.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Uttryck** |Krävs |uttryck |Uttryck som ska utvärderas |

**Exempel:**<br>
IsNull([displayName])                                                                                                
Returnerar Sant om attributet inte finns

---
### <a name="isnullorempty"></a>Isnullorempty (30000)
**Funktion:**<br> IsnullOrempty(uttryck)

**Beskrivning:**<br> Om uttrycket är null eller en tom sträng returneras true. För ett attribut utvärderas detta till Sant om attributet saknas eller finns men är en tom sträng.
Inversen av den här funktionen heter IsPresent.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Uttryck** |Krävs |uttryck |Uttryck som ska utvärderas |

**Exempel:**<br>
IsNullOrEmpty([displayName])                                               
Returnerar Sant om attributet inte finns eller är en tom sträng

---
### <a name="ispresent"></a>IsPresent (Representerar)
**Funktion:**<br> IsPresent(uttryck)

**Beskrivning:**<br> Om uttrycket utvärderas till en sträng som inte är Null och inte är tom returneras true. Inversen av denna funktion heter IsNullOrEmpty.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Uttryck** |Krävs |uttryck |Uttryck som ska utvärderas |

**Exempel:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString (IsString)
**Funktion:**<br> IsString(uttryck)

**Beskrivning:**<br> Om uttrycket kan utvärderas till en strängtyp utvärderas funktionen IsString till True.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Uttryck** |Krävs |uttryck |Uttryck som ska utvärderas |

---
### <a name="item"></a>Objekt
**Funktion:**<br> Objekt(attribut, index)

**Beskrivning:**<br> Funktionen Artikel returnerar ett objekt från en sträng/attribut med flera värden.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Attributet** |Krävs |Attribut |Attribut med flera värden som ska sökas |
| **Index** |Krävs |Integer | Indexera till ett objekt i strängen med flera värden|

**Exempel:**<br>
Punkt([proxyAdresser], 1)

---
### <a name="join"></a>Slå ihop
**Funktion:**<br> Join(separator, source1, source2, ...)

**Beskrivning:**<br> Join() liknar Append(), förutom att det kan kombinera flera **källsträngvärden** till en enda sträng och varje värde avgränsas med en **avgränsare.**

Om ett av källvärdena är ett attribut med flera värden sammanfogas alla värden i attributet, avgränsat med avgränsarens värde.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Avgränsare** |Krävs |Sträng |Sträng som används för att separera källvärden när de sammanfogas till en sträng. Kan vara "" om ingen avgränsare krävs. |
| **källa1 ... källaN** |Obligatoriskt, variabelt antal gånger |Sträng |Strängvärden som ska sammanfogas. |

---
### <a name="left"></a>Vänster
**Funktion:**<br> Vänster(Sträng,NumChars)

**Beskrivning:**<br> Funktionen Vänster returnerar ett angivet antal tecken från vänster om en sträng. Om numChars = 0 returnerar du tom sträng.
Om numChars < 0 returnerar du indatasträngen.
Om strängen är null returnerar du tom sträng.
Om strängen innehåller färre tecken än det tal som anges i numChars returneras en sträng som är identisk med strängen (det vill de vill ha alla tecken i parameter 1).

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Sträng** |Krävs |Attribut | Strängen för att returnera tecken från |
| **NumChars (NumChars)** |Krävs |Integer | Ett nummer som identifierar antalet tecken som ska returneras från början (vänster) i strängen|

**Exempel:**<br>
Vänster("John Doe", 3)                                                            
Returnerar "Joh"

---
### <a name="mid"></a>Mellan
**Funktion:**<br> Mitten (källa, start, längd)

**Beskrivning:**<br> Returnerar en delsträng av källvärdet. En delsträng är en sträng som bara innehåller några av tecknen från källsträngen.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |Vanligtvis namnet på attributet. |
| **start** |Krävs |heltal |Index i **källsträngen** där delsträng ska börja. Första tecknet i strängen kommer att ha index på 1, andra tecken kommer att ha index 2, och så vidare. |
| **Längd** |Krävs |heltal |Längden på delsträngen. Om längden slutar utanför **källsträngen** returneras delsträng från **startindex** till slutet av **källsträngen.** |

---
### <a name="normalizediacritics"></a>NormaliseraDiakritiska tecken
**Funktion:**<br> NormaliseraDiacritics(källa)

**Beskrivning:**<br> Kräver ett strängargument. Returnerar strängen, men med alla diakritiska tecken som ersätts med motsvarande icke-diakritiska tecken. Används vanligtvis för att konvertera förnamn och efternamn som innehåller diakritiska tecken (accenttecken) till juridiska värden som kan användas i olika användaridentifierare, till exempel användarnamn, SAM-kontonamn och e-postadresser.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng | Vanligtvis ett förnamn eller efternamn attribut. |

---
### <a name="not"></a>Inte
**Funktion:**<br> Inte(källa)

**Beskrivning:**<br> Vänd det booleska värdet för **källan**. Om **källvärdet** är "*Sant*", returnerar "*False*". Annars returnerar "*True*".

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Boolesk sträng |Förväntade **källvärden** är "True" eller "False". |

---
### <a name="numfromdate"></a>NumFromDate
**Funktion:**<br> NumFromDate(värde)

**Beskrivning:**<br> Funktionen NumFromDate konverterar ett DateTime-värde till Active Directory-format som krävs för att ange attribut som [accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Använd den här funktionen för att konvertera DateTime-värden som tagits emot från moln-HR-appar som Workday och SuccessFactors till motsvarande AD-representation. 

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Värde** |Krävs | Sträng | Datumtidssträngen i formatet format som stöds. Format som stöds https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspxfinns i . |

**Exempel:**<br>
* Exempel på arbetsdag <br>
  Förutsatt att du vill mappa *attributet ContractEndDate* från Workday som är i formatet *2020-12-31-08:00* till *kontoExpirerar* fältet i AD, här är hur du kan använda den här funktionen och ändra tidszonsförskjutningen för att matcha språken. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Exempel på SuccessFactors <br>
  Förutsatt att du vill mappa attributet *endDate* från SuccessFactors som är i formatet *M/d/yyyy hh:mm:ss tt* till *kontoExpirerar* fältet i AD, här är hur du kan använda den här funktionen och ändra tidszonsförskjutningen för att matcha ditt språk.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>Ta bortDuplicates
**Funktion:**<br> RemoveDuplicates(attribute)

**Beskrivning:**<br> Funktionen RemoveDuplicates tar en sträng med flera värden och kontrollerar att varje värde är unikt.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Attributet** |Krävs |Attribut med flera värden |Attribut med flera värden som kommer att ha dubbletter borttagna|

**Exempel:**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
Returnerar ett attribut för sanitized proxyAddress där alla dubblettvärden har tagits bort

---
### <a name="replace"></a>Ersätt
**Funktion:**<br> Ersätt(källa, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Beskrivning:**<br>
Ersätter värden i en sträng. Det fungerar olika beroende på de parametrar som tillhandahålls:

* När **oldValue** och **ersättvärde** tillhandahålls:
  
  * Ersätter alla förekomster av **oldValue** i **källan** med **ersättningVärde**
* När **oldValue** och **mall** tillhandahålls:
  
  * Ersätter alla förekomster av **oldValue** i **mallen** med **källvärdet**
* När **regexPattern** och **ersättvärde** tillhandahålls:

  * Funktionen tillämpar **regexPattern** på **källsträngen** och du kan använda regex-gruppnamnen för att konstruera strängen för **ersättningVärde**
* När **regexPattern**, **regexGroupName**, **ersätterValue** tillhandahålls:
  
  * Funktionen tillämpar **regexPattern** på **källsträngen** och ersätter alla värden som matchar **regexGroupName** med **ersättvärde**
* När **regexPattern**, **regexGroupName**tillhandahålls **ersättningAttributeName:**
  
  * Om **källan** inte har något värde returneras **källan**
  * Om **källan** har ett värde, använder funktionen **regexPattern** på **källsträngen** och ersätter alla värden som matchar **regexGroupName** med värdet som är associerat med **replacementAttributeName**

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |Vanligtvis namnet på attributet från **källobjektet.** |
| **gamlaVärdera** |Valfri |Sträng |Värde som ska ersättas i **källa** eller **mall**. |
| **regexMönster** |Valfri |Sträng |Regex mönster för det värde som ska ersättas i **källa**. Eller när **ersättningPropertyName** används, mönster för att extrahera värde från **ersätterPropertyName**. |
| **regexGroupName (regexGroupName)** |Valfri |Sträng |Namnet på gruppen inuti **regexPattern**. Endast när **ersättningPropertyName** används, kommer vi att extrahera värdet för denna grupp som **ersätterValue** från **ersätterPropertyName**. |
| **ersättningVärdera** |Valfri |Sträng |Nytt värde att ersätta gammalt med. |
| **ersättErNamn** |Valfri |Sträng |Namnet på det attribut som ska användas för ersättningsvärde |
| **Mall** |Valfri |Sträng |När **mallvärdet** anges letar vi efter **oldValue** i mallen och ersätter det med **källvärdet.** |

---
### <a name="selectuniquevalue"></a>VäljUniqueValue
**Funktion:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Beskrivning:**<br> Kräver minst två argument, som är unika värdegenereringsregler som definieras med uttryck. Funktionen utvärderar varje regel och kontrollerar sedan det värde som genereras för unikhet i målappen/katalogen. Det första unika värdet som hittas är det som returneras. Om alla värden redan finns i målet spärras transaktionen och orsaken loggas i granskningsloggarna. Det finns ingen övre gräns för antalet argument som kan tillhandahållas.


 - Detta är en toppnivå funktion, det kan inte kapslas.
 - Den här funktionen kan inte tillämpas på attribut som har en matchande prioritet.   
 - Den här funktionen är endast avsedd att användas för att skapa post. När du använder den med ett attribut ställer du in egenskapen **Använd mappning** **på Endast när objektet skapas**.
 - Den här funktionen stöds för närvarande endast för "Arbetsdag till Active Directory-användaretablering". Den kan inte användas med andra etableringsprogram. 


**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **unikaValueRule1 ... unikaValueRuleN** |Minst 2 krävs, ingen övre gräns |Sträng | Lista över unika värdegenereringsregler att utvärdera. |


---
### <a name="singleapproleassignment"></a>Enkeltilldelning
**Funktion:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beskrivning:**<br> Returnerar en enda appRoleAstilldelning från listan över alla appRoleAstilldelningar som tilldelats en användare för ett visst program. Den här funktionen krävs för att konvertera appRoleAssignments-objektet till en enda rollnamnssträng. Observera att det bästa är att se till att endast en appRoleAstilldelning tilldelas en användare i taget, och om flera roller tilldelas kan det hända att rollsträngen som returneras inte är förutsägbar. 

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **[appRoleAstilldelningar]** |Krävs |Sträng |**[appRoleAssignments]** objekt. |

---
### <a name="split"></a>Dela
**Funktion:**<br> Split(källa, avgränsare)

**Beskrivning:**<br> Delar en sträng i en matris med flera värden med det angivna avgränsartecknet.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |**källvärdet** som ska uppdateras. |
| **Avgränsare** |Krävs |Sträng |Anger det tecken som ska användas för att dela strängen (exempel: "") |

---
### <a name="stripspaces"></a>StripSpaces (stripspaces)
**Funktion:**<br> StripSpaces(källa)

**Beskrivning:**<br> Tar bort alla blanksteg (" ") tecken från källsträngen.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |**källvärdet** som ska uppdateras. |

---
### <a name="switch"></a>Växel
**Funktion:**<br> Switch(källa, defaultValue, key1, value1, key2, value2, ...)

**Beskrivning:**<br> När **källvärdet** matchar en **nyckel** **returnerar** värdet för **nyckeln**. Om **källvärdet** inte matchar några nycklar returnerar **defaultValue**.  **Nyckel-** och **värdeparametrar** måste alltid finnas i par. Funktionen förväntar sig alltid ett jämnt antal parametrar. Funktionen bör inte användas för referensattribut som chef. 

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |**Källvärde** som ska uppdateras. |
| **Standardvärde** |Valfri |Sträng |Standardvärde som ska användas när källan inte matchar några nycklar. Kan vara tom sträng (""). |
| **key** |Krävs |Sträng |**Nyckel** att jämföra **källvärde** med. |
| **Värde** |Krävs |Sträng |Ersättningsvärde för **källan** som matchar nyckeln. |

---
### <a name="tolower"></a>ToLower (tolower)
**Funktion:**<br> ToLower(källa, kultur)

**Beskrivning:**<br> Tar ett *källsträngvärde* och konverterar det till gemener med hjälp av de kulturregler som anges. Om det inte finns någon *kultur* info anges, då det kommer att använda Invariant kultur.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |Vanligtvis namn på attributet från källobjektet |
| **Kultur** |Valfri |Sträng |Formatet för kulturnamnet baserat på RFC 4646 är *languagecode2-country/regioncode2*, där *språkkod2* är språkkoden med två bokstäver och *lands-/regionkod2* är subkulturkoden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver som härleds från ISO 639-2.|

---
### <a name="toupper"></a>ToUpper (TillUpper)
**Funktion:**<br> ToUpper (källa, kultur)

**Beskrivning:**<br> Tar ett *källsträngvärde* och konverterar det till versaler med hjälp av de kulturregler som anges. Om det inte finns någon *kultur* info anges, då det kommer att använda Invariant kultur.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Källkod** |Krävs |Sträng |Vanligtvis namnet på attributet från källobjektet. |
| **Kultur** |Valfri |Sträng |Formatet för kulturnamnet baserat på RFC 4646 är *languagecode2-country/regioncode2*, där *språkkod2* är språkkoden med två bokstäver och *lands-/regionkod2* är subkulturkoden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver som härleds från ISO 639-2.|

---
### <a name="word"></a>Word
**Funktion:**<br> Word(sträng,WordNumber,avgränsare)

**Beskrivning:**<br> Word-funktionen returnerar ett ord som finns i en sträng, baserat på parametrar som beskriver de avgränsare som ska användas och det ordnummer som ska returneras. Varje teckensträng i sträng avgränsad med ett av tecknen i avgränsare identifieras som ord:

Om tal < 1 returnerar tom sträng.
Om strängen är null returnerar den tomma strängen.
Om strängen innehåller mindre än talord, eller om strängen inte innehåller några ord som identifieras av avgränsare, returneras en tom sträng.

**Parametrar:**<br> 

| Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
| --- | --- | --- | --- |
| **Sträng** |Krävs |Attribut med flera värden |Sträng för att returnera ett ord från.|
| **WordNumber (ordnummer)** |Krävs | Integer | Nummer som identifierar vilket ordnummer som ska returneras|
| **Avgränsare** |Krävs |Sträng| En sträng som representerar avgränsaren eller avgränsare som ska användas för att identifiera ord|

**Exempel:**<br>
Word("Den snabba bruna räven",3", ")                                                                                       
Returnerar "brun"

Word("This,string!has&many separators",3,",!&#")                                                                       
Returer "har"

---

## <a name="examples"></a>Exempel
### <a name="strip-known-domain-name"></a>Strip känt domännamn
Du måste ta bort ett känt domännamn från en användares e-postadress för att få ett användarnamn. <br>
Om domänen till exempel är "contoso.com" kan du använda följande uttryck:

**Uttryck:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Provinmatning/utgång:** <br>

* **INPUT** (post):john.doe@contoso.com" "
* **UTGÅNG:**"john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Lägga till konstant suffix i användarnamnet
Om du använder en Salesforce Sandbox kan du behöva lägga till ytterligare ett suffix i alla användarnamn innan du synkroniserar dem.

**Uttryck:** <br>
`Append([userPrincipalName], ".test")`

**Exempel på inmatning/utdata:** <br>

* **INPUT**: (userPrincipalName): "John.Doe@contoso.com"
* **UTDATA**John.Doe@contoso.com.test: " "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generera användaralias genom att sammanfoga delar av för- och efternamn
Du måste generera ett användaralias genom att ta första 3 bokstäver i användarens förnamn och första 5 bokstäver i användarens efternamn.

**Uttryck:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Exempel på inmatning/utdata:** <br>

* **INPUT** (givenName): "John"
* **INPUT** (efternamn): "Doe"
* **UTGÅNG**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Ta bort diakritiska tecken från en sträng
Du måste ersätta tecken som innehåller accenttecken med motsvarande tecken som inte innehåller accenttecken.

**Uttryck:** <br>
Normaliseradiakritiska tecken([givennamn])

**Exempel på inmatning/utdata:** <br>

* **INPUT** (givenName): "Zoë"
* **UTGÅNG**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dela upp en sträng i en matris med flera värden
Du måste ta en kommaavgränsad lista med strängar och dela upp dem i en matris som kan anslutas till ett attribut med flera värden som Salesforces Attributet PermissionSets. I det här exemplet har en lista över behörighetsgrupper fyllts i tilläggAttribute5 i Azure AD.

**Uttryck:** <br>
Split([extensionAttribute5], ",")

**Exempel på inmatning/utdata:** <br>

* **INPUT** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **UTDATA**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Utdatadatum som en sträng i ett visst format
Du vill skicka datum till ett SaaS-program i ett visst format. <br>
Du vill till exempel formatera datum för ServiceNow.

**Uttryck:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Exempel på inmatning/utdata:**

* **INGÅNG** (förlängningAttribut1): "20150123105347.1Z"
* **PRODUKTION**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Ersätta ett värde baserat på fördefinierad uppsättning alternativ

Du måste definiera tidszonen för användaren baserat på tillståndskoden som lagras i Azure AD. <br>
Om tillståndskoden inte matchar något av de fördefinierade alternativen använder du standardvärdet "Australien/Sydney".

**Uttryck:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Exempel på inmatning/utdata:**

* **INMATNING** (tillstånd): "QLD"
* **OUTPUT**: "Australien / Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Ersätta tecken med ett reguljärt uttryck
Du måste hitta tecken som matchar ett reguljärt uttrycksvärde och ta bort dem.

**Uttryck:** <br>

Ersätt([mailNickname], "[a-zA-Z_]*", "", , )

**Exempel på inmatning/utdata:**

* **INPUT** (mailNickname: "john_doe72"
* **UTGÅNG:**"72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konvertera genererat userPrincipalName (UPN) värde till gemener
I exemplet nedan genereras UPN-värdet genom att de sammanfattar källfälten PreferredFirstName och PreferredLastName och funktionen ToLower fungerar på den genererade strängen för att konvertera alla tecken till gemener. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Exempel på inmatning/utdata:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **UTDATA**john.smith@contoso.com: " "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generera unikt värde för UPN-attribut (UserPrincipalName)
Baserat på användarens förnamn, mellannamn och efternamn måste du generera ett värde för UPN-attributet och kontrollera om det är unikt i mål AD-katalogen innan du tilldelar värdet till UPN-attributet.

**Uttryck:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Exempel på inmatning/utdata:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **UTDATA**John.Smith@contoso.com: " " John.Smith@contoso.com om UPN-värdet inte redan finns i katalogen
* **UTDATA**J.Smith@contoso.com: " " John.Smith@contoso.com om UPN-värdet redan finns i katalogen
* **UTDATA**Jo.Smith@contoso.com: " " om ovanstående två UPN-värden redan finns i katalogen

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Flödesmeddelandevärde om inte NULL, annars flödesanvändarePrincipalNamn
Du vill flöda e-postattributet om det finns. Om så inte är fallet vill du flöda värdet för userPrincipalName i stället.

**Uttryck:** <br>
`Coalesce([mail],[userPrincipalName])`

**Exempel på inmatning/utdata:** <br>

* **INMATNING** (post): NULL
* **INPUT** (userPrincipalName):John.Doe@contoso.com" "
* **UTDATA**John.Doe@contoso.com: " "

## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera etablering av användare/avetablering till SaaS-appar](../app-provisioning/user-provisioning.md)
* [Anpassa attributmappningar för användaresetablering](../app-provisioning/customize-application-attributes.md)
* [Omfångsfilter för etablering av användare](define-conditional-rules-for-provisioning-user-accounts.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Kontoetableringsmeddelanden](../app-provisioning/user-provisioning.md)
* [Lista över guider om hur man integrerar SaaS-appar](../saas-apps/tutorial-list.md)
