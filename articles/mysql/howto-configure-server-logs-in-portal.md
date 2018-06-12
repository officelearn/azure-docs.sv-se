---
title: Konfigurera och komma åt serverloggen för Azure-databas för MySQL på Azure-portalen
description: Den här artikeln beskriver hur du konfigurerar och komma åt de server-loggarna i Azure-databas för MySQL från Azure Portal.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: eb35563bc21fc48d304f216e7b34cc9a77f35e83
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265370"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurera och åtkomst loggas i Azure-portalen

Du kan konfigurera, visa och hämta den [Azure-databas för MySQL serverloggen](concepts-server-logs.md) från Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att gå igenom den här instruktioner:
- [Azure-databas för MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till loggfilen för MySQL långsam frågor. 

1. Logga in på [Azure-portalen](http://portal.azure.com/).

2. Välj din Azure-databas för MySQL-servern.

3. Under den **övervakning** avsnitt i sidopanelen, Välj **serverloggen**. 
   ![Välj Server-loggar, klicka för att konfigurera](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Markera rubriken **Klicka här för att aktivera loggar och konfigurera parametrar för loggen** att se de server-parametrarna.

5. Ändra de parametrar som du måste justera. Alla ändringar du gör i den här sessionen är markerade i lila. 

   När du har ändrat parametrarna kan du klicka på **spara**. Du kan också **Ignorera** ändringarna.

   ![Klicka på Spara eller ta bort](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Återgå till listan loggar genom att klicka på den **stängningsknappen** (X ikonen) på den **serverparametrar** sidan.

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
När loggningen börjar kan du visa en lista över tillgängliga loggar och hämta individuella loggfiler i rutan i serverloggen. 

1. Öppna Azure Portal.

2. Välj din Azure-databas för MySQL-servern.

3. Under den **övervakning** avsnitt i sidopanelen, Välj **serverloggen**. Sidan visas en lista över loggfilerna, som visas:

   ![Lista över loggar](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Namngivningskonventionen för loggen är **mysql - långsam - < servernamn >-yyyymmddhh.log**. Datum och tid som används i filnamnet är tid när loggen har utfärdats. Loggfiler roteras var 24 timmar eller 7.5 GB, beroende på vilket som kommer först.

4. Om det behövs kan du använda den **sökrutan** att snabbt begränsa till en specifik logg baserat på tid/datum. Sökningen är på namnet på loggen.

5. Hämta individuella loggfiler med hjälp av den **hämta** knappen (nedåt-ikon) bredvid varje loggfil i tabellraden som visas:

   ![Klicka på ladda ned-ikon](./media/howto-configure-server-logs-in-portal/5-download.png)


## <a name="next-steps"></a>Nästa steg
- Se [Access Server-loggar i CLI](howto-configure-server-logs-in-cli.md) att lära dig hur du hämtar loggarna programmässigt.
- Lär dig mer om [serverloggen](concepts-server-logs.md) i Azure för MySQL-databas. 
- Mer information om parameterdefinitioner och MySQL loggning finns i dokumentationen för MySQL på [loggar](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

