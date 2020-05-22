---
title: Identifiera inbyggd brand vägg för hanterad instans
description: Lär dig hur du verifierar det inbyggda brand Väggs skyddet i Azure SQL Database Hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 91b8b37de69a3f08bf11650e0328b616d8633e7a
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83769929"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Kontrollera den inbyggda brandväggen i Managed Instance

De [obligatoriska inkommande säkerhets reglerna](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) för den hanterade instansen kräver att hanterings portarna 9000, 9003, 1438, 1440, 1452 kan öppnas från **vilken källa som helst** i nätverks säkerhets gruppen (NSG) som skyddar den hanterade instansen. Även om dessa portar är öppna på NSG-nivån, skyddas de på nätverks nivå av den inbyggda brand väggen.

## <a name="verify-firewall"></a>Verifiera brand väggen

Verifiera portarna genom att använda ett säkerhets skanner verktyg för att testa portarna. Följande skärm bild visar hur du använder något av dessa verktyg.

![Verifiera inbyggd brand vägg](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hanterade instanser och anslutningar finns [Azure SQL Database arkitektur för hanterad instans anslutning](sql-database-managed-instance-connectivity-architecture.md).