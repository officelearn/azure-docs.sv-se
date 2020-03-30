---
title: Azure AD Connect och federation | Microsoft-dokument
description: Den här sidan är den centrala platsen för all dokumentation om AD FS-åtgärder som använder Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0518c58abf156c718ee083ffadb0ef8e0a590252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331529"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect och federation
Med Azure Active Directory (Azure AD) Connect kan du konfigurera federation med lokala AD FS (Active Directory Federation Services) och Azure AD. Med federations inloggning kan du göra det möjligt för användare att logga in på Azure AD-baserade tjänster med sina lokala lösenord – och, när de är i företagets nätverk, utan att behöva ange sina lösenord igen. Genom att använda federationsalternativet med AD FS kan du distribuera en ny installation av AD FS eller ange en befintlig installation i en Windows Server 2012 R2-servergrupp.

Det här avsnittet är hem för information om federationsrelaterade funktioner för Azure AD Connect. Den listar länkar till alla relaterade ämnen. Länkar till Azure AD Connect finns i [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: federationsavsnitt
| Hjälpavsnitt | Vad den täcker och när den ska läsas |
|:--- |:--- |
| **Azure AD Connect user sign-in options** (Alternativ för användarinloggning i Azure AD Connect) | |
| [Förstå alternativ för användarloggning](plan-connect-user-signin.md) |Lär dig mer om olika alternativ för användarloggning och hur de påverkar azure-inloggningsanvändarupplevelsen. |
| **Installera AD FS med Hjälp av Azure AD Connect** | |
| [Krav](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Se förutsättningarna för en lyckad AD FS-installation via Azure AD Connect. |
| [Konfigurera en AD FS-servergrupp](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Installera en ny AD FS-servergrupp med hjälp av Azure AD Connect. |
| [Federera med Azure AD med alternativt inloggnings-ID](how-to-connect-fed-management.md#alternateid) | Konfigurera federation med hjälp av alternativt inloggnings-ID  |
| **Ändra AD FS-konfigurationen** | |
| [Reparera förtroendet](how-to-connect-fed-management.md#repairthetrust) |Reparera det aktuella förtroendet mellan lokala AD FS och Office 365/Azure. |
| [Lägga till en ny AD FS-server](how-to-connect-fed-management.md#addadfsserver) |Expandera en AD FS-server med ytterligare en AD FS-server efter den första installationen. |
| [Lägga till en ny AD FS WAP-server](how-to-connect-fed-management.md#addwapserver) |Expandera en AD FS-server med ytterligare en WAP-server (Web Application Proxy) efter den första installationen. |
| [Lägga till en ny federerad domän](how-to-connect-fed-management.md#addfeddomain) |Lägg till en annan domän som ska federeras med Azure AD. |
| [Uppdatera TLS/SSL-certifikatet](how-to-connect-fed-ssl-update.md)| Uppdatera TLS/SSL-certifikatet för en AD FS-servergrupp. |
| [Förnya federationscertifikat för Office 365 och Azure AD](how-to-connect-fed-o365-certs.md)|Förnya ditt O365-certifikat med Azure AD.|
| **Annan federationskonfiguration** | |
| [Federera flera instanser av Azure AD med en enda instans av AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Federera flera Azure AD med en enda AD FS-servergrupp| 
| [Lägga till en anpassad företagslogotyp/illustration](how-to-connect-fed-management.md#customlogo) |Ändra inloggningsupplevelsen genom att ange den anpassade logotypen som visas på AD FS-inloggningssidan. |
| [Lägga till en inloggningsbeskrivning](how-to-connect-fed-management.md#addsignindescription) |Ändra inloggningsbeskrivningen på AD FS-inloggningssidan. |
| [Ändra AD FS-anspråksregler](how-to-connect-fed-management.md#modclaims) |Ändra eller lägg till anspråksregler i AD FS som motsvarar Azure AD Connect-synkroniseringskonfiguration. |


## <a name="additional-resources"></a>Ytterligare resurser
* [Federerar två Azure AD med en enda AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [AD FS-distribution i Azure](how-to-connect-fed-azure-adfs.md)
* [Korsgeorgeiskt AD FS-distribution med hög tillgänglighet i Azure med Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
