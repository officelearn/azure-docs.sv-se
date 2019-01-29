---
title: 'Azure AD Connect: Deklarativ etablering uttryck | Microsoft Docs'
description: Förklarar uttryck för deklarativ etablering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 0f15915d7c515387658ee132fcc1217958728c8a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55166755"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect-synkronisering: Förstå uttryck för deklarativ etablering
Azure AD Connect-synkronisering bygger på deklarativ etablering introducerades i Forefront Identity Manager 2010. Det kan du implementera enhetlig identitet integration affärslogik utan att behöva skriva kompilerad kod.

En väsentlig del av deklarativ etablering är Uttrycksspråk som används i attributflöden. Det språk som används är en delmängd av Microsoft® Visual Basic for Applications (VBA). Det här språket används i Microsoft Office och användarna har erfarenhet av VBScript kommer också identifiera den. Deklarativ etablering Uttrycksspråk endast med hjälp av funktioner och är inte ett strukturerade språk. Det finns inga metoder eller uttryck. Functions kapslas i stället att uttryckliga programflöde.

Mer information finns i [Välkommen till Visual Basic for Applications Språkreferens för Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Attribut är starkt typbestämd. En funktion accepterar endast attribut av typen korrekt. Det är också skiftlägeskänsligt. Både funktionsnamnen och attributnamn måste ha rätt skiftläge eller ett fel inträffar.

## <a name="language-definitions-and-identifiers"></a>Definitioner av språk och identifierare
* Funktioner har ett namn följt av argument inom parentes: FunctionName (argumentet 1, argumentet N).
* Attribut som identifierats av hakparenteser: [attributeName]
* Parametrar som identifieras av procenttecken: % ParameterName %
* Strängkonstanter omges av citattecken: Till exempel ”Contoso” (Obs: måste använda raka citattecken ”” och inte typografiska citattecken ””)
* Numeriska värden anges utan citattecken och förväntas vara decimal. Hexadecimala värden föregås & H. Till exempel 98052 & HFF
* Booleska värden uttrycks med konstanter: True, False.
* Inbyggda konstanter och litteraler uttrycks med endast användarens namn: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
Deklarativ etablering använder många funktioner för att aktivera möjlighet att omvandla attributvärden. Dessa funktioner kan kapslas så resultatet från en funktion som skickas till en annan funktion.

`Function1(Function2(Function3()))`

Fullständig lista över funktioner finns i den [fungera referens](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parametrar
En parameter har definierats av en koppling eller av en administratör med hjälp av PowerShell. Parametrar som vanligtvis innehåller värden som skiljer sig från ett system till, till exempel namnet på domänen som användaren befinner sig i. Dessa parametrar kan användas i attributflöden.

Active Directory-koppling avses inkommande Synkroniseringsregler följande parametrar:

| Parameternamn | Kommentar |
| --- | --- |
| Domain.Netbios |NetBIOS-formatet för den domän som för närvarande som importeras, till exempel FABRIKAMSALES |
| Domain.FQDN |FQDN-format på den domän som för närvarande som importeras, till exempel sales.fabrikam.com |
| Domain.LDAP |LDAP-formatet för den domän som för närvarande som importeras, till exempel DC = försäljning, DC = fabrikam, DC = com |
| Forest.Netbios |NetBIOS-formatet för det skognamn som för närvarande som importeras, till exempel FABRIKAMCORP |
| Forest.FQDN |FQDN-format för det skognamn som för närvarande som importeras, till exempel fabrikam.com |
| Forest.LDAP |LDAP-formatet för det skognamn som för närvarande som importeras, till exempel DC = fabrikam, DC = com |

Systemet innehåller följande parameter som används för att hämta identifieraren för den anslutningen som för närvarande körs:  
`Connector.ID`

Här är ett exempel som fyller på domänen för metaversum-attribut med netbios-namnet på den domän där användaren finns:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatorer
Du kan använda följande operatorer:

* **Jämförelse av**: <, < =, <>, =, >, > =
* **Matematik**: +, -, \*, -
* **Sträng**: & (sammanfoga)
* **Logiska**: & & (och) || (eller)
* **Utvärderingsordning**:)

Operatörer utvärderas till vänster till höger och har samma prioritet för utvärdering. Det vill säga den \* (multiplikatorn) utvärderas inte innan - (subtraktion). 2\*(5 + 3) är inte samma som 2\*5 + 3. Hakparenteser (-) används för att ändra utvärderingsordningen när vänster till höger utvärderingsordning inte är lämpligt.

## <a name="multi-valued-attributes"></a>Flera värden attribut
Funktionerna kan användas i både enstaka och flera värden. För flera värden attribut funktionen fungerar över varje värde och gäller samma funktion för varje värde.

Exempel:  
`Trim([proxyAddresses])` Göra en Trimning av varje värde i proxyAddress-attribut.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` För varje värde med en @-sign, Ersätt den med @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Leta efter SIP-adress och ta bort den från värden.

## <a name="next-steps"></a>Nästa steg
* Läs mer om konfigurationsmodellen i [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Se hur deklarativ etablering är att använda out-of-box i [förstå standardkonfigurationen](concept-azure-ad-connect-sync-default-configuration.md).
* Se hur du ändrar något praktiskt med deklarativ etablering i [hur du gör en ändring i standardkonfigurationen](how-to-connect-sync-change-the-configuration.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

**Referensämnen**

* [Azure AD Connect-synkronisering: Referens för funktioner](reference-connect-sync-functions-reference.md)

