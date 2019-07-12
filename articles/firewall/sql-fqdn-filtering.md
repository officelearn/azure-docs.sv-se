---
title: Konfigurera regler för brandväggen för Azure-program med FQDN för SQL
description: I den här artikeln får du lära dig hur du konfigurerar SQL-FQDN i Azure application brandväggsregler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/11/2019
ms.author: victorh
ms.openlocfilehash: e188a5dda8f936ad369aa2b9222bc726bb0d6a5e
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786589"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurera regler för brandväggen för Azure-program med FQDN för SQL

> [!IMPORTANT]
> Azure application brandväggsregler med FQDN för SQL är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan nu konfigurera regler för brandväggen för Azure-program med SQL-FQDN. På så sätt kan du begränsa åtkomst från dina virtuella nätverk till endast de angivna SQL server-instanserna.

Du kan filtrera trafik med SQL-FQDN:

- Från dina virtuella nätverk till en Azure SQL-databas eller en Azure SQL Data Warehouse. Exempel: Tillåt endast åtkomst till *sql-server1.database.windows.net*.
- Från lokalt till Azure SQL-hanterade instanser eller SQL IaaS som körs i ditt virtuella nätverk.
- Från eker-till-eker till Azure SQL-hanterade instanser eller SQL IaaS som körs i ditt virtuella nätverk.

Den offentliga förhandsversionen, SQL-FQDN filtrering stöds i [-proxyläge](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) endast (port 1433). Om du använder SQL i standardläget för omdirigering kan du filtrera åtkomst med hjälp av SQL-tjänsttagg som en del av [network regler](overview.md#network-traffic-filtering-rules).
Om du använder icke-standardportar för SQL IaaS-trafik kan konfigurera du dessa portar i brandväggsreglerna för programmet.

> [!NOTE]
> Regler för program med FQDN för SQL är tillgänglig i alla regioner via Azure CLI, REST och mallar. Användargränssnittet för portalen läggs till regioner stegvis och är tillgänglig i alla regioner när distributionen är klar.

## <a name="configure-using-azure-cli"></a>Konfigurera med hjälp av Azure CLI

1. Distribuera en [Azure-brandväggen med hjälp av Azure CLI](deploy-cli.md).
2. Om du filtrerar trafik till Azure SQL Database, SQL Data Warehouse eller SQL Managed Instance kan du kontrollera att SQL-anslutningsläget är inställd på **Proxy**. Läs hur du växlar SQL anslutningsläget i [Azure SQL-Anslutningsarkitektur](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* läge kan leda till mer svarstid jämfört med *omdirigera*. Om du vill fortsätta att använda omdirigeringsläge, vilket är standard för klienter som ansluter i Azure, kan du filtrera åtkomst med hjälp av SQL [tjänsttaggen](service-tags.md) i brandväggen [network regler](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Konfigurera en regel för program med SQL-FQDN för att tillåta åtkomst till en SQLServer:

   ```azurecli
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
1. Distribuera en [Azure-brandväggen med hjälp av Azure CLI](deploy-cli.md).
2. Om du filtrerar trafik till Azure SQL Database, SQL Data Warehouse eller SQL Managed Instance kan du kontrollera att SQL-anslutningsläget är inställd på **Proxy**. Läs hur du växlar SQL anslutningsläget i [Azure SQL-Anslutningsarkitektur](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* läge kan leda till mer svarstid jämfört med *omdirigera*. Om du vill fortsätta att använda omdirigeringsläge, vilket är standard för klienter som ansluter i Azure, kan du filtrera åtkomst med hjälp av SQL [tjänsttaggen](service-tags.md) i brandväggen [network regler](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Lägg till regel för program med rätt protokoll, port och SQL-FQDN och välj sedan **spara**.
   ![program-regel med SQL-FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Åtkomst till SQL från en virtuell dator i ett virtuellt nätverk som filtrerar trafik genom brandväggen. 
5. Kontrollera att [Azure brandväggsloggar](log-analytics-samples.md) visa trafiken tillåts.

## <a name="next-steps"></a>Nästa steg

Mer information om SQL-proxy och omdirigera lägen, se [anslutningsarkitektur för Azure SQL database](../sql-database/sql-database-connectivity-architecture.md).