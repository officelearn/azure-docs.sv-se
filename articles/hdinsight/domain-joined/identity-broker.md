---
title: Använd ID Broker (förhandsversion) för hantering av autentiseringsuppgifter- Azure HDInsight
description: Lär dig mer om HDInsight ID Broker för att förenkla autentiseringen för domänanslutna Apache Hadoop-kluster.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483016"
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

## <a name="tool-integration"></a>Verktygsintegrering

HdInsight [IntelliJ-plugin-programmet](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) uppdateras för att stödja OAuth. Du kan använda det här plugin-programmet för att ansluta till klustret och skicka jobb.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-åtkomst utan lösenordshh i Azure AD DS

När ID Broker har aktiverats behöver du fortfarande en lösenordshh som lagras i Azure AD DS för SSH-scenarier med domänkonton. Om du vill ha SSH till en `kinit` domänansluten virtuell dator eller köra kommandot måste du ange ett lösenord. 

SSH-autentisering kräver att hash-värdet är tillgängligt i Azure AD DS. Om du bara vill använda SSH för administrativa scenarier kan du skapa ett molnkonto och använda det till SSH till klustret. Andra användare kan fortfarande använda Ambari- eller HDInsight-verktyg (till exempel IntelliJ-plugin-programmet) utan att lösenordshhen är tillgänglig i Azure AD DS.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synkronisera Azure Active Directory-användare med ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
* [Övervaka klusterprestanda](../hdinsight-key-scenarios-to-monitor.md)
