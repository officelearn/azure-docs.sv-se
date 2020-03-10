---
title: Konfigurera Azure Firewall-programregler med SQL-FQDN
description: I den här artikeln får du lära dig hur du konfigurerar SQL-FQDN i regler för Azure brand Väggs program.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: a42d6bcdcec2a5de7432f11216a4d8dd0c1deef9
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942574"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurera Azure Firewall-programregler med SQL-FQDN

> [!IMPORTANT]
> Azure Firewall-programregler med SQL-FQDN är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nu kan du konfigurera regler för Azure brand Väggs program med SQL-FQDN. På så sätt kan du begränsa åtkomsten från dina virtuella nätverk till endast de angivna SQL Server-instanserna.

Med SQL-FQDN kan du filtrera trafik:

- Från virtuella nätverk till en Azure SQL Database eller ett Azure SQL Data Warehouse. Till exempel: Tillåt endast åtkomst till *SQL-server1.Database.Windows.net*.
- Från lokalt till Azure SQL-hanterade instanser eller SQL-IaaS som körs i din virtuella nätverk.
- Från ekrar till ekrar till Azure SQL-hanterade instanser eller SQL-IaaS som körs i din virtuella nätverk.

Under den offentliga för hands versionen stöds endast SQL-FQDN-filtrering i [proxy-läge](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (port 1433). Om du använder SQL i standard läget för omdirigering kan du filtrera åtkomst med SQL Service-taggen som en del av [nätverks reglerna](overview.md#network-traffic-filtering-rules).
Om du använder portar som inte är standard för SQL IaaS-trafik kan du konfigurera dessa portar i brand Väggs program reglerna.

Program regler med SQL-FQDN är för närvarande tillgängliga i alla regioner via Azure Portal, Azure CLI, REST och mallar.

## <a name="configure-using-azure-cli"></a>Konfigurera med Azure CLI

1. Distribuera en [Azure-brandvägg med Azure CLI](deploy-cli.md).
2. Om du filtrerar trafik till Azure SQL Database, SQL Data Warehouse eller SQL-hanterad instans kontrollerar du att SQL Connectivity-läget är inställt på **proxy**. Information om hur du växlar läge för SQL-anslutning finns i [Inställningar för Azure SQL-anslutning](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settingse#change-azure-sql-database-connection-policy).

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

## <a name="configure-using-the-azure-portal"></a>Konfigurera med hjälp av Azure Portal
1. Distribuera en [Azure-brandvägg med Azure CLI](deploy-cli.md).
2. Om du filtrerar trafik till Azure SQL Database, SQL Data Warehouse eller SQL-hanterad instans kontrollerar du att SQL Connectivity-läget är inställt på **proxy**. Information om hur du växlar läge för SQL-anslutning finns i [Inställningar för Azure SQL-anslutning](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settingse#change-azure-sql-database-connection-policy).  

   > [!NOTE]
   > SQL- *proxyläge* kan resultera i mer latens jämfört med *omdirigering*. Om du vill fortsätta använda omdirigeringsläge, som är standardvärdet för klienter som ansluter i Azure, kan du filtrera åtkomst med SQL [service-taggen](service-tags.md) i brand Väggs [nätverks regler](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Lägg till program regeln med rätt protokoll, port och SQL FQDN och välj sedan **Spara**.
   ![program regel med SQL-FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Få åtkomst till SQL från en virtuell dator i ett VNet som filtrerar trafiken genom brand väggen. 
5. Verifiera att [Azure Firewall-loggar](log-analytics-samples.md) visar att trafiken är tillåten.

## <a name="next-steps"></a>Nästa steg

Information om SQL-proxy och omdirigerings lägen finns i [anslutnings arkitekturen för Azure SQL Database](../sql-database/sql-database-connectivity-architecture.md).