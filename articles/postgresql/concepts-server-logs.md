---
title: Serverloggar i Azure Database for PostgreSQL
description: Den här artikeln beskriver hur Azure Database för PostgreSQL genererar loggar frågan och fel och logga hur kvarhållning har konfigurerats.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: bcca8ce8d11482dd8517992297b7e8a5b94ac8b1
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435498"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Serverloggar i Azure Database for PostgreSQL 
Azure Database för PostgreSQL genererar fråge- och loggar. Åtkomst till transaktionsloggar stöds dock inte. Fråga och felloggar kan användas för att identifiera, felsöka och reparera konfigurationsfel och icke-optimal prestanda. Mer information finns i [felrapportering och loggning](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html).

## <a name="access-server-logs"></a>Åtkomst till serverloggar
Du kan visa och hämta med hjälp av Azure portal, Azure PostgreSQL-server-felloggarna [Azure CLI](howto-configure-server-logs-using-cli.md), och Azure REST API: er.

## <a name="log-retention"></a>Kvarhållning av logg
Du kan ställa in kvarhållningsperioden system-loggar med hjälp av den **log\_kvarhållning\_period** parameter som är associerade med servern. Enheten för den här parametern är dagar. Standardvärdet är tre dagar. Det maximala värdet är 7 dagar. Servern måste ha tillräckligt med allokerat lagringsutrymme så att den innehåller sparade filerna.
Loggfilerna rotera var en timme eller storlek på 100 MB, beroende på vilket som inträffar först.

## <a name="configure-logging-for-azure-postgresql-server"></a>Konfigurera loggning för Azure PostgreSQL-server
Du kan aktivera loggning av frågor och felloggning för servern. Felloggarna kan innehålla information om automatisk vakuum, anslutning och kontrollpunkter.

Du kan aktivera loggning av frågor för din instans av en PostgreSQL-databas genom att ange två serverparametrar: `log_statement` och `log_min_duration_statement`.

Den **log\_instruktionen** parameter styr vilka SQL-uttryck är inloggad. Vi rekommenderar att ställa in den här parametern ***alla*** logga alla instruktioner; standardvärdet är ingen.

Den **log\_min\_varaktighet\_instruktionen** parametern anger gränsen i millisekunder för ett uttryck som ska loggas. Alla SQL-instruktioner som körs längre än parameterinställningen loggas. Den här parametern är inaktiverad och inställd på minus 1 (-1) som standard. Aktivera den här parametern kan vara användbart vid spårning av icke-optimerade frågor i dina program.

Den **log\_min\_meddelanden** gör att du kan styra vilka meddelande nivåer skrivs till serverloggen. Standardvärdet är en varning. 

Mer information om dessa inställningar finns i [felrapportering och loggning](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) dokumentation. Konfigurera särskilt Azure Database för PostgreSQL-serverparametrar, finns i [anpassa konfigurationsparametrar för server med Azure CLI](howto-configure-server-parameters-using-cli.md).

## <a name="next-steps"></a>Nästa steg
- Du hittar loggar med hjälp av kommandoradsgränssnittet för Azure CLI i [konfigurera och åtkomst till server-loggar med Azure CLI](howto-configure-server-logs-using-cli.md).
- Mer information om serverparametrar finns [anpassa konfigurationsparametrar för server med Azure CLI](howto-configure-server-parameters-using-cli.md).
