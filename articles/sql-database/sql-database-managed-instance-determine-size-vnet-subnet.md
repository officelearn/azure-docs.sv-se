---
title: Azure SQL Database Managed Instance avgöra storleken för virtuellt nätverk/undernät | Microsoft Docs
description: Det här avsnittet beskriver hur du beräkna storleken på undernätet där den Azure SQL Database-hanterade instanser kommer att distribueras.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/22/2019
ms.openlocfilehash: cdd9f4eed30744f0eb65f8890eb1d7149f39736c
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750250"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Bestämma storleken för VNet-undernätet för Azure SQL Database Managed Instance

Azure SQL Database Managed Instance måste distribueras i en Azure [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md).

Antalet instanser som hanteras som kan distribueras i undernät för virtuellt nätverk beror på storleken på undernätet (undernätsintervall).

När du skapar en hanterad instans, tilldelar Azure ett antal virtuella datorer beroende på vilken nivå som du valde under etableringen. Eftersom de virtuella datorerna är kopplade till ditt undernät, kräver de IP-adresser. För att säkerställa hög tillgänglighet under normal drift och underhåll, kan Azure allokera fler virtuella datorer. Därför kan är antalet begärda IP-adresser i ett undernät större än antalet instanser som hanteras i det undernätet.

Enligt design, en hanterad instans måste ha minst 16 IP-adresser i ett undernät och använder upp till 256 IP-adresser. Därför kan använda du en nätmasker mellan /28 och /24 när du definierar dina IP-adressintervall för undernätet. Lite nätverk mask på/28 (14 värdar per nätverk) är en lämplig storlek för en enda generell användning eller affärskritiska distribution. Lite mask/27 (30 värdar per nätverk) är idealiskt för ett flera hanterad instans-distributioner inom samma virtuella nätverk. Masken bitars inställningarna för /26 (62 värdar) och /24 (254 värdar) kan ytterligare skalning utanför det virtuella nätverket som stöd för ytterligare hanterade instanser.

> [!IMPORTANT]
> Undernätets storlek med 16 IP-adresser är minst med begränsade möjligheter för att ytterligare Managed Instance-skala ut. Välja undernät med prefixet /27 eller under rekommenderas starkt.

## <a name="determine-subnet-size"></a>Fastställa undernätets storlek

Om du planerar att distribuera flera hanterade instanser i undernätet och behöver för att optimera på undernätets storlek, använder du parametrarna för att bilda en beräkning:

- Azure använder fem IP-adresser i undernätet för sin egen behov
- Varje instans generell användning måste två adresser
- Varje affärskritisk instans behöver fyra adresserna

**Exempel**: Du planerar att ha tre generella och två företag kritiska hanterade instanser. Att innebär att du behöver 5 + 3 * 2 + 2 * 4 = 19 IP-adresser. När IP-intervall är definierade i potensen 2, måste IP-adressintervall 32 (2 ^ 5) IP-adresser. Du måste därför reservera undernätet med nätmask på/27.

> [!IMPORTANT]
> Beräkningen ovan kommer att bli föråldrad med ytterligare förbättringar.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md).
- Läs mer om [anslutningsarkitektur för hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- Se hur du [skapa virtuellt nätverk där du ska distribuera hanterade instanser](sql-database-managed-instance-create-vnet-subnet.md)
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md)
