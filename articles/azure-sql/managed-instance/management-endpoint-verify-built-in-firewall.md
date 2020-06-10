---
title: Verifiera port säkerhet i den inbyggda brand väggen
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
ms.openlocfilehash: 561aba31bfd1cc37173f3ee41625ba3687f5e657
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660912"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Verifiera den inbyggda brand väggen för Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

De [obligatoriska inkommande säkerhets reglerna](connectivity-architecture-overview.md#mandatory-inbound-security-rules) för Azure SQL Managed instance kräver att hanterings portarna 9000, 9003, 1438, 1440 och 1452 är öppna från **vilken källa som helst** i nätverks säkerhets gruppen (NSG) som skyddar SQL-hanterad instans. Även om dessa portar är öppna på NSG-nivån, skyddas de på nätverks nivå av den inbyggda brand väggen.

## <a name="verify-firewall"></a>Verifiera brand väggen

Verifiera portarna genom att använda ett säkerhets skanner verktyg för att testa portarna. Följande skärm bild visar hur du använder något av dessa verktyg.

![Verifiera inbyggd brand vägg](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Nästa steg

Mer information om SQL-hanterad instans och anslutningar finns i [anslutnings arkitektur för Azure SQL Managed instance](connectivity-architecture-overview.md).
