---
title: Hanterad instans Bestäm VNet/undernät-storlek
description: I det här avsnittet beskrivs hur du beräknar storleken på under nätet där Azure SQL Database hanterade instanserna ska distribueras.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 7f0ef26343284b7b668e71676114586f4bec8b9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "73825744"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Bestäm storleken på VNet-undernät för Azure SQL Database Hanterad instans

Azure SQL Database hanterade instansen måste distribueras i ett [virtuellt Azure-nätverk (VNet)](../virtual-network/virtual-networks-overview.md).

Antalet hanterade instanser som kan distribueras i under nätet för VNet beror på under nätets storlek (under nätets intervall).

När du skapar en hanterad instans allokerar Azure ett antal virtuella datorer beroende på vilken nivå du valde under etableringen. Eftersom de virtuella datorerna är associerade med ditt undernät kräver de IP-adresser. För att säkerställa hög tillgänglighet vid vanliga drift-och tjänst underhåll kan Azure allokera ytterligare virtuella datorer. Därför är antalet begärda IP-adresser i ett undernät större än antalet hanterade instanser i det under nätet.

En hanterad instans kräver minst 16 IP-adresser i ett undernät och kan använda upp till 256 IP-adresser. Det innebär att du kan använda nät masker mellan/28 och/24 när du definierar IP-intervall för under nätet. En nätverks mask-bit på/28 (14 värdar per nätverk) är en lämplig storlek för en enskild generell användning eller verksamhets kritisk distribution. En mask bit till/27 (30 värdar per nätverk) är idealisk för flera distributioner av hanterade instanser inom samma VNet. Mask bit inställningar för/26 (62-värdar) och/24 (254 värdar) gör det möjligt att utöka VNet ytterligare för att stödja ytterligare hanterade instanser.

> [!IMPORTANT]
> En under näts storlek med 16 IP-adresser är minimalt med begränsad potential där en skalnings åtgärd som vCore storleks ändring inte stöds. Vi rekommenderar starkt att du väljer undernät med prefix/27 eller längst prefix.

## <a name="determine-subnet-size"></a>Bestäm under näts storlek

Om du planerar att distribuera flera hanterade instanser i under nätet och behöver optimera storleken på under näts storleken, använder du följande parametrar för att skapa en beräkning:

- Azure använder fem IP-adresser i under nätet för sina egna behov
- Varje Generell användning instans behöver två adresser
- Varje Affärskritisk instans behöver fyra adresser

**Exempel**: du planerar att ha tre generell användning och två affärskritisk hanterade instanser. Det innebär att du behöver 5 + 3 * 2 + 2 * 4 = 19 IP-adresser. Eftersom IP-intervall definieras med 2 måste du ha IP-adresser för IP-intervallet 32 (2 ^ 5). Därför måste du reservera under nätet med nät masken/27.

> [!IMPORTANT]
> Den beräkning som visas ovan kommer att bli föråldrad med ytterligare förbättringar.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans](sql-database-managed-instance.md).
- Lär dig mer om [anslutnings arkitekturen för en hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- Se hur [du skapar VNet där du ska distribuera hanterade instanser](sql-database-managed-instance-create-vnet-subnet.md)
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md)
