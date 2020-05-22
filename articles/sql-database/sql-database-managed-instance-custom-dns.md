---
title: Anpassad DNS för hanterad instans
description: I det här avsnittet beskrivs konfigurations alternativ för en anpassad DNS med en Azure SQL Database Hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 1b27d316114b84434a84fc8226eba9328a803dc8
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774370"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurera en anpassad DNS för Azure SQL Database Managed Instance

En Azure SQL Database Hanterad instans måste distribueras i ett [virtuellt Azure-nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Det finns några scenarier (till exempel DB mail, länkade servrar till andra SQL-instanser i molnet eller i hybrid miljön) som kräver att privata värdnamn matchas från den hanterade instansen. I så fall måste du konfigurera en anpassad DNS i Azure. 

Eftersom den hanterade instansen använder samma DNS för sin inre verksamhet, konfigurerar du den anpassade DNS-servern så att den kan matcha offentliga domän namn.

> [!IMPORTANT]
> Använd alltid ett fullständigt kvalificerat domän namn (FQDN) för e-postservern, SQL Server-instansen och för andra tjänster, även om de är i din privata DNS-zon. Använd till exempel `smtp.contoso.com` för din e-postserver eftersom den `smtp` inte kan matchas korrekt. Att skapa en länkad server eller replikering som refererar till virtuella SQL-datorer i samma virtuella nätverk kräver också ett fullständigt domän namn och ett DNS-suffix som är standard. Till exempel `SQLVM.internal.cloudapp.net`. Mer information finns i [namn matchning som använder din egen DNS-Server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Uppdatering av virtuella nätverks-DNS-servrar påverkar inte den hanterade instansen omedelbart. DNS-konfigurationen för hanterade instanser uppdateras när DHCP-lånet upphör att gälla eller efter plattforms uppgraderingen, beroende på vilket som inträffar först. **Användarna uppmanas att ange sina DNS-konfigurationer för virtuellt nätverk innan de skapar sin första hanterade instans.**

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans](sql-database-managed-instance.md)
- En själv studie kurs som visar hur du skapar en ny hanterad instans finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- Information om hur du konfigurerar ett VNet för en hanterad instans finns i [VNet-konfiguration för hanterade instanser](sql-database-managed-instance-connectivity-architecture.md)
