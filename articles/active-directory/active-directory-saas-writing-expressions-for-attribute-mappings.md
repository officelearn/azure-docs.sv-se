<properties
    pageTitle="Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory | Microsoft Azure"
    description="Erfahren Sie, wie Ausdruckszuordnungen verwendet werden können, um Attributwerte während der automatisierten Bereitstellung von SaaS-App-Objekten in Azure Active Directory in ein akzeptables Format zu transformieren."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2015"
    ms.author="markusvi"/>


# Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory

Wenn Sie die Bereitstellung für eine SaaS-Anwendung konfigurieren, ist einer der Attributzuordnungstypen, die Sie angeben können, eine Ausdruckszuordnung.  
 Für diese müssen Sie einen skriptartigen Ausdruck schreiben, mit dem Sie die Daten Ihrer Benutzer in Formate umwandeln können, die für die SaaS-Anwendung einfacher zu akzeptieren sind.





## Syntaxübersicht

Die Syntax für die Ausdrücke für Attributzuordnungen ist den Funktionen von Visual Basic for Applications (VBA) ähnlich.

- Der gesamte Ausdruck muss in Form von Funktionen definiert werden, die aus einem Namen, gefolgt von Argumenten in Klammern bestehen: <br>
*Funktionsname (<< Argument 1 >> <<argument N>>)*


- Sie können Funktionen ineinander verschachteln. Beispiel: <br> *Funktioneins (FunctionTwo(<<argument1>>))*


- Sie können drei verschiedene Argumententypen an die Funktionen übergeben:

   1. Attribute, die in eckige Klammern eingeschlossen werden müssen. Beispiel: [Attributname]

   2. Zeichenfolgenkonstanten, die in doppelte Anführungszeichen eingeschlossen werden müssen. Beispiel: "USA"

   3. Andere Funktionen Zum Beispiel: Funktioneins (<<argument1>>, Funktionzwei (<<argument2>>))


