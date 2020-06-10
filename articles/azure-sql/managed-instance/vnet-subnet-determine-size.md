---
title: Bestäm nödvändig under näts storlek & intervall
titleSuffix: Azure SQL Managed Instance
description: I det här avsnittet beskrivs hur du beräknar storleken på det undernät där den hanterade Azure SQL-instansen ska distribueras.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 2ad140e7f0040552cbdc2490d25a07f3d1184720
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84657860"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Bestäm nödvändig under näts storlek & intervall för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL-hanterad instans måste distribueras i ett [virtuellt Azure-nätverk (VNet)](../../virtual-network/virtual-networks-overview.md).

Antalet hanterade instanser som kan distribueras i under nätet för ett VNet beror på under nätets storlek (under nätets intervall).

När du skapar en hanterad instans allokerar Azure ett antal virtuella datorer beroende på vilken nivå du valde under etableringen. Eftersom de virtuella datorerna är associerade med ditt undernät kräver de IP-adresser. För att säkerställa hög tillgänglighet vid vanliga drift-och tjänst underhåll kan Azure allokera ytterligare virtuella datorer. Därför är antalet begärda IP-adresser i ett undernät större än antalet hanterade instanser i det under nätet.

En hanterad instans kräver minst 16 IP-adresser i ett undernät och kan använda upp till 256 IP-adresser. Det innebär att du kan använda nät masker mellan/28 och/24 när du definierar IP-intervall för under nätet. En nätverks mask-bit på/28 (14 värdar per nätverk) är en lämplig storlek för en enda allmän användnings-eller verksamhets kritisk distribution. En mask bit till/27 (30 värdar per nätverk) är idealisk för flera SQL-hanterade instans distributioner inom samma VNet. Mask bit inställningar för/26 (62-värdar) och/24 (254 värdar) gör det möjligt att utöka VNet ytterligare för att stödja ytterligare hanterade instanser.

> [!IMPORTANT]
> En under näts storlek med 16 IP-adresser är minimalt med begränsad potential där en skalnings åtgärd som vCore storleks ändring inte stöds. Vi rekommenderar starkt att du väljer undernät med prefix/27 eller längst prefix.

## <a name="determine-subnet-size"></a>Bestäm under näts storlek

Om du planerar att distribuera flera hanterade instanser i under nätet och behöver optimera storleken på under näts storleken, använder du följande parametrar för att skapa en beräkning:

- Azure använder fem IP-adresser i under nätet för sina egna behov
- Varje instans av generell användning behöver två adresser
- Varje verksamhets kritisk instans behöver fyra adresser

**Exempel**: du planerar att ha tre allmänna syften och två verksamhets kritiska hanterade instanser. Det innebär att du behöver 5 + 3 * 2 + 2 * 4 = 19 IP-adresser. Eftersom IP-intervall definieras i potenser av 2 behöver du IP-intervallet 32 (2 ^ 5) IP-adresser. Därför måste du reservera under nätet med nät masken/27.

> [!IMPORTANT]
> Den beräkning som visas ovan kommer att bli föråldrad med ytterligare förbättringar.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en Azure SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
- Lär dig mer om [anslutnings arkitektur för SQL-hanterad instans](connectivity-architecture-overview.md).
- Se så här [skapar du ett VNet där du kommer att distribuera SQL-hanterad instans](virtual-network-subnet-create-arm-template.md).
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](custom-dns-configure.md).
