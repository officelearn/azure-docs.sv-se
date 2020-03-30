---
title: Azure AD Connect molntablering uttryck och funktion referens
description: Referens
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51c14fd7f427c29c47521a7355309e62ab2254ca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78298623"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Skriva uttryck för attributmappningar i Azure Active Directory
När du konfigurerar molnetablering är en av de typer av attributmappningar som du kan ange en uttrycksmappning. 

Med uttrycksmappningen kan du anpassa attribut med hjälp av ett skriptliknande uttryck.  På så sätt kan du omvandla lokala data till ett nytt eller annat värde.  Du kanske till exempel vill kombinera två attribut till ett enda attribut eftersom det här enskilda attributet används av ett av dina molnprogram.

Följande dokument kommer att omfatta skriptliknande uttryck som används för att omvandla data.  Detta är bara en del av processen.  Därefter måste du använda det här uttrycket och placera det i en webbbegäran till din klient.  Mer information om det finns i [Transformations](how-to-transformation.md)

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
| Lista över funktioner | Beskrivning |
|-----|----|
|[Lägg till](#append)|Tar ett källsträngvärde och lägger till suffixet i slutet av det.|
|[BitAnd ()](#bitand)|Funktionen BitAnd anger angivna bitar på ett värde.|
|[CBool (en)](#cbool)|Funktionen CBool returnerar en boolesk baserat på det utvärderade uttrycket|
|[Konvertera frånBase64](#convertfrombase64)|Funktionen ConvertFromBase64 konverterar det angivna base64-kodade värdet till en vanlig sträng.|
|[Konvertera tillBase64](#converttobase64)|Funktionen ConvertToBase64 konverterar en sträng till en Unicode base64-sträng. |
|[KonverteratoUTF8Hex](#converttoutf8hex)|Funktionen ConvertToUTF8Hex konverterar en sträng till ett UTF8 Hex-kodat värde.|
|[Räkna](#count)|Funktionen Antal returnerar antalet element i ett attribut med flera värden|
|[Cstr (av Cstr)](#cstr)|Funktionen CStr konverteras till en strängdatatyp.|
|[DatumFrånNum](#datefromnum)|Funktionen DateFromNum konverterar ett värde i AD:s datumformat till en DateTime-typ.|
|[DN-ersättning](#dncomponent)|Funktionen DNComponent returnerar värdet för en angiven DN-komponent som går från vänster.|
|[Fel](#error)|Funktionen Fel används för att returnera ett anpassat fel.|
|[FormatDateTime](#formatdatetime) |Tar en datumsträng från ett format och konverterar den till ett annat format.| 
|[Guid](#guid)|Funktionen Guid genererar ett nytt slumpmässigt GUID.|           
|[Iif](#iif)|Funktionen IIF returnerar en av en uppsättning möjliga värden baserat på ett angivet villkor.|
|[Instr](#instr)|Funktionen InStr hittar den första förekomsten av en delsträng i en sträng.|
|[Ärnull](#isnull)|Om uttrycket utvärderas till Null returneras true.|
|[IsNullOrEmpty (300000)](#isnullorempty)|Om uttrycket är null eller en tom sträng returneras true.|         
|[IsPresent (Representerar)](#ispresent)|Om uttrycket utvärderas till en sträng som inte är Null och inte är tom returneras true.|    
|[IsString (IsString)](#isstring)|Om uttrycket kan utvärderas till en strängtyp utvärderas funktionen IsString till True.|
|[Objekt](#item)|Funktionen Artikel returnerar ett objekt från en sträng/attribut med flera värden.|
|[Anslut](#join) |Join() liknar Append(), förutom att det kan kombinera flera **källsträngvärden** till en enda sträng och varje värde avgränsas med en **avgränsare.**| 
|[Vänster](#left)|Funktionen Vänster returnerar ett angivet antal tecken från vänster om en sträng.|
|[Mitten](#mid) |Returnerar en delsträng av källvärdet. En delsträng är en sträng som bara innehåller några av tecknen från källsträngen.|
|[NormaliseraDiakritiska tecken](#normalizediacritics)|Kräver ett strängargument. Returnerar strängen, men med alla diakritiska tecken som ersätts med motsvarande icke-diakritiska tecken.|
|[Inte](#not) |Vänd det booleska värdet för **källan**. Om **källvärdet** är "*Sant*", returnerar "*False*". Annars returnerar "*True*".| 
|[Ta bortDuplicates](#removeduplicates)|Funktionen RemoveDuplicates tar en sträng med flera värden och kontrollerar att varje värde är unikt.| 
|[Ersätt](#replace) |Ersätter värden i en sträng. | 
|[VäljUniqueValue](#selectuniquevalue)|Kräver minst två argument, som är unika värdegenereringsregler som definieras med uttryck. Funktionen utvärderar varje regel och kontrollerar sedan det värde som genereras för unikhet i målappen/katalogen.| 
|[Enkeltilldelning](#singleapproleassignment)|Returnerar en enda appRoleAstilldelning från listan över alla appRoleAstilldelningar som tilldelats en användare för ett visst program.| 
|[Split](#split)|Delar en sträng i en matris med flera värden med det angivna avgränsartecknet.|
|[StringFromSID](#stringfromsid)|Funktionen StringFromSid konverterar en bytematris som innehåller en säkerhetsidentifierare till en sträng.| 
|[StripSpaces (stripspaces)](#stripspaces) |Tar bort alla blanksteg (" ") tecken från källsträngen.| 
|[Växel](#switch)|När **källvärdet** matchar en **nyckel** **returnerar** värdet för **nyckeln**. | 
|[ToLower (tolower)](#tolower)|Tar ett *källsträngvärde* och konverterar det till gemener med hjälp av de kulturregler som anges.| 
|[ToUpper (TillUpper)](#toupper)|Tar ett *källsträngvärde* och konverterar det till versaler med hjälp av de kulturregler som anges.|
|[Trimma](#trim)|Funktionen Trimma tar bort inledande och avslutande blanksteg från en sträng.|
|[Word](#word)|Word-funktionen returnerar ett ord som finns i en sträng, baserat på parametrar som beskriver de avgränsare som ska användas och det ordnummer som ska returneras.|

---
### <a name="append"></a>Lägg till
**Funktion:**<br> Lägg till(källa, suffix)

**Beskrivning:**<br> Tar ett källsträngvärde och lägger till suffixet i slutet av det.

**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **Källkod** |Krävs |String |Vanligtvis namnet på attributet från källobjektet. |
   | **Suffix** |Krävs |String |Strängen som du vill lägga till i slutet av källvärdet. |

---
### <a name="bitand"></a>BitAnd ()
**Beskrivning:**  
Funktionen BitAnd anger angivna bitar på ett värde.

**Syntax:**  
`num BitAnd(num value1, num value2)`

* värde1, värde2: numeriska värden som ska VARA AND'ed tillsammans

**Anmärkningar:**  
Den här funktionen konverterar båda parametrarna till den binära representationen och ställer in lite till:

* 0 - om en eller båda av motsvarande bitar i *värde1* och *värde2* är 0
* 1 - om båda motsvarande bitar är 1.

Med andra ord returnerar den 0 i alla fall utom när motsvarande bitar av båda parametrarna är 1.

**Exempel:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Returnerar 7 eftersom hexadecimalt "F" OCH "F7" utvärderar till det här värdet.

---

### <a name="cbool"></a>CBool (en)
**Beskrivning:**  
Funktionen CBool returnerar en boolesk baserat på det utvärderade uttrycket

**Syntax:**  
`bool CBool(exp Expression)`

**Anmärkningar:**  
Om uttrycket utvärderas till ett värde som inte är noll returnerar CBool Sant, annars returneras False.

**Exempel:**  
`CBool([attrib1] = [attrib2])`  

Returnerar Sant om båda attributen har samma värde.

---
### <a name="convertfrombase64"></a>Konvertera frånBase64
**Beskrivning:**  
Funktionen ConvertFromBase64 konverterar det angivna base64-kodade värdet till en vanlig sträng.

**Syntax:**  
`str ConvertFromBase64(str source)`- förutsätter Unicode för kodning  
`str ConvertFromBase64(str source, enum Encoding)`

* källa: Base64 kodad sträng  
* Kodning: Unicode, ASCII, UTF8

**Exempel**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Båda exemplen återvänder "*Hello world!*"

---
### <a name="converttobase64"></a>Konvertera tillBase64
**Beskrivning:**  
Funktionen ConvertToBase64 konverterar en sträng till en Unicode base64-sträng.  
Konverterar värdet för en matris med heltal till motsvarande strängrepresentation som kodas med bas-64 siffror.

**Syntax:**  
`str ConvertToBase64(str source)`

**Exempel:**  
`ConvertToBase64("Hello world!")`  
Returnerar "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>KonverteratoUTF8Hex
**Beskrivning:**  
Funktionen ConvertToUTF8Hex konverterar en sträng till ett UTF8 Hex-kodat värde.

**Syntax:**  
`str ConvertToUTF8Hex(str source)`

**Anmärkningar:**  
Utdataformatet för den här funktionen används av Azure Active Directory som DN-attributformat.

**Exempel:**  
`ConvertToUTF8Hex("Hello world!")`  
Returnerar 48656C6C6F20776F726C6421

---
### <a name="count"></a>Antal
**Beskrivning:**  
Funktionen Antal returnerar antalet element i ett attribut med flera värden

**Syntax:**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr (CStr)
**Beskrivning:**  
Funktionen CStr konverteras till en strängdatatyp.

**Syntax:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* värde: Kan vara ett numeriskt värde, referensattribut eller Booleskt.

**Exempel:**  
`CStr([dn])`  
Kan returnera "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DatumFrånNum
**Beskrivning:**  
Funktionen DateFromNum konverterar ett värde i AD:s datumformat till en DateTime-typ.

**Syntax:**  
`dt DateFromNum(num value)`

**Exempel:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Returnerar en DateTime som representerar 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DN-ersättning
**Beskrivning:**  
Funktionen DNComponent returnerar värdet för en angiven DN-komponent som går från vänster.

**Syntax:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: referensattributet för att tolka
* ComponentNumber: Komponenten i DN för att returnera

**Exempel:**  
`DNComponent(CRef([dn]),1)`  
Om dn är "cn=Joe,ou=..." returneras Joe

---
### <a name="error"></a>Fel
**Beskrivning:**  
Funktionen Fel används för att returnera ett anpassat fel.

**Syntax:**  
`void Error(str ErrorMessage)`

**Exempel:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Om attributet accountName inte finns, kasta ett fel på objektet.

---
### <a name="formatdatetime"></a>FormatDateTime
**Funktion:**<br> FormatDateTime(källa, inputFormat, outputFormat)

**Beskrivning:**<br> Tar en datumsträng från ett format och konverterar den till ett annat format.

**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **Källkod** |Krävs |String |Vanligtvis namnet på attributet från källobjektet. |
   | **inputFormat** |Krävs |String |Förväntat format för källvärdet. Format som stöds [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)finns i . |
   | **outputFormat** |Krävs |String |Format för utdatadatumet. |

---
### <a name="guid"></a>GUID
**Beskrivning:**  
Funktionen Guid genererar ett nytt slumpmässigt GUID

**Syntax:**  
`str Guid()`

---
### <a name="iif"></a>Iif
**Beskrivning:**  
Funktionen IIF returnerar en av en uppsättning möjliga värden baserat på ett angivet villkor.

**Syntax:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* villkor: alla värden eller uttryck som kan utvärderas till sant eller falskt.
* valueIfTrue: Om villkoret utvärderas till true, det returnerade värdet.
* valueIfFalse: Om villkoret utvärderas till falskt, det returnerade värdet.

**Exempel:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Om användaren är praktikant returnerar aliaset för en användare med "t-" tillagt i början av den, annars returnerar användarens alias som det är.

---
### <a name="instr"></a>Instr
**Beskrivning:**  
Funktionen InStr hittar den första förekomsten av en delsträng i en sträng

**Syntax:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: sträng som ska sökas
* stringmatch: sträng som finns
* start: startposition för att hitta delsträngen
* jämför: vbTextCompare eller vbBinaryCompare

**Anmärkningar:**  
Returnerar den position där delsträngen hittades eller 0 om den inte hittas.

**Exempel:**  
`InStr("The quick brown fox","quick")`  
Utvärderar till 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Utvärderar till 7

---
### <a name="isnull"></a>Ärnull
**Beskrivning:**  
Om uttrycket utvärderas till Null returneras true.

**Syntax:**  
`bool IsNull(var Expression)`

**Anmärkningar:**  
För ett attribut uttrycks en Null av att attributet inte finns.

**Exempel:**  
`IsNull([displayName])`  
Returnerar Sant om attributet inte finns i CS eller MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty (300000)
**Beskrivning:**  
Om uttrycket är null eller en tom sträng returneras true.

**Syntax:**  
`bool IsNullOrEmpty(var Expression)`

**Anmärkningar:**  
För ett attribut utvärderas detta till Sant om attributet saknas eller finns men är en tom sträng.  
Inversen av den här funktionen heter IsPresent.

**Exempel:**  
`IsNullOrEmpty([displayName])`  
Returnerar Sant om attributet inte finns eller är en tom sträng i CS eller MV.

---
### <a name="ispresent"></a>IsPresent (Representerar)
**Beskrivning:**  
Om uttrycket utvärderas till en sträng som inte är Null och inte är tom returneras true.

**Syntax:**  
`bool IsPresent(var expression)`

**Anmärkningar:**  
Inversen av denna funktion heter IsNullOrEmpty.

**Exempel:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Objekt
**Beskrivning:**  
Funktionen Artikel returnerar ett objekt från en sträng/attribut med flera värden.

**Syntax:**  
`var Item(mvstr attribute, num index)`

* attribut: attribut med flera värden
* index: index till ett objekt i strängen med flera värden.

**Anmärkningar:**  
Funktionen Artikel är användbar tillsammans med funktionen Innehåller eftersom den senare funktionen returnerar indexet till ett objekt i attributet med flera värden.

Genererar ett fel om indexet är utanför gränserna.

**Exempel:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Returnerar den primära e-postadressen.

---
### <a name="isstring"></a>IsString (IsString)
**Beskrivning:**  
Om uttrycket kan utvärderas till en strängtyp utvärderas funktionen IsString till True.

**Syntax:**  
`bool IsString(var expression)`

**Anmärkningar:**  
Används för att avgöra om CStr() kan lyckas tolka uttrycket.

---
### <a name="join"></a>Slå ihop
**Funktion:**<br> Join(separator, source1, source2, ...)

**Beskrivning:**<br> Join() liknar Append(), förutom att det kan kombinera flera **källsträngvärden** till en enda sträng och varje värde avgränsas med en **avgränsare.**

Om ett av källvärdena är ett attribut med flera värden sammanfogas alla värden i attributet, avgränsat med avgränsarens värde.

**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **Avgränsare** |Krävs |String |Sträng som används för att separera källvärden när de sammanfogas till en sträng. Kan vara "" om ingen avgränsare krävs. |
   | **källa1 ... källaN** |Obligatoriskt, variabelt antal gånger |String |Strängvärden som ska sammanfogas. |

---
### <a name="left"></a>Vänster
**Beskrivning:**  
Funktionen Vänster returnerar ett angivet antal tecken från vänster om en sträng.

**Syntax:**  
`str Left(str string, num NumChars)`

* sträng: strängen för att returnera tecken från
* NumChars: ett nummer som identifierar antalet tecken som ska returneras från början (vänster) i strängen

**Anmärkningar:**  
En sträng som innehåller de första numChars-tecknen i sträng:

* Om numChars = 0 returnerar du tom sträng.
* Om numChars < 0 returnerar du indatasträngen.
* Om strängen är null returnerar du tom sträng.

Om strängen innehåller färre tecken än det tal som anges i numChars returneras en sträng som är identisk med strängen (det vill de vill ha alla tecken i parameter 1).

**Exempel:**  
`Left("John Doe", 3)`  
Returnerar `Joh`.

---
### <a name="mid"></a>Mellan
**Funktion:**<br> Mitten (källa, start, längd)

**Beskrivning:**<br> Returnerar en delsträng av källvärdet. En delsträng är en sträng som bara innehåller några av tecknen från källsträngen.

**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **Källkod** |Krävs |String |Vanligtvis namnet på attributet. |
   | **start** |Krävs |heltal |Index i **källsträngen** där delsträng ska börja. Första tecknet i strängen kommer att ha index på 1, andra tecken kommer att ha index 2, och så vidare. |
   | **Längd** |Krävs |heltal |Längden på delsträngen. Om längden slutar utanför **källsträngen** returneras delsträng från **startindex** till slutet av **källsträngen.** |

---
### <a name="normalizediacritics"></a>NormaliseraDiakritiska tecken
**Funktion:**<br> NormaliseraDiacritics(källa)

**Beskrivning:**<br> Kräver ett strängargument. Returnerar strängen, men med alla diakritiska tecken som ersätts med motsvarande icke-diakritiska tecken. Används vanligtvis för att konvertera förnamn och efternamn som innehåller diakritiska tecken (accenttecken) till juridiska värden som kan användas i olika användaridentifierare, till exempel användarnamn, SAM-kontonamn och e-postadresser.

**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **Källkod** |Krävs |String | Vanligtvis ett förnamn eller efternamn attribut. |

---
### <a name="not"></a>Inte
**Funktion:**<br> Inte(källa)

**Beskrivning:**<br> Vänd det booleska värdet för **källan**. Om **källvärdet** är "*Sant*", returnerar "*False*". Annars returnerar "*True*".

**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **Källkod** |Krävs |Boolesk sträng |Förväntade **källvärden** är "True" eller "False". |

---
### <a name="removeduplicates"></a>Ta bortDuplicates
**Beskrivning:**  
Funktionen RemoveDuplicates tar en sträng med flera värden och kontrollerar att varje värde är unikt.

**Syntax:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exempel:**  
`RemoveDuplicates([proxyAddresses])`  
Returnerar ett attribut för sanitized proxyAddress där alla dubblettvärden har tagits bort.

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
   | **Källkod** |Krävs |String |Vanligtvis namnet på attributet från **källobjektet.** |
   | **gamlaVärdera** |Valfri |String |Värde som ska ersättas i **källa** eller **mall**. |
   | **regexMönster** |Valfri |String |Regex mönster för det värde som ska ersättas i **källa**. Eller när **ersättningPropertyName** används, mönster för att extrahera värde från **ersätterPropertyName**. |
   | **regexGroupName (regexGroupName)** |Valfri |String |Namnet på gruppen inuti **regexPattern**. Endast när **ersättningPropertyName** används, kommer vi att extrahera värdet för denna grupp som **ersätterValue** från **ersätterPropertyName**. |
   | **ersättningVärdera** |Valfri |String |Nytt värde att ersätta gammalt med. |
   | **ersättErNamn** |Valfri |String |Namnet på det attribut som ska användas för ersättningsvärde |
   | **Mall** |Valfri |String |När **mallvärdet** anges letar vi efter **oldValue** i mallen och ersätter det med **källvärdet.** |

---
### <a name="selectuniquevalue"></a>VäljUniqueValue
**Funktion:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Beskrivning:**<br> Kräver minst två argument, som är unika värdegenereringsregler som definieras med uttryck. Funktionen utvärderar varje regel och kontrollerar sedan det värde som genereras för unikhet i målappen/katalogen. Det första unika värdet som hittas är det som returneras. Om alla värden redan finns i målet spärras transaktionen och orsaken loggas i granskningsloggarna. Det finns ingen övre gräns för antalet argument som kan tillhandahållas.

> [!NOTE]
> - Detta är en toppnivå funktion, det kan inte kapslas.
> - Den här funktionen kan inte tillämpas på attribut som har en matchande prioritet.  
> - Den här funktionen är endast avsedd att användas för att skapa post. När du använder den med ett attribut ställer du in egenskapen **Använd mappning** **på Endast när objektet skapas**.
> - Den här funktionen stöds för närvarande endast för "Arbetsdag till Active Directory-användaretablering". Den kan inte användas med andra etableringsprogram. 


**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **unikaValueRule1 ... unikaValueRuleN** |Minst 2 krävs, ingen övre gräns |String | Lista över unika värdegenereringsregler att utvärdera. |


---
### <a name="singleapproleassignment"></a>Enkeltilldelning
**Funktion:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beskrivning:**<br> Returnerar en enda appRoleAstilldelning från listan över alla appRoleAstilldelningar som tilldelats en användare för ett visst program. Den här funktionen krävs för att konvertera appRoleAssignments-objektet till en enda rollnamnssträng. Observera att det bästa är att se till att endast en appRoleAstilldelning tilldelas en användare i taget, och om flera roller tilldelas kan det hända att rollsträngen som returneras inte är förutsägbar. 

**Parametrar:**<br> 

  | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
  |--- | --- | --- | --- |
  | **[appRoleAstilldelningar]** |Krävs |String |**[appRoleAssignments]** objekt. |

---
### <a name="split"></a>Dela
**Funktion:**<br> Split(källa, avgränsare)

**Beskrivning:**<br> Delar en sträng i en matris med flera värden med det angivna avgränsartecknet.

**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **Källkod** |Krävs |String |**källvärdet** som ska uppdateras. |
   | **Avgränsare** |Krävs |String |Anger det tecken som ska användas för att dela strängen (exempel: "") |

---
### <a name="stringfromsid"></a>StringFromSid
**Beskrivning:**  
Funktionen StringFromSid konverterar en bytematris som innehåller en säkerhetsidentifierare till en sträng.

**Syntax:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces (stripspaces)
**Funktion:**<br> StripSpaces(källa)

**Beskrivning:**<br> Tar bort alla blanksteg (" ") tecken från källsträngen.

**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **Källkod** |Krävs |String |**källvärdet** som ska uppdateras. |

---
### <a name="switch"></a>Växel
**Funktion:**<br> Switch(källa, defaultValue, key1, value1, key2, value2, ...)

**Beskrivning:**<br> När **källvärdet** matchar en **nyckel** **returnerar** värdet för **nyckeln**. Om **källvärdet** inte matchar några nycklar returnerar **defaultValue**.  **Nyckel-** och **värdeparametrar** måste alltid finnas i par. Funktionen förväntar sig alltid ett jämnt antal parametrar.

**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **Källkod** |Krävs |String |**Källvärde** att kontrollera. |
   | **Standardvärde** |Valfri |String |Standardvärde som ska användas när källan inte matchar några nycklar. Kan vara tom sträng (""). |
   | **key** |Krävs |String |**Nyckel** att jämföra **källvärde** med. |
   | **värde** |Krävs |String |Ersättningsvärde för **källan** som matchar nyckeln. |

---
### <a name="tolower"></a>ToLower (tolower)
**Funktion:**<br> ToLower(källa, kultur)

**Beskrivning:**<br> Tar ett *källsträngvärde* och konverterar det till gemener med hjälp av de kulturregler som anges. Om det inte finns någon *kultur* info anges, då det kommer att använda Invariant kultur.

**Parametrar:**<br> 

   | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
   | --- | --- | --- | --- |
   | **Källkod** |Krävs |String |Vanligtvis namn på attributet från källobjektet |
   | **Kultur** |Valfri |String |Formatet för kulturnamnet baserat på RFC 4646 är *languagecode2-country/regioncode2*, där *språkkod2* är språkkoden med två bokstäver och *lands-/regionkod2* är subkulturkoden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver som härleds från ISO 639-2.|

---

### <a name="toupper"></a>ToUpper (TillUpper)
**Funktion:**<br> ToUpper (källa, kultur)

**Beskrivning:**<br> Tar ett *källsträngvärde* och konverterar det till versaler med hjälp av de kulturregler som anges. Om det inte finns någon *kultur* info anges, då det kommer att använda Invariant kultur.

**Parametrar:**<br> 

  | Namn | Obligatoriskt/ upprepande | Typ | Anteckningar |
  | --- | --- | --- | --- |
  | **Källkod** |Krävs |String |Vanligtvis namnet på attributet från källobjektet. |
  | **Kultur** |Valfri |String |Formatet för kulturnamnet baserat på RFC 4646 är *languagecode2-country/regioncode2*, där *språkkod2* är språkkoden med två bokstäver och *lands-/regionkod2* är subkulturkoden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver som härleds från ISO 639-2.|

---

### <a name="trim"></a>Trim
**Beskrivning:**  
Funktionen Trimma tar bort inledande och avslutande blanksteg från en sträng.

**Syntax:**  
`str Trim(str value)`  

**Exempel:**  
`Trim(" Test ")`  
Returnerar "Test".

`Trim([proxyAddresses])`  
Tar bort inledande och avslutande blanksteg för varje värde i attributet proxyAddress.

---
### <a name="word"></a>Word
**Beskrivning:**  
Word-funktionen returnerar ett ord som finns i en sträng, baserat på parametrar som beskriver de avgränsare som ska användas och det ordnummer som ska returneras.

**Syntax:**  
`str Word(str string, num WordNumber, str delimiters)`

* sträng: strängen att returnera ett ord från.
* WordNumber: ett nummer som identifierar vilket ordnummer som ska returneras.
* avgränsare: en sträng som representerar avgränsaren/avgränsarna som ska användas för att identifiera ord

**Anmärkningar:**  
Varje teckensträng i sträng avgränsad med ett av tecknen i avgränsare identifieras som ord:

* Om tal < 1 returnerar tom sträng.
* Om strängen är null returnerar den tomma strängen.

Om strängen innehåller mindre än talord, eller om strängen inte innehåller några ord som identifieras av avgränsare, returneras en tom sträng.

**Exempel:**  
`Word("The quick brown fox",3," ")`  
Returnerar "brun"

`Word("This,string!has&many separators",3,",!&#")`  
Skulle återvända "har"

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


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
