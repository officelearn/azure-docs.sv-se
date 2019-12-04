---
title: Åtkomst till långsamma Query-loggar – Azure Portal-Azure Database for MariaDB
description: Den här artikeln beskriver hur du konfigurerar och kommer åt långsamma frågemeddelanden i Azure Database for MariaDB från Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 69a01ec021ecbade235a693b1be502353420fde0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767474"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Konfigurera och komma åt långsamma Query-loggar från Azure Portal

Du kan konfigurera, lista och ladda ned [Azure Database for MariaDB långsamma frågemeddelanden](concepts-server-logs.md) från Azure Portal.

## <a name="prerequisites"></a>Krav
Anvisningarna i den här artikeln kräver att du har [Azure Database for MariaDB Server](quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till den långsamma fråge loggen. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj din Azure Database for MariaDB-Server.

3. Under avsnittet **övervakning** på sid panelen väljer du **Server loggar**. 
   ![skärm bild av alternativen för Server loggar](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Om du vill se server parametrarna väljer du **Klicka här för att aktivera loggar och konfigurera logg parametrar**.

5. Ändra parametrarna som du behöver justera, inklusive att aktivera **slow_query_log** till **på**. Alla ändringar du gör i den här sessionen är markerade i lila. 

   När du har ändrat parametrarna väljer du **Spara**. Du kan också ignorera ändringarna.

   ![Skärm bild av alternativ för Server parametrar](./media/howto-configure-server-logs-portal/3-save-discard.png)

På sidan **Server parametrar** kan du gå tillbaka till listan över loggar genom att stänga sidan.

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
När loggningen har påbörjats kan du Visa en lista över tillgängliga långsamma frågemeddelanden och hämta enskilda loggfiler. 

1. Öppna Azure Portal.

2. Välj din Azure Database for MariaDB-Server.

3. Under avsnittet **övervakning** på sid panelen väljer du **Server loggar**. Sidan visar en lista över loggfilerna.

   ![Skärm bild av sidan Server loggar med lista över loggar markerade](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Namngivnings konventionen för loggen är **MySQL-långsamma-< ditt Server namn >-yyyymmddhh. log**. Datum och tid som används i fil namnet är den tidpunkt då loggen utfärdades. Loggfilerna roteras var 24: e timme eller 7,5 GB, beroende på vilket som kommer först.

4. Om det behövs kan du använda sökrutan för att snabbt begränsa till en specifik logg baserat på datum och tid. Sökningen har samma namn som loggen.

5. Om du vill hämta enskilda loggfiler väljer du nedåtpilen bredvid varje loggfil i tabell raden.

   ![Skärm bild av sidan Server loggar med nedåtpil ikon markerad](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

1. Under avsnittet **övervakning** i sid panelen väljer du **diagnostikinställningar** > **Lägg till diagnostikinställningar**.

   ![Skärm bild av alternativ för diagnostikinställningar](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Ange ett namn på en diagnostisk inställning.

1. Ange vilka data mottagare som de långsamma frågarna ska skickas till (lagrings konto, händelsehubben eller Log Analytics arbets yta).

1. Välj **MySqlSlowLogs** som logg typ.
![skärm bild av konfigurations alternativ för diagnostikinställningar](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. När du har konfigurerat data Sinks att skicka in de långsamma frågeresultaten till väljer du **Spara**.
![skärm bild av konfigurations alternativ för diagnostikinställningar med Spara markerat](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Få åtkomst till långsamma fråga-loggar genom att utforska dem i de data mottagare du konfigurerade. Det kan ta upp till 10 minuter innan loggarna visas.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du hämtar långsamma frågemeddelanden via programmering finns i [komma åt långsamma fråge loggar i CLI](howto-configure-server-logs-cli.md) .
- Läs mer om [långsamma frågemeddelanden](concepts-server-logs.md) i Azure Database for MariaDB.
- Mer information om parameter definitioner och loggning finns i MariaDB-dokumentationen för [loggar](https://mariadb.com/kb/en/library/slow-query-log-overview/).