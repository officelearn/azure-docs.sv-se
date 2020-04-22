---
title: Använd ID Broker (förhandsversion) för hantering av autentiseringsuppgifter- Azure HDInsight
description: Lär dig mer om HDInsight ID Broker för att förenkla autentiseringen för domänanslutna Apache Hadoop-kluster.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 1e7eaf49fb8b62259b8c619c89edffd629dfde7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685518"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Använd ID Broker (förhandsversion) för hantering av autentiseringsuppgifter

I den här artikeln beskrivs hur du konfigurerar och använder ID Broker-funktionen i Azure HDInsight. Du kan använda den här funktionen för att logga in på Apache Ambari via Azure Multi-Factor Authentication och få de Kerberos-biljetter som krävs utan att behöva lösenordshã¤ringar i Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Översikt

ID Broker förenklar komplexa autentiseringsinställningar i följande scenarier:

* Din organisation förlitar sig på federation för att autentisera användare för åtkomst till molnresurser. Tidigare, för att använda ESP-kluster (HDInsight Enterprise Security Package) var du tvungen att aktivera synkronisering av lösenord hash från din lokala miljö till Azure Active Directory. Det här kravet kan vara svårt eller oönskat för vissa organisationer.

* Du bygger lösningar som använder tekniker som är beroende av olika autentiseringsmekanismer. Apache Hadoop och Apache Ranger förlitar sig till exempel på Kerberos, medan Azure Data Lake Storage är beroende av OAuth.

ID Broker tillhandahåller en enhetlig autentiseringsinfrastruktur och tar bort kravet på synkronisering av lösenordsharningar till Azure AD DS. ID Broker består av komponenter som körs på en Windows Server VM (ID Broker-nod), tillsammans med klustergatewaynoder. 

Följande diagram visar autentiseringsflödet för alla användare, inklusive federerade användare, efter att ID Broker har aktiverats:

![Autentiseringsflöde med ID Broker](./media/identity-broker/identity-broker-architecture.png)

ID Broker kan du logga in på ESP kluster med hjälp av Multi-Factor Autentisering, utan att ange några lösenord. Om du redan har loggat in på andra Azure-tjänster, till exempel Azure-portalen, kan du logga in på ditt HDInsight-kluster med en enda inloggningsupplevelse (SSO).

## <a name="enable-hdinsight-id-broker"></a>Aktivera HDInsight ID Broker

Så här skapar du ett ESP-kluster med ID Broker aktiverat:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Följ de grundläggande skapande stegen för ett ESP-kluster. Mer information finns i [Skapa ett HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Välj **Aktivera HDInsight ID Broker**.

ID Broker-funktionen lägger till en extra virtuell dator i klustret. Den här virtuella datorn är ID Broker-noden och innehåller serverkomponenter för autentisering. ID Broker-noden är domän som är ansluten till Azure AD DS-domänen.

![Alternativ för att aktivera ID Broker](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar
Om du lägger `idbrokernode` till en ny roll som anropas med följande attribut i beräkningsprofilen för mallen skapas klustret med ID-mäklarnoden aktiverad:

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

## <a name="tool-integration"></a>Verktygsintegrering

HdInsight [IntelliJ-plugin-programmet](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) uppdateras för att stödja OAuth. Du kan använda det här plugin-programmet för att ansluta till klustret och skicka jobb.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-åtkomst utan lösenordshh i Azure AD DS

När ID Broker har aktiverats behöver du fortfarande en lösenordshh som lagras i Azure AD DS för SSH-scenarier med domänkonton. Om du vill ha SSH till en `kinit` domänansluten virtuell dator eller köra kommandot måste du ange ett lösenord. 

SSH-autentisering kräver att hash-värdet är tillgängligt i Azure AD DS. Om du bara vill använda SSH för administrativa scenarier kan du skapa ett molnkonto och använda det till SSH till klustret. Andra användare kan fortfarande använda Ambari- eller HDInsight-verktyg (till exempel IntelliJ-plugin-programmet) utan att lösenordshhen är tillgänglig i Azure AD DS.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Klienter som använder OAuth för att ansluta till HDInsight-gateway med ID Broker-konfiguration

I ID-mäklaren kan anpassade appar och klienter som ansluter till gatewayen uppdateras för att hämta den OAuth-token som krävs först. Du kan följa stegen i det här [dokumentet](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) för att hämta token med följande information:

*   OAuth resurs uri:https://hib.azurehdinsight.net 
* AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Behörighet: (namn: Cluster.ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synkronisera Azure Active Directory-användare med ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
* [Övervaka klusterprestanda](../hdinsight-key-scenarios-to-monitor.md)
