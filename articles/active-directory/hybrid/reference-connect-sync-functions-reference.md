---
title: 'Azure AD Connect-synkronisering: Functions-referens | Microsoft Docs'
description: Referens för uttryck för deklarativ etablering i Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 54378876ed3f8326a19aafa4d71347d17f1c4bab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162556"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect-synkronisering: Referens för funktioner
I Azure AD Connect används funktioner för att ändra ett attributvärde under synkroniseringen.  
Syntaxen för funktionerna uttrycks i följande format:  
`<output type> FunctionName(<input type> <position name>, ..)`

Om en funktion är överbelastad och accepterar flera syntax, visas alla giltig syntax.  
Funktionerna är starkt typbestämd och de bekräftar att typ som skickades i matchar typen dokumenterade.  
Om typen inte matchar, genereras ett fel.

Typerna uttrycks med följande syntax:

* **bin** – binära
* **bool** – booleskt
* **DT** – UTC-datum/tid
* **Enum** – uppräkning av kända konstanter
* **EXP** – uttryck, som ska utvärderas till ett booleskt värde
* **mvbin** – multivärdes binära
* **mvstr** – multivärdes sträng
* **mvref** – multivärdes-referens
* **NUM** – numeriskt
* **REF** – referens
* **str** – String
* **var** – en variant (nästan) någon annan typ
* **Annullera** – inte returnerar ett värde

Funktioner med vilka **mvbin**, **mvstr**, och **mvref** fungerar bara på flera värden attribut. Fungerar med **bin**, **str**, och **ref** arbetet med både enstaka och flera värden.

