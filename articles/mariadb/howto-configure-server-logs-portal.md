---
title: Konfigurera och få åtkomst till Server loggar för Azure Database for MariaDB i Azure Portal
description: Den här artikeln beskriver hur du konfigurerar och kommer åt Server loggar i Azure Database for MariaDB från Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: c8be9519d3393330b3022fadd2de6a49e58ecdcf
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703504"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurera och få åtkomst till Server loggar i Azure Portal

Du kan konfigurera, lista och ladda ned [Azure Database for MariaDB långsamma frågemeddelanden](concepts-server-logs.md) från Azure Portal.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- [Azure Database for MariaDB Server](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till den långsamma fråge loggen. 

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Välj din Azure Database for MariaDB-Server.

3. Under avsnittet **övervakning** på sid panelen väljer du **Server loggar**. 
   ![Select-Server loggar, klicka för att konfigurera @ no__t-1

4. Välj rubriken **Klicka här för att aktivera loggar och konfigurera logg parametrar** för att se server parametrarna.

5. Ändra parametrarna som du behöver justera, inklusive att aktivera "slow_query_log" till "ON". Alla ändringar du gör i den här sessionen är markerade i lila. 

   När du har ändrat parametrarna kan du klicka på **Spara**. Eller så kan du **Ignorera** dina ändringar.

   ![Klicka på Spara eller ta bort](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. Gå tillbaka till listan över loggar genom att klicka på **knappen Stäng** (X-ikon) på sidan **Server parametrar** .

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
När du har börjat logga in kan du Visa en lista över tillgängliga långsamma frågemeddelanden och hämta enskilda loggfiler i fönstret Server loggar. 

1. Öppna Azure Portal.

2. Välj din Azure Database for MariaDB-Server.

3. Under avsnittet **övervakning** på sid panelen väljer du **Server loggar**. Sidan visar en lista över loggfilerna som visas:

   ![Lista över loggar](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Namngivnings konventionen för loggen är **MySQL-långsamma-< ditt Server namn >-yyyymmddhh. log**. Datum och tid som används i fil namnet är tiden när loggen utfärdades. Loggfiler roteras var 24: e timme eller 7,5 GB, beroende på vilket som kommer först.

4. Om det behövs kan du använda **sökrutan** för att snabbt begränsa till en specifik logg baserat på datum/tid. Sökningen har samma namn som loggen.

5. Hämta enskilda loggfiler med knappen **Hämta** (nedåtpil) bredvid varje loggfil i tabell raden som visas:

   ![Klicka på ikonen Ladda ned](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

1. Under avsnittet **övervakning** i sid panelen väljer du **diagnostikinställningar**.

1. Klicka på "+ Lägg till diagnostisk inställning" ![Add diagnostisk inställning @ no__t-1

1. Ange ett namn på en diagnostisk inställning.

1. Ange vilka data mottagare som ska skicka långsamma frågemeddelanden (lagrings konto, händelsehubben och/eller Log Analytics arbets yta).

1. Välj "MySqlSlowLogs" som logg typ.
@no__t 0Configure-diagnostik @ no__t-1

1. När du har konfigurerat data Sinks att skicka in de långsamma frågeresultaten till kan du klicka på **Spara**.
@no__t 0Save-diagnostik @ no__t-1

1. Få åtkomst till långsamma fråga-loggar genom att utforska dem i de data mottagare du konfigurerade. Det kan ta upp till 10 minuter innan loggarna visas.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du hämtar långsamma frågemeddelanden via programmering finns i [komma åt långsamma fråge loggar i CLI](howto-configure-server-logs-cli.md) .
- Läs mer om [långsamma frågemeddelanden](concepts-server-logs.md) i Azure Database for MariaDB.
- Mer information om parameter definitioner och loggning finns i MariaDB-dokumentationen för [loggar](https://mariadb.com/kb/en/library/slow-query-log-overview/).