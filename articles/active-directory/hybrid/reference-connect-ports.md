---
title: Hybrididentitet krävs portar och protokoll – Azure | Microsoft-dokument
description: Den här sidan är en teknisk referenssida för portar som måste vara öppna för Azure AD Connect
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
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da318840426d1c0b94eab06b89ff3152df9d26fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331098"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Portar och protokoll som krävs för hybrididentitet
Följande dokument är en teknisk referens på de portar och protokoll som krävs för att implementera en hybrididentitetslösning. Använd följande bild och se motsvarande tabell.

![Vad är Azure AD Connect?](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabell 1 - Azure AD Connect och lokalt AD
I den här tabellen beskrivs de portar och protokoll som krävs för kommunikation mellan Azure AD Connect-servern och lokala AD.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |DNS-sökning på målskogen. |
| Kerberos |88 (TCP/UDP) |Kerberos-autentisering till AD-skogen. |
| MS-RPC |135 (TCP) |Används under den första konfigurationen av Azure AD Connect-guiden när den binder till AD-skogen och även under lösenordssynkronisering. |
| LDAP |389 (TCP/UDP) |Används för dataimport från AD. Data krypteras med Kerberos Sign & Seal. |
| SMB | 445 (TCP) |Används av Seamless SSO för att skapa ett datorkonto i AD-skogen. |
| LDAP/SSL |636 (TCP/UDP) |Används för dataimport från AD. Dataöverföringen är signerad och krypterad. Används endast om du använder TLS. |
| RPC |49152- 65535 (slumpmässig hög RPC-port)(TCP) |Används under den första konfigurationen av Azure AD Connect när den binder till AD-skogarna och under lösenordssynkronisering. Mer information finns i [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)och [KB224196.](https://support.microsoft.com/kb/224196) |
|WinRM  | 5985 (TCP) |Används endast om du installerar AD FS med gMSA by Azure AD Connect Wizard|
|AD DS-webbtjänster | 9389 (TCP) |Används endast om du installerar AD FS med gMSA by Azure AD Connect Wizard |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabell 2 - Azure AD Connect och Azure AD
I den här tabellen beskrivs de portar och protokoll som krävs för kommunikation mellan Azure AD Connect-servern och Azure AD.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTP |80 (TCP) |Används för att hämta CRL:er (listor över återkallade certifikat) för att verifiera TLS/SSL-certifikat. |
| HTTPS |443(TCP) |Används för att synkronisera med Azure AD. |

En lista över webbadresser och IP-adresser som du behöver öppna i brandväggen finns i [Url:er och IP-adressintervall för Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) och [Felsökning av Azure AD Connect-anslutning](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabell 3 - Azure AD Connect- och AD FS-federationsservrar/WAP
I den här tabellen beskrivs de portar och protokoll som krävs för kommunikation mellan Azure AD Connect-servern och AD FS-federationen/WAP-servrarna.  

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTP |80 (TCP) |Används för att hämta CRL:er (listor över återkallade certifikat) för att verifiera TLS/SSL-certifikat. |
| HTTPS |443(TCP) |Används för att synkronisera med Azure AD. |
| WinRM |5985 |WinRM-lyssnare |

## <a name="table-4---wap-and-federation-servers"></a>Tabell 4 - WAP- och federationsservrar
I den här tabellen beskrivs de portar och protokoll som krävs för kommunikation mellan federationsservrarna och WAP-servrarna.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTPS |443(TCP) |Används för autentisering. |

## <a name="table-5---wap-and-users"></a>Tabell 5 - WAP och användare
I den här tabellen beskrivs de portar och protokoll som krävs för kommunikation mellan användare och WAP-servrar.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTPS |443(TCP) |Används för enhetsautentisering. |
| TCP |49443 (TCP) |Används för certifikatautentisering. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabell 6a & 6b - Direktautentisering med SSO (Single Sign On) och Hash-synkronisering för lösenord med enkel inloggning (SSO)
I följande tabeller beskrivs de portar och protokoll som krävs för kommunikation mellan Azure AD Connect och Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabell 6a - Direktautentisering med SSO
|Protokoll|Portnummer|Beskrivning
| --- | --- | ---
|HTTP|80|Aktivera utgående HTTP-trafik för säkerhetsvalidering, till exempel SSL. Behövs också för att funktionen för automatisk uppdatering av kontakten ska fungera korrekt.
|HTTPS|443| Aktivera utgående HTTPS-trafik för åtgärder som att aktivera och inaktivera funktionen, registrera kopplingar, hämta anslutningsuppdateringar och hantera alla begäranden om användarloggning.

Dessutom måste Azure AD Connect kunna göra direkta IP-anslutningar till [Azure-datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabell 6b - Synkronisering av lösenord hash med SSO

|Protokoll|Portnummer|Beskrivning
| --- | --- | ---
|HTTPS|443| Aktivera SSO-registrering (krävs endast för SSO-registreringsprocessen).

Dessutom måste Azure AD Connect kunna göra direkta IP-anslutningar till [Azure-datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Återigen är detta endast krävs för SSO registreringsprocessen.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabell 7a & 7b – Azure AD Connect Health agent för (AD FS/Sync) och Azure AD
I följande tabeller beskrivs slutpunkter, portar och protokoll som krävs för kommunikation mellan Azure AD Connect Health-agenter och Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabell 7a - Portar och protokoll för Azure AD Connect Health agent för (AD FS/Sync) och Azure AD
I den här tabellen beskrivs följande utgående portar och protokoll som krävs för kommunikation mellan Azure AD Connect Health-agenter och Azure AD.  

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTPS |443(TCP) |Utgående |
| Azure Service Bus |5671 (TCP) |Utgående |

Azure Service Bus port 5671 krävs inte längre för den senaste versionen av agenten. Den senaste Azure AD Connect Health-agentversionen krävde endast port 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - Slutpunkter för Azure AD Connect Health agent för (AD FS/Sync) och Azure AD
En lista över slutpunkter finns [i avsnittet Krav för Azure AD Connect Health-agenten](how-to-connect-health-agent-install.md#requirements).

