---
title: Åtkomst till Nyckelvalv bakom en brandvägg – Azure Key Vault | Microsoft Docs
description: Läs om hur man kommer åt Azure Key Vault från ett program bakom en brandvägg
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: 50d21774-2ee1-4212-8995-570c9de603c5
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 36b431d07ad517d495316963fd6710dafab6c297
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108644"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Få åtkomst till Azure Key Vault bakom en brandvägg

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>Vilka portar, värdar eller IP-adresser ska jag öppna om du vill aktivera mitt klientprogram bakom en brandvägg att få åtkomst till nyckelvalvet?

För att få åtkomst till ett nyckelvalv måste ditt klientprogram ha åtkomst till flera slutpunkter för olika funktioner:

* Autentisering via Azure Active Directory (Azure AD).
* Hantering av Azure Key Vault. Detta omfattar att skapa, läsa, uppdatera, ta bort och ange åtkomstprinciper via Azure Resource Manager.
* Åtkomst till och hantering av objekt (nycklar och hemligheter) som lagras i själva nyckelvalvet går via den specifika slutpunkten för nyckelvalv (t.ex. [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Det finns vissa varianter beroende på din konfiguration och miljö.

## <a name="ports"></a>Portar

All trafik till nyckelvalvet för alla tre funktioner (autentisering, hantering och dataplanåtkomst) går över HTTPS: port 443. Det är dock ibland HTTP (port 80)-trafik för CRL. Klienter som har stöd för OCSP ska inte nå CRL, men kan ibland nå [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Authentication

Nyckelvalv-klientprogram behöver åtkomst till Azure Active Directory-slutpunkter för autentisering. Den slutpunkt som används beror på Azure AD-klientkonfiguration, typ av huvudkonto (användarens eller tjänstens huvudnamn) och typ av konto, t.ex. ett Microsoft-konto eller ett arbets- eller skolkonto.  

| Typ av huvudkonto | Slutpunkt:port |
| --- | --- |
| Användare som använder Microsoft-konto<br> (till exempel user@hotmail.com) |**Globalt:**<br> login.microsoftonline.com:443<br><br> **Azure i Kina:**<br> login.chinacloudapi.cn:443<br><br>**Azure för amerikanska myndigheter:**<br> login.microsoftonline.us:443<br><br>**Azure i Tyskland:**<br> login.microsoftonline.de:443<br><br> och <br>login.live.com:443 |
| Användarens eller tjänstens huvudkonto med ett arbets- eller skolkonto med Azure AD (t.ex. user@contoso.com) |**Globalt:**<br> login.microsoftonline.com:443<br><br> **Azure i Kina:**<br> login.chinacloudapi.cn:443<br><br>**Azure för amerikanska myndigheter:**<br> login.microsoftonline.us:443<br><br>**Azure i Tyskland:**<br> login.microsoftonline.de:443 |
| Användarens eller tjänstens huvudkonto med ett arbets- eller skolkonto, plus Active Directory Federation Services (AD FS) eller annan federerad slutpunkt (t.ex. user@contoso.com) |Alla slutpunkter för ett arbets- eller skolkonto, plus AD FS eller andra federerade slutpunkter |

Det finns andra möjliga avancerade scenarier. Se [Azure Active Directory Authentication Flow](../active-directory/develop/authentication-scenarios.md), [Integrera program med Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md) och [Active Directory-autentiseringsprotokoll](https://msdn.microsoft.com/library/azure/dn151124.aspx) för mer information.  

## <a name="key-vault-management"></a>Hantering av Nyckelvalv

För hantering av Nyckelvalv (CRUD och inställning av åtkomstprincip) måste klientprogrammet för nyckelvalv ha åtkomst till Azure Resource Manager-slutpunkten.  

| Typ av åtgärd | Slutpunkt:port |
| --- | --- |
| Kontrollplanåtgärder för Nyckelvalv<br> via Azure Resource Manager |**Globalt:**<br> management.azure.com:443<br><br> **Azure i Kina:**<br> management.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> management.microsoftazure.de:443 |
| Azure Active Directory Graph API |**Globalt:**<br> graph.windows.net:443<br><br> **Azure i Kina:**<br> graph.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> graph.windows.net:443<br><br> **Azure i Tyskland:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Åtgärder i Nyckelvalv

Vid all hantering och kryptografiska åtgärder för nyckelvalvsobjekt (nycklar och hemligheter) måste klientprogrammet för nyckelvalv ha åtkomst till slutpunkten för nyckelvalvet. Slutpunktens DNS-suffix är olika beroende på placeringen av ditt nyckelvalv. Slutpunkten för Nyckelvalv har följande format: *valvnamn*.*regionsspecifikt dns-suffix* som beskrivs i följande tabell.  

| Typ av åtgärd | Slutpunkt:port |
| --- | --- |
| Åtgärder inklusive kryptografiska åtgärder på nycklar, skapa, läsa, uppdatera och ta bort nycklar och hemligheter, ställa in eller få taggar och andra attribut för nyckelvalvobjekt (nycklar eller hemligheter) |**Globalt:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure i Kina:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP-adressintervall

Key Vault-tjänsten använder andra Azure-resurser, som PaaS-infrastruktur. Därför är det inte möjligt att ange ett specifikt IP-adressintervall som Key Vault-tjänstens slutpunkter har vid given tidpunkt. Om din brandvägg endast har stöd för IP-adressintervall finns det mer information i dokumentet [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Autentisering och identiteter (Azure Active Directory) är en global tjänst och växlas över till andra regioner eller flytta trafik utan föregående meddelande. I det här scenariot alla IP-adressintervall som anges i [ IP-adresser för autentisering och identitet](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip) läggas till i brandväggen.

## <a name="next-steps"></a>Nästa steg

Om du har några frågor om Key Vault ska du gå till [Azure Key Vault-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
