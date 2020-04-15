---
title: Granskningsloggar för Access - Azure-portal - Azure Database för MySQL
description: I den här artikeln beskrivs hur du konfigurerar och får åtkomst till granskningsloggarna i Azure Database for MySQL från Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: fcd436f87b24d8cdc246d2f1d33a49311e72d46b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380289"
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

- Läs mer om [granskningsloggar](concepts-audit-logs.md) i Azure Database for MySQL
- Lär dig hur du konfigurerar granskningsloggar i [Azure CLI](howto-configure-audit-logs-cli.md)