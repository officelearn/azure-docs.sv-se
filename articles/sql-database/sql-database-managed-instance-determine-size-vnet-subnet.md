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
ms.date: 12/12/2018
ms.openlocfilehash: a588f8579d45f6230c80a62f8cd3abc155d75700
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346526"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Bestämma storleken för VNet-undernätet för Azure SQL Database Managed Instance

Azure SQL Database Managed Instance måste distribueras i en Azure [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md).

Antalet instanser som hanteras som kan distribueras i undernät för virtuellt nätverk beror på storleken på undernätet (undernätsintervall).

När du skapar en hanterad instans, tilldelar Azure ett antal virtuella datorer beroende på vilken nivå som du valde under etableringen. Eftersom de virtuella datorerna är kopplade till ditt undernät, kräver de IP-adresser. För att säkerställa hög tillgänglighet under normal drift och underhåll, kan Azure allokera fler virtuella datorer. Därför kan är antalet begärda IP-adresser i ett undernät större än antalet instanser som hanteras i det undernätet. 

Enligt design, en hanterad instans måste ha minst 16 IP-adresser i ett undernät och använder upp till 256 IP-adresser. Du kan därmed använda nätmasker /28 /24 när du definierar dina IP-adressintervall för undernätet. 

> [!IMPORTANT]
> Storleken med 16 IP-adresser i undernätet är minst med begränsade möjligheter för att ytterligare Managed Instance-skala ut. Välja undernät med prefixet /27 eller under rekommenderas starkt. 

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
- 
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md)
