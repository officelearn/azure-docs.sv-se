---
title: Konfigurera och komma åt loggar för Azure Database for MariaDB i Azure Portal
description: Den här artikeln beskriver hur du konfigurerar och få åtkomst till serverloggar i Azure Database for MariaDB från Azure Portal.
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 566d5b047078b6e65b0c320027cc8c63c81a282b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986607"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurera och öppna serverloggar i Azure portal

Du kan konfigurera, visa och hämta den [Azure Database for MariaDB serverloggar](concepts-server-logs.md) från Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- [Azure Database for MariaDB-server](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till långsam frågelogg. 

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Välj din Azure Database for MariaDB-server.

3. Under den **övervakning** avsnittet i sidopanelen, Välj **serverloggar**. 
   ![Välj loggar Klicka för att konfigurera](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Markera rubriken **Klicka här för att aktivera loggar och konfigurera loggparametrarna** att se de serverparametrarna.

5. Ändra de parametrar som du behöver justera, inklusive att stänga ”slow_query_log” till ”på”. Alla ändringar du gör i den här sessionen är markerade i lila. 

   När du har ändrat parametrarna kan du klicka på **spara**. Eller så kan du **Ignorera** dina ändringar.

   ![Klicka på Spara eller ta bort](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. Gå tillbaka till listan över loggarna genom att klicka på den **stängningsknappen** (X ikonen) på den **serverparametrar** sidan.

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
Du kan visa en lista över tillgängliga loggar och hämta individuella loggfiler i fönstret serverloggar när loggning börjar. 

1. Öppna Azure Portal.

2. Välj din Azure Database for MariaDB-server.

3. Under den **övervakning** avsnittet i sidopanelen, Välj **serverloggar**. På sidan visas en lista över loggfilerna, som visas:

   ![Lista över loggar](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Namngivningskonventionen loggens är **mysql - långsam - < servernamn >-yyyymmddhh.log**. Datum och tid som används i filnamnet är tid när loggen har utfärdats. Loggfiler roteras var 24 timmar eller 7,5 GB, beroende på vilket som kommer först.

4. Om du behöver den **sökrutan** snabbt begränsa till en viss loggning baserat på datum/tid. Sökningen är på namnet på loggen.

5. Ladda ned enskilda loggfiler med hjälp av den **hämta** knappen (nedåt-ikon) bredvid varje loggfil i tabellraden som visas:

   ![Klicka på nedladdningsikonen](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="next-steps"></a>Nästa steg
- Läs mer om [serverloggar](concepts-server-logs.md) i Azure Database for MariaDB.
- Mer information om parameterdefinitioner och loggning finns i dokumentationen för MariaDB på [loggar](https://mariadb.com/kb/en/library/slow-query-log-overview/).

<!-- - See [Access Server Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download logs programmatically. -->