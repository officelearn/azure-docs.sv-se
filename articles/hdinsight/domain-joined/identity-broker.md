---
title: Använd ID-Broker (för hands version) för hantering av autentiseringsuppgifter – Azure HDInsight
description: Lär dig mer om HDInsight ID Broker för att förenkla autentisering för domänanslutna Apache Hadoop-kluster.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: ff7cb3c03edf9b421347815311796896caaffd70
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086610"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Använd ID-Broker (för hands version) för hantering av autentiseringsuppgifter

Den här artikeln beskriver hur du konfigurerar och använder funktionen ID-utjämning i Azure HDInsight. Du kan använda den här funktionen för att logga in på Apache Ambari via Azure Multi-Factor Authentication och hämta de nödvändiga Kerberos-biljetterna utan att behöva lösen ords-hashar i Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Översikt

ID-Broker fören klar avancerade konfigurations inställningar för autentisering i följande scenarier:

* Din organisation använder federationen för att autentisera användare för åtkomst till moln resurser. För att använda HDInsight Enterprise Security Package-kluster (ESP) var du tvungen att aktivera synkronisering av lösen ords-hash från din lokala miljö till Azure Active Directory. Detta krav kan vara svårt eller olämpligt för vissa organisationer.

* Du bygger lösningar som använder tekniker som förlitar sig på olika autentiseringsmekanismer. Till exempel Apache Hadoop och Apache Ranger förlitar sig på Kerberos, medan Azure Data Lake Storage är beroende av OAuth.

ID-Broker tillhandahåller en enhetlig autentiserings-infrastruktur och tar bort kravet på att synkronisera lösen ords-hashar till Azure AD DS. ID-Broker består av komponenter som körs på en virtuell Windows Server-dator (ID Broker-nod), tillsammans med klusternoder för klusternoder. 

Följande diagram visar autentiseringsschemat för alla användare, inklusive federerade användare, efter att ID-Broker har Aktiver ATS:

![Autentiseringspaket med ID-Broker](./media/identity-broker/identity-broker-architecture.png)

Med ID-Broker kan du logga in på ESP-kluster med hjälp av Multi-Factor Authentication, utan att ange lösen ord. Om du redan har loggat in på andra Azure-tjänster, till exempel Azure Portal, kan du logga in på ditt HDInsight-kluster med enkel inloggning (SSO).

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

HDInsight [IntelliJ-plugin-programmet](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) har uppdaterats för att stödja OAuth. Du kan använda det här plugin-programmet för att ansluta till klustret och skicka jobb.

Du kan också använda [Spark & Hive-verktyg för vs Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) för att utnyttja antecknings böcker och skicka jobb.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-åtkomst utan hash för lösen ord i Azure AD DS

När ID-Broker har Aktiver ATS behöver du fortfarande en lösen ords-hash som lagras i Azure AD DS för SSH-scenarier med domän konton. För SSH till en domänansluten virtuell dator, eller för att köra `kinit` kommandot, måste du ange ett lösen ord. 

SSH-autentisering kräver att hash är tillgängligt i Azure AD DS. Om du bara vill använda SSH i administrativa scenarier kan du skapa ett enda moln konto och använda det för SSH till klustret. Andra användare kan fortfarande använda Ambari-eller HDInsight-verktyg (t. ex. IntelliJ-plugin-programmet) utan att lösen ordet hash är tillgängligt i Azure AD DS.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Klienter som använder OAuth för att ansluta till HDInsight Gateway med ID Broker-installation

I installationen av ID-Broker kan anpassade appar och klienter som ansluter till gatewayen uppdateras för att först hämta den begärda OAuth-token. Du kan följa stegen i det här [dokumentet](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) för att hämta token med följande information:

*   URI för OAuth-resurs:`https://hib.azurehdinsight.net` 
* AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Behörighet: (namn: cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera ett HDInsight-kluster med Enterprise Security Package med Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synkronisera Azure Active Directory-användare med ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
* [Övervaka klusterprestanda](../hdinsight-key-scenarios-to-monitor.md)
