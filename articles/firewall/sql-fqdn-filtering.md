---
title: Konfigurera Programregler för Azure Firewall med SQL FQDN
description: I den här artikeln får du lära dig hur du konfigurerar SQL FQDN i Azure Firewall-programregler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501509"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurera Programregler för Azure Firewall med SQL FQDN

> [!IMPORTANT]
> Azure Firewall-programregler med SQL FQDN är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan nu konfigurera Azure Firewall-programregler med SQL FQDN. På så sätt kan du begränsa åtkomsten från dina virtuella nätverk till endast de angivna SQL-serverinstanserna.

Med SQL FQDN kan du filtrera trafik:

- Från dina virtuella nätverk till en Azure SQL-databas eller ett Azure SQL Data Warehouse. Till exempel: Tillåt endast åtkomst till *sql-server1.database.windows.net*.
- Från lokalt till Azure SQL Managed Instances eller SQL IaaS som körs i dina virtuella nätverk.
- Från eker till talade till Azure SQL Managed Instances eller SQL IaaS som körs i dina virtuella nätverk.

Under den offentliga förhandsversionen stöds SQL FQDN-filtrering endast i [proxyläge](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (port 1433). Om du använder SQL i standardomdirigeringsläge kan du filtrera åtkomst med SQL-tjänsttaggen som en del av [nätverksreglerna](overview.md#network-traffic-filtering-rules).
Om du använder icke-standardportar för SQL IaaS-trafik kan du konfigurera dessa portar i brandväggsprogramreglerna.

Programregler med SQL FQDN är för närvarande tillgängliga i alla regioner via Azure-portalen, Azure CLI, REST och mallar.

## <a name="configure-using-azure-cli"></a>Konfigurera med Azure CLI

1. Distribuera en [Azure-brandvägg med Azure CLI](deploy-cli.md).
2. Om du filtrerar trafik till Azure SQL Database, SQL Data Warehouse eller SQL Managed Instance kontrollerar du att SQL-anslutningsläget är inställt på **Proxy**. Mer information om hur du växlar SQL-anslutningsläge finns i [Azure SQL Connectivity Settings](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > SQL *proxy-läge* kan resultera i mer latens jämfört med *omdirigering*. Om du vill fortsätta använda omdirigeringsläge, som är standard för klienter som ansluter inom Azure, kan du filtrera åtkomst med [SQL-tjänsttaggen](service-tags.md) i [brandväggsnätverksregler](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Konfigurera en programregel med SQL FQDN för att tillåta åtkomst till en SQL-server:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Konfigurera med hjälp av Azure-portalen
1. Distribuera en [Azure-brandvägg med Azure CLI](deploy-cli.md).
2. Om du filtrerar trafik till Azure SQL Database, SQL Data Warehouse eller SQL Managed Instance kontrollerar du att SQL-anslutningsläget är inställt på **Proxy**. Mer information om hur du växlar SQL-anslutningsläge finns i [Azure SQL Connectivity Settings](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > SQL *proxy-läge* kan resultera i mer latens jämfört med *omdirigering*. Om du vill fortsätta använda omdirigeringsläge, som är standard för klienter som ansluter inom Azure, kan du filtrera åtkomst med [SQL-tjänsttaggen](service-tags.md) i [brandväggsnätverksregler](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Lägg till programregeln med rätt protokoll, port och SQL FQDN och välj sedan **Spara**.
   ![programregel med SQL FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Få tillgång till SQL från en virtuell dator i ett virtuellt nätverk som filtrerar trafiken genom brandväggen. 
5. Verifiera att [Azure-brandväggsloggar](log-analytics-samples.md) visar att trafiken är tillåten.

## <a name="next-steps"></a>Nästa steg

Mer information om SQL-proxy- och omdirigeringslägen finns i [Azure SQL-databasanslutningsarkitektur](../sql-database/sql-database-connectivity-architecture.md).