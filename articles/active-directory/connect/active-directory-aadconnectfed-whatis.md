---
title: Azure AD Connect och federation | Microsoft Docs
description: "Den här sidan är den centrala platsen för all dokumentation om AD FS-åtgärder som använder Azure AD Connect."
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: anandy
ms.openlocfilehash: 6822320c92d106d28607289a90f2f08a51e04070
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect och federation
Azure Active Directory (AD Azure) Connect, kan du konfigurera federation med lokala Active Directory Federation Services (AD FS) och Azure AD. Med federation inloggning, kan du aktivera användare att logga in på Azure AD-baserade tjänster med sina lokala lösenord – och på företagsnätverket, utan att behöva ange sina lösenord igen. Du kan distribuera en ny installation av AD FS med hjälp av alternativet federation med AD FS eller ange en befintlig installation i en Windows Server 2012 R2-servergrupp.

Det här avsnittet är hem för information om federation-relaterade funktioner för Azure AD Connect. Visas en lista med länkar till alla närliggande ämnen. Länkar till Azure AD Connect finns [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: federation avsnitt
| Avsnitt | Det täcker och när du ska läsa den |
|:--- |:--- |
| **Azure AD Connect användaren inloggningsalternativ** | |
| [Förstå användaren inloggningsalternativ](active-directory-aadconnect-user-signin.md) |Lär dig mer om olika användare inloggningsalternativ och hur de påverkar Azure inloggning användarupplevelsen. |
| **Installera AD FS med hjälp av Azure AD Connect** | |
| [Förutsättningar](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Se krav för en AD FS-installationen ska lyckas via Azure AD Connect. |
| [Konfigurera en AD FS-servergrupp](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Installera en ny AD FS-servergrupp med hjälp av Azure AD Connect. |
| [Federera med Azure AD med hjälp av alternativa inloggnings-ID](active-directory-aadconnect-federation-management.md#alternateid) | Konfigurera federation med hjälp av alternativa inloggnings-ID  |
| **Ändra AD FS-konfigurationen** | |
| [Reparera förtroendet](active-directory-aadconnect-federation-management.md#repairthetrust) |Reparera det aktuella förtroendet mellan lokala AD FS och Office 365-/ Azure. |
| [Lägg till en ny AD FS-server](active-directory-aadconnect-federation-management.md#addadfsserver) |Expandera en AD FS-servergrupp med en ytterligare AD FS-servern efter den första installationen. |
| [Lägg till en ny AD FS WAP-server](active-directory-aadconnect-federation-management.md#addwapserver) |Expandera en AD FS-servergrupp med ytterligare en Webbprogramproxy (WAP) server efter den första installationen. |
| [Lägg till en ny extern domän](active-directory-aadconnect-federation-management.md#addfeddomain) |Lägg till en annan domän att bli federerad med Azure AD. |
| [Uppdatera SSL-certifikatet](active-directory-aadconnectfed-ssl-update.md)| Uppdatera SSL-certifikatet för en AD FS-servergrupp. |
| **Andra federation-konfiguration** | |
| [Federera flera instanser av Azure AD med en enda instans av AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Federera flera Azure AD med enda AD FS-servergrupp| 
| [Lägg till en anpassad företagets logotyp/bild](active-directory-aadconnect-federation-management.md#customlogo) |Ändra inloggningen genom att ange anpassad logotyp som visas på inloggningssidan för AD FS. |
| [Lägg till en beskrivning för inloggning](active-directory-aadconnect-federation-management.md#addsignindescription) |Ändra en beskrivning på inloggningssidan för AD FS. |
| [Ändra anspråksregler i AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Ändra eller lägga till anspråksregler i AD FS som motsvarar i Azure AD Connect sync konfiguration. |


## <a name="additional-resources"></a>Ytterligare resurser
* [Federering två Azure AD med enda AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [AD FS-distribution i Azure](active-directory-aadconnect-azure-adfs.md)
* [Hög tillgänglighet geografiska mellan AD FS-distribution i Azure med Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
