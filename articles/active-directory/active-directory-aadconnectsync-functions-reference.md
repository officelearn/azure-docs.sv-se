<properties
    pageTitle="Azure AD Connect-Synchronisierung: Funktionsreferenz | Microsoft Azure"
    description="Referenz der Ausdrücke für die deklarative Bereitstellung in der Azure AD Connect-Synchronisierung."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="StevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2015"
    ms.author="markusvi"/>


# Azure AD Connect-Synchronisierung: Funktionsreferenz


In der Azure Active Directory Connect-Synchronisierung werden Funktionen verwendet, um Attributwerte während der Synchronisierung zu ändern. <br>
Die Syntax der Funktionen wird im folgenden Format angegeben: <br>
`<output type> FunctionName(<input type> <position name>, ..)`

Wenn eine Funktion überladen ist und mehrere Syntaxen akzeptiert, werden alle gültigen Syntaxen aufgeführt.<br>
Die Funktionen sind stark typisiert und überprüfen, ob der übergebene Typ dem dokumentierten Typ entspricht.<br>
Wenn der Typ nicht übereinstimmt, wird ein Fehler ausgegeben.

Die Typen werden mit der folgenden Syntax ausgedrückt:

- **Bin** – Binärwert
- **Bool** – boolescher Wert
- **DT** – UTC-Datum/Uhrzeit
- **Enum** – Enumeration von bekannten Konstanten
- **exp** – Ausdruck, der einen booleschen Wert ausgewertet werden soll.
- **Mvbin** – mehrwertiger Binärwert
- **Mvstr** – Verweis mit mehreren Werten
- **NUM** – numerische
- **Ref** – Verweis mit einem Wert
- **Str** – einwertige Zeichenfolge
- **Var** – eine Variante (fast) jeden anderen Typs
- **void** – keinen Wert zurückgibt



## Funktionsreferenz

----------
**Konvertierung:**

