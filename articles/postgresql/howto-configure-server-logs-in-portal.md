---
title: Hantera loggar - Azure-portal - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du konfigurerar och kommer åt serverloggarna (.loggfiler) i Azure Database for PostgreSQL - Single Server från Azure-portalen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763699"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Konfigurera och komma åt Azure Database för PostgreSQL - Single Server-loggar från Azure-portalen

Du kan konfigurera, lista och hämta [Azure-databasen för PostgreSQL-loggar](concepts-server-logs.md) från Azure-portalen.

## <a name="prerequisites"></a>Krav
Stegen i den här artikeln kräver att du har [Azure Database för PostgreSQL-server](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till frågeloggar och felloggar. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj din Azure Database for PostgreSQL-server.

3. Under avsnittet **Övervakning** i sidofältet väljer du **Serverloggar**. 

   ![Skärmbild av serverloggar](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Om du vill se serverparametrarna väljer du **Klicka här för att aktivera loggar och konfigurera loggparametrar**.

5. Ändra de parametrar som du behöver justera. Alla ändringar du gör i den här sessionen markeras i lila.

   När du har ändrat parametrarna väljer du **Spara**. Eller så kan du ignorera ändringarna. 

   ![Skärmbild av alternativ för serverparametrar](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

På sidan **Serverparametrar** kan du gå tillbaka till listan med loggar genom att stänga sidan.

## <a name="view-list-and-download-logs"></a>Visa list- och nedladdningsloggar
När loggningen har börjat kan du visa en lista över tillgängliga loggar och hämta enskilda loggfiler. 

1. Öppna Azure Portal.

2. Välj din Azure Database for PostgreSQL-server.

3. Under avsnittet **Övervakning** i sidofältet väljer du **Serverloggar**. Sidan visar en lista över dina loggfiler.

   ![Skärmbild av sidan Serverloggar, med en lista över loggar markerade](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Namngivningskonventionen för loggen är **postgresql-yyyy-mm-dd_hh0000.log**. Datum och tid som används i filnamnet är den tidpunkt då loggen utfärdades. Loggfilerna roterar varje timme eller 100 MB, beroende på vilket som inträffar först.

4. Om det behövs kan du använda sökrutan för att snabbt begränsa till en viss logg, baserat på datum och tid. Sökningen är på namnet på loggen.

   ![Skärmbild av sidan Serverloggar, med sökruta och resultat markerade](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Om du vill hämta enskilda loggfiler markerar du nedpilsikonen bredvid varje loggfil på tabellraden.

   ![Skärmbild av sidan Serverloggar med nedpilsikonen markerad](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Nästa steg
- Se [Access-serverloggar i CLI](howto-configure-server-logs-using-cli.md) om du vill lära dig hur du hämtar loggar programmässigt.
- Läs mer om [serverloggar](concepts-server-logs.md) i Azure Database för PostgreSQL. 
- Mer information om parameterdefinitionerna och PostgreSQL-loggningen finns i PostgreSQL-dokumentationen om [felrapportering och loggning](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

