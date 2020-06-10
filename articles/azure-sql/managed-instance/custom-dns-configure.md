---
title: Anpassad DNS
titleSuffix: Azure SQL Managed Instance
description: I det här avsnittet beskrivs konfigurations alternativ för en anpassad DNS med en hanterad Azure SQL-instans.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 6a6cca1228c9891c023ce96532dc89b2f527e9a3
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84655525"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Konfigurera en anpassad DNS för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL-hanterad instans måste distribueras i ett [virtuellt Azure-nätverk (VNet)](../../virtual-network/virtual-networks-overview.md). Det finns några scenarier (till exempel DB mail, länkade servrar till andra SQL Server instanser i molnet eller hybrid miljön) som kräver att privata värdnamn matchas från SQL-hanterad instans. I så fall måste du konfigurera en anpassad DNS i Azure. 

Eftersom SQL-hanterad instans använder samma DNS för sin inre verksamhet, konfigurerar du den anpassade DNS-servern så att den kan matcha offentliga domän namn.

> [!IMPORTANT]
> Använd alltid ett fullständigt kvalificerat domän namn (FQDN) för e-postservern, för SQL Server-instansen och för andra tjänster, även om de är i din privata DNS-zon. Använd till exempel `smtp.contoso.com` för din e-postserver eftersom den `smtp` inte kan matchas korrekt. Att skapa en länkad server eller replikering som refererar SQL Server virtuella datorer i samma virtuella nätverk kräver också ett fullständigt domän namn och ett DNS-suffix som är standard. Exempelvis `SQLVM.internal.cloudapp.net`. Mer information finns i [namn matchning som använder din egen DNS-Server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Uppdatering av virtuella nätverks-DNS-servrar påverkar omedelbart SQL-hanterad instans. DNS-konfigurationen för SQL-hanterad instans uppdateras när DHCP-lånet upphör att gälla eller efter plattforms uppgraderingen, beroende på vilket som inträffar först. **Användarna uppmanas att ange sina DNS-konfigurationer för virtuellt nätverk innan de skapar sin första hanterade instans.**

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en Azure SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
- En själv studie kurs som visar hur du skapar en ny hanterad instans finns i [skapa en hanterad instans](instance-create-quickstart.md).
- Information om hur du konfigurerar ett VNet för en hanterad instans finns i [VNet-konfiguration för hanterade instanser](connectivity-architecture-overview.md).
