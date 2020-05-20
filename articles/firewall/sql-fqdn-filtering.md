---
title: Konfigurera Azure Firewall-programregler med SQL-FQDN
description: I den här artikeln får du lära dig hur du konfigurerar SQL-FQDN i regler för Azure brand Väggs program.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: 8b67574f435681d8071eda1ad954dcafb5124cbf
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655096"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurera Azure Firewall-programregler med SQL-FQDN

Nu kan du konfigurera regler för Azure brand Väggs program med SQL-FQDN. På så sätt kan du begränsa åtkomsten från dina virtuella nätverk till endast de angivna SQL Server-instanserna.

Med SQL-FQDN kan du filtrera trafik:

- Från virtuella nätverk till en Azure SQL Database eller ett Azure SQL Data Warehouse. Till exempel: Tillåt endast åtkomst till *SQL-server1.Database.Windows.net*.
- Från lokalt till Azure SQL-hanterade instanser eller SQL-IaaS som körs i din virtuella nätverk.
- Från ekrar till ekrar till Azure SQL-hanterade instanser eller SQL-IaaS som körs i din virtuella nätverk.

SQL-FQDN-filtrering stöds endast i [proxy-läge](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (port 1433). Om du använder SQL i standard läget för omdirigering kan du filtrera åtkomst med SQL Service-taggen som en del av [nätverks reglerna](overview.md#network-traffic-filtering-rules).
Om du använder portar som inte är standard för SQL IaaS-trafik kan du konfigurera dessa portar i brand Väggs program reglerna.

## <a name="configure-using-azure-cli"></a>Konfigurera med Azure CLI

1. Distribuera en [Azure-brandvägg med Azure CLI](deploy-cli.md).
2. Om du filtrerar trafik till Azure SQL Database, SQL Data Warehouse eller SQL-hanterad instans kontrollerar du att SQL Connectivity-läget är inställt på **proxy**. Information om hur du växlar läge för SQL-anslutning finns i [Inställningar för Azure SQL-anslutning](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > SQL- *proxyläge* kan resultera i mer latens jämfört med *omdirigering*. Om du vill fortsätta använda omdirigeringsläge, som är standardvärdet för klienter som ansluter i Azure, kan du filtrera åtkomst med SQL [service-taggen](service-tags.md) i brand Väggs [nätverks regler](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Konfigurera en program regel med SQL FQDN för att tillåta åtkomst till en SQL-Server:

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
2. Om du filtrerar trafik till Azure SQL Database, SQL Data Warehouse eller SQL-hanterad instans kontrollerar du att SQL Connectivity-läget är inställt på **proxy**. Information om hur du växlar läge för SQL-anslutning finns i [Inställningar för Azure SQL-anslutning](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > SQL- *proxyläge* kan resultera i mer latens jämfört med *omdirigering*. Om du vill fortsätta använda omdirigeringsläge, som är standardvärdet för klienter som ansluter i Azure, kan du filtrera åtkomst med SQL [service-taggen](service-tags.md) i brand Väggs [nätverks regler](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Lägg till program regeln med rätt protokoll, port och SQL FQDN och välj sedan **Spara**.
   ![program regel med SQL FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Få åtkomst till SQL från en virtuell dator i ett VNet som filtrerar trafiken genom brand väggen. 
5. Verifiera att [Azure Firewall-loggar](log-analytics-samples.md) visar att trafiken är tillåten.

## <a name="next-steps"></a>Nästa steg

Information om SQL-proxy och omdirigerings lägen finns i [anslutnings arkitekturen för Azure SQL Database](../sql-database/sql-database-connectivity-architecture.md).