---
title: Verifiera port säkerhet i inbyggd brand vägg
description: Lär dig hur du verifierar det inbyggda brand Väggs skyddet i Azure SQL-hanterad instans.
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
ms.openlocfilehash: 04a630e51a68fab8f6c60262fbd14fad2e4aef1c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045076"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Verifiera den inbyggda brand väggen för Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

De [obligatoriska inkommande säkerhets reglerna](connectivity-architecture-overview.md#mandatory-inbound-security-rules) för Azure SQL Managed instance kräver att hanterings portarna 9000, 9003, 1438, 1440, 1452 kan öppnas från **vilken källa som helst** i nätverks säkerhets gruppen (NSG) som skyddar SQL-hanterad instans. Även om dessa portar är öppna på NSG-nivån, skyddas de på nätverks nivå av den inbyggda brand väggen.

## <a name="verify-firewall"></a>Verifiera brand väggen

Verifiera portarna genom att använda ett säkerhets skanner verktyg för att testa portarna. Följande skärm bild visar hur du använder något av dessa verktyg.

![Verifiera inbyggd brand vägg](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Nästa steg

Mer information om SQL-hanterade instanser och anslutningar finns i [anslutnings arkitektur för Azure SQL Managed instance](connectivity-architecture-overview.md).
