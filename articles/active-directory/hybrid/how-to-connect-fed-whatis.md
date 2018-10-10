---
title: Azure AD Connect och federation | Microsoft Docs
description: Den här sidan är den centrala platsen för all dokumentation om AD FS-åtgärder som använder Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 214dd95bb277053794656e1ba3dd148c085688ce
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900452"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect och federation
Azure Active Directory (Azure AD) Connect kan du konfigurera federation med en lokal Active Directory Federation Services (AD FS) och Azure AD. Med federation inloggning, kan du ge användare att logga in på Azure AD-baserade tjänster med sina lokala lösenord – och på företagets nätverk, utan att behöva ange sina lösenord igen. Du kan distribuera en ny installation av AD FS med hjälp av alternativet federation med AD FS, eller du kan ange en befintlig installation i en Windows Server 2012 R2-servergrupp.

Det här avsnittet är hittar du information om federation-relaterade funktioner för Azure AD Connect. Den visar länkar till alla närliggande ämnen. Länkar till Azure AD Connect finns i [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: federation ämnen
| Avsnitt | Den beskriver hur och när du ska läsa den |
|:--- |:--- |
| **Azure AD Connect-inloggning användaralternativ** | |
| [Förstå inloggningsalternativ för användare](plan-connect-user-signin.md) |Läs mer om olika användare logga in alternativ och hur de påverkar Azure-inloggning användarupplevelsen. |
| **Installera AD FS med hjälp av Azure AD Connect** | |
| [Förutsättningar](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Se kraven för en lyckad AD FS-installation via Azure AD Connect. |
| [Konfigurera AD FS-servergrupp](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Installera en ny AD FS-servergrupp med hjälp av Azure AD Connect. |
| [Federera med Azure AD med hjälp av alternativa inloggnings-ID ](how-to-connect-fed-management.md#alternateid) | Konfigurera federation med hjälp av alternativa inloggnings-ID  |
| **Ändra AD FS-konfigurationen** | |
| [Reparera förtroendet](how-to-connect-fed-management.md#repairthetrust) |Reparera det aktuella förtroendet mellan lokala AD FS och Office 365/Azure. |
| [Lägg till en ny AD FS-server](how-to-connect-fed-management.md#addadfsserver) |Expandera en AD FS-servergrupp med en ytterligare AD FS-servern efter den första installationen. |
| [Lägg till en ny AD FS-WAP-server](how-to-connect-fed-management.md#addwapserver) |Expandera en AD FS-servergrupp med en ytterligare Web Application Proxy (WAP)-server efter den första installationen. |
| [Lägg till en ny federerad domän](how-to-connect-fed-management.md#addfeddomain) |Lägg till en annan domän som ska federeras med Azure AD. |
| [Uppdatera SSL-certifikatet](how-to-connect-fed-ssl-update.md)| Uppdatera SSL-certifikatet för AD FS-servergrupp. |
| [Förnya federationscertifikat för Office 365 och Azure AD](how-to-connect-fed-o365-certs.md)|Förnya ditt O365-certifikat med Azure AD.|
| **Annan federationskonfiguration** | |
| [Federera flera instanser av Azure AD med en enda instans av AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Federera flera Azure AD med en enda AD FS-servergrupp| 
| [Lägg till en anpassad logotyp/bild](how-to-connect-fed-management.md#customlogo) |Ändra inloggningen genom att ange anpassade logotypen som visas på inloggningssidan för AD FS. |
| [Lägg till en inloggning beskrivning](how-to-connect-fed-management.md#addsignindescription) |Ändra inloggning beskrivning på inloggningssidan för AD FS. |
| [Ändra anspråksregler för AD FS](how-to-connect-fed-management.md#modclaims) |Ändra eller lägga till anspråksregler i AD FS som relaterar till Azure AD Connect sync-konfigurationen. |


## <a name="additional-resources"></a>Ytterligare resurser
* [Federera två Azure AD med en enda AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [AD FS-distribution i Azure](how-to-connect-fed-azure-adfs.md)
* [Hög tillgänglighet över geografiska områden AD FS-distribution i Azure med Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
