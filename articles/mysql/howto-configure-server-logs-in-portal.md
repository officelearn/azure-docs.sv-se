---
title: Komma åt långsamma frågeloggar - Azure-portalen - Azure Database för MySQL
description: I den här artikeln beskrivs hur du konfigurerar och får åtkomst till långsamma loggar i Azure Database för MySQL från Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 0261ff7ca8a60dc5fd986a64b9944f9cb9f101e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062502"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Konfigurera och komma åt långsamma frågeloggar från Azure-portalen

Du kan konfigurera, lista och hämta [Azure Database for MySQL långsamma frågeloggar](concepts-server-logs.md) från Azure-portalen.

## <a name="prerequisites"></a>Krav
Stegen i den här artikeln kräver att du har [Azure Database för MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till mysql-långsam frågelogg. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj din Azure-databas för MySQL-server.

3. Under avsnittet **Övervakning** i sidofältet väljer du **Serverloggar**. 
   ![Skärmbild av serverloggar](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Om du vill se serverparametrarna väljer du **Klicka här för att aktivera loggar och konfigurera loggparametrar**.

5. Ändra de parametrar som du behöver justera. Alla ändringar du gör i den här sessionen markeras i lila. 

   När du har ändrat parametrarna väljer du **Spara**. Eller så kan du ignorera ändringarna.

   ![Skärmbild av alternativ för serverparametrar](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

På sidan **Serverparametrar** kan du gå tillbaka till listan med loggar genom att stänga sidan.

## <a name="view-list-and-download-logs"></a>Visa list- och nedladdningsloggar
När loggningen har påbörjats kan du visa en lista över tillgängliga långsamma frågeloggar och hämta enskilda loggfiler.

1. Öppna Azure Portal.

2. Välj din Azure-databas för MySQL-server.

3. Under avsnittet **Övervakning** i sidofältet väljer du **Serverloggar**. Sidan visar en lista över dina loggfiler.

   ![Skärmbild av sidan Serverloggar, med en lista över loggar markerade](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Namngivningskonventionen för loggen är **mysql-slow-< din server namn>-yyymmddhh.log**. Datum och tid som används i filnamnet är den tidpunkt då loggen utfärdades. Loggfiler roteras var 24:e timme eller 7,5 GB, beroende på vilket som inträffar först. 

4. Om det behövs kan du använda sökrutan för att snabbt begränsa till en viss logg, baserat på datum och tid. Sökningen är på namnet på loggen.

5. Om du vill hämta enskilda loggfiler markerar du nedpilsikonen bredvid varje loggfil på tabellraden.

   ![Skärmbild av sidan Serverloggar med nedpilsikonen markerad](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

1. Under avsnittet **Övervakning** i sidofältet väljer du **Diagnostikinställningar** > **Lägg till diagnostikinställningar**.

   ![Skärmbild av alternativ för diagnostikinställningar](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Ange ett diagnostikinställningsnamn.

1. Ange vilka datamottagare som ska skickas till de långsamma frågeloggarna (lagringskonto, händelsenav eller Logganalysarbetsyta).

1. Välj **MySqlSlowLogs** som loggtyp.
![Skärmbild av konfigurationsalternativ för diagnostikinställningar](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. När du har konfigurerat datamottagarena så att de långsamma frågeloggarna ska ledas till väljer du **Spara**.
![Skärmbild av konfigurationsalternativ för diagnostikinställningar, med Spara markerat](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. Öppna de långsamma frågeloggarna genom att utforska dem i de datamottagare som du har konfigurerat. Det kan ta upp till 10 minuter innan loggarna visas.

## <a name="next-steps"></a>Nästa steg
- Se [Snabb fråga För långsam åtkomst Loggar i CLI](howto-configure-server-logs-in-cli.md) om du vill lära dig hur du hämtar långsamma frågeloggar programmässigt.
- Läs mer om [långsamma frågeloggar](concepts-server-logs.md) i Azure Database för MySQL.
- Mer information om parameterdefinitionerna och MySQL-loggningen finns i MySQL-dokumentationen i [loggar .](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)