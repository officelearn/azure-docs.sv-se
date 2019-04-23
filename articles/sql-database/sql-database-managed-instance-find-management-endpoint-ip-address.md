---
title: Identifiera hanteringsslutpunkten för Azure SQL Database Managed Instance | Microsoft Docs
description: Lär dig hur du hämtar Azure SQL Database Managed Instance endpoint offentliga IP-adress för hantering och verifiera dess inbyggda brandväggsskydd
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b7eb9ecd6b94aad263346ad6b5c45b694e0bd46f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797966"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Fastställa slutpunktens IP-adress för hantering

Det virtuella klustret i Azure SQL Database Managed Instance innehåller en hanteringsslutpunkt som Microsoft använder för hanteringsåtgärder. Hanteringsslutpunkten är skyddad med en inbyggd brandvägg i nätverket och det ömsesidig certifikatverifiering på programnivå. Du kan fastställa IP-adressen för hanteringsslutpunkten, men du kan inte komma åt den här slutpunkten.

## <a name="determine-ip-address"></a>Fastställa IP-adress

Anta att Managed Instance värden är `mi-demo.xxxxxx.database.windows.net`. Kör `nslookup` med värdnamn.

![Lösa interna värdnamn](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Nu göra en annan `nslookup` för att ta bort markerade namn den `.vnet.` segment. Du får den offentliga IP-adressen när du har kört kommandot.

![Matcha den offentliga IP-adressen](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hanterade instanser och anslutning finns i [Azure SQL Database hanterad instans Anslutningsarkitektur](sql-database-managed-instance-connectivity-architecture.md).
