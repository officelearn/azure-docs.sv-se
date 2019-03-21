---
title: Azure Stack datacenter-integrering – publicera slutpunkter | Microsoft Docs
description: Lär dig hur du publicerar Azure Stack-slutpunkter i ditt datacenter
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 0bfd280e2a0a63c8fb41d0813a0ac44784e7a055
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259185"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack datacenter-integrering – publicera slutpunkter

Azure Stack ställer in virtuella IP-adresser (VIP) för dess infrastrukturroller. Dessa virtuella IP-adresser tilldelas från den offentliga IP-adresspoolen. Varje VIP skyddas med en åtkomstkontrollista (ACL) i nätverkslagret programvarudefinierade. ACL: er används också för de fysiska växlarna (trera Övervakare och BMC) för att ytterligare skydda lösningen. En DNS-post skapas för varje slutpunkt i externa DNS-zon som angetts vid tidpunkten för distribution.


Arkitekturdiagram för följande visar olika nätverk lager och ACL: er:

![Strukturella bild](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portar och protokoll (inkommande)

En uppsättning infrastruktur virtuella IP-adresser krävs för Azure Stack publiceringsslutpunkterna till externa nätverk. Den *slutpunkt (VIP)* tabell visas varje slutpunkt, krävs port och protokoll. Referera till dokumentationen för specifika resource provider för slutpunkter som kräver ytterligare resource-leverantörer, till exempel SQL-resursprovider.

Intern infrastruktur för virtuella IP-adresser inte visas eftersom de inte krävs för att publicera Azure Stack.

> [!Note]  
> Användaren virtuella IP-adresser är dynamiska, definieras av användare själva med ingen kontroll av Azure Stack-operatör.

> [!Note]
> Från och med uppdateringen 1811 portar mellan 12495 30015 krävs nu inte längre vara öppna på grund av att lägga till den [tillägget värden](azure-stack-extension-host-prepare.md).

|Slutpunkt (VIP)|DNS vara värd för en post|Protokoll|Portar|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administratör)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (administratör)|Adminmanagement.  *&lt;region >.&lt; FQDN >*|HTTPS|443|
|Portal (användare)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (user)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista över återkallade certifikat|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP OCH UDP|53|
|Värd | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (användare)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (administratör)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lagringskö|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage-tabell|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL-resursprovider|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL-resursprovider|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|VPN-gatewayer|     |     |[Se vanliga frågor och svar för VPN-gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Portar och URL: er (utgående)

Azure Stack stöder endast transparent proxy-servrar. I en distribution där en transparent proxy överordnade länkar till en traditionell proxyserver måste du tillåta följande portar och URL: er för utgående kommunikation:

> [!Note]  
> Azure Stack har inte stöd för att använda ExpressRoute för att nå de Azure-tjänster som anges i tabellen nedan.

|Syfte|Mål-URL|Protokoll|Portar|Källnätverk|
|---------|---------|---------|---------|---------|
|Identitet|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|Offentliga VIP - /27<br>Infrastruktur för offentliga nätverk|
|Marketplace-syndikering|https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|Offentliga VIP - /27|
|Patch & Update|https://&#42;.azureedge.net|HTTPS|443|Offentliga VIP - /27|
|Registrering|https:\//management.azure.com|HTTPS|443|Offentliga VIP - /27|
|Användning|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net |HTTPS|443|Offentliga VIP - /27|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*. updates.microsoft.com<br>*. download.microsoft.com<br>https:\//msdl.microsoft.com/download/symbols<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Offentliga VIP - /27<br>Infrastruktur för offentliga nätverk|
|NTP|(IP för NTP-server för distribution)|UDP|123|Offentliga VIP - /27|
|DNS|(IP-DNS-server för distribution)|TCP<br>UDP|53|Offentliga VIP - /27|
|LISTAN ÖVER ÅTERKALLADE CERTIFIKAT|(URL under CRL-distributionspunkter på ditt certifikat)|HTTP|80|Offentliga VIP - /27|
|LDAP|Active Directory-skog för Graph-integrering|TCP<br>UDP|389|Offentliga VIP - /27|
|LDAP SSL|Active Directory-skog för Graph-integrering|TCP|636|Offentliga VIP - /27|
|LDAP GC|Active Directory-skog för Graph-integrering|TCP|3268|Offentliga VIP - /27|
|LDAP GC SSL|Active Directory-skog för Graph-integrering|TCP|3269|Offentliga VIP - /27|
|AD FS|Metadataslutpunkt för AD FS för AD FS-integrering|TCP|443|Offentliga VIP - /27|
|     |     |     |     |     |

> [!Note]  
> Utgående URL: er är belastningsutjämnad med Azure traffic manager för att tillhandahålla den bästa möjliga anslutningen baserat på geografisk plats. Med att läsa in belastningsutjämnade URL: er, Microsoft kan uppdatera och ändra serverdelsslutpunkter utan att påverka kunder. Microsoft delar inte listan över IP-adresser för belastningsutjämnade URL: er. Du bör använda en enhet som stöder filtrering efter URL i stället för IP.

## <a name="next-steps"></a>Nästa steg

[Krav för Azure Stack PKI](azure-stack-pki-certs.md)
