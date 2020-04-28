---
title: Åtkomst gransknings loggar – Azure Portal-Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar och får åtkomst till gransknings loggarna i Azure Database for MySQL från Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: fcd436f87b24d8cdc246d2f1d33a49311e72d46b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81380289"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Konfigurera och få åtkomst till gransknings loggar för Azure Database for MySQL i Azure Portal

Du kan konfigurera [Azure Database for MySQL gransknings loggar](concepts-audit-logs.md) och diagnostikinställningar från Azure Portal.

> [!IMPORTANT]
> Gransknings logg funktionen är för närvarande en för hands version.

## <a name="prerequisites"></a>Krav

För att gå igenom den här instruktions guiden behöver du:

- [Azure Database for MySQL Server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurera gransknings loggning

Aktivera och konfigurera gransknings loggning.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj din Azure Database for MySQL-server.

1. Under avsnittet **Inställningar** på sid panelen väljer du **Server parametrar**.
    ![Serverparametrar](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Uppdatera **audit_log_enabled** -parametern till på.
    ![Aktivera gransknings loggar](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Välj de [händelse typer](concepts-audit-logs.md#configure-audit-logging) som ska loggas genom att uppdatera **audit_log_events** -parametern.
    ![Granska logg händelser](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Lägg till alla MySQL-användare som ska undantas från loggning genom att uppdatera **audit_log_exclude_users** -parametern. Ange användare genom att ange sitt användar namn för MySQL.
    ![Exkludera användare i gransknings logg](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. När du har ändrat parametrarna kan du klicka på **Spara**. Eller så kan du **Ignorera** dina ändringar.
    ![Spara](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

1. Under avsnittet **övervakning** i sid panelen väljer du **diagnostikinställningar**.

1. Klicka på "+ Lägg till diagnostisk inställning ![" Lägg till diagnostikinställningar](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Ange ett namn på en diagnostisk inställning.

1. Ange vilka data mottagare som ska skicka gransknings loggarna (lagrings konto, händelsehubben och/eller Log Analytics arbets yta).

1. Välj "MySqlAuditLogs" som logg typ.
![Konfigurera diagnostisk inställning](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. När du har konfigurerat data Sinks att skicka in gransknings loggarna till kan du klicka på **Spara**.
![Spara diagnostisk inställning](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Få åtkomst till gransknings loggarna genom att utforska dem i de data mottagare du konfigurerade. Det kan ta upp till 10 minuter innan loggarna visas.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [gransknings loggar](concepts-audit-logs.md) i Azure Database for mysql
- Lär dig hur du konfigurerar gransknings loggar i [Azure CLI](howto-configure-audit-logs-cli.md)