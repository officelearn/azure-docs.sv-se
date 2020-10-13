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
ms.openlocfilehash: 4d64f7423d537958b6d3c388cb12f23bd2e30e36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087108"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Säker hybrid åtkomst: säkra äldre appar med Azure Active Directory

Nu kan du skydda dina lokala och molnbaserade program för äldre autentisering genom att ansluta dem till Azure Active Directory (AD) med:

- [Azure-AD-programproxy](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Dina befintliga program leverans styrenheter och nätverk](#sha-through-networking-and-delivery-controllers)

- [Program för virtuella privata nätverk (VPN) och Software-Defined perimeter (SDP)](#sha-through-vpn-and-sdp-applications)

Du kan överbrygga luckan och förstärka din säkerhets position över alla program med Azure AD-funktioner som Azure AD- [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) och Azure AD [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection).

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Säker hybrid åtkomst (SHA) via Azure AD-programproxy
  
Med [Application Proxy](https://aka.ms/whyappproxy) kan du ge [säker fjärråtkomst](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) till dina lokala webb program. Användarna behöver inte använda en VPN. Användare drar nytta av att enkelt ansluta till sina program från valfri enhet efter en [enkel inloggning](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso). Programproxyn tillhandahåller fjärråtkomst som en tjänst och gör att du [enkelt kan publicera dina lokala program](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) till användare utanför företags nätverket. Det hjälper dig att skala din hantering av moln åtkomst utan att du behöver ändra dina lokala program. [Planera en Azure AD-programproxy-distribution](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) som nästa steg.

## <a name="azure-ad-partner-integrations"></a>Integrering av Azure AD-partner

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA via nätverk och leverans kontroller

Förutom [Azure AD-programproxy](https://aka.ms/whyappproxy)så att du kan använda det här [ramverket utan förtroende](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), och Microsoft samarbetar med leverantörer från tredje part. Du kan använda dina befintliga nätverks-och leverans styrenheter och enkelt skydda äldre program som är viktiga för dina affärs processer, men som du inte kunde skydda tidigare med Azure AD. Det är troligt att du redan har allt du behöver för att börja skydda programmen.

![Bild som visar säker hybrid åtkomst med nätverks partners och App proxy](./media/secure-hybrid-access/secure-hybrid-access.png)

Följande nätverks leverantörer erbjuder färdiga lösningar och detaljerad vägledning för integrering med Azure AD.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix Application Delivery Controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Kemp:](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA via VPN och SDP-program

Med hjälp av VPN-och SDP-lösningar kan du när som helst tillhandahålla säker åtkomst till företagets nätverk från vilken enhet som helst, oavsett var de befinner sig och skyddar din organisations data. Genom att ha Azure AD som en identitetsprovider (IDP) kan du använda moderna autentiserings-och auktoriseringsregler som Azure AD [enkel inloggning](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) och [Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) för att skydda dina lokala äldre program.  

![Bild som visar säker hybrid åtkomst med VPN-partners och App proxy ](./media/secure-hybrid-access/app-proxy-vpn.png)

Följande VPN-och SDP-leverantörer erbjuder färdiga lösningar och detaljerad vägledning för integrering med Azure AD.

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)

• [Palo-nätverk global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