[CBool](#cbool) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [CDate](#cdate) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [CGuid](#cguid) & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; [ConvertFromBase64](#convertfrombase64) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [ConvertToBase64](#converttobase64) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [ConvertFromUTF8Hex](#convertfromutf8hex) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [ConvertToUTF8Hex](#converttoutf8hex) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [CNum](#cnum)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [CRef](#cref) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [CStr](#cstr)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [StringFromGuid](#StringFromGuid) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [StringFromSid](#stringfromsid)

**Datum/Uhrzeit:**

[DateAdd](#dateadd) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [DateFromNum](#datefromnum)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [FormatDateTime](#formatdatetime)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Jetzt](#now)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [NumFromDate](#numfromdate)

**Verzeichnis**

[DNComponent](#dncomponent)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [DNComponentRev](#dncomponentrev) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [EscapeDNComponent](#escapedncomponent)

**Prüfung:**

[IsBitSet](#isbitset)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [IsDate](#isdate) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [IsEmpty](#isempty)
& Nbsp; & Nbsp; & Nbsp; & Nbsp; [IsGuid](#isguid) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [IsNull](#isnull) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [IsNullOrEmpty](#isnullorempty) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [IsNumeric](#isnumeric)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [IsPresent](#ispresent) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [IsString](#isstring)

**Mathematisch:**

[BitAnd](#bitand) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [BitOr](#bitor) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [RandomNum](#randomnum)

**Mehrwertig**

[Enthält](#contains) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Anzahl](#count) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Artikel](#item)   & Nbsp; & Nbsp; & Nbsp; & Nbsp; [ItemOrNull](#itemornull) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Join](#join) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [RemoveDuplicates](#removeduplicates) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Split](#split)

**Programmablauf:**

[Fehler](#error) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [IIF](#iif)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Switch](#switch)


**Text**

[GUID](#guid) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [InStr](#instr) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [InStrRev](#instrrev) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [LCase](#lcase) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Links](#left) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Len](#len) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [LTrim](#ltrim)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Mid](#mid)  & Nbsp; & Nbsp; & Nbsp; & Nbsp; [PadLeft](#padleft) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [PadRight](#padright) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [PCase](#pcase)   & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Ersetzen Sie](#replace) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [ReplaceChars](#replacechars) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Rechts](#right) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [RTrim](rtrim) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Trim](#trim) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [UCase](#ucase) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Word](#word)

----------
### BitAnd

**Beschreibung:**<br>
BitAnd-Funktion legt angegebene Bits auf einen Wert fest.

**Syntax:**<br>
`num BitAnd(num value1, num value2)`

- value1, value2: Numerische Werte, die mit AND verknüpft werden sollen.

**Hinweise:**<br>
Diese Funktion konvertiert beide Parameter in die binäre Darstellung und setzt ein bit auf:

- 0 – Wenn ein oder beide entsprechenden Bits in *Maske* und *Flag* 0
- 1 – wenn beide entsprechenden Bits 1 sind.

Anders gesagt: sie gibt in allen Fällen 0 zurück, außer wenn die entsprechenden Bits beider Parameter 1 sind.

**Beispiel:**<br>
`BitAnd(&HF, &HF7)`<br>
Gibt 7 zurück, da sich die hexadezimale "F" AND "F7" auf diesen Wert.

----------
### BitOr

**Beschreibung:** <br>
BitOr-Funktion legt angegebene Bits auf einen Wert fest.

**Syntax:** <br>
`num BitOr(num value1, num value2)`

- value1, value2: Numerische Werte, die mit OR verknüpft werden sollen.

**Hinweise:** <br>
Diese Funktion konvertiert beide Parameter in die binäre Darstellung und legt ein bit auf 1, wenn eine oder beide der entsprechenden Bits in Maske und Kennzeichen auf 1 sind, und 0, wenn beide entsprechenden Bits 0 sind. <br>
Anders gesagt gibt sie in allen Fällen 1 zurück, außer wenn die entsprechenden Bits der beiden Parameter 0 sind.

----------
### CBool

**Beschreibung:**<br>
CBool-Funktion gibt einen booleschen Wert ab, der basierend auf der ausgewertete Ausdruck

**Syntax:** <br>
`bool CBool(exp Expression)`

**Hinweise:**<br>
Wenn der Ausdruck einen Wert ungleich null ergibt dann CBool "true" zurückgibt andernfalls es gibt False zurück.


**Beispiel:**<br>
`CBool([attrib1] = [attrib2])` <br>

Gibt True zurück, wenn beide Attribute den gleichen Wert haben.




----------
### CDate

**Beschreibung:**<br>
Die CDate-Funktion gibt einen UTC-DateTime-Wert aus einer Zeichenfolge zurück. "DateTime" ist kein synchronisierungsspezifischer Attributtyp, wird jedoch von einigen Funktionen verwendet.

**Syntax:**<br>
`dt CDate(str value)`

- value: Eine Zeichenfolge mit Datum, Uhrzeit und optional einer Zeitzone.

**Hinweise:**<br>
Die zurückgegebene Zeichenfolge ist immer in UTC.

**Beispiel:**<br>
`CDate([employeeStartTime])` <br>
Gibt ein DateTime-Wert auf Grundlage des Mitarbeiters Startzeit

`CDate("2013-01-10 4:00 PM -8")` <br>
Gibt einen datetime-Wert zurück "2013-01-11 12:00 Uhr"




----------
### CGuid

**Beschreibung:**<br>
Die CGuid-Funktion konvertiert die Zeichenfolgendarstellung einer GUID in die binäre Darstellung.

**Syntax:**<br>
`bin CGuid(str GUID)GUID`

- Eine in diesem Muster formatierte Zeichenfolge: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx oder {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}




----------
### Contains

**Beschreibung:**<br>
Die Contains-Funktion sucht nach einer Zeichenfolge in ein mehrwertiges Attribut

**Syntax:**<br>
`num Contains (mvstring attribute, str search)` -Groß-/Kleinschreibung<br>
`num Contains (mvstring attribute, str search, enum Casetype)`<br>
`num Contains (mvref attribute, str search)` -Groß-/Kleinschreibung

- Attribut: das mehrwertige Attribut zu suchen.<br>
- Suche: Zeichenfolge, die im Attribut gesucht.<br>
- Casetype: CaseInsensitive oder CaseSensitive.<br>

Gibt den Index in dem mehrwertigen Attribut zurück, in dem die Zeichenfolge gefunden wurde. Wenn die Zeichenfolge nicht gefunden wird, wird 0 zurückgegeben.


**Hinweise:**<br>
Für mehrwertigen Zeichenfolgenattributen findet die Suche Teilzeichenfolgen in den Werten.<br>
Für Verweisattribute muss die Suchzeichenfolge genau mit dem Wert übereinstimmen, der als Übereinstimmung betrachtet wird.

**Beispiel:**<br>
`IIF(Contains([proxyAddresses],”SMTP:”)>0,[proxyAddresses],Error(“No primary SMTP address found.”))`<br>
Wenn das Attribut "proxyAddresses" eine primäre e-Mail-Adresse hat (angegeben durch die Großbuchstaben "SMTP:"), sonst Rückgabe ProxyAddress-Attribut einen Fehler zurück.




----------
### ConvertFromBase64

**Beschreibung:**<br>
Die ConvertFromBase64-Funktion konvertiert den angegebenen base64-codierten Wert in eine reguläre Zeichenfolge.

**Syntax:**<br>
`str ConvertFromBase64(str source)` -geht von Unicode-Codierung <br>
`str ConvertFromBase64(str source, enum Encoding)`

- source: Base64-codierte Zeichenfolge<br>
- Codierung: Unicode, ASCII, UTF8.

**Beispiel**<br>
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`<br>
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

In beiden Beispielen zurückgeben "*Hello World!*"




----------
### ConvertFromUTF8Hex

**Beschreibung:**<br>
Die ConvertFromUTF8Hex-Funktion konvertiert den angegebenen UTF8-Hex-codierten Wert in eine Zeichenfolge.

**Syntax:**<br>
`str ConvertFromUTF8Hex(str source)`

- source: UTF8-2-Byte-codierte Zeichenfolge

**Hinweise:**<br>
Der Unterschied zwischen dieser Funktion und ConvertFromBase64([],UTF8), ist das Ergebnis Anzeigenamen für das DN-Attribut.<br>
Dieses Format wird von Azure Active Directory als DN verwendet.

**Beispiel:**<br>
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`<br>
Gibt "*Hello World!*"




----------
### ConvertToBase64

**Beschreibung:** <br>
Die ConvertToBase64-Funktion konvertiert eine Zeichenfolge in eine Unicode-base64-Zeichenfolge.<br>
Konvertiert den Wert eines Arrays von ganzen Zahlen in die entsprechende mit Base64-Ziffern codierte Zeichenfolgendarstellung.

**Syntax:** <br>
`str ConvertToBase64(str source)`

**Beispiel:** <br>
`ConvertToBase64("Hello world!")` <br>
Gibt "sablagwababvacaadwbvahiababkacea" zurück.




----------
### ConvertToUTF8Hex

**Beschreibung:**<br>
Die ConvertToUTF8Hex-Funktion konvertiert eine Zeichenfolge in einen UTF8-Hex-codierten Wert.

**Syntax:**<br>
`str ConvertToUTF8Hex(str source)`

**Hinweise:**<br>
Das Ausgabeformat dieser Funktion wird von Azure Active Directory als DN-Attributformat verwendet.

**Beispiel:** <br>
`ConvertToUTF8Hex("Hello world!")` <br>
Gibt 48656C6C6F20776F726C6421




----------
### Count

**Beschreibung:**<br>
Die Count-Funktion gibt die Anzahl der Elemente in einem mehrwertigen Attribut zurück.

**Syntax:** <br>
`num Count(mvstr attribute)`




----------
### CNum

**Beschreibung:** <br>
Die CNum-Funktion akzeptiert eine Zeichenfolge und gibt einen numerischen Datentyp zurück.

**Syntax:** <br>
`num CNum(str value)`




----------
### CRef

**Beschreibung:** <br>
Konvertiert eine Zeichenfolge in ein Verweisattribut

**Syntax:** <br>
`ref CRef(str value)`

**Beispiel:** <br>
`CRef(“CN=LC Services,CN=Microsoft,CN=lcspool01, CN=Pools,CN=RTC Service,” & %Forest.LDAP%)`




----------
### CStr

**Beschreibung:** <br>
Die CStr-Funktion konvertiert in einen Zeichenfolgen-Datentyp.

**Syntax:** <br>
`str CStr(num value)` <br>
`str CStr(ref value)` <br>
`str CStr(bool value)` <br>

- value: Kann ein numerischer Wert, ein Verweisattribut oder ein boolescher Wert sein.

**Beispiel:** <br>
`CStr([dn]) <br>`
Zurückgeben könnte "Cn = Joe, dc = Contoso, dc = com"




----------
### DateAdd

**Beschreibung:** <br>
Gibt ein Datum mit einem Datum, zu dem ein angegebenes Zeitintervall hinzugefügt wurde.

**Syntax:** <br>
`dt DateAdd(str interval, num value, dt date)`

- interval: Zeichenfolgenausdruck, der das Zeitintervall angibt, das hinzugefügt werden soll. Die Zeichenfolge muss einen der folgenden Werte aufweisen:
 - yyyy: Jahr
 - q: Quartal
 - m: Monat
 - y: Tag des Jahres
 - d: Tag
 - w: Wochentag
 - ww: Woche
 - h: Stunde
 - n: Minute
 - s: Sekunde
- value: Die Anzahl der Einheiten, die Sie hinzufügen möchten. Der Wert kann positiv (für Datumsangaben in der Zukunft) oder negativ (für Datumsangaben in der Vergangenheit) sein.
- date: DateTime-Wert, der das Datum darstellt, dem das Intervall hinzugefügt wird.

**Beispiel:** <br>
`DateAdd("m", 3, CDate("2001-01-01"))` <br>
Fügt 3 Monate hinzu und gibt einen datetime-Wert, der "2001-04-01" darstellt




----------
### DateFromNum

**Beschreibung:** <br>
Die DateFromNum-Funktion konvertiert ein Wert im AD Datumsformat in einen DateTime-Typ.

**Syntax:** <br>
`dt DateFromNum(num value)`

**Beispiel:** <br>
`DateFromNum([lastLogonTimestamp])` <br>
`DateFromNum(129699324000000000)` <br>
Gibt einen datetime-Wert darstellt 2012-01-01 23:00:00




----------
### DNComponent

**Beschreibung:** <br>
Die DNComponent-Funktion gibt den Wert einer angegebenen DN-Komponente von links.

**Syntax:** <br>
`str DNComponent(ref dn, num ComponentNumber)`

- dn: Das zu interpretierende Verweisattribut
- ComponentNumber: Die Komponente im zurückzugebenden DN

**Beispiel:** <br>
`DNComponent([dn],1)`  <br>
DN "Cn = Joe, Ou =..., Joe zurückgegeben




----------
### DNComponentRev

**Beschreibung:** <br>
Die DNComponentRev-Funktion gibt den Wert einer angegebenen DN-Komponente von rechts (Ende).

**Syntax:** <br>
`str DNComponentRev(ref dn, num ComponentNumber)` <br>
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- dn: Das zu interpretierende Verweisattribut
- ComponentNumber: Die Komponente im zurückzugebenden DN
- Options: DC – alle Komponenten mit "dc=" ignorieren.

**Beispiel:** <br>
`If dn is “cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com” then DNComponentRev([dn],3)` <br>  `DNComponentRev([dn],1,”DC”)` <br>
Beide geben uns.




----------
### Error

**Beschreibung:** <br>
Die Error-Funktion wird verwendet, um einen benutzerdefinierten Fehler zurückzugeben.

**Syntax:** <br>
`void Error(str ErrorMessage)`

**Beispiel:** <br>
`IIF(IsPresent([accountName]),[accountName],Error(“AccountName is required”))` <br>
Wenn das Attribut Kontoname nicht vorhanden ist, wird ein Fehler für das Objekt.




----------
### EscapeDNComponent

**Beschreibung:** <br>
Die EscapeDNComponent-Funktion akzeptiert eine Komponente eines DN mit Escapezeichen, sodass es im LDAP dargestellt werden kann.

**Syntax:** <br>
`str EscapeDNComponent(str value)`

**Beispiel:** <br>
`EscapeDNComponent(“cn=” & [displayName]) & “,” & %ForestLDAP%` <br>
Stellt sicher, dass das Objekt in einem LDAP-Verzeichnis erstellt werden kann, auch wenn das Attribut DisplayName Zeichen enthält, die in LDAP mit Escapezeichen versehen werden müssen.




----------
### FormatDateTime

**Beschreibung:** <br>
Die FormatDateTime-Funktion wird verwendet, um einen datetime-Wert in eine Zeichenfolge mit einem angegebenen Format zu formatieren.

**Syntax:** <br>
`str FormatDateTime(dt value, str format)`

- value: Ein Wert im DateTime-Format  <br>
- format: Eine Zeichenfolge, die das Format darstellt, in das konvertiert werden soll.

**Hinweise:** <br>
Die möglichen Werte für das Format finden Sie hier: [benutzerdefinierte Datums-/Zeitformate (Format-Funktion)](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Beispiel:** <br>

`FormatDateTime(CDate(“12/25/2007”),”yyyy-mm-dd”)` <br>
Ergibt "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),”yyyyMMddHHmmss.0Z”)` <br>
Kann "20140905081453.0Z" ergeben.




----------
### GUID

**Beschreibung:** <br>
GUID-Funktion generiert eine GUID

**Syntax:** <br>
`str GUID()`




----------
### IIF

**Beschreibung:** <br>  
Die IIF-Funktion gibt einen von einem Satz möglicher Werte auf Grundlage einer angegebenen Bedingung.

**Syntax:** <br>
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition: Ein beliebiger Wert oder Ausdruck, der als True oder False ausgewertet werden kann.
- valueIfTrue: Ein Wert, der zurückgegeben wird, falls die Bedingung als True ausgewertet wird.
- valueIfFalse: Ein Wert, der zurückgegeben wird, falls die Bedingung als False ausgewertet wird.

**Beispiel:** <br>
`IIF([employeeType]=“Intern”,”t-“&[alias],[alias])` <br>
Gibt der Alias eines Benutzers mit"t" am Anfang hinzugefügt, wenn der Benutzer einen neuen Mitarbeiter einzufügen, andernfalls ist der Alias des Benutzers unverändert zurückgegeben.




----------
### InStr

**Beschreibung:** <br>
Die InStr-Funktion sucht das erste Vorkommen einer Teilzeichenfolge in einer Zeichenfolge

**Syntax:** <br>

`num InStr(str stringcheck, str stringmatch)` <br>
`num InStr(str stringcheck, str stringmatch, num start)` <br>
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck: Zu durchsuchende Zeichenfolge <br>
- stringmatch: Zu findende Zeichenfolge <br>
- start: Startposition zum Finden der Teilzeichenfolge <br>
- compare: VbTextCompare oder VbBinaryCompare

**Hinweise:** <br>
Gibt die Position, in dem die Teilzeichenfolge gefunden wurde, oder 0, wenn nicht gefunden.

**Beispiel:** <br>
`InStr("The quick brown fox","quick")` <br>
/ / Ergibt 5

`InStr("repEated","e",3,vbBinaryCompare)` <br>
/ / Ergibt 7




----------
### InStrRev

**Beschreibung:** <br>
Die InStrRev-Funktion sucht das letzte Vorkommen einer Teilzeichenfolge in einer Zeichenfolge

**Syntax:** <br>
`num InstrRev(str stringcheck, str stringmatch)` <br>
`num InstrRev(str stringcheck, str stringmatch, num start)` <br>
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck: Zu durchsuchende Zeichenfolge <br>
- stringmatch: Zu findende Zeichenfolge <br>
- start: Startposition zum Finden der Teilzeichenfolge <br>
- compare: VbTextCompare oder VbBinaryCompare

**Hinweise:** <br>
Gibt die Position, in dem die Teilzeichenfolge gefunden wurde, oder 0, wenn nicht gefunden.

**Beispiel:** <br>
`InStrRev("abbcdbbbef","bb")` <br>
Gibt 7




----------
### IsBitSet

**Beschreibung:** <br>
Die Funktion "isbitset" testet, ob ein bit festgelegt ist oder nicht

**Syntax:** <br>
`bool IsBitSet(num value, num flag)`

- value: Ein numerischer Wert, der ausgewertet wird. flag: Ein numerischer Wert, der das Bit angibt, das ausgewertet werden soll.

**Beispiel:** <br>
`IsBitSet(&HF,4)` <br>
Gibt True zurück, da Bit "4" im Hexadezimalwert "F" festgelegt ist




----------
### IsDate

**Beschreibung:** <br>
Die IsDate-Funktion zu True ausgewertet wird, wenn der Ausdruck sein kann als DateTime-Typ ausgewertet.

**Syntax:** <br>
`bool IsDate(var Expression)`

**Hinweise:** <br>
Verwendet, um zu bestimmen, ob CDate() erfolgreich sein wird.




----------
###IsEmpty

**Beschreibung:** <br>  
Die IsEmpty-Funktion ergibt True, wenn das Attribut in CS oder MV vorhanden ist, aber auf eine leere Zeichenfolge ausgewertet wird.

**Syntax:** <br>
`bool IsEmpty(var Expression)`




----------
###IsGuid

**Beschreibung:** <br>
Die IsGuid-Funktion ergibt True, wenn die Zeichenfolge in eine GUID konvertiert werden kann.

**Syntax:** <br>
`bool IsGuid(str GUID)`

**Hinweise:** <br>
Eine GUID ist definiert als eine Zeichenfolge, die nach einem der folgenden Muster: Xxxxxxxx-Xxxx-Xxxx-Xxxx-Xxxxxxxxxxxx oder {Xxxxxxxx-Xxxx-Xxxx-Xxxx-Xxxxxxxxxxxx}

Wird verwendet, um zu bestimmen, ob "CGuid()" erfolgreich eingesetzt werden kann.

**Beispiel:** <br>
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)` <br>
Wenn das StrAttribute ein GUID-Format aufweist, eine binäre Darstellung zurückgegeben, andernfalls wird Null zurückgegeben.




----------
###IsNull

**Beschreibung:** <br>
Die IsNull-Funktion zurückgibt true, wenn der Ausdruck zu Null ausgewertet wird.

**Syntax:** <br>
`bool IsNull(var Expression)`

**Hinweise:** <br>
Für ein Attribut ist ein NULL-Wert durch das Fehlen des Attributs ausgedrückt.

**Beispiel:** <br>
`IsNull([displayName])` <br>
Gibt True zurück, wenn das Attribut nicht in CS oder MV vorhanden ist.




----------
###IsNullOrEmpty

**Beschreibung:** <br>
Die IsNullOrEmpty-Funktion gibt true zurück, wenn der Ausdruck null ist oder eine leere Zeichenfolge zurück.

**Syntax:** <br>
`bool IsNullOrEmpty(var Expression)`

**Hinweise:** <br>
Für ein Attribut würde dies auf "true" ausgewertet, wenn das Attribut nicht vorhanden ist oder vorhanden ist, aber eine leere Zeichenfolge.<br>
Die Umkehrung dieser Funktion heißt "IsPresent".

**Beispiel:** <br>
`IsNull([displayName])` <br>
Gibt True zurück, wenn das Attribut nicht vorhanden ist oder eine leere Zeichenfolge in CS oder MV ist.




----------
### IsNumeric

**Beschreibung:** <br>
Die IsNumeric-Funktion gibt einen booleschen Wert, der angibt, ob ein Ausdruck als Zahlentyp ausgewertet werden kann.

**Syntax:** <br>
`bool IsNumeric(var Expression)`

**Hinweise:** <br>
Verwendet, um zu bestimmen, ob CNum() beim Analysieren des Ausdrucks erfolgreich sein wird.




----------
### IsString

**Beschreibung:** <br>
Die IsString-Funktion ergibt True, wenn der Ausdruck als Zeichenfolgentyp ausgewertet werden kann.

**Syntax:** <br>
`bool IsString(var expression)`

**Hinweise:** <br>
Verwendet, um zu bestimmen, ob CStr() beim Analysieren des Ausdrucks erfolgreich sein wird.




----------
### IsPresent

**Beschreibung:** <br>
True, wenn der Ausdruck in eine Zeichenfolge ergibt, die nicht Null und nicht leer ist, gibt die Funktion "IsPresent" zurück.

**Syntax:** <br>
`bool IsPresent(var expression)`

**Hinweise:** <br>
Die Umkehrung dieser Funktion heißt IsNullOrEmpty.

**Beispiel:** <br>

`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`




----------
### Item

**Beschreibung:** <br>
Die Item-Funktion gibt ein Element aus einer mehrwertigen Zeichenfolge/einem Attribut zurück.

**Syntax:** <br>
`var Item(mvstr attribute, num index)`

- attribute: Mehrwertiges Attribut <br>
- index: Index für ein Element in der mehrwertigen Zeichenfolge

**Hinweise:** <br>
Die Item-Funktion wird zusammen mit der Contains-Funktion, da letztere den Index für ein Element im mehrwertigen Attribut zurückgibt.

Gibt einen Fehler aus, wenn der Index außerhalb des gültigen Bereichs liegt.

**Beispiel:** <br>
`Mid(Item([proxyAddress],Contains([proxyAddress], ”SMTP:”)),6)`  <br>
Die primäre e-Mail-Adresse zurückgegeben.




----------
### ItemOrNull

**Beschreibung:** <br>
Die ItemOrNull-Funktion gibt ein Element aus einer mehrwertigen Zeichenfolge/einem Attribut zurück.

**Syntax:** <br>
`var ItemOrNull(mvstr attribute, num index)`

- attribute: Mehrwertiges Attribut <br>
- index: Index für ein Element in der mehrwertigen Zeichenfolge

**Hinweise:** <br>
Die ItemOrNull-Funktion wird zusammen mit der Contains-Funktion, da letztere den Index für ein Element im mehrwertigen Attribut zurückgibt.

Wenn der Index außerhalb des gültigen Bereichs liegt, wird ein Nullwert zurückgegeben.




----------
### Join

**Beschreibung:** <br>
Die Join-Funktion akzeptiert eine mehrwertige Zeichenfolge und gibt eine einwertige Zeichenfolge zurück, wobei angegebene Trennzeichen zwischen den einzelnen Elementen eingefügt.

**Syntax:** <br>
`str Join(mvstr attribute)` <br>
`str Join(mvstr attribute, str Delimiter)`

- attribute: Mehrwertiges Attribut mit Zeichenfolgen, die verknüpft werden sollen. <br>
- delimiter: Eine beliebige Zeichenfolge, die die Teilzeichenfolgen in der zurückgegebenen Zeichenfolge trennt. Wenn nicht angegeben, wird das Leerzeichen (" ") verwendet. Wenn "delimiter" eine Zeichenfolge der Länge 0 ("") oder "Nothing" ist, werden alle Elemente in der Liste ohne Trennzeichen verkettet.

**Hinweise**<br>
Es ist die Parität zwischen den Join und Split-Funktionen. Die Funktion "Join" nimmt ein Array von Zeichenfolgen entgegen, verknüpft sie mit einer Trennzeichenfolge und gibt eine einzige Zeichenfolge zurück. Die Funktion "Split" nimmt eine Zeichenfolge entgegen, trennt sie mit dem Trennzeichen und gibt ein Array von Zeichenfolgen zurück. Ein wichtiger Unterschied ist jedoch, dass "Join" Zeichenfolgen mit einer beliebigen Trennzeichenfolge verketten kann, "Split" aber nur Zeichenfolgen mit einem einzigen Trennzeichen trennen kann.

**Beispiel:** <br>
`Join([proxyAddresses],”,”)` <br>
Zurückgeben: "SMTP:john.doe@contoso.com, smtp:jd@contoso.com"




----------
### LCase

**Beschreibung:** <br>
Die LCase-Funktion konvertiert alle Zeichen in einer Zeichenfolge in Kleinbuchstaben.

**Syntax:** <br>
`str LCase(str value)`

**Beispiel:** <br>
`LCase(“TeSt”)` <br>
Gibt "Test".




----------
### Left

**Beschreibung:** <br>
Die Left-Funktion gibt eine angegebene Anzahl von Zeichen von der linken Seite einer Zeichenfolge zurück.

**Syntax:** <br>
`str Left(str string, num NumChars)`

- string: Zeichenfolge, aus der Zeichen zurückgegeben werden <br>
- numChars: Diese Zahl gibt die Anzahl der Zeichen an, die vom Anfang der Zeichenfolge (links) zurückgeben werden

**Hinweise:** <br>
Eine Zeichenfolge, die die ersten "NUMCHARS" Zeichen in Zeichenfolge enthält:

- Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
- Wenn "numChars" < 0, wird die Eingabezeichenfolge zurückgegeben.
- Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.

Wenn die Zeichenfolge weniger Zeichen enthält als in "numChars" angegeben, wird eine identische (alle in Parameter 1 enthaltenen Zeichen enthaltende) Zeichenfolge zurückgegeben.

**Beispiel:** <br>
`Left(“John Doe”, 3)` <br>
Gibt "Joh" zurück.




----------
### Len

**Beschreibung:** <br>
Die Len-Funktion gibt die Anzahl der Zeichen in einer Zeichenfolge zurück.

**Syntax:** <br>
`num Len(str value)`

**Beispiel:** <br>
`Len(“John Doe”)` <br>
Gibt 8




----------
### LTrim

**Beschreibung:** <br>
LTrim-Funktion entfernt führende Leerzeichen aus einer Zeichenfolge.

**Syntax:** <br>
`str LTrim(str value)`

**Beispiel:** <br>
`LTrim(“ Test ”)` <br>
Gibt "Test"




----------
### Mid

**Beschreibung:** <br>
Die Mid-Funktion gibt eine angegebene Anzahl von Zeichen aus einer angegebenen Position in einer Zeichenfolge zurück.

**Syntax:** <br>
`str Mid(str string, num start, num NumChars)`

- string: Zeichenfolge, aus der Zeichen zurückgegeben werden <br>
- start: Eine Zahl zur Angabe der Ausgangsposition in der Zeichenfolge, ab der Zeichen zurückgegeben werden
- numChars: Eine Zahl zur Angabe der Zeichen, die ab der Position in der Zeichenfolge zurückgegeben werden


**Hinweise:** <br>
Geben Sie "NUMCHARS"-Zeichen ab der Ausgangsposition in der Zeichenfolge zurück.<br>
Eine Zeichenfolge, die "numChars" Zeichen ab der Ausgangsposition in der Zeichenfolge enthält:

- Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
- Wenn "numChars" < 0, wird die Eingabezeichenfolge zurückgegeben.
- Wenn "start" > Länge der Zeichenfolge, wird die Eingabezeichenfolge zurückgegeben
- Wenn "start" < = 0, wird die Eingabezeichenfolge zurückgegeben
- Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.

Wenn nicht "numChars" Zeichen ab der Ausgangsposition in der Zeichenfolge vorhanden sind, werden so viele Zeichen wie vorhanden zurückgegeben.

**Beispiel:** <br>

`Mid(“John Doe”, 3, 5)` <br>
Gibt "hn Do" zurück.

`Mid(“John Doe”, 6, 999)` <br>
Gibt "Doe"




----------
### Now

**Beschreibung:** <br>
Die Now-Funktion gibt einen datetime-Wert angeben, das aktuelle Datum und die Uhrzeit gemäß Systemdatum und-Uhrzeit Ihres Computers zurück.

**Syntax:** <br>
`dt Now()`




----------
### NumFromDate

**Beschreibung:** <br>
Die Funktion "numfromdate" gibt ein Datum im AD Datumsformat zurück.

**Syntax:** <br>
`num NumFromDate(dt value)`


**Beispiel:** <br>
`NumFromDate(CDate("2012-01-01 23:00:00"))` <br>
Gibt 129699324000000000 zurück.




----------
### PadLeft

**Beschreibung:** <br>
Die PadLeft-Funktion links füllt eine Zeichenfolge mit einer angegebenen Länge mit einem bereitgestellten Auffüllzeichen.

**Syntax:** <br>
`str PadLeft(str string, num length, str padCharacter)`

- string: Die aufzufüllende Zeichenfolge. <br>
- length: Eine ganze Zahl, die die gewünschte Länge der Zeichenfolge angibt. <br>
- padCharacter: Eine Zeichenfolge, die aus einem einzelnen Zeichen besteht, das als Auffüllzeichen verwendet werden soll



----------
### Hinweise

- Wenn die Länge der Zeichenfolge "length" unterschreitet, wird "padCharacter" wiederholt an den Anfang der Zeichenfolge (links) gesetzt, bis ihre Länge "length" entspricht.
- "padCharacter" kann ein Leerzeichen sein, jedoch kein Nullwert.
- Wenn die Länge der Zeichenfolge gleich oder größer als "length" ist, wird die Zeichenfolge unverändert zurückgegeben.
- Wenn die Länge der Zeichenfolge größer als oder gleich "length" ist, wird eine zur Zeichenfolge identische Zeichenfolge zurückgegeben.
- Wenn die Länge der Zeichenfolge kleiner als "length" ist, wird eine neue Zeichenfolge in gewünschter Länge zurückgegeben, die die mit "padCharacter" aufgefüllte Zeichenfolge enthält.
- Wenn die Zeichenfolge einen Nullwert aufweist, gibt die Funktion eine leere Zeichenfolge zurück. 

**Beispiel:** <br>
`PadLeft(“User”, 10, “0”)` <br>
Gibt "000000User" zurück.




----------
### PadRight

**Beschreibung:** <br>
Die PadRight-Funktion rechts füllt eine Zeichenfolge mit einer angegebenen Länge mit einem bereitgestellten Auffüllzeichen.

**Syntax:** <br>
`str PadRight(str string, num length, str padCharacter)`

- string: Die aufzufüllende Zeichenfolge.
- length: Eine ganze Zahl, die die gewünschte Länge der Zeichenfolge angibt.
- padCharacter: Eine Zeichenfolge, die aus einem einzelnen Zeichen besteht, das als Auffüllzeichen verwendet werden soll

**Hinweise:**

- Wenn die Länge der Zeichenfolge kleiner als "length" ist, wird "padCharacter" wiederholt an das Ende der Zeichenfolge (rechts) gesetzt, bis ihre Länge "length" entspricht.
- "padCharacter" kann ein Leerzeichen sein, jedoch kein Nullwert.
- Wenn die Länge der Zeichenfolge gleich oder größer als "length" ist, wird die Zeichenfolge unverändert zurückgegeben.
- Wenn die Länge der Zeichenfolge größer als oder gleich "length" ist, wird eine zur Zeichenfolge identische Zeichenfolge zurückgegeben.
- Wenn die Länge der Zeichenfolge kleiner als "length" ist, wird eine neue Zeichenfolge in gewünschter Länge zurückgegeben, die die mit "padCharacter" aufgefüllte Zeichenfolge enthält.
- Wenn die Zeichenfolge einen Nullwert aufweist, gibt die Funktion eine leere Zeichenfolge zurück. 


**Beispiel:** <br>
`PadRight(“User”, 10, “0”)` <br>
Gibt "User000000" zurück.




----------
### PCase

**Beschreibung:** <br>
Die PCase-Funktion konvertiert das erste Zeichen jedes Leerzeichen getrennten Worts in einer Zeichenfolge in Großbuchstaben und alle anderen Zeichen werden in Kleinbuchstaben konvertiert.

**Syntax:** <br>
`String PCase(string)`

**Beispiel:** <br>
`PCase(“TEsT”)` <br>
Gibt "Test" zurück.




----------
### RandomNum

**Beschreibung:** <br>
Die RandomNum-Funktion gibt eine Zufallszahl zwischen einem angegebenen Intervall zurück.

**Syntax:** <br>
`num RandomNum(num start, num end)`

- start: Eine Zahl, die die untere Grenze des zu generierenden Zufallswerts angibt <br>
- end: Eine Zahl, die die obere Grenze des zu generierenden Zufallswerts angibt

**Beispiel:** <br>
`Random(100,999)` <br>
Gibt 734 zurück.




----------
### RemoveDuplicates

**Beschreibung:** <br>
RemoveDuplicates-Funktion akzeptiert eine mehrwertige Zeichenfolge und stellen Sie sicher, dass jeder Wert eindeutig ist.

**Syntax:** <br>  
`mvstr RemoveDuplicates(mvstr attribute)`

**Beispiel:** <br>
`RemoveDuplicates([proxyAddresses])` <br>
Gibt ein sicheres ProxyAddress-Attribut zurück, aus dem alle doppelten Werte entfernt wurden.




----------
### Replace

**Beschreibung:** <br>
Die Replace-Funktion ersetzt alle Vorkommen einer Zeichenfolge in eine andere Zeichenfolge.

**Syntax:** <br>
`str Replace(str string, str OldValue, str NewValue)`

- string: Eine Zeichenfolge, in der Werte ersetzt werden sollen <br>
- OldValue: Die Zeichenfolge, die gesucht und ersetzt werden soll <br>
- NewValue: Die Zeichenfolge, die die gesuchte Zeichenfolge ersetzen soll


**Hinweise:** <br>
Die Funktion erkennt die folgenden speziellen Moniker:

- \n – neue Zeile
- \r – Wagenrücklauf
- \t – Tabulator


**Beispiel:** <br>

`Replace([address],”\r\n”,”, “)` <br>
Ersetzt CRLF durch ein Komma und Leerzeichen und führen zu "One-Microsoft Möglichkeit, Redmond, WA, USA"




----------
### ReplaceChars

**Beschreibung:** <br>
Die ReplaceChars-Funktion ersetzt alle Vorkommen von Zeichen in der Ersetzenmuster-Zeichenfolge gefunden.

**Syntax:** <br>
`str ReplaceChars(str string, str ReplacePattern)`

- string: Eine Zeichenfolge, in der Zeichen ersetzt werden sollen
- ReplacePattern: Eine Zeichenfolge, die ein Wörterbuch mit zu ersetzenden Zeichen enthält

Das Format lautet "{source1}:{target1},{source2}:{target2},{sourceN},{targetN}", wobei "source" das zu findende Zeichen und "target" die Zeichenfolge ist, durch die es ersetzt wird.


**Hinweise:**

- Die Funktion ersetzt alle Vorkommen von definierten Quellen durch die Ziele.
- Die Quelle muss genau ein Zeichen (Unicode) sein.
- Die Quelle kann nicht leer sein oder mehrere Zeichen enthalten (Analysefehler).
- Das Ziel kann mehrere Zeichen enthalten, z. B. ö:oe, β:ss.
- Das Ziel kann leer sein, um anzugeben, dass das Zeichen entfernt werden soll.
- Bei der Quelle wird die Groß-/Kleinschreibung beachtet, und es muss eine genaue Übereinstimmung vorliegen.
- Die reservierten Zeichen "," (Komma) und ":" (Doppelpunkt) können mit dieser Funktion nicht ersetzt werden.
- Leerzeichen und andere Whitespaces in der Zeichenfolge "ReplacePattern" werden ignoriert.


**Beispiel:** <br>
' %ReplaceString% = ':, å "," a "," ä "," å "," a "," Ö, Arbeitsressource

`ReplaceChars(”Räksmörgås”,%ReplaceString%)` <br>
Gibt Raksmorgas zurück.

`ReplaceChars(“O’Neil”,%ReplaceString%)` <br>
Gibt "ONeil", der Apostroph ist definiert, entfernt werden sollen.




----------
### Right

**Beschreibung:** <br>
Die Right-Funktion gibt eine angegebene Anzahl von Zeichen von rechts (Ende) einer Zeichenfolge zurück.

**Syntax:** <br>
`str Right(str string, num NumChars)`

- string: Zeichenfolge, aus der Zeichen zurückgegeben werden
- numChars: Diese Zahl gibt die Anzahl der Zeichen an, die vom Ende der Zeichenfolge (rechts) zurückgeben werden

**Hinweise:** <br>
"NUMCHARS" Zeichen werden von der letzten Position der Zeichenfolge zurückgegeben.

Eine Zeichenfolge, die die letzten "numChars" Zeichen in der Zeichenfolge enthält:

- Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
- Wenn "numChars" < 0, wird die Eingabezeichenfolge zurückgegeben.
- Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.

Wenn die Zeichenfolge weniger Zeichen enthält als in "numChars" angegeben, wird eine identische Zeichenfolge zurückgegeben.

**Beispiel:** <br>
`Right(“John Doe”, 3)` <br>
Gibt "Doe" zurück.




----------
### RTrim

**Beschreibung:** <br>
Die RTrim-Funktion entfernt nachgestellte Leerzeichen aus einer Zeichenfolge.

**Syntax:** <br>
`str RTrim(str value)`

**Beispiel:** <br>
`RTrim(“ Test ”)` <br>
Gibt "Test" zurück.




----------
### Split

**Beschreibung:** <br>
Die Split-Funktion akzeptiert eine durch Trennzeichen getrennte Zeichenfolge und erleichtert eine mehrwertige Zeichenfolge.


**Syntax:** <br>
`mvstr Split(str value, str delimiter)` < Br?
`mvstr Split(str value, str delimiter, num limit)`

- value: Die Zeichenfolge mit Trennzeichen
- delimiter: Einzelnes Zeichen, das als Trennzeichen verwendet werden soll
- limit: Maximale Anzahl der Werte, die zurückgegeben werden

**Beispiel:** <br>
`Split(“SMTP:john.doe@contoso.com,smtp:jd@contoso.com”,”,”)` <br>
Gibt eine mehrwertige Zeichenfolge mit 2 Elementen für das Attribut "ProxyAddress" nützlich




----------
### StringFromGuid

**Beschreibung:** <br>
Die StringFromGuid-Funktion akzeptiert eine binäre GUID und konvertiert es in eine Zeichenfolge

**Syntax:** <br>
`str StringFromGuid(bin GUID)`




----------
### StringFromSid

**Beschreibung:** <br>
Die StringFromSid-Funktion konvertiert ein Bytearray oder ein mehrwertiges Bytearray eine Sicherheits-ID in eine Zeichenfolge oder mehrwertige Zeichenfolge.

**Syntax:** <br>
`str StringFromSid(bin ObjectSID)` <br>
`mvstr StringFromSid(mvbin ObjectSID)`




----------
### Switch

**Beschreibung:** <br>
Switch-Funktion wird verwendet, um ein einzelner Wert basierend auf ausgewerteten Bedingungen zurückgegeben.

**Syntax:** <br>
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr: Variantenausdruck, den Sie auswerten möchten
- value: Wert, der zurückgegeben werden soll, wenn der entsprechende Ausdruck True ergibt

**Hinweise:** <br>
Die Argumentliste der Switch-Funktion besteht aus Paaren von Ausdrücken und Werten. Die Ausdrücke werden von links nach rechts ausgewertet, und der Wert, der mit dem ersten als True ausgewerteten Ausdruck verknüpft ist, wird zurückgegeben. Wenn die Teile nicht richtig paarweise angegeben werden, tritt ein Laufzeitfehler auf.

Wenn z. B. "expr1" True ist, gibt "Switch" "value1" zurück. Wenn "expr-1" False, aber "expr-2" True ist, gibt "Switch" "value-2" zurück usw.

Switch gibt nichts zurück, wenn:
- Keiner der Ausdrücke TRUE ist.
- Der erste als TRUE ausgewertete Ausdruck über einen zugehörigen Wert verfügt, der Null ist.

"Switch" wertet alle Ausdrücke aus, auch wenn die Funktion nur einen von ihnen zurückgibt. Aus diesem Grund sollten Sie auf unerwünschte Nebeneffekte achten. Wenn z. B. die Berechnung eines beliebigen Ausdrucks zu einer Division durch 0 führt, tritt ein Fehler auf.

Ein Wert kann auch die Funktion "Error" sein, die eine benutzerdefinierte Zeichenfolge zurückgibt.

**Beispiel:** <br>
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error(“Unknown city”))` <br>
Gibt die Sprache zurück, die in einigen wichtigen Städten gesprochen wird, andernfalls wird einen Fehler zurückgegeben.




----------
### Trim

**Beschreibung:** <br>
Die Glätten-Funktion entfernt führende und nachfolgende Leerzeichen aus einer Zeichenfolge.

**Syntax:** <br>
`str Trim(str value)` <br>
`mvstr Trim(mvstr value)`

**Beispiel:** <br>
`Trim(“ Test ”)` <br>
Gibt "Test" zurück.

`Trim([proxyAddresses])` <br>
Entfernt führende und nachfolgende Leerzeichen für jeden Wert im ProxyAddress-Attribut.




----------
### UCase

**Beschreibung:** <br>
Die UCase-Funktion konvertiert alle Zeichen in einer Zeichenfolge in Großbuchstaben.

**Syntax:** <br>
`str UCase(str string)`

**Beispiel:** <br>
`UCase(“TeSt”)` <br>
Gibt "TEST".




----------
### Word

**Beschreibung:** <br>
Die Word-Funktion gibt ein Wort innerhalb einer Zeichenfolge basierend auf Parametern, die die zu verwendenden Trennzeichen und die Anzahl von zurückzugebenden beschreibt.

**Syntax:** <br>
`str Word(str string, num WordNumber, str delimiters)`

- string: Zeichenfolge, aus der ein Wort zurückgegeben wird
- WordNumber: Eine Zahl, die die Nummer des zurückzugebenden Worts angibt
- delimiters: Eine Zeichenfolge, die das/die Trennzeichen angibt, das/die verwendet werden soll(en), um Wörter zu identifizieren

**Hinweise:** <br>
Jede Zeichenfolge in der von einem Zeichen in Trennzeichen getrennte Zeichenfolge werden als Wörter identifiziert:

- Wenn "WordNumber" < 1, wird eine leere Zeichenfolge zurückgegeben
- Wenn "string" einen Nullwert hat, wird eine leere Zeichenfolge zurückgegeben

Wenn "string" weniger als "WordNumber" Wörter enthält, oder "string" keine Wörter enthält, die durch Trennzeichen identifiziert werden, wird eine leere Zeichenfolge zurückgegeben.


**Beispiel:** <br>
`Word(“The quick brown fox”,3,” “)` <br>
Gibt "brown"

`Word(“This,string!has&many seperators”,3,”,!&#”)` <br>
Gibt "has" zurück


## Zusätzliche Ressourcen

* [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)


<!--Image references-->


