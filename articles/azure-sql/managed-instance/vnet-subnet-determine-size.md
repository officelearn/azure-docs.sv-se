---
title: Bestäm nödvändig under näts storlek & intervall
titleSuffix: Azure SQL Managed Instance
description: I det här avsnittet beskrivs hur du beräknar storleken på under nätet där de Azure SQL-hanterade instanserna ska distribueras.
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
ms.openlocfilehash: 5eebde1fc95cb50f8ff7c13b6cbc411484ddf943
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050935"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Bestäm nödvändig under näts storlek & intervall för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL-hanterad instans måste distribueras i ett [virtuellt Azure-nätverk (VNet)](../../virtual-network/virtual-networks-overview.md).

Antalet SQL-hanterade instanser som kan distribueras i under nätet för VNet beror på under nätets storlek (under nätets intervall).

När du skapar en SQL-hanterad instans allokerar Azure ett antal virtuella datorer beroende på vilken nivå du valde under etableringen. Eftersom de virtuella datorerna är associerade med ditt undernät kräver de IP-adresser. För att säkerställa hög tillgänglighet vid vanliga drift-och tjänst underhåll kan Azure allokera ytterligare virtuella datorer. Därför är antalet begärda IP-adresser i ett undernät större än antalet SQL-hanterade instanser i det under nätet.

Enligt design behöver en SQL-hanterad instans minst 16 IP-adresser i ett undernät och kan använda upp till 256 IP-adresser. Det innebär att du kan använda nät masker mellan/28 och/24 när du definierar IP-intervall för under nätet. En nätverks mask-bit på/28 (14 värdar per nätverk) är en lämplig storlek för en enskild generell användning eller verksamhets kritisk distribution. En mask bit på/27 (30 värdar per nätverk) är idealisk för distributioner av flera SQL-hanterade instanser inom samma VNet. Mask bit inställningar för/26 (62-värdar) och/24 (254 värdar) gör det möjligt att skala ut det virtuella nätverket ytterligare för att stödja ytterligare SQL-hanterade instanser.

> [!IMPORTANT]
> En under näts storlek med 16 IP-adresser är minimalt med begränsad potential där en skalnings åtgärd som vCore storleks ändring inte stöds. Vi rekommenderar starkt att du väljer undernät med prefix/27 eller längst prefix.

## <a name="determine-subnet-size"></a>Bestäm under näts storlek

Om du planerar att distribuera flera SQL-hanterade instanser i under nätet och behöver optimera storleken på under näts storleken, använder du följande parametrar för att skapa en beräkning:

- Azure använder fem IP-adresser i under nätet för sina egna behov
- Varje Generell användning instans behöver två adresser
- Varje Affärskritisk instans behöver fyra adresser

**Exempel**: du planerar att ha tre generell användning och två affärskritisk SQL-hanterade instanser. Det innebär att du behöver 5 + 3 * 2 + 2 * 4 = 19 IP-adresser. Eftersom IP-intervall definieras med 2 måste du ha IP-adresser för IP-intervallet 32 (2 ^ 5). Därför måste du reservera under nätet med nät masken/27.

> [!IMPORTANT]
> Den beräkning som visas ovan kommer att bli föråldrad med ytterligare förbättringar.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en SQL-hanterad instans](sql-managed-instance-paas-overview.md).
- Lär dig mer om [anslutnings arkitektur för SQL-hanterad instans](connectivity-architecture-overview.md).
- Se hur [du skapar VNet där du ska distribuera SQL-hanterade instanser](virtual-network-subnet-create-arm-template.md)
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](custom-dns-configure.md)
