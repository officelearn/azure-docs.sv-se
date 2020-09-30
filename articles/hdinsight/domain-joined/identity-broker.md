---
title: Använd ID-Broker (för hands version) för hantering av autentiseringsuppgifter – Azure HDInsight
description: Lär dig mer om HDInsight ID Broker för att förenkla autentisering för domänanslutna Apache Hadoop-kluster.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 8f1e0a6aecc9702552a3dd66acc8dc7eb5bf1d85
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91529955"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID Broker (för hands version)

Den här artikeln beskriver hur du konfigurerar och använder funktionen HDInsight ID Broker (HIB) i Azure HDInsight. Du kan använda den här funktionen för att få modern OAuth-autentisering till Apache Ambari samtidigt som du har Multi-Factor Authentication (MFA) tillämpning utan att behöva äldre lösen ords-hashvärden i Azure Active Directory Domain Services (AAD-DS).

## <a name="overview"></a>Översikt

HIB fören klar komplicerade autentiseringar i följande scenarier:

* Din organisation använder federationen för att autentisera användare för åtkomst till moln resurser. För att använda HDInsight Enterprise Security Package-kluster (ESP) var du tvungen att aktivera synkronisering av lösen ord från din lokala miljö till Azure Active Directory (Azure AD). Detta krav kan vara svårt eller olämpligt för vissa organisationer.

* Din organisation vill framtvinga MFA för webb-/HTTP-baserad åtkomst till Apache Ambari och andra kluster resurser.

HIB tillhandahåller den infrastruktur för autentisering som möjliggör protokoll över gång från OAuth (modern) till Kerberos (äldre) utan att behöva synkronisera lösen ords hashar till AAD-DS. Den här infrastrukturen består av komponenter som körs på en virtuell Windows Server-dator (ID Broker-nod), tillsammans med klusternoder för klusternoder.

Följande diagram visar det moderna OAuth-baserade autentiseringsschemat för alla användare, inklusive federerade användare, efter att ID-Broker har Aktiver ATS:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Autentiseringspaket med ID-Broker":::

I det här diagrammet måste klienten (t. ex. webbläsare eller appar) Hämta OAuth-token först och sedan presentera token till gateway i en HTTP-begäran. Om du redan har loggat in på andra Azure-tjänster, till exempel Azure Portal, kan du logga in på ditt HDInsight-kluster med enkel inloggning (SSO).

Det kan fortfarande finnas många äldre program som endast stöder grundläggande autentisering (dvs. användar namn/lösen ord). I dessa scenarier kan du fortfarande använda HTTP Basic-autentisering för att ansluta till kluster-gatewayerna. I den här installationen måste du se till att nätverks anslutningen från Gateway-noderna till Federations slut punkten (ADFS-slutpunkt) för att säkerställa en direkt rad syn från Gateway-noder.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Autentiseringspaket med ID-Broker":::

Använd följande tabell för att fastställa det bästa alternativet för autentisering baserat på din organisations behov:

|Autentiseringsalternativ |HDInsight-konfiguration | Faktorer att överväga |
|---|---|---|
| Fullständigt OAuth | ESP + HIB | 1. det säkraste alternativet (MFA stöds) 2.    Passion hash Sync krävs inte. 3.  Ingen SSH/kinit/keytab-åtkomst för lokal-konton, som inte har något lösen ords-hash i AAD-DS. 4.   Moln konton kan fortfarande vara SSH/kinit/keytab. 5. Webbaserad åtkomst till Ambari via OAuth 6.  Kräver att äldre appar uppdateras (JDBC/ODBC osv.) för att stödja OAuth.|
| OAuth + Basic-autentisering | ESP + HIB | 1. webbaserad åtkomst till Ambari via OAuth 2. Äldre appar fortsätter att använda Basic auth. 3. MFA måste inaktive ras för grundläggande åtkomst till autentisering. 4. Passion hash Sync krävs inte. 5. Ingen SSH/kinit/keytab-åtkomst för lokal-konton, som inte har något lösen ords-hash i AAD-DS. 6. Moln konton kan fortfarande vara SSH-/kinit. |
| Fullständigt grundläggande autentisering | ESP | 1. mest likt lokal-installationer. 2. Lösen ordets hash-synkronisering till AAD-DS krävs. 3. Lokal-konton kan SSH/kinit eller använda keytab. 4. MFA måste inaktive ras om lagrings utrymmet ADLS Gen2 |


