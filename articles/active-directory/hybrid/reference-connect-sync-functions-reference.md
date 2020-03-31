---
title: 'Synkronisering av Azure AD Connect: Funktionsreferens | Microsoft-dokument'
description: Referens för deklarativa etableringsuttryck i Azure AD Connect-synkronisering.
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
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c3102480e316c634930c356ae02f769767b7d08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900048"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Synkronisering av Azure AD Connect: Funktionsreferens
I Azure AD Connect används funktioner för att ändra ett attributvärde under synkroniseringen.  
Funktionernas syntax uttrycks med följande format:  
`<output type> FunctionName(<input type> <position name>, ..)`

Om en funktion är överbelastad och accepterar flera syntaxer visas alla giltiga syntaxer.  
Funktionerna skrivs starkt och kontrollerar att typen som skickas in matchar den dokumenterade typen.  
Om typen inte matchar genereras ett fel.

Typerna uttrycks med följande syntax:

* **bin** – Binär
* **bool** – Boolesk
* **dt** – UTC Datum/tid
* **uppräkning** – Uppräkning av kända konstanter
* **exp** – Uttryck, som förväntas utvärderas till en boolesk
* **mvbin** – Flervärdesbinär
* **mvstr** – Sträng med flera värden
* **mvref** – referens med flera värden
* **num** – Numeriskt
* **ref** – Referens
* **str** – Sträng
* **var** – En variant av (nästan) någon annan typ
* **ogiltigt** – returnerar inte ett värde

Funktionerna med typerna **mvbin**, **mvstr**och **mvref** kan bara fungera på attribut med flera värden. Funktioner med **lagerplats,** **str**och **ref** fungerar på både attribut med ett värde och flera värden.

## <a name="functions-reference"></a>Referens för funktioner

