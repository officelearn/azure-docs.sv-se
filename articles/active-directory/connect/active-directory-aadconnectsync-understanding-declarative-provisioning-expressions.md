---
title: 'Azure AD Connect: Uttryck för deklarativ etablering | Microsoft Docs'
description: Beskrivs uttryck för deklarativ etablering.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 797c0949aceea415652a72df5ee23ef9888ab975
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect-synkronisering: Förstå uttryck för deklarativ etablering
Azure AD Connect-synkronisering bygger på deklarativ etablering introducerades i Forefront Identity Manager 2010. På så sätt kan du implementera affärslogik fullständig identity integration utan att behöva skriva koden.

En väsentlig del av deklarativ etablering är språket för uttryck som används i attributflöden. Språket är en delmängd av Microsoft® Visual Basic for Applications (VBA). Det här språket används i Microsoft Office och användarna har erfarenhet av VBScript tolkar även. Deklarativ etablering Uttrycksspråk endast med hjälp av funktioner och är inte ett strukturerade språk. Det finns inga metoder eller instruktioner. Funktioner kapslas i stället för snabb programflöde.

Mer information finns i [Välkommen till Visual Basic for Applications Språkreferens för Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Attribut har strikt typkontroll. En funktion accepterar endast attribut av typen korrekt. Det är också skiftlägeskänsligt. Både funktionsnamnen och attributnamn måste ha rätt skiftläge eller ett felmeddelande.

## <a name="language-definitions-and-identifiers"></a>Språk definitioner och identifierare
* Funktioner har ett namn följt av argument inom parentes: FunctionName (argumentet 1, argumentet N).
* Attribut som identifieras av hakparenteser: [attributeName]
* Parametrar som identifieras av procenttecken: % ParameterName %
* Strängkonstanter omges av citattecken: till exempel ”Contoso” (Obs: måste använda enkla citattecken ”” och inte typografiska citattecken ””)
* Numeriska värden anges utan citattecken och förväntas vara decimal. Hexadecimala värden föregås av & H. Till exempel 98052 & HFF
* Booleska värden uttrycks med konstanter: SANT, FALSKT.
* Inbyggda konstanter och literaler uttrycks med endast deras namn: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funktioner
Deklarativ etablering använder många funktioner för att aktivera möjligheten att omvandla attributvärden. Dessa funktioner kan kapslas så resultatet från en funktion som skickas till en annan funktion.

`Function1(Function2(Function3()))`

En fullständig lista över funktioner finns i den [fungerar referens](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Parametrar
En parameter har definierats av en koppling eller av en administratör med hjälp av PowerShell. Parametrar som vanligtvis innehåller värden som skiljer sig från ett system till, till exempel namnet på domänen användaren finns i. Dessa parametrar kan användas i attributflöden.

Active Directory-koppling avses regler för inkommande synkronisering följande parametrar:

| Parameternamn | Kommentar |
| --- | --- |
| Domain.Netbios |NetBIOS-format för den domän som för närvarande importeras, till exempel FABRIKAMSALES |
| Domain.FQDN |FQDN-format i domänen som importerats, till exempel sales.fabrikam.com |
| Domain.LDAP |LDAP-format för den domän som för närvarande importeras, till exempel DC = försäljning, DC = fabrikam, DC = com |
| Forest.Netbios |Skogens namn för närvarande importeras, till exempel FABRIKAMCORP NetBIOS-format |
| Forest.FQDN |Skogens namn för närvarande importeras, till exempel fabrikam.com FQDN-format |
| Forest.LDAP |LDAP-format för skogsnamnet som för närvarande importeras, till exempel DC = fabrikam, DC = com |

Systemet innehåller följande parameter som används för att hämta ID för koppling som för närvarande körs:  
`Connector.ID`

Här är ett exempel som fyller på domänen metaversum-attribut med netbios-namnet på den domän där användaren:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatorer
Du kan använda följande operatorer:

* **Jämförelse**: <, < =, <>, =, >, > =
* **Matematik**: +, -, \*, -
* **Strängen**: & (sammanfoga)
* **Logisk**: & & (och). (eller)
* **Utvärderingsordningen**:)

Operatorer utvärderas vänster till höger och har samma prioritet för utvärdering. Det vill säga den \* (multiplikator) utvärderas inte innan - (subtraktion). 2\*(5 + 3) inte är samma som 2\*5 + 3. Hakparenteser () används för att ändra utvärderingsordningen när vänster till höger utvärderingsordning inte är lämpligt.

## <a name="multi-valued-attributes"></a>Flera värden attribut
Funktionerna fungerar med både enstaka och flera värden. Funktionen fungerar över varje värde för flera värden attribut och gäller samma funktion för varje värde.

Exempel:  
`Trim([proxyAddresses])` Göra en Trimning av varje värde i attributet proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` För varje värde med en @-sign, Ersätt den med @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Leta efter SIP-adress och ta bort den från värden.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Konfigurationsmodell i [förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Se hur deklarativ etablering är att använda out-of-box i [förstå standardkonfigurationen](active-directory-aadconnectsync-understanding-default-configuration.md).
* Se hur du ändrar något praktiska med deklarativ etablering i [hur du gör en ändring i standardkonfigurationen](active-directory-aadconnectsync-change-the-configuration.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)

**Referensinformation**

* [Azure AD Connect-synkronisering: funktioner referens](active-directory-aadconnectsync-functions-reference.md)

