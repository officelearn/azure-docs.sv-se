---
title: Granskningsloggar för Access - Azure CLI - Azure Database för MySQL
description: I den här artikeln beskrivs hur du konfigurerar och får åtkomst till granskningsloggarna i Azure Database for MySQL från Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384173"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Konfigurera och komma åt granskningsloggar i Azure CLI

Du kan konfigurera [Azure-databasen för MySQL-granskningsloggar](concepts-audit-logs.md) från Azure CLI.

> [!IMPORTANT]
> Granskningsloggfunktionen är för närvarande i förhandsgranskning.

## <a name="prerequisites"></a>Krav

För att gå igenom den här guiden behöver du:

- [Azure-databas för MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här programguiden kräver att du använder Azure CLI version 2.0 eller senare. Om du vill bekräfta versionen anger du `az --version`i kommandotolken i Azure CLI . Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Konfigurera granskningsloggning

Aktivera och konfigurera granskningsloggning med hjälp av följande steg:

1. Aktivera granskningsloggar genom att ange **parametern audit_logs_enabled** på "ON". 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Välj de [händelsetyper](concepts-audit-logs.md#configure-audit-logging) som ska loggas genom att uppdatera **parametern audit_log_events.**
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Lägg till alla MySQL-användare som ska uteslutas från loggning genom att uppdatera **parametern audit_log_exclude_users.** Ange användare genom att ange deras MySQL-användarnamn.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Lägg till alla specifika MySQL-användare som ska inkluderas för loggning genom att uppdatera **parametern audit_log_include_users.** Ange användare genom att ange deras MySQL-användarnamn.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [granskningsloggar](concepts-audit-logs.md) i Azure Database for MySQL
- Lär dig hur du konfigurerar granskningsloggar i [Azure-portalen](howto-configure-audit-logs-portal.md)