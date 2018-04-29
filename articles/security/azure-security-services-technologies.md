---
title: Säkerheten i Azure-tjänster och teknik | Microsoft Docs
description: Artikeln innehåller en granskad lista över Azure-säkerhet tjänster och teknik.
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: eedfca2506f9e34b8e5039b0f101b1d4e68ef5a7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-security-services-and-technologies"></a>Säkerheten i Azure-tjänster och teknik

I vår diskussioner med aktuella och framtida Azure-kunder uppmanas vi ofta ”har du en lista över alla relaterade tjänster och teknik som Azure har att erbjuda”?

När du utvärderar molnalternativ service provider, är det bra att ha den här informationen.

Följande är vårt första arbete med att tillhandahålla en lista. Över tiden, den här listan ändrar och växer, precis som Azure. Kategoriseras i listan och listan över kategorier kommer också att växa med tiden. Se till att markera den här sidan med jämna mellanrum att hålla dig uppdaterad på vår säkerhetsrelaterade tjänster och teknik.

## <a name="azure-security---general"></a>Azure Security - Allmänt

* [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/)
* [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)
* [Azure Disk Encryption](azure-security-disk-encryption.md)
* [Log Analytics](../log-analytics/log-analytics-overview.md)
* [Utveckling och testning i Azure-labb](https://azure.microsoft.com/documentation/services/devtest-lab/)

## <a name="azure-storage-security"></a>Azure Storage-säkerhet

* [Azure Storage Service-kryptering](../storage/common/storage-service-encryption.md)
* [StorSimple krypterade Hybrid lagring](https://azure.microsoft.com/documentation/services/storsimple/)
* [Azure klientsidan kryptering](../storage/common/storage-client-side-encryption.md)
* [Azure Storage signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Azure Lagringskontonycklar](../storage/common/storage-create-storage-account.md)
* [Azure-filresurser med SMB 3.0-kryptering](../storage/files/storage-dotnet-how-to-use-files.md)
* [Azure Storage Analytics](https://msdn.microsoft.com/library/hh343270.aspx)

## <a name="azure-database-security"></a>Azure Database-säkerhet

* [Azure SQL-brandvägg](../sql-database/sql-database-firewall-configure.md)
* [Azure SQL Cell kryptering](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)
* [Kryptering för Azure SQL-anslutning](../sql-database/sql-database-control-access.md)
* [Azure SQL-autentisering](../sql-database/sql-database-control-access.md)
* [Azure SQL alltid kryptering](https://msdn.microsoft.com/library/mt163865.aspx)
* [Kryptering på Azure SQL kolumn](https://msdn.microsoft.com/library/ms179331.aspx)
* [Azure SQL Transparent datakryptering](https://msdn.microsoft.com/library/dn948096.aspx)
* [Azure SQL Database Auditing](../sql-database/sql-database-auditing.md)

## <a name="azure-identity-and-access-management"></a>Azure identitets- och åtkomsthantering

* [Azure rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md)
* [Azure Active Directory](../active-directory/active-directory-whatis.md)
* [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-get-started.md)
* [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)
* [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)

## <a name="backup-and-disaster-recovery"></a>Säkerhetskopiering och katastrofåterställning

* [Azure Backup](https://azure.microsoft.com/documentation/services/backup/)
* [Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery/)

## <a name="azure-networking"></a>Azure-nätverk

* [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md)
* [Azure VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure Programgateway](../application-gateway/application-gateway-introduction.md)
* [Azure Load Balancer](../load-balancer/load-balancer-overview.md)
* [Azure ExpressRoute](../expressroute/expressroute-introduction.md)
* [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
* [Azure Application Proxy](../active-directory/active-directory-application-proxy-enable.md)
