---
title: Konfigurera och komma åt loggar för Azure Database för MySQL i Azure Portal
description: Den här artikeln beskriver hur du konfigurerar och få åtkomst till serverloggar i Azure Database for MySQL från Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: e0701d2e10b366a6bf849512484fb216c42823bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525890"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurera och öppna serverloggar i Azure portal

Du kan konfigurera, visa och hämta den [Azure Database for MySQL serverloggar](concepts-server-logs.md) från Azure-portalen.

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
Du kan visa en lista över tillgängliga loggar och hämta individuella loggfiler i fönstret serverloggar när loggning börjar. 

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
- Se [åtkomst till serverloggar i CLI](howto-configure-server-logs-in-cli.md) information om hur du hämtar loggar via programmering.
- Läs mer om [serverloggar](concepts-server-logs.md) i Azure Database för MySQL. 
- Mer information om parameterdefinitioner och MySQL-loggning finns i dokumentationen för MySQL på [loggar](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

