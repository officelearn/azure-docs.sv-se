---
title: Azure MFA Server och VP-nätverk från tredje part – Azure Active Directory
description: Steg-för-steg-konfigurationsguider för Azure MFA Server för att integrera med Cisco, Citrix och Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01decb99a9eb24ae60250f83f1f961b4c1690bc0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652852"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Avancerade scenarier med Azure MFA Server och VPN-lösningar från tredje part

Azure Multi-Factor Authentication Server (Azure MFA Server) kan användas för att sömlöst ansluta till olika VPN-lösningar från tredje part. Den här artikeln&reg; fokuserar på Cisco ASA VPN-apparat, Citrix NetScaler SSL VPN-apparat och Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN-apparat. Vi skapade konfigurationsguider för att ta itu med dessa tre vanliga apparater. Azure MFA Server kan också integreras med de flesta andra system som använder RADIUS, LDAP, IIS eller anspråksbaserad autentisering till AD FS. Du hittar mer information i [Azure MFA Server-konfigurationer](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare bör använda molnbaserad Azure Multi-Factor-autentisering. Befintliga kunder som har aktiverat MFA Server före den 1 juli kommer att kunna ladda ner den senaste versionen, framtida uppdateringar och generera aktiveringsautentiseringsuppgifter som vanligt.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN-apparat och Azure MFA Server
Azure MFA Server integreras&reg; med din Cisco ASA VPN-apparat för att ge ytterligare säkerhet för Cisco AnyConnect&reg; VPN-inloggningar och portalåtkomst.  Du kan använda antingen LDAP- eller RADIUS-protokollet.  Välj något av följande om du vill hämta de detaljerade steg-för-steg-konfigurationsguiderna.

| Konfigurationsguide | Beskrivning |
| --- | --- |
| [Cisco ASA med Anyconnect VPN och Azure MFA-konfiguration för LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrera din Cisco ASA VPN-apparat med Azure MFA med LDAP |
| [Cisco ASA med Anyconnect VPN och Azure MFA-konfiguration för RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrera din Cisco ASA VPN-apparat med Azure MFA med RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN och Azure MFA Server
Azure MFA Server integreras med din Citrix NetScaler SSL VPN-apparat för att ge ytterligare säkerhet för Citrix NetScaler SSL VPN-inloggningar och portalåtkomst.  Du kan använda antingen LDAP- eller RADIUS-protokollet.  Välj något av följande om du vill hämta de detaljerade steg-för-steg-konfigurationsguiderna.

| Konfigurationsguide | Beskrivning |
| --- | --- |
| [Citrix NetScaler SSL VPN och Azure MFA-konfiguration för LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrera din Citrix NetScaler SSL VPN med Azure MFA-apparat med LDAP |
| [Citrix NetScaler SSL VPN och Azure MFA-konfiguration för RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrera din Citrix NetScaler SSL VPN-apparat med Azure MFA med RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL VPN-apparat och Azure MFA Server
Azure MFA Server integreras med din Europer/Pulse Secure SSL VPN-apparat för att ge ytterligare säkerhet för Europer/Pulse Secure SSL VPN-inloggningar och portalåtkomst.  Du kan använda antingen LDAP- eller RADIUS-protokollet.  Välj något av följande om du vill hämta de detaljerade steg-för-steg-konfigurationsguiderna.

| Konfigurationsguide | Beskrivning |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN och Azure MFA-konfiguration för LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrera din Europer/Pulse Secure SSL VPN med Azure MFA-apparat med LDAP |
| [Juniper/Pulse Secure SSL VPN och Azure MFA-konfiguration för RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrera din Europer/Pulse Secure SSL SSL VPN-apparat med Azure MFA med RADIUS |

## <a name="next-steps"></a>Nästa steg

- [Utöka din befintliga autentiseringsinfrastruktur med NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Konfigurera inställningar för Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
