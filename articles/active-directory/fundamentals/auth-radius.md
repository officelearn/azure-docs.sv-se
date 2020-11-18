---
title: RADIUS-autentisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå RADIUS-autentisering med Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ec8062eb864c877b0f3659ca2dd8f103e935071
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836792"
---
# <a name="radius-authentication-with-azure-active-directory"></a>RADIUS-autentisering med Azure Active Directory

Remote Authentication Dial-In User Service (RADIUS) är ett nätverks protokoll som skyddar ett nätverk genom att aktivera centraliserad autentisering och auktorisering av fjärranslutna användare. Många program använder fortfarande RADIUS-protokollet för att autentisera användare.

Microsoft Windows Server har en roll som kallas nätverks princip Server (NPS), som kan fungera som en RADIUS-server och stöd för RADIUS-autentisering.

Azure Active Directory (Azure AD) möjliggör Multi-Factor Authentication med RADIUS-baserade system. Om en kund vill tillämpa Azure AD Multi-Factor Authentication på någon av de tidigare nämnda RADIUS-arbetsbelastningarna kan de installera Azure AD Multi-Factor Authentication NPS-tillägget på sin Windows NPS-server. 

Windows NPS-servern autentiserar en användares autentiseringsuppgifter mot Active Directory och skickar sedan Multi-Factor Authentication begäran till Azure. Användaren får sedan en utmaning på sin mobila autentiserare. När åtgärden har slutförts tillåts klient programmet att ansluta till tjänsten. 

## <a name="use-when"></a>Använd när: 

Du måste lägga till Multi-Factor Authentication till program som
* ett virtuellt privat nätverk (VPN)
* WiFi-åtkomst
* Fjärrskrivbordsgateway (RDG)
* VDI (Virtual Desktop Infrastructure)
* Andra som är beroende av RADIUS-protokollet för att autentisera användare i tjänsten. 

> [!NOTE]
> I stället för att förlita dig på RADIUS och Azure AD Multi-Factor Authentication NPS-tillägget för att använda Azure AD Multi-Factor Authentication till VPN-arbetsbelastningar, rekommenderar vi att du uppgraderar VPN-till-SAML och direkt federering av VPN med Azure AD. Detta ger ditt VPN en hel bredd av Azure AD-skydd, inklusive villkorlig åtkomst, Multi-Factor Authentication, enhetskompatibilitet och identitets skydd.

![arkitektur diagram](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>Systemets komponenter 

* **Klient program (VPN-klient)**: skickar autentiseringsbegäran till RADIUS-klienten.

* **RADIUS-klient**: konverterar förfrågningar från klient program och skickar dem till RADIUS-servern som har NPS-tillägget installerat.

* **RADIUS-server**: ansluter med Active Directory för att utföra den primära autentiseringen för RADIUS-begäran. Vid lyckad överföring skickas begäran till Azure AD Multi-Factor Authentication NPS-tillägget.

* **NPS-tillägg**: utlöser en begäran till Azure AD Multi-Factor Authentication för en sekundär autentisering. Om det lyckas Slutför NPS-tillägget autentiseringsbegäran genom att tillhandahålla RADIUS-servern med säkerhetstoken som omfattar Multi-Factor Authentication-anspråk, utfärdat av Azures säkerhetstokentjänst.

* **Azure ad Multi-Factor Authentication**: kommunicerar med Azure AD för att hämta användarens information och utför en sekundär autentisering med hjälp av en verifieringsmetod som kon figurer ATS av användaren.

## <a name="implement-radius-with-azure-ad"></a>Implementera RADIUS med Azure AD 

* [Tillhandahåll Azure AD Multi-Factor Authentication-funktioner med NPS](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension) 

* [Konfigurera Azure AD Multi-Factor Authentication NPS-tillägget](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-advanced) 

* [VPN med Azure AD Multi-Factor Authentication att använda NPS-tillägget](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-vpn) 

  
‎ 

 
