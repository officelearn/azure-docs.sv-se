---
title: Azure AD Secure hybrid-åtkomst | Microsoft Docs
description: I den här artikeln beskrivs partner lösningar för att integrera ditt äldre lokala, offentliga moln eller privata moln program med Azure AD. Skydda dina äldre appar genom att ansluta program leverans styrenheter eller nätverk till Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a936552bd7d3b97e2c027f7b0d23abd7c21b6e2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94652203"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Säker hybrid åtkomst: säkra äldre appar med Azure Active Directory

Nu kan du skydda dina lokala och molnbaserade program för äldre autentisering genom att ansluta dem till Azure Active Directory (AD) med:

- [Azure AD Programproxy](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Dina befintliga program leverans styrenheter och nätverk](#sha-through-networking-and-delivery-controllers)

- [Program för virtuella privata nätverk (VPN) och Software-Defined perimeter (SDP)](#sha-through-vpn-and-sdp-applications)

Du kan överbrygga luckan och förstärka din säkerhets position över alla program med Azure AD-funktioner som Azure AD- [villkorlig åtkomst](../conditional-access/overview.md) och Azure AD [Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Säker hybrid åtkomst (SHA) via Azure AD-programproxy
  
Med [Application Proxy](./what-is-application-proxy.md) kan du ge [säker fjärråtkomst](./application-proxy.md) till dina lokala webb program. Användarna behöver inte använda en VPN. Användare drar nytta av att enkelt ansluta till sina program från valfri enhet efter en [enkel inloggning](./add-application-portal-setup-sso.md). Programproxyn tillhandahåller fjärråtkomst som en tjänst och gör att du [enkelt kan publicera dina lokala program](./application-proxy-add-on-premises-application.md) till användare utanför företags nätverket. Det hjälper dig att skala din hantering av moln åtkomst utan att du behöver ändra dina lokala program. [Planera en Azure AD-programproxy-distribution](./application-proxy-deployment-plan.md) som nästa steg.

## <a name="azure-ad-partner-integrations"></a>Integrering av Azure AD-partner

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA via nätverk och leverans kontroller

Förutom [Azure AD-programproxy](./what-is-application-proxy.md)så att du kan använda det här [ramverket utan förtroende](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), och Microsoft samarbetar med leverantörer från tredje part. Du kan använda dina befintliga nätverks-och leverans styrenheter och enkelt skydda äldre program som är viktiga för dina affärs processer, men som du inte kunde skydda tidigare med Azure AD. Det är troligt att du redan har allt du behöver för att börja skydda programmen.

![Bild som visar säker hybrid åtkomst med nätverks partners och App proxy](./media/secure-hybrid-access/secure-hybrid-access.png)

Följande nätverks leverantörer erbjuder färdiga lösningar och detaljerad vägledning för integrering med Azure AD.

- [Akamai Enterprise Application Access (EAA)](../saas-apps/akamai-tutorial.md)

- [Citrix Application Delivery Controller (ADC)](../saas-apps/citrix-netscaler-tutorial.md)

- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)

- [Kemp:](../saas-apps/kemp-tutorial.md)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA via VPN och SDP-program

Med hjälp av VPN-och SDP-lösningar kan du när som helst tillhandahålla säker åtkomst till företagets nätverk från vilken enhet som helst, oavsett var de befinner sig och skyddar din organisations data. Genom att ha Azure AD som en identitetsprovider (IDP) kan du använda moderna autentiserings-och auktoriseringsregler som Azure AD [enkel inloggning](./what-is-single-sign-on.md) och [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) för att skydda dina lokala äldre program.  

![Bild som visar säker hybrid åtkomst med VPN-partners och App proxy ](./media/secure-hybrid-access/app-proxy-vpn.png)

Följande VPN-och SDP-leverantörer erbjuder färdiga lösningar och detaljerad vägledning för integrering med Azure AD.

• [Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)

• [Fortinet](../saas-apps/fortigate-ssl-vpn-tutorial.md)

• [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)

• [Palo-nätverk global Protect](../saas-apps/paloaltoadmin-tutorial.md)

• [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)