---
title: Identifiera hanteringsslutpunkten för Azure SQL Database Managed Instance | Microsoft Docs
description: Lär dig hur du hämtar Azure SQL Database Managed Instance endpoint offentliga IP-adress för hantering och verifiera dess inbyggda brandväggsskydd
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 45ddf1c75dd22f5074c2017185bc0ed3be0b2a80
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872704"
---
# <a name="discover-azure-sql-database-managed-instance-management-endpoint"></a>Upptäck Azure SQL Database Managed Instance hanteringsslutpunkten

Azure SQL Database Managed Instance [virtuellt kluster](sql-database-managed-instance-connectivity-architecture.md) innehåller en hanteringsslutpunkt som Microsoft använder för att hantera den hanterade instansen. Hanteringsslutpunkten skyddas med inbyggda brandväggen på nätverket och det ömsesidig certifikatverifiering på programnivå.

När anslutningar öppnas från inuti den hanterade instansen (säkerhetskopiering, granskningsloggen) visas den som trafiken kommer ifrån från management endpoint offentlig IP-adress. Du kan begränsa åtkomst till offentliga tjänster från hanterad instans genom att ställa in brandväggsregler för att tillåta hanterad instans-IP-adressen.

> [!NOTE]
> Detta gäller inte ställa in brandväggsregler för Azure-tjänster som finns i samma region som hanterad instans som Azure-plattformen har en optimering för trafik som går mellan de tjänster som är samordnad.

Den här artikeln förklarar hur du kan få management endpoint offentliga IP-adress och hur för att verifiera dess inbyggda brandväggsskydd.

## <a name="finding-the-management-endpoint-public-ip-address"></a>Att hitta management endpoint offentliga IP-adress

Anta att Managed Instance värden är `mi-demo.xxxxxx.database.windows.net`. Kör `nslookup` med värdnamn.

![Lösa interna värdnamn](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Nu göra en annan `nslookup` för att ta bort markerade namn den `.vnet.` segment. Du får den offentliga IP-adressen till följd av det här kommandot körs.

![Matcha den offentliga IP-adressen](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="verifying-the-managed-instance-built-in-firewall"></a>Verifiera den inbyggda brandväggen för hanterad instans

Den hanterade instansen [obligatoriska inkommande säkerhetsregler](sql-database-managed-instance-vnet-configuration.md#mandatory-inbound-security-rules) kräver hanteringsportar 9000, 9003, 1438, 1440, 1452 vara öppna från **vilken källa som helst** på den Nätverkssäkerhetsgrupp (NSG) som skyddar hanterat Instans. Även om dessa portar är öppna på NSG-nivå, är de skyddade på nätverksnivå med den inbyggda brandväggen.

Använda någon skanner säkerhetsverktyget för att testa portarna för att kontrollera dessa portar. Följande skärmbild visar hur du använder något av dessa verktyg.

![Verifiera inbyggda brandväggen](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hanterade instanser och anslutning finns i [Azure SQL Database hanterad instans Anslutningsarkitektur](sql-database-managed-instance-connectivity-architecture.md).