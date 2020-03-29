---
title: 'Azure AD Connect: Deklarativa etableringsuttryck | Microsoft-dokument'
description: Förklarar deklarativa etableringsuttrycken.
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
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc7c9dba49bf37db1f039d43b0450c65884c74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245509"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Synkronisering av Azure AD Connect: Förstå deklarativa etableringsuttryck
Azure AD Connect-synkronisering bygger på deklarativ etablering som först introducerades i Forefront Identity Manager 2010. Det gör att du kan implementera din fullständiga identitetsintegrering affärslogik utan att behöva skriva kompilerad kod.

En viktig del av deklarativa etablering är uttrycksspråket som används i attributflöden. Språket som används är en delmängd av Microsoft® Visual Basic® for Applications (VBA). Det här språket används i Microsoft Office och användare med erfarenhet av VBScript kommer också att känna igen det. Uttrycksspråket Deklarativ etablering använder bara funktioner och är inte ett strukturerat språk. Det finns inga metoder eller uttalanden. Funktioner kapslas i stället för att uttrycka programflödet.

Mer information finns i [Välkommen till språkreferensen för Visual Basic for Applications för Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Attributen skrivs starkt. En funktion accepterar bara attribut av rätt typ. Det är också skiftlägeskänsligt. Både funktionsnamn och attributnamn måste ha rätt hölje eller ett fel genereras.

## <a name="language-definitions-and-identifiers"></a>Språkdefinitioner och identifierare
* Funktioner har ett namn följt av argument inom parentes: FunctionName(argument 1, argument N).
* Attribut identifieras med hakparenteser: [attributeName]
* Parametrar identifieras med procenttecken: %ParameterName%
* Strängkonstanter omges av citattecken: Till exempel "Contoso" (Obs: måste använda raka citattecken "" och inte smarta citattecken "")
* Numeriska värden uttrycks utan citattecken och förväntas vara decimaler. Hexadecimala värden föregås av &H. Till exempel 98052, &HFF
* Booleska värden uttrycks med konstanter: Sant, Falskt.
* Inbyggda konstanter och litteraler uttrycks med endast namn: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funktioner
Deklarativ etablering använder många funktioner för att möjliggöra möjligheten att omvandla attributvärden. Dessa funktioner kan kapslas så att resultatet från en funktion skickas in till en annan funktion.

`Function1(Function2(Function3()))`

Den fullständiga listan över funktioner finns i [funktionsreferensen](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parametrar
En parameter definieras antingen av en connector eller av en administratör som använder PowerShell. Parametrar innehåller vanligtvis värden som skiljer sig från system till system, till exempel namnet på den domän som användaren finns i. Dessa parametrar kan användas i attributflöden.

Active Directory Connector innehöll följande parametrar för inkommande synkroniseringsregler:

| Parameternamn | Kommentar |
| --- | --- |
| Domän.Netbios |Netbios-format för den domän som för närvarande importeras, till exempel FABRIKAMSALES |
| Domän.FQDN |FQDN-format för den domän som för närvarande importeras, till exempel sales.fabrikam.com |
| Domän.LDAP |LDAP-format för den domän som för närvarande importeras, till exempel DC=sales,DC=fabrikam,DC=com |
| Skog.Netbios |Netbios-format för det skogsnamn som för närvarande importeras, till exempel FABRIKAMCORP |
| Skog.FQDN |FQDN-format för det skogsnamn som för närvarande importeras, till exempel fabrikam.com |
| Skog.LDAP |LDAP-format för det skogsnamn som för närvarande importeras, till exempel DC=fabrikam,DC=com |

Systemet tillhandahåller följande parameter, som används för att hämta identifieraren för den anslutning som körs:  
`Connector.ID`

Här är ett exempel som fyller metaversumattributdomänen med netbios-namnet på den domän där användaren finns:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatorer
Följande operatorer kan användas:

* **Jämförelse**: <, <=, <>, =, >, >=
* **Matematik**: +, \*-, , -
* **Sträng**: & (sammanfoga)
* **Logisk**: && (och), || (eller)
* **Utvärderingsorder**: ( )

Operatorer utvärderas från vänster till höger och har samma utvärderingsprioritet. Det vill \* än ,(multiplikatorn) utvärderas inte före - (subtraktion). 2\*(5+3) är inte samma\*sak som 2 5+3. Parenteserna ( ) används för att ändra utvärderingsordningen när det inte är lämpligt att ändra utvärderingsordningen från vänster till höger.

## <a name="multi-valued-attributes"></a>Attribut med flera värden
Funktionerna kan fungera på både attribut med ett värde och flera värden. För attribut med flera värden fungerar funktionen över varje värde och använder samma funktion på varje värde.

Ett exempel:  
`Trim([proxyAddresses])`Gör en trimning av varje värde i attributet proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`För varje värde @-signmed en @contoso.comersätter du domänen med .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Leta efter SIP-adressen och ta bort den från värdena.

## <a name="next-steps"></a>Nästa steg
* Läs mer om konfigurationsmodellen i [Förstå deklarativa etablering .](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Se hur deklarativ etablering används direkt i [Förstå standardkonfigurationen](concept-azure-ad-connect-sync-default-configuration.md).
* Se hur du gör en praktisk ändring med deklarativ etablering i [Så här gör du en ändring av standardkonfigurationen](how-to-connect-sync-change-the-configuration.md).

**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

**Referensämnen**

* [Synkronisering av Azure AD Connect: Funktionsreferens](reference-connect-sync-functions-reference.md)

