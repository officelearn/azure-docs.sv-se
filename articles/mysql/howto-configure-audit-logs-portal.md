---
title: Konfigurera och komma åt granskningsloggarna för Azure Database för MySQL i Azure-portalen
description: Den här artikeln beskriver hur du konfigurerar och få åtkomst till granskningsloggarna i Azure Database for MySQL från Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: dfa57b3f597ba1c01dc78421cc04074f7b6a858e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063055"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Konfigurera och komma åt granskningsloggar i Azure portal

Du kan konfigurera den [Azure Database for MySQL-granskningsloggar](concepts-audit-logs.md) och diagnostikinställningar från Azure-portalen.

> [!IMPORTANT]
> Granska loggen funktionen finns för närvarande i förhandsversion.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att gå igenom den här guiden, måste du:

- [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurera granskningsloggning

Aktivera och konfigurera granskningsloggning.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj din Azure Database for MySQL-server.

1. Under den **inställningar** avsnittet i sidopanelen, Välj **serverparametrar**.
    ![Serverparametrar](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Uppdatera den **audit_log_enabled** parametern on.
    ![Aktivera granskningsloggar](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Markera händelserna som loggas genom att uppdatera den **audit_log_events** parametern.
    ![Logga granskningshändelser](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Lägg till de MySQL-användarna som ska undantas från att logga in genom att uppdatera den **audit_log_exclude_users** parametern. Ange användare genom att ange deras användarnamn för MySQL.
    ![Granska loggen undanta användare](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. När du har ändrat parametrarna kan du klicka på **spara**. Eller så kan du **Ignorera** dina ändringar.
    ![Spara](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

1. Under den **övervakning** avsnittet i sidopanelen, Välj **diagnostikinställningar**.

1. Klicka på ”+ Lägg till diagnostikinställning” ![Lägg till diagnostikinställning](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Ange ett namn för diagnostikinställningar.

1. Ange vilka data egenskaperna för att skicka granskningsloggarna (storage-konto, händelsehubb och/eller Log Analytics-arbetsytan).

1. Välj ”MySqlAuditLogs” som loggtyp av.
![Konfigurera diagnostikinställningar](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. När du har konfigurerat datamottagare för att skicka vidare granskningsloggar för att du kan klicka på **spara**.
![Spara diagnostikinställning](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Få åtkomst till granskningsloggarna genom att utforska dem i datamottagare som du har konfigurerat. Det kan ta upp till 10 minuter innan loggarna visas.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [granskningsloggar](concepts-audit-logs.md) i Azure Database för MySQL.