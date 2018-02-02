---
title: "Azure stacken datacenter integrering – publicera slutpunkter"
description: "Lär dig hur du publicerar Azure Stack slutpunkter i ditt datacenter"
services: azure-stack
author: jeffgilb
ms.service: azure-stack
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: 
ms.openlocfilehash: e368109adc7db4c589ac37b28c4891cb3ec5346f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure stacken datacenter integrering – publicera slutpunkter

*Gäller för: Azure Stack integrerat system*

Azure-stacken ställer in olika slutpunkter (VIP - virtuella IP-adresser) för dess infrastrukturroller. Dessa virtuella IP-adresser tilldelas från den offentliga IP-adresspoolen. Varje VIP är skyddad med en åtkomstkontrollista (ACL) i nätverkslagret för programvarudefinierade. ACL: er används också för de fysiska växlarna (TORs och BMC) för att ytterligare skydda lösningen. En DNS-post skapas för varje slutpunkt i externa DNS-zonen som angavs vid tidpunkten för distribution.


Arkitekturdiagram för följande visar olika nätverk lager och ACL: er:

![Arkitekturdiagram](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portar och protokoll (inkommande)

VIP infrastruktur som krävs för publicering Azure Stack-slutpunkter till externa nätverk visas i följande tabell. I listan visas varje slutpunkt, krävs port och protokoll. Slutpunkter som krävs för ytterligare resursproviders som SQL-resursprovidern och andra, beskrivs i dokumentationen för specifik resurs-providern.

Intern infrastruktur för VIP visas inte eftersom de inte krävs för att publicera Azure-stacken.

> [!NOTE]
> Användaren VIP: er är dynamisk, definieras användarna själva har ingen kontroll av Azure Stack-operatorn.


|Slutpunkt (VIP)|En post för DNS-värdpost|Protokoll|Portar|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portalen (administratör)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (administratör)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portalen (användare)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (user)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista över återkallade certifikat|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP OCH UDP|53|
|Key Vault (användare)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (administratör)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lagringskö|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabell för lagring|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL-Resursprovidern|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL-Resursprovidern|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304

## <a name="ports-and-urls-outbound"></a>Portar och URL: er (utgående)

Azure-stacken stöder endast transparent proxy-servrar. I en distribution där en transparent proxy överordnade länkar till en traditionell proxyserver måste du tillåta följande portar och URL: er för utgående kommunikation:


|Syfte|Webbadress|Protokoll|Portar|
|---------|---------|---------|---------|
|Identitet|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Marketplace-syndikeringsfeed|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Korrigering & uppdatering|https://&#42;.azureedge.net|HTTPS|443|
|Registrering|https://management.azure.com|HTTPS|443|
|Användning|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|


## <a name="next-steps"></a>Nästa steg
[Krav för Azure-stacken PKI](azure-stack-pki-certs.md)