- Bei Zeichenfolgenkonstanten, in denen ein umgekehrter Schrägstrich ( \ ) oder ein Anführungszeichen ( " ) benötigt wird, muss dieser bzw. dieses mit einem umgekehrten Schrägstrichsymbol ( \ ) versehen werden. Zum Beispiel: "Firmenname: \"Contoso\ ""



## Liste der Funktionen

[Fügen Sie](#append) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [FormatDateTime](#formatdatetime) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Join](#join) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Mid](#mid) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Nicht](#not) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Ersetzen Sie](#replace) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [StripSpaces](#stripspaces) & Nbsp; & Nbsp; & Nbsp; & Nbsp; [Switch](#switch)





----------
### Anfügen

**Funktion:**<br> 
Append(Source, Suffix)

**Beschreibung:**<br> 
Nimmt einen quellzeichenfolgenwert und hängt ein Suffix an das Ende an.
 
**Parameter:**<br> 

|Name| Erforderlich/wiederholt | Typ | Hinweise |
|--- | ---                 | ---  | ---   |
| **Quelle** | Erforderlich | String | Normalerweise der Name des Attributs aus dem Quellobjekt |
| **Suffix** | Erforderlich | String | Die Zeichenfolge, die Sie am Ende des Quellwerts anfügen möchten |


----------
### FormatDateTime

**Funktion:**<br> 
FormatDateTime (Quelle, Eingabeformat, Ausgabeformat)

**Beschreibung:**<br> 
Eine Datumszeichenfolge eines Formats und konvertiert ihn in ein anderes Format.
 
**Parameter:**<br> 

|Name| Erforderlich/wiederholt | Typ | Hinweise |
|--- | ---                 | ---  | ---   |
| **Quelle** | Erforderlich | String | Normalerweise der Name des Attributs aus dem Quellobjekt |
| **Eingabeformat** | Erforderlich | String | Erwartetes Format des Quellwerts. Informationen zu den unterstützten Formaten finden Sie unter [http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **Ausgabeformat** | Erforderlich | String | Format des Ausgabedatums. |



----------
### Join

**Funktion:**<br> 
Join (Trennzeichen, Quelle1, Quelle2, …)

**Beschreibung:**<br> 
Join() ist Append() ähnlich, außer dass es mehrere kombinieren kann **Quelle** Zeichenfolge in eine einzelne Zeichenfolge und jede getrennt werden durch eine **Trennzeichen** Zeichenfolge.

Wenn einer der Quellwerte ein mehrwertiges Attribut ist, werden die einzelnen Werte in diesem Attribut miteinander verknüpft und dabei durch den Trennzeichenwert getrennt.

 
**Parameter:**<br> 

|Name| Erforderlich/wiederholt | Typ | Hinweise |
|--- | ---                 | ---  | ---   |
| **Trennzeichen** | Erforderlich | String | Zeichenfolge, die zur Trennung von Quellwerten verwendet wird, wenn diese zu einer einzelnen Zeichenfolge zusammengesetzt werden. Kann "" sein, wenn kein Trennzeichen erforderlich ist. |
| ** Quelle1... Quellen ** | Erforderlich, unterschiedlich oft | String | Zeichenfolgenwerte verknüpft sind. |



----------
### Mid

**Funktion:**<br> 
Mid (Quelle, Start, Länge)

**Beschreibung:**<br> 
Gibt eine Teilzeichenfolge des Quellwerts zurück. Eine Teilzeichenfolge ist eine Zeichenfolge, die nur einige der Zeichen aus der Quellzeichenfolge enthält.


**Parameter:**<br> 

|Name| Erforderlich/wiederholt | Typ | Hinweise |
|--- | ---                 | ---  | ---   |
| **Quelle** | Erforderlich | String | Normalerweise der Name des Attributs. |
| **start** | Erforderlich | integer | Index in der **Quelle** Zeichenfolge, in dem die Teilzeichenfolge beginnen soll. Das erstes Zeichen in der Zeichenfolge hat den Index 1, das zweite Zeichen hat den Index 2 usw. |
| **Länge** | Erforderlich | Ganze Zahl | Die Länge der Teilzeichenfolge. Wenn die Länge außerhalb der **Quelle** Zeichenfolge zurückgeben Funktion Teilzeichenfolge aus **start** Index bis zum Ende der **Quelle** Zeichenfolge. |




----------
### Nicht

**Funktion:**<br> 
NOT(Quelle)

**Beschreibung:**<br> 
Schaltet den booleschen Wert, der die **Quelle**. Wenn **Quelle** Wert ist "*True*", gibt "*" false "*". Andernfalls "*True*".


**Parameter:**<br> 

|Name| Erforderlich/wiederholt | Typ | Hinweise |
|--- | ---                 | ---  | ---   |
| **Quelle** | Erforderlich | Boolesche Zeichenfolge | Erwartete **Quelle** Werte sind "True" oder "False"... |



----------
### Replace

**Funktion:**<br> 
ObsoleteReplace (Quelle, AlterWert, Regexmuster, Regexgruppenname, Ersatzwert, Ersatzattributname, Vorlage)

**Beschreibung:**<br>
Ersetzt Werte in einer Zeichenfolge. Sie funktioniert unterschiedlich, je nachdem, welche Parameter angegeben werden:

- Wenn **OldValue** und **Ersatzwert** werden bereitgestellt:

   - Ersetzt alle Vorkommen von OldValue in der Quelle durch replacementValue

- Wenn **OldValue** und **Vorlage** werden bereitgestellt:

   - Ersetzt alle Vorkommen der **OldValue** in die **Vorlage** mit der **Quelle** Wert

- Wenn **Regexmuster**, **Regexgruppenname**, **Ersatzwert** werden bereitgestellt:

   - Ersetzt alle Werte, die mit dem "AlterWertRegexMuster" in der Quellzeichenfolge übereinstimmen, durch den "Ersatzwert".

- Wenn **Regexmuster**, **Regexgruppenname**, **Ersatzattributname** werden bereitgestellt:

   - Wenn **Quelle** Wert besitzt, **Quelle** zurückgegeben

   - Wenn **Quelle** keinen Wert besitzt, verwendet **Regexmuster** und **Regexgruppenname** zum Extrahieren von Ersatzwert aus der Eigenschaft mit **Ersatzattributname**. Der Ersatzwert wird als Ergebnis zurückgegeben.


**Parameter:**<br> 

|Name| Erforderlich/wiederholt | Typ | Hinweise |
|--- | ---                 | ---  | ---   |
| **Quelle** | Erforderlich | String | Normalerweise der Name des Attributs aus dem Quellobjekt |
| **AlterWert** | Optional | String | Wert im ersetzt werden **Quelle** oder **Vorlage**. |
| **RegexMuster** | Optional | String | Regex-Muster für den Wert im ersetzt werden **Quelle**. Wenn "Ersatzeigenschaftsname" verwendet wird, das Muster, das zum Extrahieren des Werts aus der Ersatzeigenschaft verwendet wird. |
| **RegexGruppenname** | Optional | String | Name der Gruppe im **Regexmuster**. Nur, wenn "ersatzeigenschaftsname" verwendet wird, wird der Wert dieser Gruppe als Ersatzwert aus der Ersatzeigenschaft extrahiert. |
| **Ersatzwert** | Optional | String | Neuer Wert, durch den der alte Wert ersetzt wird. |
| **Ersatzattributname** | Optional | String | Name des Attributs, das für den Ersatzwert verwendet werden soll, wenn die Quelle keinen Wert besitzt. |
| **Vorlage** | Optional | String | Wenn **Vorlage** Wert bereitgestellt wird, wird **OldValue** in der Vorlage und Ersetzen Sie ihn mit dem Quellwert. |



----------
### StripSpaces

**Funktion:**<br> 
StripSpaces(Quelle)

**Beschreibung:**<br> 
Entfernt alle Leerzeichen ("") aus der Quellzeichenfolge.

**Parameter:**<br> 

|Name| Erforderlich/wiederholt | Typ | Hinweise |
|--- | ---                 | ---  | ---   |
| **Quelle** | Erforderlich | String | **Quelle** Wert aktualisieren. |



----------
### Switch

**Funktion:**<br> 
Switch (Quelle, DefaultValue, key1, Wert1, Schlüssel2, Wert2,...)

**Beschreibung:**<br> 
Wenn **Quelle** übereinstimmt eine **Schlüssel**, gibt **Wert**  **Schlüssel**. Wenn **Quelle** Wert stimmt nicht mit keinem Schlüssel, gibt **DefaultValue**.  **Schlüssel** und **Wert** Parameter müssen immer paarweise stammen. d.h. die Funktion erwartet immer eine gerade Anzahl von Parametern.

**Parameter:**<br> 

|Name| Erforderlich/wiederholt | Typ | Hinweise |
|--- | ---                 | ---  | ---   |
| **Quelle** | Erforderlich | String | **Quelle** Wert aktualisieren. |
| **defaultValue** | Optional | String | Der Standardwert, der verwendet werden soll, wenn die Quelle mit keinem Schlüssel übereinstimmt. Kann eine leere Zeichenfolge ("") sein. |
| **key** | Erforderlich | String | **Schlüssel** Vergleichen **Quelle** mit Wert. |
| **value** | Erforderlich | String | Ersatzwert für die **Quelle** mit dem Schlüssel übereinstimmt. |



## Beispiele

### Entfernen eines bekannten Domänennamens

Sie müssen einen bekannten Domänennamen aus der E-Mail-Adresse eines Benutzers entfernen, um einen Benutzernamen zu erhalten. <br>
Beispielsweise ist die Domäne "contoso.com", können klicken Sie dann den folgenden Ausdruck Sie:


**Ausdruck:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Beispieleingabe /-Ausgabe:** <br>

- **Eingabe** (e-Mail): "john.doe@contoso.com"

- **Ausgabe**: "Jonas.baar"


### Anfügen eines konstanten Suffixes an einen Benutzernamen

Wenn Sie eine Salesforce Sandbox verwenden, müssen Sie möglicherweise ein weiteres Suffix an alle Benutzernamen anfügen, bevor Sie diese synchronisieren.




**Ausdruck:** <br>
`Append([userPrincipalName], ".test"))`

**Beispieleingabe/-ausgabe:** <br>

- **Eingabe**: (UserPrincipalName): "John.Doe@contoso.com"


- **Ausgabe**: "John.Doe@contoso.com.test"





### Generieren eines Benutzeralias durch Verketten von Teilen des Vor- und Nachnamens

Sie müssen einen Benutzeralias generieren, indem Sie die ersten drei Buchstaben des Vornamens und die ersten fünf Buchstaben des Nachnamens des Benutzers verwenden.


**Ausdruck:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Beispieleingabe/-ausgabe:** <br>

- **Eingabe** (GivenName): "John"

- **Eingabe** (Surname): "Doe"

- **Ausgabe**: "JohDoe"




### Ausgabedatum eines Datums als Zeichenfolge in einem bestimmten Format

Sie möchten Datumsangaben in einem bestimmten Format an eine SaaS-Anwendung senden. <br>
Sie möchten z. B. Datumsangaben für ServiceNow formatieren.



**Ausdruck:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Beispieleingabe/-ausgabe:**

- **Eingabe** (extensionAttribute1): "20150123105347.1Z"

- **AUSGABE**: "2015-01-23"





### Ersetzen eines Werts anhand eines vordefinierten Satzes von Optionen

Sie müssen die Zeitzone des Benutzers anhand des Bundesstaatscodes festlegen, der in Azure AD gespeichert ist. <br>
Wenn der Statuscode mit einer der vordefinierten Optionen entspricht, verwenden Sie Standardwert "Australien/Sydney".


**Ausdruck:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Beispieleingabe/-ausgabe:**

- **Eingabe** (State): "QLD"

- **Ausgabe**: "Australien/Brisbane"


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]


