---
title: "Konfigurera och komma åt serverloggen för PostgreSQL i Azure Portal | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar och komma åt de server-loggarna i Azure-databas för PostgreSQL från Azure Portal."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: 388af25aa7cf623fa3ff4167943cf94fb5c566d8
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurera och åtkomst loggas i Azure-portalen

Du kan konfigurera, visa och hämta den [Azure-databas för MySQL serverloggen](concepts-server-logs.md) från Azure-portalen.

## <a name="prerequisites"></a>Krav
Du behöver följande för att gå igenom den här instruktioner:
- [Azure-databas för PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till frågeloggar och i felloggarna. 

1. Logga in på [Azure Portal](http://portal.azure.com/).

2. Välj din Azure-databas för PostgreSQL-servern.

3. Under den **övervakning** avsnitt i sidopanelen, Välj **serverloggen**. 

   ![Välj Server-loggar och välj ”Klicka här om du vill aktivera...”](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Markera rubriken **Klicka här för att aktivera loggar och konfigurera parametrar för loggen** att se de server-parametrarna.

5. Välj den **visa fler** expander att se en utökad lista över tillgängliga parametrar. 

   Mer information om definitionerna av parametrar finns i dokumentationen för PostgreSQL på [felrapportering och loggning](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

   ![Kort lista över loggen parametrar. Klicka på Visa fler för lång](./media/howto-configure-server-logs-in-portal/2-show-more.png)

6. Ändra de parametrar som du måste justera. Alla ändringar du gör i den här sessionen är markerade i lila.

   När du har ändrat parametrarna kan du klicka på **spara**. Du kan också **Ignorera** ändringarna. 

   ![Lång lista med parametrar med ändringar att spara eller ta bort](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

7. Återgå till listan loggar genom att klicka på den **stängningsknappen** (X ikonen) på den **serverparametrar** sidan.

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
När loggningen börjar kan du visa en lista över tillgängliga loggar och hämta individuella loggfiler i rutan i serverloggen. 

1. Öppna Azure-portalen.

2. Välj din Azure-databas för PostgreSQL-servern.

3. Under den **övervakning** avsnitt i sidopanelen, Välj **serverloggen**. Sidan visas en lista över loggfilerna, som visas:

   ![Loggar serverlista](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Namngivningskonventionen för loggen är **postgresql-åååå-mm-dd_hh0000.log**. Datum och tid som används i filnamnet är tid när loggen har utfärdats. Loggfilerna rotera var en timme eller 100 MB storlek, beroende på vilket som inträffar först.

4. Om det behövs kan du använda den **sökrutan** att snabbt begränsa till en specifik logg baserat på tid/datum. Sökningen är på namnet på loggen.

   ![Exempelsökning på loggen namn](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Hämta individuella loggfiler med hjälp av den **hämta** knappen (nedåt-ikon) bredvid varje loggfil i tabellraden som visas:

   ![Klicka på ladda ned-ikon](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Nästa steg
- Se [Access Server-loggar i CLI](howto-configure-server-logs-using-cli.md) att lära dig hur du hämtar loggarna programmässigt.
- Lär dig mer om [serverloggen](concepts-server-logs.md) i Azure DB för PostgreSQL. 
- Mer information om parameterdefinitioner och PostgreSQL loggning finns i dokumentationen för PosgreSQL på [felrapportering och loggning](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

