---
title: Krävs för Hybrididentitet portar och protokoll – Azure | Microsoft Docs
description: Den här sidan är en teknisk referens för portar som krävs för att vara öppna för Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 38a344fb6e67beae6310480646d84a1fe7730f84
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461050"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Portar och protokoll som krävs för hybrididentitet
Följande dokument är en teknisk referens på de nödvändiga portar och protokoll för att implementera en hybrididentitetslösning. Använd följande bild och referera till motsvarande tabell.

![Vad är Azure AD Connect?](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabell 1 – Azure AD Connect och en lokal AD
Den här tabellen beskriver de portar och protokoll som krävs för kommunikation mellan Azure AD Connect-servern och lokala AD.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |DNS-sökning på målskogen. |
| Kerberos |88 (TCP/UDP) |Kerberos-autentisering till AD-skogen. |
| MS-RPC |135 (TCP/UDP) |Används under den initiala konfigurationen av Azure AD Connect-guiden när den Binder till AD-skogen och även under synkronisering av lösenord. |
| LDAP |389 (TCP/UDP) |Används för import av data från AD. Data krypteras med Kerberos logga & försegla. |
| RPC | 445 (TCP/UDP) |Används av sömlös enkel inloggning för att skapa ett datorkonto i AD-skogen. |
| LDAP/SSL |636 (TCP/UDP) |Används för import av data från AD. Dataöverföringen signeras och krypteras. Används endast om du använder SSL. |
| RPC |49152 – 65535 (slumpmässiga hög RPC Port)(TCP/UDP) |Används under den inledande konfigurationen av Azure AD Connect när det har bindningar till AD-skogar och under synkronisering av lösenord. Se [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017), och [KB224196](https://support.microsoft.com/kb/224196) för mer information. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabell 2 – Azure AD Connect och Azure AD
Den här tabellen beskriver de portar och protokoll som krävs för kommunikation mellan Azure AD Connect-servern och Azure AD.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Används för att hämta listor över återkallade certifikat (listor över återkallade certifikat) att verifiera SSL-certifikat. |
| HTTPS |443(TCP/UDP) |Används för att synkronisera med Azure AD. |

En lista över URL: er och IP-adresser som du behöver öppna i brandväggen, se [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabell 3 – Azure AD Connect och AD FS federationsservrar/WAP
Den här tabellen beskriver de portar och protokoll som krävs för kommunikation mellan Azure AD Connect-server och AD FS-federationsservrar/WAP-servrar.  

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Används för att hämta listor över återkallade certifikat (listor över återkallade certifikat) att verifiera SSL-certifikat. |
| HTTPS |443(TCP/UDP) |Används för att synkronisera med Azure AD. |
| WinRM |5985 |WinRM-lyssnare |

## <a name="table-4---wap-and-federation-servers"></a>Tabell 4 - WAP och federationsservrar
Den här tabellen beskriver de portar och protokoll som krävs för kommunikation mellan federationsservrar och WAP-servrarna.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Används för autentisering. |

## <a name="table-5---wap-and-users"></a>Tabell 5 - WAP och användare
Den här tabellen beskriver de portar och protokoll som krävs för kommunikation mellan användare och WAP-servrarna.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Används för autentisering. |
| TCP |49443 (TCP) |Används för autentisering med datorcertifikat. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabellen 6a & 6b - direktautentisering med enkel inloggning (SSO) och Lösenordshashsynkronisering med enkel inloggning (SSO)
Följande tabeller beskriver de portar och protokoll som krävs för kommunikation mellan Azure AD Connect och Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabellen 6a - direktautentisering med enkel inloggning
|Protokoll|Portnummer|Beskrivning
| --- | --- | ---
|HTTP|80|Aktivera utgående HTTP-trafik för Säkerhetsvalidering, till exempel SSL. Dessutom behövs för funktionen för automatisk uppdatering anslutningen ska fungera korrekt.
|HTTPS|443| Aktivera utgående HTTPS-trafik för åtgärder som aktiverar och inaktiverar funktionen, registrera kopplingar, hämtar uppdateringar för anslutningen och begäranden som alla användare logga in.

Dessutom kan Azure AD Connect behöver för att kunna ansluta direkt IP till den [Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabellen 6b - Lösenordshashsynkronisering med enkel inloggning

|Protokoll|Portnummer|Beskrivning
| --- | --- | ---
|HTTPS|443| Aktivera SSO-registrering (krävs endast för registreringsprocessen för enkel inloggning).

Dessutom kan Azure AD Connect behöver för att kunna ansluta direkt IP till den [Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Igen, detta är endast krävs för SSO-registreringen.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabell 7 a & 7b – Azure AD Connect Health agent för (AD FS/Sync) och Azure AD
I följande tabeller beskrivs de slutpunkter, portar och protokoll som krävs för kommunikation mellan Azure AD Connect Health-agenterna och Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabellen 7a - portar och protokoll för Azure AD Connect Health agent för (AD FS/Sync) och Azure AD
Den här tabellen beskriver följande utgående portar och protokoll som krävs för kommunikation mellan Azure AD Connect Health-agenterna och Azure AD.  

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Utgående |
| Azure Service Bus |5671 (TCP/UDP) |Utgående |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - slutpunkter för Azure AD Connect Health agent för (AD FS/Sync) och Azure AD
En lista över slutpunkter finns i [avsnittet krav för Azure AD Connect Health-agenten](how-to-connect-health-agent-install.md#requirements).

