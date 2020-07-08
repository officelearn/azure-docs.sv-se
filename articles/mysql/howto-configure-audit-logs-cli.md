---
title: Åtkomst gransknings loggar – Azure CLI – Azure Database for MySQL
description: Den här artikeln beskriver hur du konfigurerar och får åtkomst till gransknings loggarna i Azure Database for MySQL från Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: 813e86e8446932d1618fff1853cee8972570f43f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85363027"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Konfigurera och få åtkomst till gransknings loggar i Azure CLI

Du kan konfigurera [gransknings loggarna för Azure Database for MySQL](concepts-audit-logs.md) från Azure CLI.

## <a name="prerequisites"></a>Krav

För att gå igenom den här instruktions guiden behöver du:

- [Azure Database for MySQL Server](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här instruktions guiden kräver att du använder Azure CLI version 2,0 eller senare. Bekräfta versionen genom att ange i kommando tolken för Azure CLI `az --version` . Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Konfigurera gransknings loggning

>[!IMPORTANT]
> Vi rekommenderar att du bara loggar de händelse typer och användare som krävs för gransknings syfte för att säkerställa att serverns prestanda inte påverkas kraftigt.

Aktivera och konfigurera gransknings loggning med följande steg:

1. Aktivera gransknings loggar genom att ange parametern **audit_logs_enabled** till "på". 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Välj de [händelse typer](concepts-audit-logs.md#configure-audit-logging) som ska loggas genom att uppdatera **audit_log_events** -parametern.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Lägg till alla MySQL-användare som ska undantas från loggning genom att uppdatera **audit_log_exclude_users** -parametern. Ange användare genom att ange sitt användar namn för MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Lägg till eventuella speciella MySQL-användare som ska ingå i loggning genom att uppdatera **audit_log_include_users** -parametern. Ange användare genom att ange sitt användar namn för MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [gransknings loggar](concepts-audit-logs.md) i Azure Database for mysql
- Lär dig hur du konfigurerar gransknings loggar i [Azure Portal](howto-configure-audit-logs-portal.md)