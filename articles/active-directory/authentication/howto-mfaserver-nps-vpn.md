---
title: Avancerade scenarier med Azure MFA och virtuella privata nätverk från tredje part
description: Stegvisa konfigurationsguider för Azure MFA att integrera med Cisco, Citrix och Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 75eb522e4576929ec2898df35a3af97d54db5d7b
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162039"
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Avancerade scenarier med Azure Multi-Factor Authentication och tredje parts VPN-lösningar

Azure Multi-Factor Authentication kan användas för att smidigt ansluta till olika tredje parts VPN-lösningar. Den här artikeln handlar om Cisco® ASA VPN-installationen, Citrix NetScaler SSL VPN-installationen och Juniper nätverk säker åtkomst/Pulse Secure ansluta säkra SSL VPN-installationen. Vi har skapat guider för enhetskonfiguration för att åtgärda dessa tre vanliga installationer. Multi-Factor Authentication Server kan också integreras med de flesta andra system som använder RADIUS, LDAP, IIS eller Anspråksbaserad autentisering till AD FS. Du hittar mer information finns i [MFA Server-konfigurationer](howto-mfaserver-deploy.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN-enhet och Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integreras med din Cisco® ASA VPN-installation för att ge ytterligare säkerhet för Cisco AnyConnect® VPN inloggningar och portalåtkomst.  Du kan använda antingen RADIUS eller LDAP-protokollet.  Välj något av följande för att ladda ned de detaljerade stegvisa konfigurationsguider.

| Konfigurationsguide | Beskrivning |
| --- | --- |
| [Cisco ASA med Anyconnect VPN- och Azure MFA-konfiguration för LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrera din Cisco ASA VPN-installation med Azure MFA med LDAP |
| [Cisco ASA med Anyconnect VPN- och Azure MFA-konfigurationen för RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrera din Cisco ASA VPN-installation med Azure MFA med RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN- och Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integreras med din Citrix NetScaler SSL VPN-installation för att ge ytterligare säkerhet för Citrix NetScaler SSL VPN-inloggningar och portalåtkomst.  Du kan använda antingen RADIUS eller LDAP-protokollet.  Välj något av följande för att ladda ned de detaljerade stegvisa konfigurationsguider.

| Konfigurationsguide | Beskrivning |
| --- | --- |
| [Citrix NetScaler SSL VPN- och Azure MFA-konfigurationen för LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrera din Citrix NetScaler SSL VPN med Azure MFA-installationen genom att använda LDAP |
| [Citrix NetScaler SSL VPN- och Azure MFA-konfigurationen för RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrera din Citrix NetScaler SSL VPN-installation med Azure MFA med RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN-enhet och Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integreras med din Juniper/Pulse Secure SSL VPN-installation för att ge ytterligare säkerhet för Juniper/Pulse Secure SSL VPN-inloggningar och portalåtkomst.  Du kan använda antingen RADIUS eller LDAP-protokollet.  Välj något av följande för att ladda ned de detaljerade stegvisa konfigurationsguider.

| Konfigurationsguide | Beskrivning |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN- och Azure MFA-konfigurationen för LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrera din Juniper/Pulse Secure SSL VPN med Azure MFA-installationen genom att använda LDAP |
| [Juniper/Pulse Secure SSL VPN- och Azure MFA-konfigurationen för RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrera din Juniper/Pulse Secure SSL VPN-installation med Azure MFA med RADIUS |

## <a name="next-steps"></a>Nästa steg

- [Utöka din befintliga infrastruktur för autentisering med NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Konfigurera inställningar för Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)