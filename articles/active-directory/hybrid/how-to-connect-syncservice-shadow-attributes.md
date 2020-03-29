---
title: Skuggattribut för Azure AD Connect-synkroniseringstjänst | Microsoft-dokument
description: Beskriver hur skuggattribut fungerar i synkroniseringstjänsten Azure AD Connect.
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
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60384719"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Skugga attribut för Azure AD Connect-synkroniseringstjänst
De flesta attribut representeras på samma sätt i Azure AD som i din lokala Active Directory. Men vissa attribut har viss speciell hantering och attributvärdet i Azure AD kan skilja sig från vad Azure AD Connect synkroniserar.

## <a name="introducing-shadow-attributes"></a>Introduktion till skuggattribut
Vissa attribut har två representationer i Azure AD. Både det lokala värdet och ett beräknat värde lagras. Dessa extra attribut kallas skuggattribut. De två vanligaste attributen där du ser det här beteendet är **userPrincipalName** och **proxyAddress**. Ändringen av attributvärden sker när det finns värden i dessa attribut som representerar icke-verifierade domäner. Men synkroniseringsmotorn i Connect läser värdet i skuggattributet så ur sitt perspektiv har attributet bekräftats av Azure AD.

Du kan inte se skuggattributen med Azure-portalen eller med PowerShell. Men att förstå konceptet hjälper dig att felsöka vissa scenarier där attributet har olika värden lokalt och i molnet.

Om du vill bättre förstå beteendet kan du titta på det här exemplet från Fabrikam:  
![Domäner](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
De har flera UPN-suffix i sin lokala Active Directory, men de har bara verifierat en.

### <a name="userprincipalname"></a>userPrincipalName
En användare har följande attributvärden i en icke-verifierad domän:

| Attribut | Värde |
| --- | --- |
| lokalt användarePrincipalNamn | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD-användarePrincipalNamn | lee.sperry@fabrikam.onmicrosoft.com |

Attributet userPrincipalName är det värde som visas när du använder PowerShell.

Eftersom det verkliga lokala attributvärdet lagras i Azure AD, när du verifierar fabrikam.com-domänen, uppdaterar Azure AD attributet userPrincipalName med värdet från shadowUserPrincipalName. Du behöver inte synkronisera några ändringar från Azure AD Connect för att dessa värden ska uppdateras.

### <a name="proxyaddresses"></a>proxyAddresses
Samma process för att endast inkludera verifierade domäner förekommer också för proxyAddresses, men med ytterligare logik. Kontrollen efter verifierade domäner sker bara för postlådeanvändare. En e-postaktiverad användare eller kontakt representerar en användare i en annan Exchange-organisation och du kan lägga till alla värden i proxyAdresser till dessa objekt.

För en postlådeanvändare, antingen lokalt eller i Exchange Online, visas endast värden för verifierade domäner. Det kan se ut så här:

| Attribut | Värde |
| --- | --- |
| lokala proxyAdresser | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAdresser | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

I det här fallet **smtp:abbie.spencer\@fabrikam.com** togs bort eftersom domänen inte har verifierats. Men Exchange la också **SIP:\@abbie.spencer fabrikamonline.com**. Fabrikam har inte använt Lync/Skype lokalt, men Azure AD och Exchange Online förbereder sig för det.

Den här logiken för proxyAddresses kallas **ProxyCalc**. ProxyCalc anropas vid varje ändring på en användare när:

- Användaren har tilldelats en serviceplan som innehåller Exchange Online även om användaren inte har licensierats för Exchange. Om användaren till exempel har tilldelats Office E3 SKU, men bara tilldelats SharePoint Online. Detta gäller även om postlådan fortfarande är lokalt.
- Attributet msExchRecipientTypeDetails har ett värde.
- Du kan ändra proxyAdresser eller userPrincipalName.

ProxyCalc kan ta lite tid att bearbeta en ändring på en användare och är inte synkron med Azure AD Connect-exportprocessen.

> [!NOTE]
> ProxyCalc-logiken har några ytterligare beteenden för avancerade scenarier som inte dokumenteras i det här avsnittet. Det här avsnittet tillhandahålls för att du ska förstå beteendet och inte dokumentera all intern logik.

### <a name="quarantined-attribute-values"></a>Attributvärden i karantän
Skuggattribut används också när det finns dubblettattributvärden. Mer information finns i [dubblettattributets återhämtning](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Se även
* [Synkronisering av Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
