---
title: Åtkomst till Nyckelvalv bakom brandvägg | Microsoft Docs
description: Lär dig hur du får åtkomst till Nyckelvalv från ett program som finns bakom en brandvägg
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/13/2016
ms.author: ambapat

---
# Åtkomst till Nyckelvalv bakom brandvägg
### F: Mitt klientprogram för nyckelvalv måste ligga bakom en brandvägg, vilka portar/värdar/IP-adresser ska jag öppna för att ge åtkomst till nyckelvalvet?
För att få åtkomst till ett nyckelvalv måste ditt klientprogram kunna få åtkomst till flera slutpunkter för olika funktioner.

* Autentisering (via Azure Active Directory)
* Hantering av Nyckelvalv (vilket inkluderar skapa/läsa/updatera/radera och även inställning av åtkomstprinciper) via Azure Resource Manager
* Åtkomst till och hantering av objekt (nycklar och hemligheter) som lagras i själva nyckelvalvet går via den specifika slutpunkten för nyckelvalv (t.ex. [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Det finns vissa varianter beroende på din konfiguration och miljö.   

## Portar
All trafik till nyckelvalvet för alla 3 funktioner (autentisering, hantering och dataplanåtkomst) går över HTTPS: Port 443. För CRL är det dock ibland HTTP (port 80)-trafik. Klienter som har stöd för OCSP ska inte nå CRL, men kan ibland nå [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## Autentisering
Nyckelvalv-klientprogrammet behöver åtkomst till Azure Active Directory-slutpunkter för autentisering. Den slutpunkt som används beror på AAD-klientkonfiguration och typ av huvudkonto - användarens huvudnamn, tjänstens huvudnamn och typ av konto, t.ex. Microsoft-konto eller organisations-ID.  

| Typ av huvudkonto | Slutpunkt:port |
| --- | --- |
| Användare som använder Microsoft-konto<br> (t.ex. användare@hotmail.com) |**Globalt:**<br> login.microsoftonline.com:443<br><br> **Azure i Kina:**<br> login.chinacloudapi.cn:443<br><br>**Azure för amerikanska myndigheter:**<br> login-us.microsoftonline.com:443<br><br>**Azure i Tyskland:**<br> login.microsoftonline.de:443<br><br> och <br>login.live.com:443 |
| Användarens/tjänstens huvudnamn med organisations-ID med AAD (t.ex. användare@contoso.com) |**Globalt:**<br> login.microsoftonline.com:443<br><br> **Azure i Kina:**<br> login.chinacloudapi.cn:443<br><br>**Azure för amerikanska myndigheter:**<br> login-us.microsoftonline.com:443<br><br>**Azure i Tyskland:**<br> login.microsoftonline.de:443 |
| Användarens/tjänstens huvudnamn med organisations-ID + ADFS eller annan extern slutpunkt (t.ex. användare@contoso.com) |Alla ovanstående slutpunkter för organisations-ID plus ADFS eller andra externa slutpunkter |

Det finns andra möjliga avancerade scenarier. Se [Azure Active Directory Authentication Flow](/documentation/articles/active-directory-authentication-scenarios/), [Integrera program med Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) och [Active Directory-autentiseringsprotokoll](https://msdn.microsoft.com/library/azure/dn151124.aspx) för mer information.  

## Hantering av Nyckelvalv
För hantering av Nyckelvalv (CRUD och inställning av åtkomstprincip) måste klientprogrammet för nyckelvalv ha åtkomst till Azure Resource Manager-slutpunkten.  

| Typ av åtgärd | Slutpunkt:port |
| --- | --- |
| Kontrollplanåtgärder för Nyckelvalv<br> via Azure Resource Manager |**Globalt:**<br> management.azure.com:443<br><br> **Azure i Kina:**<br> management.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> management.microsoftazure.de:443 |
| Azure Active Directory Graph API |**Globalt:**<br> graph.windows.net:443<br><br> **Azure i Kina:**<br> graph.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> graph.windows.net:443<br><br> **Azure i Tyskland:**<br> graph.cloudapi.de:443 |

## Åtgärder i Nyckelvalv
Vid all hantering och kryptografiska åtgärder för nyckelvalvsobjekt (nycklar och hemligheter) måste klientprogrammet för nyckelvalv ha åtkomst till slutpunkten för nyckelvalvet. Beroende på placeringen av ditt Nyckelvalv är slutpunktens DNS-suffix olika. Slutpunkten för Nyckelvalv har följande format: <valvnamnet>. <regionsspecifikt-dns-suffix> som beskrivs i tabellen nedan.  

| Typ av åtgärd | Slutpunkt:port |
| --- | --- |
| Åtgärder för Nyckelvalv som kryptografiska åtgärder för nycklar, skapa/läs/uppdatera/radera nycklar och hemligheter, ange/hämta taggar och andra attribut för nyckelvalvsobjekt (nycklar/hemligheter) |**Globalt:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure i Kina:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## IP-adressintervall
Nyckelvalvtjänsten använder i sin tur andra Azure-resurser som PaaS-infrastruktur, därför är det inte möjligt att ange ett specifikt omfång för IP-adresser som nyckelvalvets slutpunkter har vid en given tidpunkt. Om din brandvägg emellertid endast har stöd för IP-adressintervall finns det mer information i dokumentet [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653).   För autentisering och identitet (Azure Active Directory) måste ditt program kunna ansluta till de slutpunkter som beskrivs i [Autentisering och identitetsadresser](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## Nästa steg
* Om du har några frågor om Nyckelvalv ska du gå till [Azure Key Vault-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!--HONumber=Sep16_HO3-->


