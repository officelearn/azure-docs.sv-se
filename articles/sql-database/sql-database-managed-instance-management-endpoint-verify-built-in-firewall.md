---
title: Upptäck inbyggd brandvägg för hanterade instanser
description: Lär dig hur du verifierar inbyggt brandväggsskydd i Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 555ef56aafa37a1e1d384f945b04f9237adc5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821806"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Kontrollera den inbyggda brandväggen i Managed Instance

De obligatoriska reglerna för [den hanterade](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) instansen kräver att hanteringsportar 9000, 9003, 1438, 1440, 1452 är öppna från valfri **källa** i NSG (Network Security Group) som skyddar den hanterade instansen. Även om dessa portar är öppna på NSG-nivå skyddas de på nätverksnivå av den inbyggda brandväggen.

## <a name="verify-firewall"></a>Verifiera brandvägg

Om du vill verifiera dessa portar använder du alla säkerhetsskannerverktyg för att testa dessa portar. Följande skärmbild visar hur du använder något av dessa verktyg.

![Verifiera inbyggd brandvägg](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hanterade instanser och anslutning finns i [Azure SQL Database Managed Instance Connectivity Architecture](sql-database-managed-instance-connectivity-architecture.md).