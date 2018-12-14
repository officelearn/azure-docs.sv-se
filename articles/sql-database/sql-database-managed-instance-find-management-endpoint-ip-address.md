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
ms.date: 12/04/2018
ms.openlocfilehash: 83eea565a12ee5201c42b543cdbdad72ddc28ca9
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346727"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Fastställa slutpunktens IP-adress för hantering

Det virtuella klustret i Azure SQL Database Managed Instance innehåller en hanteringsslutpunkt som Microsoft använder för hanteringsåtgärder. Hanteringsslutpunkten är skyddad med en inbyggd brandvägg i nätverket och det ömsesidig certifikatverifiering på programnivå. Du kan fastställa IP-adressen för hanteringsslutpunkten, men du kan inte komma åt den här slutpunkten.

## <a name="determine-ip-address"></a>Fastställa IP-adress

Anta att Managed Instance värden är `mi-demo.xxxxxx.database.windows.net`. Kör `nslookup` med värdnamn.

![Lösa interna värdnamn](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Nu göra en annan `nslookup` för att ta bort markerade namn den `.vnet.` segment. Du får den offentliga IP-adressen till följd av det här kommandot körs.

![Matcha den offentliga IP-adressen](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)


## <a name="next-steps"></a>Nästa steg

Mer information om hanterade instanser och anslutning finns i [Azure SQL Database hanterad instans Anslutningsarkitektur](sql-database-managed-instance-connectivity-architecture.md).
