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
ms.date: 11/02/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ce6b889d23959e9d04b95ec2bc5847340b596448
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915844"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect och federation
Azure Active Directory (Azure AD) Connect kan du konfigurera federation med en lokal Active Directory Federation Services (AD FS) och Azure AD. Med federation inloggning, kan du ge användare att logga in på Azure AD-baserade tjänster med sina lokala lösenord – och på företagets nätverk, utan att behöva ange sina lösenord igen. Du kan distribuera en ny installation av AD FS med hjälp av alternativet federation med AD FS, eller du kan ange en befintlig installation i en Windows Server 2012 R2-servergrupp.

Det här avsnittet är hittar du information om federation-relaterade funktioner för Azure AD Connect. Den visar länkar till alla närliggande ämnen. Länkar till Azure AD Connect finns i [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: federation ämnen
| Avsnitt | Den beskriver hur och när du ska läsa den |
|:--- |:--- |
| **Azure AD Connect-inloggning användaralternativ** | |
| [Förstå inloggningsalternativ för användare](active-directory-aadconnect-user-signin.md) |Läs mer om olika användare logga in alternativ och hur de påverkar Azure-inloggning användarupplevelsen. |
| **Installera AD FS med hjälp av Azure AD Connect** | |
| [Förutsättningar](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Se kraven för en lyckad AD FS-installation via Azure AD Connect. |
| [Konfigurera AD FS-servergrupp](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Installera en ny AD FS-servergrupp med hjälp av Azure AD Connect. |
| [Federera med Azure AD med hjälp av alternativa inloggnings-ID ](active-directory-aadconnect-federation-management.md#alternateid) | Konfigurera federation med hjälp av alternativa inloggnings-ID  |
| **Ändra AD FS-konfigurationen** | |
| [Reparera förtroendet](active-directory-aadconnect-federation-management.md#repairthetrust) |Reparera det aktuella förtroendet mellan lokala AD FS och Office 365/Azure. |
| [Lägg till en ny AD FS-server](active-directory-aadconnect-federation-management.md#addadfsserver) |Expandera en AD FS-servergrupp med en ytterligare AD FS-servern efter den första installationen. |
| [Lägg till en ny AD FS-WAP-server](active-directory-aadconnect-federation-management.md#addwapserver) |Expandera en AD FS-servergrupp med en ytterligare Web Application Proxy (WAP)-server efter den första installationen. |
| [Lägg till en ny federerad domän](active-directory-aadconnect-federation-management.md#addfeddomain) |Lägg till en annan domän som ska federeras med Azure AD. |
| [Uppdatera SSL-certifikatet](active-directory-aadconnectfed-ssl-update.md)| Uppdatera SSL-certifikatet för AD FS-servergrupp. |
| [Förnya federationscertifikat för Office 365 och Azure AD](active-directory-aadconnect-o365-certs.md)|Förnya ditt O365-certifikat med Azure AD.|
| **Annan federationskonfiguration** | |
| [Federera flera instanser av Azure AD med en enda instans av AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Federera flera Azure AD med en enda AD FS-servergrupp| 
| [Lägg till en anpassad logotyp/bild](active-directory-aadconnect-federation-management.md#customlogo) |Ändra inloggningen genom att ange anpassade logotypen som visas på inloggningssidan för AD FS. |
| [Lägg till en inloggning beskrivning](active-directory-aadconnect-federation-management.md#addsignindescription) |Ändra inloggning beskrivning på inloggningssidan för AD FS. |
| [Ändra anspråksregler för AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Ändra eller lägga till anspråksregler i AD FS som relaterar till Azure AD Connect sync-konfigurationen. |


## <a name="additional-resources"></a>Ytterligare resurser
* [Federera två Azure AD med en enda AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [AD FS-distribution i Azure](active-directory-aadconnect-azure-adfs.md)
* [Hög tillgänglighet över geografiska områden AD FS-distribution i Azure med Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
