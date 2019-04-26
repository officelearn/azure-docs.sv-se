---
title: Azure AD Connect sync-tjänsten shadow attribut | Microsoft Docs
description: Beskriver hur shadow attribut fungerar i Azure AD Connect-synkroniseringstjänsten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 07/13/2017
ms.date: 04/09/2019
ms.subservice: hybrid
ms.author: v-junlch
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60384719"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect sync-tjänsten shadow attribut
De flesta attribut representeras på samma sätt i Azure AD som de är i din lokala Active Directory. Men vissa attribut har en särskild hantering och attributvärdet i Azure AD kan skilja sig från vad som synkroniserar Azure AD Connect.

## <a name="introducing-shadow-attributes"></a>Introduktion till shadow attribut
Vissa attribut har två representationer i Azure AD. Både den lokala-värde och ett beräknat värde lagras. Dessa extra attribut kallas shadow attribut. De två vanligaste attributen där du ser det här beteendet är **userPrincipalName** och **proxyAddress**. Ändringen av attributvärden sker när det finns värden i dessa attribut som representerar icke kontrollerade domäner. Men Synkroniseringsmotorn i Connect läser värdet i attributet shadow det från dess perspektiv, attributet har bekräftats av Azure AD.

Du kan inte se shadow attribut med hjälp av Azure portal eller med PowerShell. Men förstå begreppet hjälper dig att felsöka vissa scenarier där attributet har olika värden på lokalt och i molnet.

För att bättre förstå beteendet, titta på det här exemplet från Fabrikam:  
![Domäner](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
De har flera UPN-suffix i sin lokala Active Directory, men de bara har verifierat en.

### <a name="userprincipalname"></a>userPrincipalName
En användare har följande attributvärden i en icke-kontrollerad domän:

| Attribut | Värde |
| --- | --- |
| lokalt userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD-shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD userPrincipalName | lee.sperry@fabrikam.partner.onmschina.cn |

Attributet userPrincipalName är det värde som du ser när du använder PowerShell.

Eftersom det verkliga lokala attributvärdet lagras i Azure AD, när du har kontrollerat domänen fabrikam.com, uppdaterar Azure AD userPrincipalName-attribut med värdet från shadowUserPrincipalName. Du behöver inte synkronisera ändringar från Azure AD Connect för dessa värden som ska uppdateras.

### <a name="proxyaddresses"></a>proxyAddresses
Även utförs samma procedur för att bara inkludera verifierade domäner för proxyAddresses, men med vissa ytterligare logik. Sök efter verifierade domäner sker endast för användare postlådan. En e-postaktiverade användare eller kontakt representerar en användare i en annan Exchange-organisation och du kan lägga till alla värden i proxyAddresses till dessa objekt.

För en postlåda användare, antingen lokalt eller i Exchange Online visas endast värden för verifierade domäner. Det kan se ut så här:

| Attribut | Värde |
| --- | --- |
| lokala proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

I det här fallet **smtp:abbie.spencer\@fabrikam.com** har tagits bort eftersom domänen inte har verifierats. Men Exchange har även lagt till **SIP:abbie.spencer\@fabrikamonline.com**. Fabrikam har inte använts Lync/Skype på plats, men Azure AD och Exchange Online Förbered för den.

Den här logiken för proxyAddresses kallas **ProxyCalc**. ProxyCalc har anropats med varje ändring för en användare när:

- Användaren har tilldelats en serviceplan som innehåller Exchange Online, även om användaren inte har licensierats för Exchange. Exempel: om användaren har tilldelats Office E3 SKU: N, men endast har tilldelats SharePoint Online. Detta gäller även om postlådan är fortfarande på plats.
- Attributet msExchRecipientTypeDetails har ett värde.
- Du gör en ändring i proxyAddresses eller userPrincipalName.

ProxyCalc kan ta lite tid att bearbeta en ändring för en användare och är inte synkroniserad med Azure AD Connect-exporten.

> [!NOTE]
> Logiken som ProxyCalc har vissa ytterligare beteenden för avancerade scenarier som inte beskrivs i det här avsnittet. Det här avsnittet ges för dig att förstå beteendet och inte dokumentera alla internt.

### <a name="quarantined-attribute-values"></a>I karantän attributvärden
Shadow attribut används också när det finns dubblerade attributvärden. Mer information finns i [duplicerad attributåterhämtning](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Se också
* [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

<!-- Update_Description: wording update -->