---
title: Hanterad instans bestämmer VNet/undernätsstorlek
description: I det här avsnittet beskrivs hur du beräknar storleken på undernätet där Azure SQL Database Managed Instances ska distribueras.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825744"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Fastställ VNet-undernätsstorlek för Azure SQL Database Managed Instance

Azure SQL Database Managed Instance måste distribueras i ett virtuellt [Azure-nätverk (VNet)](../virtual-network/virtual-networks-overview.md).

Antalet hanterade instanser som kan distribueras i undernätet i virtuella nätverk beror på storleken på undernätet (undernätsintervall).

När du skapar en hanterad instans allokerar Azure ett antal virtuella datorer beroende på vilken nivå du valde under etableringen. Eftersom dessa virtuella datorer är associerade med ditt undernät kräver de IP-adresser. Azure kan allokera ytterligare virtuella datorer för att säkerställa hög tillgänglighet under regelbunden drift och underhåll av tjänsten. Därför är antalet nödvändiga IP-adresser i ett undernät större än antalet hanterade instanser i det undernätet.

Enligt design behöver en hanterad instans minst 16 IP-adresser i ett undernät och kan använda upp till 256 IP-adresser. Därför kan du använda en nätmask mellan /28 och /24 när du definierar ip-intervall för undernät. En nätverksmaskbit /28 (14 värdar per nätverk) är en bra storlek för en enda allmän eller affärskritisk distribution. En maskbit /27 (30 värdar per nätverk) är idealisk för flera hanterade instansdistributioner inom samma virtuella nätverk. Maskbitinställningar för /26 (62 värdar) och /24 (254 värdar) gör det möjligt att skala ut vnet ytterligare för att stödja ytterligare hanterade instanser.

> [!IMPORTANT]
> En undernätsstorlek med 16 IP-adresser är ett minimum med begränsad potential där en skalningsåtgärd som vCore-storleksändring inte stöds. Det rekommenderas starkt att välja undernät med prefixet /27 eller längst prefix.

## <a name="determine-subnet-size"></a>Bestämma undernätsstorlek

Om du planerar att distribuera flera hanterade instanser i undernätet och behöver optimera på undernätsstorleken använder du dessa parametrar för att göra en beräkning:

- Azure använder fem IP-adresser i undernätet för sina egna behov
- Varje instans för allmänt syfte behöver två adresser
- Varje affärskritisk instans behöver fyra adresser

**Exempel:** Du planerar att ha tre allmänna ändamål och två affärskritiska hanterade instanser. Det betyder att du behöver 5 + 3 * 2 + 2 * 4 = 19 IP-adresser. Eftersom IP-intervall definieras med effekt av 2 behöver du IP-intervallet på 32 (2^5) IP-adresser. Därför måste du reservera undernätet med nätmask på /27.

> [!IMPORTANT]
> Beräkningen som visas ovan kommer att bli föråldrad med ytterligare förbättringar.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans](sql-database-managed-instance.md).
- Läs mer om [anslutningsarkitektur för hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- Se hur [du skapar VNet där du ska distribuera hanterade instanser](sql-database-managed-instance-create-vnet-subnet.md)
- För DNS-problem finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md)