## <a name="functions-reference"></a>Referens för funktioner
| Lista över funktioner |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
| **Certifikat** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[ CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Konvertering** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#StringFromGuid) |[StringFromSid](#stringfromsid) | |
| **Datum / tid** | | | | |
| [DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[nu](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Directory** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Utvärdering** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matematiska** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Flera värden** | | | | |
| [innehåller](#contains) |[Antal](#count) |[Objekt](#item) |[ItemOrNull](#itemornull) | |
| [Anslut dig](#join) |[RemoveDuplicates](#removeduplicates) |[Split](#split) | | |
| **Programflöde** | | | | |
| [Fel](#error) |[IIF](#iif) |[Välj](#select) |[Switch](#switch) | |
| [där](#where) |[med](#with) | | | |
| **Text** | | | | |
| [GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [Vänster](#left) |[Len](#len) |[LTrim](#ltrim) |[Mid](#mid) | |
| [PadLeft](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[Ersätt](#replace) | |
| [ReplaceChars](#replacechars) |[Right](#right) |[RTrim](#rtrim) |[Trim](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

- - -
### <a name="bitand"></a>BitAnd
**Beskrivning:**  
Funktionen BitAnd anger angivna bits på ett värde.

**Syntax:**  
`num BitAnd(num value1, num value2)`

* value1, value2: numeriska värden som ska vara AND'ed tillsammans

**Anmärkning:**  
Den här funktionen konverterar båda parametrarna till binär representation och anger lite till:

* 0 – om en eller båda motsvarande bitar i *mask* och *flaggan* är 0
* 1 – om båda motsvarande bits är 1.

Med andra ord, returnerar 0, utom när de motsvarande delarna av båda parametrarna är 1.

**Exempel:**  
`BitAnd(&HF, &HF7)`  
Returnerar 7 eftersom hexadecimala ”F” och ”F7” utvärderas till det här värdet.

- - -
### <a name="bitor"></a>BitOr
**Beskrivning:**  
Funktionen BITOR-anger angivna bits på ett värde.

**Syntax:**  
`num BitOr(num value1, num value2)`

* value1, value2: numeriska värden som ska vara sammansatta med or tillsammans

**Anmärkning:**  
Den här funktionen konverterar båda parametrarna till binär representation och anger lite 1 om en eller båda motsvarande bitar i mask och flaggan är mellan 1 och 0 om båda motsvarande bits är 0. Med andra ord, returnerar 1, utom där de motsvarande delarna av båda parametrarna är 0.

- - -
### <a name="cbool"></a>CBool
**Beskrivning:**  
Funktionen CBool returnerar ett booleskt värde baserat på det utvärderade uttrycket

**Syntax:**  
`bool CBool(exp Expression)`

**Anmärkning:**  
Om uttrycket utvärderas till ett annat värde och sedan CBool returnerar True, annars returneras False.

**Exempel:**  
`CBool([attrib1] = [attrib2])`  

Returnerar True om båda attribut har samma värde.

- - -
### <a name="cdate"></a>CDate
**Beskrivning:**  
Funktionen CDate returnerar en UTC-datum/tid från en sträng. DateTime är inte en intern Attributtyp som är synkroniserade men används av vissa funktioner.

**Syntax:**  
`dt CDate(str value)`

* Värde: En sträng med ett datum, tid och du kan också tidszon

**Anmärkning:**  
Den returnerade strängen är alltid i UTC.

**Exempel:**  
`CDate([employeeStartTime])`  
Returnerar ett datetime-värde baserat på medarbetarens starttid

`CDate("2013-01-10 4:00 PM -8")`  
Returnerar ett datum/tid som representerar ”2013-01-11 12:00 AM”


- - -
### <a name="certextensionoids"></a>CertExtensionOids
**Beskrivning:**  
Returnerar Oid-värden för alla kritiska tillägg av ett certifikatobjekt.

**Syntax:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certformat"></a>CertFormat
**Beskrivning:**  
Returnerar namnet på formatet för den här X.509v3-certifikat.

**Syntax:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certfriendlyname"></a>CertFriendlyName
**Beskrivning:**  
Returnerar det associerade aliaset för ett certifikat.

**Syntax:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certhashstring"></a>CertHashString
**Beskrivning:**  
Returnerar SHA1-hash-värdet för X.509v3-certifikat som en hexadecimal sträng.

**Syntax:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certissuer"></a>CertIssuer
**Beskrivning:**  
Returnerar namnet på den certifikatutfärdare som utfärdade X.509v3-certifikat.

**Syntax:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certissuerdn"></a>CertIssuerDN
**Beskrivning:**  
Returnerar det unika namnet på certifikatutfärdaren.

**Syntax:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certissueroid"></a>CertIssuerOid
**Beskrivning:**  
Returnerar Oid för certifikatutfärdaren.

**Syntax:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Beskrivning:**  
Returnerar nyckelalgoritm-information för den här X.509v3-certifikat som en sträng.

**Syntax:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Beskrivning:**  
Returnerar nyckelalgoritm parametrarna för X.509v3-certifikat som en hexadecimal sträng.

**Syntax:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certnameinfo"></a>CertNameInfo
**Beskrivning:**  
Returnerar ämne och Utfärdarens namn från ett certifikat.

**Syntax:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.
*   X509NameType: X509NameType värdet för ämnet.
*   includesIssuerName: true att inkludera Utfärdarens namn; Annars, FALSKT.

- - -
### <a name="certnotafter"></a>CertNotAfter
**Beskrivning:**  
Returnerar datum i lokal tid efter vilken en certifikatet inte längre är giltigt.

**Syntax:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certnotbefore"></a>CertNotBefore
**Beskrivning:**  
Returnerar datum i lokal tid som ett certifikat börjar gälla.

**Syntax:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Beskrivning:**  
Returnerar Oid för den offentliga nyckeln för X.509v3-certifikat.

**Syntax:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Beskrivning:**  
Returnerar Oid för offentlig nyckel parametrarna för X.509v3-certifikat.

**Syntax:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certserialnumber"></a>CertSerialNumber
**Beskrivning:**  
Returnerar serienumret för X.509v3-certifikat.

**Syntax:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Beskrivning:**  
Returnerar Oid av algoritmen som används för att skapa signaturen för ett certifikat.

**Syntax:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certsubject"></a>CertSubject
**Beskrivning:**  
Hämtar det unika ämnesnamnet från ett certifikat.

**Syntax:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Beskrivning:**  
Returnerar det unika ämnesnamnet från ett certifikat.

**Syntax:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Beskrivning:**  
Returnerar Oid för ämnesnamnet från ett certifikat.

**Syntax:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certthumbprint"></a>CertThumbprint
**Beskrivning:**  
Returnerar tumavtrycket för ett certifikat.

**Syntax:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="certversion"></a>CertVersion
**Beskrivning:**  
Returnerar X.509-Formatversion för ett certifikat.

**Syntax:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.

- - -
### <a name="cguid"></a>CGuid
**Beskrivning:**  
Funktionen CGuid konverterar strängrepresentation av en GUID till dess binär representation.

**Syntax:**  
`bin CGuid(str GUID)`

* En sträng formaterad i det här mönstret: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx eller {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

- - -
### <a name="contains"></a>Contains
**Beskrivning:**  
Contains-funktionen returnerar en sträng inuti ett flervärdesattribut

**Syntax:**  
`num Contains (mvstring attribute, str search)` -skiftlägeskänsligt  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` -skiftlägeskänsligt

* attributet: attributet med flera värden för att söka.
* Sök: sträng att söka efter i attributet.
* Casetype: CaseInsensitive eller CaseSensitive.

Returnerar index i attributet med flera värden där strängen hittades. 0 returneras om strängen inte hittas.

**Anmärkning:**  
Söker efter delsträngar i värdena för flera värden strängattribut sökningen.  
Sökt sträng måste exakt matcha värdet för att anses vara en matchning för referensattribut.

**Exempel:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Om attributet proxyAddresses har en primär e-postadress (anges med versaler ”SMTP”:), returnerar proxyAddress-attribut, annars returneras ett fel.

- - -
### <a name="convertfrombase64"></a>ConvertFromBase64
**Beskrivning:**  
Funktionen ConvertFromBase64 konverterar det angivna base64-kodad värdet till en vanlig sträng.

**Syntax:**  
`str ConvertFromBase64(str source)` -förutsätter Unicode för kodning  
`str ConvertFromBase64(str source, enum Encoding)`

* Källa: Base64-kodad sträng  
* Kodning: Unicode, ASCII, UTF8

**Exempel**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Båda exemplen ger ”*Hello world!*”

- - -
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Beskrivning:**  
Funktionen ConvertFromUTF8Hex konverterar det angivna UTF8 Hex-kodade-värdet till en sträng.

**Syntax:**  
`str ConvertFromUTF8Hex(str source)`

* Källa: UTF8 2-bytes kodade förekomster av textsträngen

**Anmärkning:**  
Skillnaden mellan den här funktionen och ConvertFromBase64([],UTF8) i att resultatet är eget för attributet DN.  
Det här formatet används av Azure Active Directory som unikt namn.

**Exempel:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Returnerar ”*Hello world!*”

- - -
### <a name="converttobase64"></a>ConvertToBase64
**Beskrivning:**  
Funktionen ConvertToBase64 konverterar en sträng till en Unicode base64-sträng.  
Konverterar värdet för en matris av heltal till dess motsvarande strängrepresentation som är kodat med Base64-siffror.

**Syntax:**  
`str ConvertToBase64(str source)`

**Exempel:**  
`ConvertToBase64("Hello world!")`  
Returns "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

- - -
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Beskrivning:**  
Funktionen ConvertToUTF8Hex konverterar en sträng till ett värde för UTF8 Hex-kodade.

**Syntax:**  
`str ConvertToUTF8Hex(str source)`

**Anmärkning:**  
Utdataformatet för den här funktionen används av Azure Active Directory som DN Attributformat.

**Exempel:**  
`ConvertToUTF8Hex("Hello world!")`  
Returns 48656C6C6F20776F726C6421

- - -
### <a name="count"></a>Antal
**Beskrivning:**  
Count-funktionen returnerar antalet element i ett flervärdesattribut

**Syntax:**  
`num Count(mvstr attribute)`

- - -
### <a name="cnum"></a>CNum
**Beskrivning:**  
Funktionen CNum tar en sträng och returnerar en numerisk datatyp.

**Syntax:**  
`num CNum(str value)`

- - -
### <a name="cref"></a>CRef
**Beskrivning:**  
Konverterar en sträng till ett referensattribut

**Syntax:**  
`ref CRef(str value)`

**Exempel:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

- - -
### <a name="cstr"></a>CStr
**Beskrivning:**  
Funktionen CStr konverteras till datatypen string.

**Syntax:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* Värde: Kan vara ett numeriskt värde, referensattribut eller booleskt värde.

**Exempel:**  
`CStr([dn])`  
Kan returnera ”cn = Joe, dc = contoso, dc = com”

- - -
### <a name="dateadd"></a>DateAdd
**Beskrivning:**  
Returnerar ett datum som innehåller ett datum som ett angivet tidsintervall har lagts till.

**Syntax:**  
`dt DateAdd(str interval, num value, dt date)`

* intervall: Stränguttryck som är tidsintervallet som du vill lägga till. Strängen måste ha något av följande värden:
  * åååå år
  * q Quarter
  * miljoner månad
  * y-dag på året
  * d dag
  * w veckodag
  * ww vecka
  * h timme
  * n minut
  * s andra
* Värde: Antal enheter som du vill lägga till. Det kan vara positivt (för att hämta framtida datum) eller negativt (för att hämta tidigare datum).
* datum: Datum/tid som representerar som intervallet ska läggas till.

**Exempel:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Lägger till tre månader och returnerar ett datetime-värde som representerar ”2001-04-01”.

- - -
### <a name="datefromnum"></a>DateFromNum
**Beskrivning:**  
Funktionen DateFromNum konverterar ett värde i Active Directorys datum-format till ett DateTime-typen.

**Syntax:**  
`dt DateFromNum(num value)`

**Exempel:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Returnerar ett datetime-värde som representerar 2012-01-01 23:00:00

- - -
### <a name="dncomponent"></a>DNComponent
**Beskrivning:**  
DNComponent returneras värdet för en angiven DN-komponent som kommer från vänster.

**Syntax:**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: referensattribut att tolka
* ComponentNumber: Komponenten i det unika namnet ska returneras

**Exempel:**  
`DNComponent(CRef([dn]),1)`  
Om dn är ”cn = Joe, ou =...”, returneras Joe

- - -
### <a name="dncomponentrev"></a>DNComponentRev
**Beskrivning:**  
DNComponentRev returneras värdet för en angiven DN-komponent som kommer från höger (slut).

**Syntax:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: referensattribut att tolka
* ComponentNumber - komponenten i det unika namnet ska returneras
* Alternativ: DC – Ignorera alla komponenter med ”dc =”

**Exempel:**  
If dn is "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com" then  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Båda att returnera oss.

- - -
### <a name="error"></a>Fel
**Beskrivning:**  
Fel-funktionen används för att returnera ett anpassat fel.

**Syntax:**  
`void Error(str ErrorMessage)`

**Exempel:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Om attributet accountName inte är tillgänglig, returnerade ett fel på objektet.

- - -
### <a name="escapedncomponent"></a>EscapeDNComponent
**Beskrivning:**  
Funktionen EscapeDNComponent tar en komponent i ett unikt namn och lämnar den så att den kan visas i LDAP.

**Syntax:**  
`str EscapeDNComponent(str value)`

**Exempel:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Ser till att objektet kan skapas i en LDAP-katalog, även om attributet visningsnamn har tecken måste undantas i LDAP.

- - -
### <a name="formatdatetime"></a>formatDateTime
**Beskrivning:**  
Funktionen FormatDateTime används för att formatera ett datetime-värde till en sträng med ett angivet format

**Syntax:**  
`str FormatDateTime(dt value, str format)`

* värde: ett värde i DateTime-format
* format: en sträng som representerar format för att konvertera till.

**Anmärkning:**  
De möjliga värdena för formatet finns här: [Anpassat datum och tid-format för funktionen FORMAT](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Exempel:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resultat i ”2007-12-25”.

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Kan leda till ”20140905081453.0Z”

- - -
### <a name="guid"></a>GUID
**Beskrivning:**  
Funktionen Guid genererar en ny, slumpmässig GUID

**Syntax:**  
`str Guid()`

- - -
### <a name="iif"></a>IIF
**Beskrivning:**  
Funktionen returnerar ett av en uppsättning möjliga värden baserat på ett angivet villkor.

**Syntax:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* villkor: ett värde eller uttryck som kan utvärderas till true eller false.
* värdeomsant: Om villkoret utvärderas till SANT, det returnerade värdet.
* valueIfFalse: Om villkoret utvärderas till false, värdet som returneras.

**Exempel:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Om användaren är en intern, returnerar du alias för en användare med ”t-” läggs till i början av det, annan returnerar användarens alias skick.

- - -
### <a name="instr"></a>InStr
**Beskrivning:**  
InStr funktionen söker efter den första förekomsten av en understräng i en sträng

**Syntax:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: strängen som ska sökas igenom
* stringmatch: strängen som ska hittas
* Starta: startposition för att hitta delsträngen
* compare: vbTextCompare or vbBinaryCompare

**Anmärkning:**  
Returnerar positionen där delsträngen hittades eller 0 om hittades inte.

**Exempel:**  
`InStr("The quick brown fox","quick")`  
Evalues till 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Utvärderar till 7

- - -
### <a name="instrrev"></a>InStrRev
**Beskrivning:**  
Funktionen InStrRev söker efter den sista förekomsten av en understräng i en sträng

**Syntax:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: strängen som ska sökas igenom
* stringmatch: strängen som ska hittas
* Starta: startposition för att hitta delsträngen
* compare: vbTextCompare or vbBinaryCompare

**Anmärkning:**  
Returnerar positionen där delsträngen hittades eller 0 om hittades inte.

**Exempel:**  
`InStrRev("abbcdbbbef","bb")`  
Returnerar 7

- - -
### <a name="isbitset"></a>IsBitSet
**Beskrivning:**  
Funktionen IsBitSet nätverkstester om en stund har angetts eller inte

**Syntax:**  
`bool IsBitSet(num value, num flag)`

* värde: ett numeriskt värde som är evaluated.flag: ett numeriskt värde som bitar som ska utvärderas

**Exempel:**  
`IsBitSet(&HF,4)`  
Returnerar SANT eftersom ”4”-biten är aktiverad i det hexadecimala värdet ”F”

- - -
### <a name="isdate"></a>IsDate
**Beskrivning:**  
Om uttrycket kan vara utvärderar som en DateTime-typ, och sedan funktionen IsDate utvärderas till SANT.

**Syntax:**  
`bool IsDate(var Expression)`

**Anmärkning:**  
Används för att avgöra om CDate() kan genomföras.

- - -
### <a name="iscert"></a>IsCert
**Beskrivning:**  
Returnerar SANT om rådata kan serialiseras i .NET X509Certificate2 certifikat-objekt.

**Syntax:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Byte-matris representation av ett X.509-certifikat. Byte-matris kan vara binär (DER)-kodade eller Base64-kodad X.509-data.
- - -
### <a name="isempty"></a>IsEmpty
**Beskrivning:**  
Om attributet finns i CS eller MV men utvärderas till en tom sträng, och sedan funktionen IsEmpty utvärderas till SANT.

**Syntax:**  
`bool IsEmpty(var Expression)`

- - -
### <a name="isguid"></a>IsGuid
**Beskrivning:**  
Om strängen kunde konverteras till ett GUID, utvärderas funktionen IsGuid till true.

**Syntax:**  
`bool IsGuid(str GUID)`

**Anmärkning:**  
Ett GUID som har definierats som en sträng som följa en av dessa mönster: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx eller {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Används för att avgöra om CGuid() kan genomföras.

**Exempel:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Om StrAttribute har ett GUID-format, returnerar en binär representation, annars returnerar Null.

- - -
### <a name="isnull"></a>IsNull
**Beskrivning:**  
Om uttrycket utvärderas till Null, returnerar funktionen IsNull true.

**Syntax:**  
`bool IsNull(var Expression)`

**Anmärkning:**  
Ett null-värde uttrycks av att attributet för ett attribut.

**Exempel:**  
`IsNull([displayName])`  
Returnerar True om attributet inte finns i CS eller MV.

- - -
### <a name="isnullorempty"></a>IsNullOrEmpty
**Beskrivning:**  
Om uttrycket är null eller en tom sträng, returnerar funktionen IsNullOrEmpty true.

**Syntax:**  
`bool IsNullOrEmpty(var Expression)`

**Anmärkning:**  
För ett attribut, skulle detta utvärderas till SANT om attributet finns inte eller finns men är en tom sträng.  
Inversen till den här funktionen har namnet IsPresent.

**Exempel:**  
`IsNullOrEmpty([displayName])`  
Returnerar True om attributet finns inte eller är en tom sträng i CS eller MV.

- - -
### <a name="isnumeric"></a>IsNumeric
**Beskrivning:**  
IsNumeric-funktionen returnerar ett booleskt värde som anger om ett uttryck kan utvärderas som en nummertypen.

**Syntax:**  
`bool IsNumeric(var Expression)`

**Anmärkning:**  
Används för att avgöra om CNum() kan vara att parsa uttrycket.

- - -
### <a name="isstring"></a>IsString
**Beskrivning:**  
Om det kan utvärderas till en strängtyp, utvärderar funktionen IsString till True.

**Syntax:**  
`bool IsString(var expression)`

**Anmärkning:**  
Används för att avgöra om CStr() kan vara att parsa uttrycket.

- - -
### <a name="ispresent"></a>IsPresent
**Beskrivning:**  
Om uttrycket utvärderas till en sträng som inte är Null och inte är tom, returnerar funktionen IsPresent true.

**Syntax:**  
`bool IsPresent(var expression)`

**Anmärkning:**  
Inversen till den här funktionen har namnet IsNullOrEmpty.

**Exempel:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

- - -
### <a name="item"></a>Objekt
**Beskrivning:**  
Funktionen Item returnerar ett objekt från en sträng/flervärdesattribut.

**Syntax:**  
`var Item(mvstr attribute, num index)`

* attributet: flervärdesattribut
* index: index till ett objekt i strängen med flera värden.

**Anmärkning:**  
Funktionen Item är användbar tillsammans med funktionen innehåller eftersom funktionen senare returnerar indexet för ett objekt i attributet med flera värden.

Genererar ett fel om indexet är utanför intervallet.

**Exempel:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Returnerar den primära e-postadressen.

- - -
### <a name="itemornull"></a>ItemOrNull
**Beskrivning:**  
ItemOrNull-funktionen returnerar ett objekt från en sträng/flervärdesattribut.

**Syntax:**  
`var ItemOrNull(mvstr attribute, num index)`

* attributet: flervärdesattribut
* index: index till ett objekt i strängen med flera värden.

**Anmärkning:**  
ItemOrNull-funktionen är användbar tillsammans med funktionen innehåller eftersom funktionen senare returnerar indexet för ett objekt i attributet med flera värden.

Om indexet är utanför intervallet, returnerar du värdet Null.

- - -
### <a name="join"></a>Slå ihop
**Beskrivning:**  
Join-funktionen tar en sträng med flera värden och returnerar en enstaka sträng med angiven avgränsare infogas mellan varje element.

**Syntax:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* attribut: Flervärdesattribut som innehåller strängar som ska anslutas.
* avgränsare: Valfri sträng, som används för att avgränsa delsträngar i den returnerade strängen. Om detta utelämnas blir blanksteg (””) används. Om avgränsare är en tom sträng (””) eller något, alla objekt i listan är sammanfogat med utan avgränsare.

**Kommentarer**  
Det finns paritet mellan funktionerna koppling och dela. Join-funktionen tar en matris med strängar och kopplar ihop dem med hjälp av en avgränsare sträng för att returnera en sträng. Funktionen Split tar en sträng och skiljer den på avgränsare att returnera en matris med strängar. En viktig skillnad är dock att Join kan konkatenera strängar med valfri avgränsare sträng, dela kan endast separata strängar med avgränsaren ett enskilt tecken.

**Exempel:**  
`Join([proxyAddresses],",")`  
Kan returnera ”:SMTP:john.doe@contoso.com,smtp:jd@contoso.com”

- - -
### <a name="lcase"></a>LCase
**Beskrivning:**  
Funktionen LCase konverterar alla tecken i en textsträng till gemener.

**Syntax:**  
`str LCase(str value)`

**Exempel:**  
`LCase("TeSt")`  
Returnerar ”test”.

- - -
### <a name="left"></a>Vänster
**Beskrivning:**  
Funktionen vänster returnerar ett angivet antal tecken från vänster i en sträng.

**Syntax:**  
`str Left(str string, num NumChars)`

* sträng: strängen som ska returnera tecken från
* NumChars: ett nummer som identifierar antalet tecken att returnera från början (vänster) av sträng

**Anmärkning:**  
En sträng som innehåller de första numChars tecken i strängen:

* Om numChars = 0, returneras en tom sträng.
* Om numChars < 0, returnerar Indatasträngen.
* Om strängen är null returnera tom sträng.

Om strängen innehåller färre tecken än nummer anges i numChars, returneras en sträng som är identisk med sträng (som är, som innehåller alla tecken i parameter 1).

**Exempel:**  
`Left("John Doe", 3)`  
Returnerar ”Joh”.

- - -
### <a name="len"></a>Len
**Beskrivning:**  
Funktionen Len returnerar antalet tecken i en sträng.

**Syntax:**  
`num Len(str value)`

**Exempel:**  
`Len("John Doe")`  
Returnerar 8

- - -
### <a name="ltrim"></a>LTrim
**Beskrivning:**  
LTrim-funktionen tar bort inledande blanksteg från en sträng.

**Syntax:**  
`str LTrim(str value)`

**Exempel:**  
`LTrim(" Test ")`  
Returnerar ”Test”

- - -
### <a name="mid"></a>Mid
**Beskrivning:**  
Mid-funktionen returnerar ett angivet antal tecken från en angiven position i en sträng.

**Syntax:**  
`str Mid(str string, num start, num NumChars)`

* sträng: strängen som ska returnera tecken från
* Starta: ett nummer som identifierar Start position i strängen att returnera tecken från
* NumChars: ett nummer som identifierar antalet tecken att returnera från positionen i strängen

**Anmärkning:**  
Returnera numChars tecken med början från positionen start i strängen.  
En sträng som innehåller numChars tecken från position start i strängen:

* Om numChars = 0, returneras en tom sträng.
* Om numChars < 0, returnerar Indatasträngen.
* Om starta > hur lång sträng, returnerar Indatasträngen.
* Om starta < = 0, returnerar Indatasträngen.
* Om strängen är null returnera tom sträng.

Om det inte numChar tecken returneras kvar i strängen position, så många tecken som möjligt.

**Exempel:**  
`Mid("John Doe", 3, 5)`  
Returnerar ”hn gör”.

`Mid("John Doe", 6, 999)`  
Returnerar ”Berg”

- - -
### <a name="now"></a>Nu
**Beskrivning:**  
Funktionen nu returnerar ett datetime-värde som anger aktuellt datum och tid, beroende på datorns systemdatum och tid.

**Syntax:**  
`dt Now()`

- - -
### <a name="numfromdate"></a>NumFromDate
**Beskrivning:**  
NumFromDate-funktionen returnerar ett datum i Active Directorys datumformat.

**Syntax:**  
`num NumFromDate(dt value)`

**Exempel:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Returns 129699324000000000

- - -
### <a name="padleft"></a>padLeft
**Beskrivning:**  
De PadLeft funktionen vänster-Pad en sträng till en angiven längd med en angiven utfyllnadstecknet.

**Syntax:**  
`str PadLeft(str string, num length, str padCharacter)`

* sträng: strängen utfyllnad.
* längd: Ett heltal som representerar den önskade längden på strängen.
* padCharacter: En sträng som består av ett enskilt tecken som används som pad tecken

**Anmärkning:**

* Om längden på strängen är mindre än längden, läggs upprepade gånger padCharacter i början (vänster) av sträng tills den har en längd lika med längden.
* padCharacter kan vara ett blanksteg, men den kan inte vara ett null-värde.
* Om längden på strängen är lika med eller större än längden, returneras sträng oförändrade.
* Om strängen har en längd som är större än eller lika med längden, returneras en sträng som är identisk med sträng.
* Om längden på strängen är mindre än längden, returneras en ny sträng med längden som innehåller strängen fylls ut med en padCharacter.
* Om strängen är null returnerar funktionen en tom sträng.

**Exempel:**  
`PadLeft("User", 10, "0")`  
Returnerar ”000000User”.

- - -
### <a name="padright"></a>PadRight
**Beskrivning:**  
De PadRight funktionen höger-Pad en sträng till en angiven längd med en angiven utfyllnadstecknet.

**Syntax:**  
`str PadRight(str string, num length, str padCharacter)`

* sträng: strängen utfyllnad.
* längd: Ett heltal som representerar den önskade längden på strängen.
* padCharacter: En sträng som består av ett enskilt tecken som används som pad tecken

**Anmärkning:**

* Om längden på strängen är mindre än längden, sedan läggs padCharacter upprepade gånger till (höger) slutet av strängen förrän den har en längd som är lika med längden.
* PadCharacter kan vara ett blanksteg, men den kan inte vara ett null-värde.
* Om längden på strängen är lika med eller större än längden, returneras sträng oförändrade.
* Om strängen har en längd som är större än eller lika med längden, returneras en sträng som är identisk med sträng.
* Om längden på strängen är mindre än längden, returneras en ny sträng med längden som innehåller strängen fylls ut med en padCharacter.
* Om strängen är null returnerar funktionen en tom sträng.

**Exempel:**  
`PadRight("User", 10, "0")`  
Returnerar ”User000000”.

- - -
### <a name="pcase"></a>PCase
**Beskrivning:**  
Funktionen PCase konverterar det första tecknet i varje blankstegsavgränsad ord i en sträng till versal och alla andra tecken omvandlas till gemener.

**Syntax:**  
`String PCase(string)`

**Anmärkning:**

* Den här funktionen ger inte rätt skiftläge för att konvertera ett ord som är helt och hållet versaler, till exempel en förkortning för närvarande.

**Exempel:**  
`PCase("TEsT")`  
Returnerar ”Test”.

`PCase(LCase("TEST"))`  
Returnerar ”Test”

- - -
### <a name="randomnum"></a>RandomNum
**Beskrivning:**  
RandomNum-funktionen returnerar ett slumptal mellan ett visst intervall.

**Syntax:**  
`num RandomNum(num start, num end)`

* Starta: ett nummer som identifierar den undre gränsen för slumpmässigt värde att generera
* End: ett nummer som identifierar den övre gränsen för slumpmässigt värde att generera

**Exempel:**  
`Random(100,999)`  
Returnera 734.

- - -
### <a name="removeduplicates"></a>RemoveDuplicates
**Beskrivning:**  
Funktionen RemoveDuplicates tar en sträng med flera värden och kontrollera att alla värden är unikt.

**Syntax:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exempel:**  
`RemoveDuplicates([proxyAddresses])`  
Returnerar ett språkoberoende proxyAddress-attribut där alla dubblettvärden har tagits bort.

- - -
### <a name="replace"></a>Ersätt
**Beskrivning:**  
Ersätt funktionen ersätter alla förekomster av en textsträng till en annan sträng.

**Syntax:**  
`str Replace(str string, str OldValue, str NewValue)`

* sträng: En sträng att ersätta värdena i.
* OldValue: Sträng att söka efter och ersätta.
* NewValue: Strängen som ska ersätta till.

**Anmärkning:**  
Funktionen identifierar följande särskilda monikers:

* \n – New Line
* \r – Carriage Return
* \t – fliken

**Exempel:**  
`Replace([address],"\r\n",", ")`  
Ersätter CRLF med ett kommatecken och ett blanksteg och kan leda till ”en Microsoft sätt, Redmond, WA, USA”

- - -
### <a name="replacechars"></a>ReplaceChars
**Beskrivning:**  
Funktionen ReplaceChars ersätter alla förekomster av tecken i strängen ReplacePattern.

**Syntax:**  
`str ReplaceChars(str string, str ReplacePattern)`

* sträng: En sträng som ska ersätta tecken i.
* ReplacePattern: en sträng som innehåller en ordlista med tecken som ska ersättas.

Formatet är {källa1}: {target1}, {källa2}: {target2}, {källan}, {targetN} där källan är tecknet för att hitta och rikta in strängen som ska ersättas med.

**Anmärkning:**

* Funktionen tar varje förekomst av definierade källor och ersätter dem med mål.
* Måste vara exakt 1 (unicode) tecken.
* Källan kan inte vara tomt eller längre än ett tecken (parsningsfel).
* Målet kan ha flera tecken, till exempel ö:oe, β:ss.
* Målet kan anges som anger att tecknet ska tas bort.
* Källan är skiftlägeskänsligt och måste vara en exakt matchning.
* (Kommatecken) och: (kolon) är reserverade tecken som inte kan ersättas med hjälp av den här funktionen.
* Blanksteg och andra vit tecken i strängen ReplacePattern ignoreras.

**Exempel:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Returnerar Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Returnerar ”ONeil”, enskild skalstreck definieras till att tas bort.

- - -
### <a name="right"></a>Höger
**Beskrivning:**  
Funktionen höger returnerar ett angivet antal tecken från höger (end) i en sträng.

**Syntax:**  
`str Right(str string, num NumChars)`

* sträng: strängen som ska returnera tecken från
* NumChars: ett nummer som identifierar antalet tecken att returnera från (höger) slutet av strängen

**Anmärkning:**  
NumChars tecken som ska returneras från den sista positionen för strängen.

En sträng som innehåller de senaste numChars tecken i strängen:

* Om numChars = 0, returneras en tom sträng.
* Om numChars < 0, returnerar Indatasträngen.
* Om strängen är null returnera tom sträng.

Om strängen innehåller färre tecken än nummer anges i NumChars, returneras en sträng som är identisk med sträng.

**Exempel:**  
`Right("John Doe", 3)`  
Returnerar ”Berg”.

- - -
### <a name="rtrim"></a>RTrim
**Beskrivning:**  
RTrim-funktionen tar bort avslutande blanksteg från en sträng.

**Syntax:**  
`str RTrim(str value)`

**Exempel:**  
`RTrim(" Test ")`  
Returnerar ”Test”.

- - -
### <a name="select"></a>Välj
**Beskrivning:**  
Processen för alla värden i en med flera värden attribut (eller resultatet av ett uttryck) baserat på funktionen som anges.

**Syntax:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item: Representerar ett element i attributet med flera värden
* attributet: attributet med flera värden
* uttryck: ett uttryck som returnerar en uppsättning värden
* villkor: alla funktioner som kan bearbeta ett objekt i attributet

**Exempel:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Returnera alla värden i flervärdesattribut Annantelefon när bindestreck (-) har tagits bort.

- - -
### <a name="split"></a>Delat
**Beskrivning:**  
Funktionen Split tar en sträng som avgränsas med en avgränsare och gör den till en sträng med flera värden.

**Syntax:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* värde: sträng med ett avgränsningstecken för att avgränsa.
* avgränsare: tecken som ska användas som avgränsare.
* gränsen: maximalt antal värden som kan returnera.

**Exempel:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Returnerar en sträng som har flera värden med 2 element användbart för proxyAddress-attribut.

- - -
### <a name="stringfromguid"></a>StringFromGuid
**Beskrivning:**  
Funktionen GUIDFromString tar en binär GUID och konverterar den till en sträng

**Syntax:**  
`str StringFromGuid(bin GUID)`

- - -
### <a name="stringfromsid"></a>StringFromSid
**Beskrivning:**  
Funktionen StringFromSid konverterar en bytematris som innehåller en säkerhetsidentifierare till en sträng.

**Syntax:**  
`str StringFromSid(bin ObjectSID)`  

- - -
### <a name="switch"></a>Växel
**Beskrivning:**  
Funktionen växeln används för att returnera ett enstaka värde baserat på utvärderade villkoren.

**Syntax:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* uttryck: Varianten uttryck som du vill utvärdera.
* Värde: Värde som ska returneras om motsvarande uttrycket är sant.

**Anmärkning:**  
Argumentlistan växel funktionen består av par med uttryck och värden. Uttryck som utvärderas från vänster till höger och returneras värdet som är associerade med det första uttrycket ska utvärderas till SANT. Om delarna inte korrekt tillsammans ger inträffar ett fel.

Om Uttr1 är SANT returnerar växel value1. Om uttryck-1 är False, men uttryck-2 är sant, returnerar växel värdet 2 och så vidare.

Växeln returnerar en ingenting om:

* Ingen av uttryck som är sant.
* Det första SANT uttrycket har ett motsvarande värde som är Null.

Växeln utvärderar alla uttryck, trots att den returnerar endast en av dem. Därför bör du se för oönskade sidoeffekter. Utvärderingen av ett uttryck som resulterar i en division med noll-fel, inträffar ett fel.

Värdet kan också vara funktionen fel som returneras en anpassad sträng.

**Exempel:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Returnerar det språk som talas i vissa större städer, annars returneras ett fel.

- - -
### <a name="trim"></a>Trimma
**Beskrivning:**  
Rensa-funktionen tar bort inledande och avslutande blanksteg från en sträng.

**Syntax:**  
`str Trim(str value)`  

**Exempel:**  
`Trim(" Test ")`  
Returnerar ”Test”.

`Trim([proxyAddresses])`  
Tar bort inledande och avslutande blanksteg för varje värde i proxyAddress-attribut.

- - -
### <a name="ucase"></a>UCase
**Beskrivning:**  
Funktionen UCase konverterar alla tecken i en sträng till versaler.

**Syntax:**  
`str UCase(str string)`

**Exempel:**  
`UCase("TeSt")`  
Returnerar ”TEST”.

- - -
### <a name="where"></a>Var

**Beskrivning:**  
Returnerar en delmängd av värden från ett med flera värden attribut (eller resultatet av ett uttryck) som baserat på specifika villkor.

**Syntax:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item: Representerar ett element i attributet med flera värden
* attributet: attributet med flera värden
* villkor: ett uttryck som kan utvärderas till true eller false
* uttryck: ett uttryck som returnerar en uppsättning värden

**Exempel:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Returnera certifikatvärden i den flervärdesattribut userCertificate som inte har upphört att gälla.

- - -
### <a name="with"></a>Med
**Beskrivning:**  
Med funktionen är ett sätt att förenkla ett komplext uttryck med hjälp av en variabel som representerar en underuttryck som visas en eller flera gånger i ett komplext uttryck.

**Syntax:**
`With(var variable, exp subExpression, exp complexExpression)`  
* variabel: Representerar underuttryck.
* underuttryck: underuttryck som representeras av variabeln.
* complexExpression: Ett komplext uttryck.

**Exempel:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Har samma funktioner:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Som returnerar endast läggs certifikatvärden i userCertificate-attributet.


- - -
### <a name="word"></a>Word
**Beskrivning:**  
Word-funktionen returnerar ett ord som ingår i en sträng som baseras på parametrar som beskriver avgränsarna som ska användas och word-nummer för att returnera.

**Syntax:**  
`str Word(str string, num WordNumber, str delimiters)`

* sträng: strängen som ska returnera ett ord från.
* WordNumber: ett nummer som identifierar vilka word tal ska returnera.
* avgränsare: en sträng som representerar delimiter(s) som ska användas för att identifiera ord

**Anmärkning:**  
Varje sträng med tecken i strängen avgränsade med något av tecknen i avgränsare identifieras som ord:

* Om nummer < 1, returnerar tom sträng.
* Om strängen är null returnerar tom sträng.

Om strängen innehåller mindre än antalet ord, eller strängen innehåller inte några ord som identifieras av avgränsare, returneras en tom sträng.

**Exempel:**  
`Word("The quick brown fox",3," ")`  
Returnerar ”brown”

`Word("This,string!has&many separators",3,",!&#")`  
Returnerar ”har”

## <a name="additional-resources"></a>Ytterligare resurser
* [Förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect Sync: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
