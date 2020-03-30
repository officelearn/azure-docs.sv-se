---
title: Anpassad Apache Ambari-databas på Azure HDInsight
description: Lär dig hur du skapar HDInsight-kluster med din egen anpassade Apache Ambari-databas.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240162"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Konfigurera HDInsight-kluster med en anpassad Ambari DB

Apache Ambari förenklar hanteringen och övervakningen av ett Apache Hadoop-kluster. Ambari ger ett lättanvänt webbgränssnitt och REST API. Ambari ingår i HDInsight-kluster och används för att övervaka klustret och göra konfigurationsändringar.

I normal klustergenerering, som beskrivs i andra artiklar, till exempel [Konfigurera kluster i HDInsight,](hdinsight-hadoop-provision-linux-clusters.md)distribueras Ambari i en [S0 Azure SQL-databas](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) som hanteras av HDInsight och inte är tillgänglig för användare.

Med den anpassade Ambari DB-funktionen kan du distribuera ett nytt kluster och konfigurera Ambari i en extern databas som du hanterar. Distributionen görs med en Azure Resource Manager-mall. Den här funktionen har följande fördelar:

- Anpassning - du väljer storlek och bearbetningskapacitet för databasen. Om du har stora kluster som bearbetar intensiva arbetsbelastningar kan en Ambari-databas med lägre specifikationer bli en flaskhals för hanteringsåtgärder.
- Flexibilitet - du kan skala databasen efter behov för att passa dina behov.
- Kontroll - du kan hantera säkerhetskopior och säkerhet för din databas på ett sätt som passar dina organisationers krav.

I resten av den här artikeln beskrivs följande punkter:

- krav för att använda den anpassade Ambari DB-funktionen
- de åtgärder som krävs för att etablera HDInsight-kluster med din egen externa databas för Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Anpassade Ambari DB-krav

Du kan distribuera en anpassad Ambari DB med alla klustertyper och versioner. Flera kluster kan inte använda samma Ambari DB.

Den anpassade Ambari DB har följande andra krav:

- Du måste ha en befintlig Azure SQL DB-server och databas.
- Databasen som du anger för Ambari-installationen måste vara tom. Det bör inte finnas några tabeller i standard-dbo-schemat.
- Användaren som används för att ansluta till databasen ska ha behörigheten SELECT, CREATE TABLE och INSERT i databasen.
- Aktivera alternativet [Tillåt åtkomst till Azure-tjänster](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) på Azure SQL-servern där du ska vara värd för Ambari.
- Hantering av IP-adresser från HDInsight-tjänsten måste tillåtas i SQL Server. Se [IP-adresser för HDInsight-hantering](hdinsight-management-ip-addresses.md) för en lista över IP-adresser som måste läggas till i SQL-serverbrandväggen.

När du är värd för din Apache Ambari DB i en extern databas, kom ihåg följande punkter:

- Du är ansvarig för de extra kostnaderna för Azure SQL DB som innehåller Ambari.
- Säkerhetskopiera din anpassade Ambari DB med jämna mellanrum. Azure SQL Database genererar säkerhetskopior automatiskt, men tidsramen för lagring av säkerhetskopiering varierar. Mer information finns i [Lär dig mer om automatiska säkerhetskopieringar av SQL Database](../sql-database/sql-database-automated-backups.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Distribuera kluster med en anpassad Ambari DB

Om du vill skapa ett HDInsight-kluster som använder din egen externa Ambari-databas använder du den [anpassade Ambari DB Quickstart-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Redigera parametrarna `azuredeploy.parameters.json` i för att ange information om det nya klustret och databasen som ska innehålla Ambari.

Du kan börja distributionen med Azure CLI. Ersätt `<RESOURCEGROUPNAME>` med den resursgrupp där du vill distribuera klustret.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Nästa steg

- [Använda extern metadatalagring i Azure HDInsight](hdinsight-use-external-metadata-stores.md)