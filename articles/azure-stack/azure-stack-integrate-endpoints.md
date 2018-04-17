---
title: Azure stacken datacenter integrering – publicera slutpunkter | Microsoft Docs
description: Lär dig hur du publicerar Azure Stack slutpunkter i ditt datacenter
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: 014dbf6ff6d30bfb988c404bfe35693fe68f22fd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure stacken datacenter integrering – publicera slutpunkter
Azure-stacken konfigurerar virtuella IP-adresser (VIP) för sin infrastrukturroller. Dessa virtuella IP-adresser tilldelas från den offentliga IP-adresspoolen. Varje VIP är skyddad med en åtkomstkontrollista (ACL) i nätverkslagret för programvarudefinierade. ACL: er används också för de fysiska växlarna (TORs och BMC) för att ytterligare skydda lösningen. En DNS-post skapas för varje slutpunkt i den externa DNS-zon som anges vid tidpunkten för distribution.


Arkitekturdiagram för följande visar olika nätverk lager och ACL: er:

![Strukturella bild](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portar och protokoll (inkommande)

En uppsättning infrastruktur VIP-adresser krävs för publicering Azure Stack-slutpunkter till externa nätverk. Den *slutpunkt (VIP)* tabellen visar var och en slutpunkt, krävs port och protokoll. Finns i dokumentationen för specifik resurs-providern för slutpunkter som kräver ytterligare resursleverantörer, till exempel SQL-resursprovidern.

Intern infrastruktur för VIP inte visas eftersom de inte krävs för att publicera Azure-stacken.

> [!NOTE]
> Användaren VIP: er är dynamisk, definieras användarna själva har ingen kontroll av Azure Stack-operatorn.


|Slutpunkt (VIP)|En post för DNS-värdpost|Protokoll|Portar|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portalen (administratör)|adminportal.  *&lt;region >.&lt; FQDN >*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (administratör)|adminmanagement.  *&lt;region >.&lt; FQDN >*|HTTPS|443<br>30024|
|Portalen (användare)|portalen.  *&lt;region >.&lt; FQDN >*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (användare)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista över återkallade certifikat|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP OCH UDP|53|
|Key Vault (användare)|&#42;.Vault.  *&lt;region >.&lt; FQDN >*|HTTPS|443|
|Key Vault (administratör)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lagringskö|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabell för lagring|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Lagringsblob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL-Resursprovidern|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300 44304|
|MySQL-Resursprovidern|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300 44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP OCH UDP|21, 1021 10001 101000 (FTP)<br>990 (FTPS)|

## <a name="ports-and-urls-outbound"></a>Portar och URL: er (utgående)

Azure-stacken stöder endast transparent proxy-servrar. I en distribution där en transparent proxy överordnade länkar till en traditionell proxyserver måste du tillåta följande portar och URL: er för utgående kommunikation:


|Syfte|URL|Protokoll|Portar|
|---------|---------|---------|---------|
|Identitet|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com|HTTP<br>HTTPS|80<br>443|
|Marketplace-syndikeringsfeed|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Korrigering & uppdatering|https://&#42;.azureedge.net|HTTPS|443|
|Registrering|https://management.azure.com|HTTPS|443|
|Användning|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|
|Windows Defender|. wdcp.microsoft.com<br>. wdcpalt.microsoft.com<br>*. updates.microsoft.com<br>*. download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>http://crl.microsoft.com/pki/crl/products<br>http://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|



## <a name="next-steps"></a>Nästa steg

[Krav för Azure-stacken PKI](azure-stack-pki-certs.md)