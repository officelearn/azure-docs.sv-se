---
title: Granskningsloggar för Access - Azure-portal - Azure Database för MySQL
description: I den här artikeln beskrivs hur du konfigurerar och får åtkomst till granskningsloggarna i Azure Database for MySQL från Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 188ef3a1b9777c37f8557a69e19887638a973611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062534"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Konfigurera och komma åt granskningsloggar för Azure Database för MySQL i Azure-portalen

Du kan konfigurera [Azure Database for MySQL-granskningsloggar](concepts-audit-logs.md) och diagnostikinställningar från Azure-portalen.

> [!IMPORTANT]
> Granskningsloggfunktionen är för närvarande i förhandsgranskning.

## <a name="prerequisites"></a>Krav

För att gå igenom den här guiden behöver du:

- [Azure-databas för MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurera granskningsloggning

Aktivera och konfigurera granskningsloggning.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj din Azure-databas för MySQL-server.

1. Välj **Serverparametrar**under avsnittet **Inställningar** i sidofältet .
    ![Serverparametrar](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Uppdatera **parametern audit_log_enabled** till ON.
    ![Aktivera granskningsloggar](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Välj de [händelsetyper](concepts-audit-logs.md#configure-audit-logging) som ska loggas genom att uppdatera **parametern audit_log_events.**
    ![Granskningslogghändelser](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Lägg till alla MySQL-användare som ska uteslutas från loggning genom att uppdatera **parametern audit_log_exclude_users.** Ange användare genom att ange deras MySQL-användarnamn.
    ![Granskningslogg utesluter användare](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. När du har ändrat parametrarna kan du klicka på **Spara**. Eller så kan du **ignorera** ändringarna.
    ![Spara](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

1. Under avsnittet **Övervakning** i sidofältet väljer du **Diagnostikinställningar**.

1. Klicka på "+ Lägg ![till diagnostikinställning" Lägg till diagnostikinställning](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Ange ett diagnostikinställningsnamn.

1. Ange vilka datamottagare som ska skickas till granskningsloggarna (lagringskonto, händelsenav och/eller Logganalysarbetsyta).

1. Välj "MySqlAuditLogs" som loggtyp.
![Konfigurera diagnostikinställning](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. När du har konfigurerat datamottagarena så att granskningsloggarna har förts till kan du klicka på **Spara**.
![Spara diagnostikinställning](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Öppna granskningsloggarna genom att utforska dem i de datamottagare som du har konfigurerat. Det kan ta upp till 10 minuter innan loggarna visas.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [granskningsloggar](concepts-audit-logs.md) i Azure Database för MySQL.