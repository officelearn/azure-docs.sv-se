---
title: Azure AD Connect sync shadow attribut | Microsoft Docs
description: "Beskriver hur shadow attribut fungerar i Azure AD Connect-synkroniseringstjänsten."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 0b6a7f22d744480a40a878c979986cdd7667109c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect sync shadow attribut
De flesta attribut representeras på samma sätt i Azure AD som i din lokala Active Directory. Men vissa attribut har en särskild hantering och attributvärdet i Azure AD kan vara annorlunda än Azure AD Connect synkroniserar.

## <a name="introducing-shadow-attributes"></a>Introduktion till shadow attribut
Vissa attribut har två representationer i Azure AD. Lagras både lokalt värde och ett beräknat värde. Dessa extra attribut kallas shadow attribut. De två vanligaste attributen där du ser det här beteendet är **userPrincipalName** och **proxyAddress**. Ändringen i attributvärden händer när det finns värden i attributen som motsvarar icke verifierade domäner. Men Synkroniseringsmotorn i Connect läser värdet i attributet shadow så dess ur, attributet har bekräftats av Azure AD.

Du kan inte se shadow-attribut med hjälp av Azure portal eller PowerShell. Men förstå begreppet hjälper dig att felsöka vissa scenarier där attributet har olika värden på lokalt och i molnet.

För att bättre förstå beteendet kan du titta på det här exemplet från Fabrikam:  
![Domäner](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
De har flera UPN-suffix i sina lokala Active Directory, men de bara har verifierat en.

### <a name="userprincipalname"></a>UserPrincipalName
En användare har följande attributvärden i en icke-verifierad domän:

| Attribut | Värde |
| --- | --- |
| lokala userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD-shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD-userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

Attributet userPrincipalName är det värde som visas när du använder PowerShell.

Eftersom det verkliga lokal attributvärdet lagras i Azure AD, när du verifiera domänen fabrikam.com uppdaterar attributet userPrincipalName med värdet från shadowUserPrincipalName Azure AD. Du behöver inte synkronisera ändringarna från Azure AD Connect för dessa värden uppdateras.

### <a name="proxyaddresses"></a>proxyAddresses
Samma process för att bara inkludera verifierade domäner inträffar också för proxyAddresses men med vissa ytterligare logik. Sök efter verifierade domäner sker endast för postlådan användare. En e-postaktiverade användare eller kontakta representerar en användare i en annan Exchange-organisation och du kan lägga till alla värden i proxyAddresses dessa objekt.

För en postlåda användare, antingen lokalt eller i Exchange Online visas bara värden för verifierade domäner. Det kan se ut så här:

| Attribut | Värde |
| --- | --- |
| lokala proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

I det här fallet  **smtp:abbie.spencer@fabrikam.com**  har tagits bort eftersom den domänen inte har verifierats. Men har även lagt till Exchange  **SIP:abbie.spencer@fabrikamonline.com** . Fabrikam inte har använt Lync/Skype lokal, men Azure AD och Exchange Online förbereda för den.

Den här logiken för proxyAddresses kallas **ProxyCalc**. ProxyCalc anropas med varje ändring för en användare när:

- Användaren har tilldelats en serviceplan som innehåller Exchange Online, även om användaren inte har licensierats för Exchange. Till exempel om användaren har tilldelats Office E3 SKU: N, men endast har tilldelats SharePoint Online. Detta gäller även om postlådan är fortfarande lokalt.
- Attributet msExchRecipientTypeDetails har ett värde.
- Du gör en ändring i proxyAddresses eller userPrincipalName.

ProxyCalc kan ta lite tid att bearbeta en ändring för en användare och är inte synkroniserad med Azure AD Connect exporten.

> [!NOTE]
> ProxyCalc logiken har vissa ytterligare beteenden för avancerade scenarier som inte beskrivs i det här avsnittet. Det här avsnittet har angetts för dig att förstå beteendet och inte dokumentera alla internt.

### <a name="quarantined-attribute-values"></a>I karantän attributvärden
Shadow-attribut används också när det finns dubblerade attributvärden. Mer information finns i [dubblettattribut återhämtning](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Se även
* [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