## <a name="enable-hdinsight-id-broker"></a>Aktivera HDInsight ID-Broker

Gör så här för att skapa ett ESP-kluster med ID Broker aktiverat:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Följ de grundläggande stegen för att skapa ett ESP-kluster. Mer information finns i [skapa ett HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Välj **Aktivera HDInsight ID Broker**.

Med ID Broker-funktionen läggs en extra virtuell dator till i klustret. Den här virtuella datorn är noden ID-Broker och innehåller Server komponenter som stöder autentisering. Noden ID Broker är domän ansluten till Azure AD DS-domänen.

![Alternativ för att aktivera ID-Broker](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar
Om du lägger till en ny roll `idbrokernode` som heter med följande attribut till din malls beräknings profil skapas klustret med NODEN ID Broker Enabled:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>Verktygs integrering

HDIsngith-verktyg har uppdaterats till internt stöd för OAuth. Vi rekommenderar starkt att du använder dessa verktyg för modern OAuth-baserad åtkomst till klustren. HDInsight [IntelliJ-plugin-programmet](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) kan användas för Java-baserade program, till exempel Scala. [Spark & Hive-verktyg för vs Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) kan användas av PySpark-och Hive-jobb. De stöder både batch-och interaktiva jobb.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-åtkomst utan hash för lösen ord i Azure AD DS

|SSH-alternativ |Faktorer att överväga |
|---|---|
| Lokalt VM-konto (t. ex. sshuser) | 1. du angav det här kontot när klustret skapades. 2.  Det finns ingen Kerberos-authication för det här kontot |
| Endast moln konto (t. ex. alice@contoso.onmicrosoft.com ) | 1. lösen ordets hash är tillgänglig i AAD-DS 2. Kerberos-autentisering är möjlig via SSH Kerberos |
| Lokal-konto (t. ex. alice@contoso.com ) | 1. SSH Kerberos-autentisering är bara möjlig om lösen ordets hash är tillgängligt i AAD-DS, annars kan den här användaren inte använda SSH till klustret |

För SSH till en domänansluten virtuell dator, eller för att köra `kinit` kommandot, måste du ange ett lösen ord. SSH Kerberos-autentisering kräver att hash är tillgängligt i AAD-DS. Om du bara vill använda SSH i administrativa scenarier kan du skapa ett enda moln konto och använda det för SSH till klustret. Andra lokal-användare kan fortfarande använda Ambari-eller HDInsight-verktyg eller HTTP Basic-autentisering utan att lösen ordet hash är tillgängligt i AAD-DS.

Om din organisation inte synkroniserar lösen ords-hashar till AAD-DS, bör du skapa endast en enda moln användare i Azure AD och tilldela den som kluster administratör när du skapar klustret och använder det för administrations skäl, vilket omfattar att få rot åtkomst till de virtuella datorerna via SSH.

Information om hur du felsöker problem med autentisering finns i den här [guiden](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues).

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>Klienter som använder OAuth för att ansluta till HDInsight-Gateway med HIB

I HIB-installationen kan anpassade appar och klienter som ansluter till gatewayen uppdateras för att hämta den begärda OAuth-token först. Du kan följa stegen i det här [dokumentet](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) för att hämta token med följande information:

*   URI för OAuth-resurs: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Behörighet: (namn: cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

När du har skaffat OAuth-token kan du använda det i Authorization-huvudet för HTTP-begäran till kluster-gatewayen (t. ex. https:// <clustername> -int.azurehdinsight.net). Till exempel kan ett exempel på en spiral-kommando till Apache livy API se ut så här:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>Nästa steg

* [Konfigurera ett HDInsight-kluster med Enterprise Security Package med Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synkronisera Azure Active Directory-användare med ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
* [Övervaka klusterprestanda](../hdinsight-key-scenarios-to-monitor.md)
