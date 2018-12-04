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
ms.openlocfilehash: 99ec559429d66becc20e038e43349f5369afac39
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856757"
---
# <a name="discover-azure-sql-database-managed-instance-management-endpoint"></a>Upptäck Azure SQL Database Managed Instance hanteringsslutpunkten 

## <a name="overview"></a>Översikt
Azure SQL Managed Instance [virtuellt kluster](sql-database-managed-instance-connectivity-architecture.md) innehåller hanteringsslutpunkten som Microsoft använder för att hantera instansen.  

Hanteringsslutpunkten skyddas med inbyggda brandväggen på nätverket och det ömsesidig certifikatverifiering på programnivå. 

När anslutningar öppnas från inuti den hanterade instansen (CLR, länkad server, säkerhetskopiering, granskningsloggen osv) verkar det som trafiken kommer ifrån från management endpoint offentlig IP-adress. Du kan begränsa åtkomst till offentliga tjänster från hanterad instans genom att ställa in brandväggsregler som tillåter bara den här IP-adress.

> [!NOTE]
> Detta gäller inte ställa in brandväggsregler för Azure-tjänster som finns i samma region som hanterad instans som plattform har optimering för trafik som går mellan de tjänster som är samordnad. 

Den här artikeln förklarar hur du kan få management endpoint offentliga IP-adress och hur för att verifiera dess inbyggda brandväggsskydd.

## <a name="finding-management-endpoint-public-ip-address"></a>Att hitta management endpoint offentlig IP-adress
Anta att MI värden är _mi demo.xxxxxx.database.windows.net_. Kör _nslookup_ med värdnamn.

![Lösa interna värdnamn](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Nu göra en annan _nslookup_ för markerade namn med removed_.vnet._segment. Du får den offentliga IP-adressen till följd av det här kommandot körs.

![Matcha den offentliga IP-adressen](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="verifying-managed-instance-built-in-firewall"></a>Verifiera Managed Instance inbyggda brandväggen
Hanterad instans [obligatoriska inkommande säkerhetsregler](sql-database-managed-instance-vnet-configuration.md#mandatory-inbound-security-rules) kräver hanteringsportar 9000, 9003, 1438, 1440, 1452 vara öppna från valfri källa på Nätverkssäkerhetsgruppen som skyddar den hanterade instansen. Även om dessa portar är öppna på NSG-nivå, är de skyddade på nätverksnivå med inbyggda brandväggen.

Du kan använda någon skanner säkerhetsverktyget för att testa de här portarna för att kontrollera dessa portar. Följande skärmbild visar hur du använder något av dessa verktyg.

![Verifiera inbyggda brandväggen](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)
