---
title: Anpassad DNS-tjänst för hanterad instans
description: I det här avsnittet beskrivs konfigurationsalternativ för en anpassad DNS med en hanterad Azure SQL-databas-hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247086"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurera en anpassad DNS för Azure SQL Database Managed Instance

En hanterad Azure SQL-databas-instans måste distribueras i ett virtuellt [Azure-nätverk (VNet).](../virtual-network/virtual-networks-overview.md) Det finns några scenarier (till exempel db-e-post, länkade servrar till andra SQL-instanser i molnet eller hybridmiljön) som kräver att privata värdnamn matchas från den hanterade instansen. I det här fallet måste du konfigurera en anpassad DNS i Azure. 

Eftersom hanterad instans använder samma DNS för sitt inre arbete konfigurerar du den anpassade DNS-servern så att den kan matcha offentliga domännamn.

> [!IMPORTANT]
> Använd alltid ett fullständigt kvalificerat domännamn (FQDN) för e-postservern, SQL Server-instansen och för andra tjänster, även om de ligger inom din privata DNS-zon. Använd till `smtp.contoso.com` exempel för e-postservern eftersom `smtp` den inte fungerar korrekt. Skapa en länkad server eller replikering som refererar till VIRTUELLA SQL-datorer i samma virtuella nätverk kräver också en FQDN och ett standard-DNS-suffix. Till exempel `SQLVM.internal.cloudapp.net`. Mer information finns i [Namnmatchning som använder din egen DNS-server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Uppdatering av DNS-servrar i det virtuella nätverket påverkar inte hanterad instans omedelbart. DNS-konfigurationen för hanterade instanser uppdateras när DHCP-lånet har gått ut eller efter plattformsuppgraderingen, beroende på vilket som inträffar först. **Användare rekommenderas att ställa in sin DNS-konfiguration för virtuellt nätverk innan de skapar sin första hanterade instans.**

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans](sql-database-managed-instance.md)
- En självstudiekurs som visar hur du skapar en ny hanterad instans finns i [Skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- Information om hur du konfigurerar ett VNet för en hanterad instans finns i [VNet-konfiguration för hanterade instanser](sql-database-managed-instance-connectivity-architecture.md)
