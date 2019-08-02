---
title: Anpassad DNS för Azure SQL Database Hanterad instans Microsoft Docs
description: I det här avsnittet beskrivs konfigurations alternativ för en anpassad DNS med en Azure SQL Database Hanterad instans.
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
ms.openlocfilehash: 588fac1fc48396584188eec44f21a7005dc8ed96
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567558"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurera en anpassad DNS för Azure SQL Database Hanterad instans

En Azure SQL Database Hanterad instans måste distribueras i ett [virtuellt Azure-nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Det finns några scenarier (till exempel DB mail, länkade servrar till andra SQL-instanser i molnet eller i hybrid miljön) som kräver att privata värdnamn matchas från den hanterade instansen. I så fall måste du konfigurera en anpassad DNS i Azure. 

Eftersom den hanterade instansen använder samma DNS för sin inre verksamhet måste du konfigurera den anpassade DNS-servern så att den kan matcha offentliga domän namn.

   > [!IMPORTANT]
   > Använd alltid fullständigt kvalificerade domän namn (FQDN) för e-postservrar, SQL-servrar och andra tjänster, även om de är i din privata DNS-zon. Använd `smtp.contoso.com` till exempel för e-postserver `smtp` eftersom enkla inte kommer att lösas korrekt.

   > [!IMPORTANT]
   > Uppdatering av virtuella nätverks-DNS-servrar påverkar inte den hanterade instansen omedelbart. Den hanterade instansens DNS-konfiguration kommer att uppdateras när DHCP-lånet upphör att gälla eller när plattformen upgarade, vad som kommer först. **Användarna uppmanas att ange sina DNS-konfigurationer för virtuellt nätverk innan de skapar sin första hanterade instans.**

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans](sql-database-managed-instance.md)
- En själv studie kurs som visar hur du skapar en ny hanterad instans finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- Information om hur du konfigurerar ett VNet för en hanterad instans finns i [VNet-konfiguration för hanterade instanser](sql-database-managed-instance-connectivity-architecture.md)
