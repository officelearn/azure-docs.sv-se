---
title: Konfigurera och komma åt långsamma frågeloggar för Azure Database för MySQL i Azure-portalen
description: Den här artikeln beskriver hur du konfigurerar och komma åt långsam loggarna i Azure Database för MySQL från Azure-portalen.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: b16ac525d41eb2423828a647fdb75fd3f4a80a31
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052712"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>Konfigurera och åtkomst långsam fråga loggar i Azure portal

Du kan konfigurera, visa och hämta den [Azure Database for MySQL långsamma frågeloggar](concepts-server-logs.md) från Azure-portalen.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom den här guiden, måste du:
- [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till den långsam frågeloggen för MySQL. 

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Välj din Azure Database for MySQL-server.

3. Under den **övervakning** avsnittet i sidopanelen, Välj **serverloggar**. 
   ![Välj loggar Klicka för att konfigurera](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Markera rubriken **Klicka här för att aktivera loggar och konfigurera loggparametrarna** att se de serverparametrarna.

5. Ändra de parametrar som du behöver justera. Alla ändringar du gör i den här sessionen är markerade i lila. 

   När du har ändrat parametrarna kan du klicka på **spara**. Eller så kan du **Ignorera** dina ändringar.

   ![Klicka på Spara eller ta bort](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Gå tillbaka till listan över loggarna genom att klicka på den **stängningsknappen** (X ikonen) på den **serverparametrar** sidan.

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
Du kan visa en lista över tillgängliga långsamma frågeloggar och hämta individuella loggfiler i fönstret serverloggar när loggning börjar.

1. Öppna Azure Portal.

2. Välj din Azure Database for MySQL-server.

3. Under den **övervakning** avsnittet i sidopanelen, Välj **serverloggar**. På sidan visas en lista över loggfilerna, som visas:

   ![Lista över loggar](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Namngivningskonventionen loggens är **mysql - långsam - < servernamn >-yyyymmddhh.log**. Datum och tid som används i filnamnet är tid när loggen har utfärdats. Loggfiler roteras var 24 timmar eller 7,5 GB, beroende på vilket som kommer först.

4. Om du behöver den **sökrutan** snabbt begränsa till en viss loggning baserat på datum/tid. Sökningen är på namnet på loggen.

5. Ladda ned enskilda loggfiler med hjälp av den **hämta** knappen (nedåt-ikon) bredvid varje loggfil i tabellraden som visas:

   ![Klicka på nedladdningsikonen](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="next-steps"></a>Nästa steg
- Se [åtkomst långsam fråga loggar i CLI](howto-configure-server-logs-in-cli.md) att lära dig hur du laddar ned långsamma frågeloggar programmässigt.
- Läs mer om [långsamma frågeloggar](concepts-server-logs.md) i Azure Database för MySQL.
- Mer information om parameterdefinitioner och MySQL-loggning finns i dokumentationen för MySQL på [loggar](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).