| Lista över funktioner |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Certifikat** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat (CertFormat)](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString (CertHashString)](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersion (CertVersion)](#certversion) |[IsCert (IsCert)](#iscert) | | | |
| **Konvertering** | | | | |
| [CBool (en)](#cbool) |[CDate (olikartade)](#cdate) |[CGuid (på ett sätt)](#cguid) |[Konvertera frånBase64](#convertfrombase64) | |
| [Konvertera tillBase64](#converttobase64) |[Konvertera frånUTF8Hex](#convertfromutf8hex) |[KonverteratoUTF8Hex](#converttoutf8hex) |[CNum (på andra)](#cnum) | |
| [Cref](#cref) |[CStr (CStr)](#cstr) |[SträngFrånGuid](#stringfromguid) |[StringFromSid](#stringfromsid) | |
| **Datum/tid** | | | | |
| [DatumLägg till](#dateadd) |[DatumFrånNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[Nwo](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Katalog** | | | | |
| [DN-ersättning](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Utvärdering** | | | | |
| [IsBitset (IsBitSet)](#isbitset) |[IsDate (olika)](#isdate) |[Isempty (mer från Isempty)](#isempty) |[IsGuid (av " IsGuid )](#isguid) | |
| [Ärnull](#isnull) |[IsNullOrEmpty (300000)](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent (Representerar)](#ispresent) | |
| [IsString (IsString)](#isstring) | | | | |
| **Matematik** | | | | |
| [BitAnd ()](#bitand) |[BitOr (bitor)](#bitor) |[Slumpmässigt antal](#randomnum) | | |
| **Flera värden** | | | | |
| [Innehåller](#contains) |[Räkna](#count) |[Objekt](#item) |[ItemOrNull (ArtikelOrNull)](#itemornull) | |
| [Anslut](#join) |[Ta bortDuplicates](#removeduplicates) |[Split](#split) | | |
| **Programflöde** | | | | |
| [Fel](#error) |[Iif](#iif) |[Välj](#select) |[Växel](#switch) | |
| [Där](#where) |[With](#with) | | | |
| **Text** | | | | |
| [Guid](#guid) |[Instr](#instr) |[InStrRev (instrrev)](#instrrev) |[LCase (LCase)](#lcase) | |
| [Vänster](#left) |[Len](#len) |[Ltrim](#ltrim) |[Mitten](#mid) | |
| [PadVändning](#padleft) |[PadRätt](#padright) |[PCase (på andra sätt)](#pcase) |[Ersätt](#replace) | |
| [ErsättDiagram](#replacechars) |[Rätt](#right) |[RTrim (RTrim)](#rtrim) |[Trimma](#trim) | |
| [UCase (UCase)](#ucase) |[Word](#word) | | | |

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
`BitAnd(&HF, &HF7)`  
Returnerar 7 eftersom hexadecimalt "F" OCH "F7" utvärderar till det här värdet.

---
### <a name="bitor"></a>BitOr (bitor)
**Beskrivning:**  
Funktionen BitOr anger angivna bitar på ett värde.

**Syntax:**  
`num BitOr(num value1, num value2)`

* värde1, värde2: numeriska värden som ska OR'ed tillsammans

**Anmärkningar:**  
Den här funktionen konverterar båda parametrarna till den binära representationen och ställer in lite till 1 om en eller båda av motsvarande bitar i mask och flagga är 1 och till 0 om båda motsvarande bitar är 0. Med andra ord returneras 1 i alla fall utom där motsvarande bitar av båda parametrarna är 0.

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
### <a name="cdate"></a>CDate (olikartade)
**Beskrivning:**  
Funktionen CDate returnerar en UTC DateTime från en sträng. DateTime är inte en inbyggd attributtyp i Sync men används av vissa funktioner.

**Syntax:**  
`dt CDate(str value)`

* Värde: En sträng med datum, tid och valfri tidszon

**Anmärkningar:**  
Den returnerade strängen finns alltid i UTC.

**Exempel:**  
`CDate([employeeStartTime])`  
Returnerar en DateTime baserat på medarbetarens starttid

`CDate("2013-01-10 4:00 PM -8")`  
Returnerar en DateTime som representerar "2013-01-11 12:00 AM"


---
### <a name="certextensionoids"></a>CertExtensionOids
**Beskrivning:**  
Returnerar Oid-värdena för alla kritiska tillägg för ett certifikatobjekt.

**Syntax:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certformat"></a>CertFormat (CertFormat)
**Beskrivning:**  
Returnerar namnet på formatet för det här X.509v3-certifikatet.

**Syntax:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Beskrivning:**  
Returnerar det associerade aliaset för ett certifikat.

**Syntax:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certhashstring"></a>CertHashString (CertHashString)
**Beskrivning:**  
Returnerar SHA1-hash-värdet för X.509v3-certifikatet som en hexadecimal sträng.

**Syntax:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certissuer"></a>CertIssuer
**Beskrivning:**  
Returnerar namnet på certifikatutfärdaren som utfärdade X.509v3-certifikatet.

**Syntax:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Beskrivning:**  
Returnerar certifikatutfärdarens unika namn.

**Syntax:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certissueroid"></a>CertIssuerOid
**Beskrivning:**  
Returnerar certifikatutfärdarens Oid.

**Syntax:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Beskrivning:**  
Returnerar nyckelalgoritminformationen för det här X.509v3-certifikatet som en sträng.

**Syntax:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Beskrivning:**  
Returnerar nyckelalgoritmparametrarna för X.509v3-certifikatet som en hexadecimal sträng.

**Syntax:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certnameinfo"></a>CertNameInfo
**Beskrivning:**  
Returnerar ämnes- och utfärdarnamnen från ett certifikat.

**Syntax:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.
*   X509NameType: X509NameType-värdet för ämnet.
*   includesIssuerName: sant att inkludera emittentens namn; annars falskt.

---
### <a name="certnotafter"></a>CertNotAfter
**Beskrivning:**  
Returnerar datumet i lokal tid efter vilket ett certifikat inte längre är giltigt.

**Syntax:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certnotbefore"></a>CertNotBefore
**Beskrivning:**  
Returnerar datumet i lokal tid då ett certifikat blir giltigt.

**Syntax:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Beskrivning:**  
Returnerar Oid för den offentliga nyckeln för X.509v3-certifikatet.

**Syntax:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Beskrivning:**  
Returnerar Oid för de offentliga nyckelparametrarna för X.509v3-certifikatet.

**Syntax:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Beskrivning:**  
Returnerar serienumret för X.509v3-certifikatet.

**Syntax:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Beskrivning:**  
Returnerar Oid för den algoritm som används för att skapa signaturen för ett certifikat.

**Syntax:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certsubject"></a>CertSubject
**Beskrivning:**  
Hämtar ämnet som skiljer namn från ett certifikat.

**Syntax:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Beskrivning:**  
Returnerar det ämne som skiljer namnet från ett certifikat.

**Syntax:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Beskrivning:**  
Returnerar Oid för ämnesnamnet från ett certifikat.

**Syntax:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certthumbprint"></a>CertThumbprint
**Beskrivning:**  
Returnerar tumavtrycket för ett certifikat.

**Syntax:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="certversion"></a>CertVersion (CertVersion)
**Beskrivning:**  
Returnerar X.509-formatversionen av ett certifikat.

**Syntax:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.

---
### <a name="cguid"></a>CGuid (på ett sätt)
**Beskrivning:**  
Funktionen CGuid konverterar strängrepresentationen av ett GUID till dess binära representation.

**Syntax:**  
`bin CGuid(str GUID)`

* En sträng formaterad i detta mönster: xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx eller {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>Innehåller
**Beskrivning:**  
Funktionen Innehåller hittar en sträng inuti ett attribut med flera värden

**Syntax:**  
`num Contains (mvstring attribute, str search)`- skiftlägeskänslig  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`- skiftlägeskänslig

* attribut: attributet med flera värden för sökning.
* sökning: sträng att hitta i attributet.
* Casetype: CaseInsensitive eller CaseSensitive.

Returnerar index i attributet med flera värden där strängen hittades. 0 returneras om strängen inte hittas.

**Anmärkningar:**  
För strängattribut med flera värden hittar sökningen delsträngar i värdena.  
För referensattribut måste den sökta strängen exakt matcha det värde som ska betraktas som en matchning.

**Exempel:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Om attributet proxyAddresses har en primär e-postadress (indikerad av versaler "SMTP:"), returnerar du attributet proxyAddress, annars returnerar du ett fel.

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
### <a name="convertfromutf8hex"></a>Konvertera frånUTF8Hex
**Beskrivning:**  
Funktionen ConvertFromUTF8Hex konverterar det angivna UTF8 Hex-kodade värdet till en sträng.

**Syntax:**  
`str ConvertFromUTF8Hex(str source)`

* källa: UTF8 2-byte kodade sting

**Anmärkningar:**  
Skillnaden mellan den här funktionen och ConvertFromBase64([],UTF8) genom att resultatet är vänligt för DN-attributet.  
Det här formatet används av Azure Active Directory som DN.

**Exempel:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Returnerar "*Hello world!*"

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
### <a name="cnum"></a>CNum (på andra)
**Beskrivning:**  
Funktionen CNum tar en sträng och returnerar en numerisk datatyp.

**Syntax:**  
`num CNum(str value)`

---
### <a name="cref"></a>Cref
**Beskrivning:**  
Konverterar en sträng till ett referensattribut

**Syntax:**  
`ref CRef(str value)`

**Exempel:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

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
### <a name="dateadd"></a>DateAdd
**Beskrivning:**  
Returnerar ett datum som innehåller ett datum till vilket ett angivet tidsintervall har lagts till.

**Syntax:**  
`dt DateAdd(str interval, num value, dt date)`

* intervall: Stränguttryck som är det tidsintervall som du vill lägga till. Strängen måste ha något av följande värden:
  * yyyy år
  * q Kvartal
  * m Månad
  * y Årets dag
  * d Dag
  * w Veckodag
  * ww vecka
  * h Timme
  * n Minut
  * s Andra
* värde: Antalet enheter som du vill lägga till. Det kan vara positivt (för att få datum i framtiden) eller negativ (för att få datum i det förflutna).
* datum: DateTime som representerar datum till vilket intervallet läggs till.

**Exempel:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Lägger till 3 månader och returnerar en DateTime som representerar "2001-04-01".

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
### <a name="dncomponentrev"></a>DNComponentRev
**Beskrivning:**  
Funktionen DNComponentRev returnerar värdet för en angiven DN-komponent som går från höger (slutet).

**Syntax:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn: referensattributet för att tolka
* ComponentNumber - Komponenten i DN för att returnera
* Alternativ: DC – Ignorera alla komponenter med "dc="

**Exempel:**  
Om dn är "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com"  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Båda återvänder USA.

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
### <a name="escapedncomponent"></a>EscapeDNComponent
**Beskrivning:**  
Funktionen EscapeDNComponent tar en komponent i en DN och undgår den så att den kan representeras i LDAP.

**Syntax:**  
`str EscapeDNComponent(str value)`

**Exempel:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Ser till att objektet kan skapas i en LDAP-katalog även om attributet displayName har tecken som måste hämtas i LDAP.

---
### <a name="formatdatetime"></a>FormatDateTime
**Beskrivning:**  
Funktionen FormatDateTime används för att formatera en DateTime till en sträng med ett angivet format

**Syntax:**  
`str FormatDateTime(dt value, str format)`

* värde: ett värde i DateTime-formatet
* format: en sträng som representerar det format som ska konverteras till.

**Anmärkningar:**  
Möjliga värden för formatet finns här: [Anpassade datum- och tidsformat för funktionen FORMAT](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Exempel:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resultat i "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Kan resultera i "20140905081453.0Z"

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
### <a name="instrrev"></a>InStrRev (instrrev)
**Beskrivning:**  
Funktionen InStrRev hittar den senaste förekomsten av en delsträng i en sträng

**Syntax:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: sträng som ska sökas
* stringmatch: sträng som finns
* start: startposition för att hitta delsträngen
* jämför: vbTextCompare eller vbBinaryCompare

**Anmärkningar:**  
Returnerar den position där delsträngen hittades eller 0 om den inte hittas.

**Exempel:**  
`InStrRev("abbcdbbbef","bb")`  
Returnerar 7

---
### <a name="isbitset"></a>IsBitset (IsBitSet)
**Beskrivning:**  
Funktionen IsBitSet testar om en bit är inställd eller inte

**Syntax:**  
`bool IsBitSet(num value, num flag)`

* värde: ett numeriskt värde som utvärderas.flagga: ett numeriskt värde som har det bit som ska utvärderas

**Exempel:**  
`IsBitSet(&HF,4)`  
Returnerar Sant eftersom biten "4" anges i det hexadecimala värdet "F"

---
### <a name="isdate"></a>IsDate (olika)
**Beskrivning:**  
Om uttrycket kan utvärderas som en DateTime-typ utvärderas funktionen IsDate till True.

**Syntax:**  
`bool IsDate(var Expression)`

**Anmärkningar:**  
Används för att avgöra om CDate() kan lyckas.

---
### <a name="iscert"></a>IsCert (IsCert)
**Beskrivning:**  
Returnerar sant om rådata kan serialiseras till .NET X509Certificate2-certifikatobjektet.

**Syntax:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Återgivning av bytematris för ett X.509-certifikat. Byte-matrisen kan vara binär (DER) kodad eller Base64-kodade X.509-data.
---
### <a name="isempty"></a>IsEmpty
**Beskrivning:**  
Om attributet finns i CS eller MV men utvärderas till en tom sträng utvärderas funktionen IsEmpty till True.

**Syntax:**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid (av " IsGuid )
**Beskrivning:**  
Om strängen kunde konverteras till ett GUID utvärderas funktionen IsGuid till true.

**Syntax:**  
`bool IsGuid(str GUID)`

**Anmärkningar:**  
En GUID definieras som en sträng efter ett av dessa mönster: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx eller {xxxxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Används för att avgöra om CGuid() kan lyckas.

**Exempel:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Om StrAttribute har ett GUID-format returnerar du en binär representation och returnerar annars en Null.

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
### <a name="isnumeric"></a>IsNumeric
**Beskrivning:**  
Funktionen Isnumeric returnerar ett booleskt värde som anger om ett uttryck kan utvärderas som en taltyp.

**Syntax:**  
`bool IsNumeric(var Expression)`

**Anmärkningar:**  
Används för att avgöra om CNum() kan lyckas tolka uttrycket.

---
### <a name="isstring"></a>IsString (IsString)
**Beskrivning:**  
Om uttrycket kan utvärderas till en strängtyp utvärderas funktionen IsString till True.

**Syntax:**  
`bool IsString(var expression)`

**Anmärkningar:**  
Används för att avgöra om CStr() kan lyckas tolka uttrycket.

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
### <a name="itemornull"></a>ItemOrNull (ArtikelOrNull)
**Beskrivning:**  
Funktionen ItemOrNull returnerar ett objekt från en sträng/attribut med flera värden.

**Syntax:**  
`var ItemOrNull(mvstr attribute, num index)`

* attribut: attribut med flera värden
* index: index till ett objekt i strängen med flera värden.

**Anmärkningar:**  
Funktionen ItemOrNull är användbar tillsammans med funktionen Innehåller eftersom den senare funktionen returnerar indexet till ett objekt i attributet med flera värden.

Om indexet är utanför gränserna returnerar du ett Null-värde.

---
### <a name="join"></a>Slå ihop
**Beskrivning:**  
Funktionen Anslut tar en sträng med flera värden och returnerar en sträng med ett värde med angiven avgränsare infogad mellan varje objekt.

**Syntax:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* attribut: Attribut med flera värden som innehåller strängar som ska sammanfogas.
* avgränsare: Valfri sträng som används för att separera delsträngarna i den returnerade strängen. Om det utelämnas används blankstegstecknet (" "). Om Avgränsare är en sträng med nolllängd ("") eller Ingenting sammanfogas alla objekt i listan utan avgränsare.

**Anmärkningar**  
Det finns paritet mellan funktionerna Gå med och Dela. Funktionen Anslut tar en matris med strängar och sammanfogar dem med hjälp av en avgränsare sträng för att returnera en enda sträng. Funktionen Dela tar en sträng och separerar den vid avgränsaren för att returnera en matris med strängar. En viktig skillnad är dock att Join kan sammanfoga strängar med valfri avgränsare sträng, Split kan bara separera strängar med ett enda tecken avgränsare.

**Exempel:**  
`Join([proxyAddresses],",")`  
Kan återvända:SMTP:john.doe@contoso.comsmtp:jd@contoso.com" , "

---
### <a name="lcase"></a>LCase (LCase)
**Beskrivning:**  
Funktionen LCase konverterar alla tecken i en sträng till gemener.

**Syntax:**  
`str LCase(str value)`

**Exempel:**  
`LCase("TeSt")`  
Returnerar "test".

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
Returnerar "Joh".

---
### <a name="len"></a>Len
**Beskrivning:**  
Funktionen Len returnerar antalet tecken i en sträng.

**Syntax:**  
`num Len(str value)`

**Exempel:**  
`Len("John Doe")`  
Returnerar 8

---
### <a name="ltrim"></a>Ltrim
**Beskrivning:**  
Funktionen LTrim tar bort inledande blanksteg från en sträng.

**Syntax:**  
`str LTrim(str value)`

**Exempel:**  
`LTrim(" Test ")`  
Returnerar "Test"

---
### <a name="mid"></a>Mellan
**Beskrivning:**  
Funktionen Mitt returnerar ett angivet antal tecken från en angiven position i en sträng.

**Syntax:**  
`str Mid(str string, num start, num NumChars)`

* sträng: strängen för att returnera tecken från
* start: ett nummer som identifierar startpositionen i sträng för att returnera tecken från
* NumChars: ett nummer som identifierar antalet tecken som ska returneras från position i sträng

**Anmärkningar:**  
Returnera numChars-tecken från position börjar i sträng.  
En sträng som innehåller numChars-tecken från position börjar i sträng:

* Om numChars = 0 returnerar du tom sträng.
* Om numChars < 0 returnerar du indatasträngen.
* Om du börjar > längden på strängen returnerar du indatasträngen.
* Om start <= 0 returnerar du indatasträngen.
* Om strängen är null returnerar du tom sträng.

Om det inte finns numChar-tecken kvar i sträng från positionsstart returneras så många tecken som möjligt.

**Exempel:**  
`Mid("John Doe", 3, 5)`  
Returnerar "hn Do".

`Mid("John Doe", 6, 999)`  
Returnerar "Doe"

---
### <a name="now"></a>Now
**Beskrivning:**  
Funktionen Nu returnerar en DateTime som anger aktuellt datum och aktuell tid, beroende på datorns systemdatum och -tid.

**Syntax:**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Beskrivning:**  
Funktionen NumFromDate returnerar ett datum i AD:s datumformat.

**Syntax:**  
`num NumFromDate(dt value)`

**Exempel:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Returer 12969932400000000000

---
### <a name="padleft"></a>PadVändning
**Beskrivning:**  
PadLeft-funktionen till vänster-pads en sträng till en angiven längd med hjälp av ett medföljande utfyllnad tecken.

**Syntax:**  
`str PadLeft(str string, num length, str padCharacter)`

* sträng: strängen till pad.
* längd: Ett heltal som representerar önskad stränglängd.
* padCharacter: En sträng som består av ett enda tecken att använda som pad-tecken

**Anmärkningar:**

* Om strängens längd är kortare läggs padCharacter upprepade gånger till i början (vänster) av strängen tills den har en längd som är lika med längden.
* PadCharacter kan vara ett blanksteg, men det kan inte vara ett null-värde.
* Om strängens längd är lika med eller större än längden returneras strängen oförändrad.
* Om strängen har en längd som är större än eller lika med längden returneras en sträng som är identisk med strängen.
* Om strängens längd är mindre än längden returneras en ny sträng med önskad längd som innehåller strängvadd med en padCharacter.
* Om strängen är null returnerar funktionen en tom sträng.

**Exempel:**  
`PadLeft("User", 10, "0")`  
Returnerar "000000Användare".

---
### <a name="padright"></a>PadRätt
**Beskrivning:**  
PadRight-funktionen till höger-pads en sträng till en angiven längd med hjälp av ett medföljande utfyllnad tecken.

**Syntax:**  
`str PadRight(str string, num length, str padCharacter)`

* sträng: strängen till pad.
* längd: Ett heltal som representerar önskad stränglängd.
* padCharacter: En sträng som består av ett enda tecken att använda som pad-tecken

**Anmärkningar:**

* Om strängens längd är kortare läggs padCharacter upprepade gånger till slutet (till höger) på strängen tills den har en längd som är lika med längden.
* padCharacter kan vara ett blanksteg, men det kan inte vara ett null-värde.
* Om strängens längd är lika med eller större än längden returneras strängen oförändrad.
* Om strängen har en längd som är större än eller lika med längden returneras en sträng som är identisk med strängen.
* Om strängens längd är mindre än längden returneras en ny sträng med önskad längd som innehåller strängvadd med en padCharacter.
* Om strängen är null returnerar funktionen en tom sträng.

**Exempel:**  
`PadRight("User", 10, "0")`  
Returnerar "User00000".

---
### <a name="pcase"></a>PCase (på andra sätt)
**Beskrivning:**  
Funktionen PCase konverterar det första tecknet i varje avgränsat blanksteg i en sträng till versaler och alla andra tecken konverteras till gemener.

**Syntax:**  
`String PCase(string)`

**Anmärkningar:**

* Den här funktionen ger för närvarande inte rätt hölje för att konvertera ett ord som är helt versaler, till exempel en akronym.

**Exempel:**  
`PCase("TEsT")`  
Returnerar "Test".

`PCase(LCase("TEST"))`  
Returnerar "Test"

---
### <a name="randomnum"></a>Slumpmässigt antal
**Beskrivning:**  
Funktionen RandomNum returnerar ett slumptal mellan ett angivet intervall.

**Syntax:**  
`num RandomNum(num start, num end)`

* start: ett nummer som identifierar den nedre gränsen för det slumpmässiga värdet för att generera
* slut: ett nummer som identifierar den övre gränsen för det slumpmässiga värdet för att generera

**Exempel:**  
`Random(100,999)`  
Kan återvända 734.

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
**Beskrivning:**  
Funktionen Ersätt ersätter alla förekomster av en sträng till en annan sträng.

**Syntax:**  
`str Replace(str string, str OldValue, str NewValue)`

* sträng: En sträng som värden ska ersättas i.
* OldValue: Strängen att söka efter och ersätta.
* NewValue: Strängen att ersätta till.

**Anmärkningar:**  
Funktionen känner igen följande speciella monikers:

* \n – Ny linje
* \r – Återresa för vagn
* \t – Fliken

**Exempel:**  
`Replace([address],"\r\n",", ")`  
Ersätter CRLF med ett kommatecken och utrymme, och kan leda till "One Microsoft Way, Redmond, WA, USA"

---
### <a name="replacechars"></a>ErsättDiagram
**Beskrivning:**  
Funktionen ReplaceChars ersätter alla förekomster av tecken som finns i strängen ReplacePattern.

**Syntax:**  
`str ReplaceChars(str string, str ReplacePattern)`

* sträng: En sträng som du vill ersätta tecken i.
* ReplacePattern: en sträng som innehåller en ordlista med tecken att ersätta.

Formatet är {source1}:{target1},{source2}:{target2},{sourceN},{targetN} där källan är det tecken att hitta och rikta strängen att ersätta med.

**Anmärkningar:**

* Funktionen tar varje förekomst av definierade källor och ersätter dem med målen.
* Källan måste vara exakt ett (unicode)-tecken.
* Källan får inte vara tom eller längre än ett tecken (tolkningsfel).
* Målet kan ha flera tecken, till exempel ö:oe, β:ss.
* Målet kan vara tomt och ange att tecknet ska tas bort.
* Källan är skiftlägeskänslig och måste vara en exakt matchning.
* , (komma) och : (kolon) är reserverade tecken och kan inte ersättas med den här funktionen.
* Blanksteg och andra vita tecken i strängen ReplacePattern ignoreras.

**Exempel:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Returnerar Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Returnerar "ONeil", den enda bocken definieras för att tas bort.

---
### <a name="right"></a>Höger
**Beskrivning:**  
Högerfunktionen returnerar ett angivet antal tecken från höger (slutet) i en sträng.

**Syntax:**  
`str Right(str string, num NumChars)`

* sträng: strängen för att returnera tecken från
* NumChars: ett nummer som identifierar antalet tecken som ska returneras från slutet (till höger) i strängen

**Anmärkningar:**  
NumChars-tecken returneras från den sista positionen för strängen.

En sträng som innehåller de senaste numChars-tecknen i sträng:

* Om numChars = 0 returnerar du tom sträng.
* Om numChars < 0 returnerar du indatasträngen.
* Om strängen är null returnerar du tom sträng.

Om strängen innehåller färre tecken än det tal som anges i NumChars returneras en sträng som är identisk med strängen.

**Exempel:**  
`Right("John Doe", 3)`  
Returnerar "Doe".

---
### <a name="rtrim"></a>RTrim (RTrim)
**Beskrivning:**  
Funktionen RTrim tar bort efterföljande blanksteg från en sträng.

**Syntax:**  
`str RTrim(str value)`

**Exempel:**  
`RTrim(" Test ")`  
Returnerar "Test".

---
### <a name="select"></a>Välj
**Beskrivning:**  
Bearbeta alla värden i ett attribut med flera värden (eller utdata från ett uttryck) baserat på den angivna funktionen.

**Syntax:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* objekt: Representerar ett element i attributet med flera värden
* attribut: attributet med flera värden
* uttryck: ett uttryck som returnerar en samling värden
* villkor: alla funktioner som kan bearbeta ett objekt i attributet

**Exempel:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Returnera alla värden i attributet otherPhone med flera värden (-) har tagits bort.

---
### <a name="split"></a>Dela
**Beskrivning:**  
Funktionen Dela tar en sträng separerad med en avgränsare och gör den till en sträng med flera värden.

**Syntax:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* värde: strängen med ett avgränsare tecken att separera.
* avgränsare: enstaka tecken som ska användas som avgränsare.
* gräns: maximalt antal värden som kan returneras.

**Exempel:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Returnerar en sträng med flera värden med två element som är användbara för attributet proxyAddress.

---
### <a name="stringfromguid"></a>SträngFrånGuid
**Beskrivning:**  
Funktionen StringFromGuid tar ett binärt GUID och konverterar det till en sträng

**Syntax:**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Beskrivning:**  
Funktionen StringFromSid konverterar en bytematris som innehåller en säkerhetsidentifierare till en sträng.

**Syntax:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Växel
**Beskrivning:**  
Funktionen Växla används för att returnera ett enda värde baserat på utvärderade villkor.

**Syntax:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* uttr: Variantuttryck som du vill utvärdera.
* värde: Värde som ska returneras om motsvarande uttryck är Sant.

**Anmärkningar:**  
Argumentlistan Växla funktion består av par av uttryck och värden. Uttrycken utvärderas från vänster till höger och värdet som associeras med det första uttrycket som ska utvärderas till Sant returneras. Om delarna inte är korrekt ihopkopplade uppstår ett körningsfel.

Om exempel expr1 till exempel är Sant returnerar Switch värde1. Om uttr-1 är Falskt, men expr-2 är Sant, returnerar Switch värde-2 och så vidare.

Switch returnerar ett ingenting om:

* Inget av uttrycken är sant.
* Det första True-uttrycket har ett motsvarande värde som är Null.

Växeln utvärderar alla uttryck, även om den bara returnerar ett av dem. Av denna anledning, Du bör titta efter oönskade biverkningar. Om utvärderingen av ett uttryck till exempel resulterar i en division med noll fel uppstår ett fel.

Värde kan också vara funktionen Fel, som skulle returnera en anpassad sträng.

**Exempel:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Returnerar det språk som talas i vissa större städer, annars returnerar ett fel.

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
### <a name="ucase"></a>UCase (UCase)
**Beskrivning:**  
UCase-funktionen konverterar alla tecken i en sträng till versaler.

**Syntax:**  
`str UCase(str string)`

**Exempel:**  
`UCase("TeSt")`  
Returnerar "TEST".

---
### <a name="where"></a>Var

**Beskrivning:**  
Returnerar en delmängd av värden från ett attribut med flera värden (eller utdata från ett uttryck) baserat på ett visst villkor.

**Syntax:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* objekt: Representerar ett element i attributet med flera värden
* attribut: attributet med flera värden
* villkor: alla uttryck som kan utvärderas till sant eller falskt
* uttryck: ett uttryck som returnerar en samling värden

**Exempel:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Returnera certifikatvärdena i attributet UserCertificate med flera värden som inte har upphört att gälla.

---
### <a name="with"></a>With
**Beskrivning:**  
Funktionen Med är ett sätt att förenkla ett komplext uttryck genom att använda en variabel för att representera en underuttryck som visas en eller flera gånger i det komplexa uttrycket.

**Syntax:**
`With(var variable, exp subExpression, exp complexExpression)`  
* variabel: Representerar subexpressionen.
* subExpression: subexpression representerad av variabel.
* complexExpression: Ett komplext uttryck.

**Exempel:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Är funktionellt likvärdig med:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Som returnerar endast ejexpirerade certifikatvärden i attributet userCertificate.


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

## <a name="additional-resources"></a>Ytterligare resurser
* [Förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Synkronisering av Azure AD Connect: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
