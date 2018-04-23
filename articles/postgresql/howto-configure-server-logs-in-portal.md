---
title: Konfigurera och komma åt serverloggen för PostgreSQL i Azure Portal
description: Den här artikeln beskriver hur du konfigurerar och komma åt de server-loggarna i Azure-databas för PostgreSQL från Azure Portal.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: aa9823c65b342f922ca78a51ecd3055dfac62869
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurera och åtkomst loggas i Azure-portalen

Du kan konfigurera, visa och hämta den [Azure-databas för PostgreSQL serverloggen](concepts-server-logs.md) från Azure-portalen.

## <a name="prerequisites"></a>Krav
Du behöver följande för att gå igenom den här instruktioner:
- [Azure-databas för PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till frågeloggar och i felloggarna. 

1. Logga in på [Azure-portalen](http://portal.azure.com/).

2. Välj din Azure-databas för PostgreSQL-servern.

3. Under den **övervakning** avsnitt i sidopanelen, Välj **serverloggen**. 

   ![Välj Server-loggar och välj ”Klicka här om du vill aktivera...”](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Markera rubriken **Klicka här för att aktivera loggar och konfigurera parametrar för loggen** att se de server-parametrarna.

5. Ändra de parametrar som du måste justera. Alla ändringar du gör i den här sessionen är markerade i lila.

   När du har ändrat parametrarna kan du klicka på **spara**. Du kan också **Ignorera** ändringarna. 

   ![Lång lista med parametrar med ändringar att spara eller ta bort](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Återgå till listan loggar genom att klicka på den **stängningsknappen** (X ikonen) på den **serverparametrar** sidan.

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
När loggningen börjar kan du visa en lista över tillgängliga loggar och hämta individuella loggfiler i rutan i serverloggen. 

1. Öppna Azure Portal.

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
- Mer information om parameterdefinitioner och PostgreSQL loggning finns i dokumentationen för PostgreSQL på [felrapportering och loggning](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

