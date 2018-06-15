---
title: Avancerade scenarier med Azure MFA och tredje parts VPN-anslutningar
description: Steg för steg-konfiguration guider för Azure MFA att integrera med Cisco, Citrix och Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 725f2893419b2b921f262fc4516f0f8a2ecf4c37
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869299"
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Avancerade scenarier med Azure Multi-Factor Authentication och VPN-lösningar

Azure Multi-Factor Authentication kan användas för att ansluta sömlöst med olika VPN-lösningar från tredje part. Den här artikeln fokuserar på Cisco® ASA VPN-enhet, Citrix NetScaler SSL VPN-enhet och Juniper nätverk säker åtkomst/Pulse Secure ansluta säkra SSL VPN-enhet. Vi har skapat configuration guider för att åtgärda dessa tre vanliga installationer. Multi-Factor Authentication-servern kan även integreras med de flesta andra system som använder RADIUS, LDAP, IIS eller Anspråksbaserad autentisering med AD FS. Du hittar mer information finns i [MFA serverkonfigurationer](howto-mfaserver-deploy.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN-enhet och Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integreras med Cisco® ASA VPN-enhet för att ge extra säkerhet för Cisco AnyConnect® VPN-inloggningar och portalåtkomst.  Du kan använda antingen RADIUS eller LDAP-protokollet.  Välj något av följande för att hämta guider för detaljerade steg för steg-konfigurationen.

| Konfigurationsguiden | Beskrivning |
| --- | --- |
| [Cisco ASA med Anyconnect VPN och Azure MFA-konfiguration för LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrera din Cisco ASA VPN-enhet med Azure MFA med LDAP |
| [Cisco ASA med Anyconnect VPN och Azure MFA-konfiguration för RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrera din Cisco ASA VPN-enhet med Azure MFA med RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN- och Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integreras med Citrix NetScaler SSL VPN-enhet för att ge extra säkerhet för Citrix NetScaler SSL VPN-inloggningar och portalåtkomst.  Du kan använda antingen RADIUS eller LDAP-protokollet.  Välj något av följande för att hämta guider för detaljerade steg för steg-konfigurationen.

| Konfigurationsguiden | Beskrivning |
| --- | --- |
| [Citrix NetScaler SSL VPN- och Azure MFA-konfiguration för LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrera din Citrix NetScaler SSL VPN med Azure MFA-enhet med LDAP |
| [Citrix NetScaler SSL VPN- och Azure MFA-konfigurationen för RADIUS-](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrera din Citrix NetScaler SSL VPN-enhet med Azure MFA med RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN-enhet och Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integreras med Juniper/Pulse Secure SSL VPN-enhet för att ge extra säkerhet för Juniper/Pulse Secure SSL VPN-inloggningar och portalåtkomst.  Du kan använda antingen RADIUS eller LDAP-protokollet.  Välj något av följande för att hämta guider för detaljerade steg för steg-konfigurationen.

| Konfigurationsguiden | Beskrivning |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN- och Azure MFA-konfiguration för LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrera din Juniper/Pulse Secure SSL VPN med Azure MFA-enhet med LDAP |
| [Juniper/Pulse Secure SSL VPN- och Azure MFA-konfigurationen för RADIUS-](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrera din Juniper/Pulse Secure SSL VPN-enhet med Azure MFA med RADIUS |

## <a name="next-steps"></a>Nästa steg

- [Utöka din befintliga infrastruktur för autentisering med NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Konfigurera inställningar för Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)