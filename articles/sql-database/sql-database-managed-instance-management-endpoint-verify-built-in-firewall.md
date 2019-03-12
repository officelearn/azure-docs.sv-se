---
title: Upptäck Azure SQL Database Managed Instance inbyggda brandväggen | Microsoft Docs
description: Lär dig hur du verifierar inbyggda brandväggsskydd i Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: f059ac4149a385a12b440db0ad6394a343a1f810
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761930"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Verifiera den inbyggda brandväggen för hanterad instans

Den hanterade instansen [obligatoriska inkommande säkerhetsregler](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) kräver hanteringsportar 9000, 9003, 1438, 1440, 1452 vara öppna från **vilken källa som helst** på den Nätverkssäkerhetsgrupp (NSG) som skyddar hanterat Instans. Även om dessa portar är öppna på NSG-nivå, är de skyddade på nätverksnivå med den inbyggda brandväggen.

## <a name="verify-firewall"></a>Kontrollera brandväggen

Använda någon skanner säkerhetsverktyget för att testa portarna för att kontrollera dessa portar. Följande skärmbild visar hur du använder något av dessa verktyg.

![Verifiera inbyggda brandväggen](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hanterade instanser och anslutning finns i [Azure SQL Database hanterad instans Anslutningsarkitektur](sql-database-managed-instance-connectivity-architecture.md).