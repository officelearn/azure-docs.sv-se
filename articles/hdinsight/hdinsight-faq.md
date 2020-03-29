---
title: Vanliga frågor och svar i Azure HDInsight
description: Vanliga frågor och svar om HDInsight
keywords: vanliga frågor och svar, vanliga frågor och svar
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: b05b83086cc9d8449d9517897f347b6e2685aa95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720358"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Vanliga frågor och svar

Den här artikeln innehåller svar på några av de vanligaste frågorna om hur du kör [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Skapa eller ta bort HDInsight-kluster

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Hur etablerar jag ett HDInsight-kluster?

Information om hur du granskar vilka typer av HDInsight-kluster som är tillgängliga och etableringsmetoderna finns [i Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Hur tar jag bort ett befintligt HDInsight-kluster?

Mer information om hur du tar bort ett kluster när det inte längre används finns i [Ta bort ett HDInsight-kluster](hdinsight-delete-cluster.md).

Lämna minst 30 till 60 minuter mellan att skapa och ta bort åtgärder. Annars kan åtgärden misslyckas med följande felmeddelande:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Hur väljer jag rätt antal kärnor eller noder för min arbetsbelastning?

Det lämpliga antalet kärnor och andra konfigurationsalternativ beror på olika faktorer.

Mer information finns i [Kapacitetsplanering för HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Vad kan jag göra när etableringen av kluster misslyckas på grund av ett kapacitetsproblem?

Vanliga kapacitetsproblemfel och begränsningstekniker finns i det här avsnittet.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Fel: Distributionen skulle överskrida kvoten "800"

Azure har en kvot på 800 distributioner per resursgrupp. Olika kvoter tillämpas per resursgrupp, prenumeration, konto eller andra scope. Din prenumeration kan till exempel konfigureras så att antalet kärnor för en region begränsas. Om du försöker distribuera en virtuell dator som har fler kärnor än det tillåtna beloppet visas ett felmeddelande om att kvoten har överskridits.

LÃ¶s problemet genom att ta bort de distributioner som inte längre behövs med hjälp av Azure-portalen, CLI eller PowerShell.

Mer information finns i [Åtgärda fel med resurskvoter](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Fel: Den maximala noden överskred de tillgängliga kärnorna i den här regionen

Din prenumeration kanske har konfigurerats så att antalet kärnor för en region begränsas. Om du försöker distribuera en resurs som har fler kärnor än det tillåtna beloppet visas ett felmeddelande om att kvoten har överskridits.

Följ dessa steg om du vill begära en kvotökning:

1. Gå till [Azure-portalen](https://portal.azure.com)och välj **Hjälp + support**.
   
1. Välj **Ny supportbegäran**.
   
1. Ange följande information på fliken **Grunderna** på sidan **Ny supportbegäran:**
   
   - **Typ av problem:** Välj **Tjänst- och prenumerationsgränser (kvoter)**.
   - **Prenumeration:** Välj den prenumeration som du vill ändra.
   - **Typ av kvot:** Välj **HDInsight**.

Mer information finns i [Skapa ett supportärende för att öka antalet kärnor](hdinsight-capacity-planning.md#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Vilka är de olika typerna av noder i ett HDInsight-kluster?

Azure HDInsight-kluster har olika typer av virtuella datorer eller noder. Varje nodtyp spelar en roll i systemets funktion.

Mer information finns i [Resurstyper i Azure HDInsight-kluster](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Enskilda komponenter

### <a name="can-i-install-additional-components-on-my-cluster"></a>Kan jag installera ytterligare komponenter i klustret?

Ja. Om du vill installera ytterligare komponenter eller anpassa klusterkonfigurationen använder du:

- Skript under eller efter skapandet. Skript anropas via [skriptåtgärd](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), vilket är ett konfigurationsalternativ som du kan använda från Azure-portalen, HDInsight Windows PowerShell-cmdlets eller HDInsight .NET SDK. Det här konfigurationsalternativet kan användas från Azure-portalen, HDInsight Windows PowerShell-cmdlets eller HDInsight .NET SDK.

- [HDInsight Application Platform](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) för att installera ekosystemprogram.

En lista över komponenter som stöds finns [i Vilka är Apache Hadoop-komponenterna och versionerna tillgängliga med HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Kan jag uppgradera de enskilda komponenterna som är förinstallerade i klustret?

Om du uppgraderar inbyggda komponenter eller program som är förinstallerade i klustret stöds inte den resulterande konfigurationen av Microsoft. Dessa systemkonfigurationer har inte testats av Microsoft. Försök att använda en annan version av HDInsight-klustret som kanske redan har den uppgraderade versionen av komponenten förinstallerad.

Det går till exempel inte att uppgradera Hive som en enskild komponent. HDInsight är en hanterad tjänst, och många tjänster är integrerade med Ambari-server och testas. Om du uppgraderar en Hive på egen hand ändras de indexerade binärfilerna för andra komponenter och orsaka problem med komponentintegrering i klustret.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Kan Spark och Kafka köras på samma HDInsight-kluster?

Nej, det går inte att köra Apache Kafka och Apache Spark på samma HDInsight-kluster. Skapa separata kluster för Kafka och Spark för att undvika problem med resurskonkurrens.

### <a name="how-do-i-change-timezone-in-ambari"></a>Hur ändrar jag tidszon i Ambari?

1. Öppna Ambari Web UI på `https://CLUSTERNAME.azurehdinsight.net`, där CLUSTERNAME är namnet på klustret.
2. I det övre högra hörnet väljer du admin | Inställningar. 

   ![Ambari-inställningar](media/hdinsight-faq/ambari-settings.png)

3. I fönstret Användarinställningar väljer du den nya tidszonen i listrutan Tidszon och klickar sedan på Spara.

   ![Ambari-användarinställningar](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metaarkiv

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Hur migrerar jag från det befintliga metabutiken till Azure SQL Server? 

Information om hur du migrerar från SQL Server till Azure SQL Server finns i [Självstudiekurs: Migrera SQL Server till en enda databas eller poolad databas i Azure SQL Database offline med DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Tas Hive-metabutiken bort när klustret tas bort?

Det beror på vilken typ av metabutik som klustret är konfigurerat för att använda.

För en standardmetastore: Standardmetastore är en del av klustrets livscykel. När du tar bort ett kluster tas även motsvarande metabutik och metadata bort.

För en anpassad metabutik: Metabutikens livscykel är inte knuten till ett klusters livscykel. Därför kan du skapa och ta bort kluster utan att förlora metadata. Metadata som Hive-scheman sparas även efter att du har tagit bort och återskapat HDInsight-klustret.

Mer information finns [i Använda externa metadatalager i Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Migrerar du också standardprinciperna för Ranger-databasen om du migrerar ett Hive-metalager?

Nej, principdefinitionen finns i Ranger-databasen, så att migrera Ranger-databasen migrerar principen.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Kan du migrera en Hive-metabutik från ett ESP-kluster (Enterprise Security Package) till ett kluster som inte är ESP och vice versa?

Ja, du kan migrera en Hive-metabutik från en ESP till ett kluster som inte är ESP.Yes, you can migrate a Hive metastore from an ESP to a non-ESP cluster.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hur kan jag uppskatta storleken på en Hive-databas för metalager?

En Hive-metabutik används för att lagra metadata för datakällor som används av Hive-servern. Storlekskraven beror delvis på antalet och komplexiteten i dina Hive-datakällor och kan inte uppskattas i förskott. Som beskrivs i [Hive metastore bästa praxis](hdinsight-use-external-metadata-stores.md#hive-metastore-best-practices)kan du börja med en S2-nivå, som ger 50 DTU och 250 GB lagringsutrymme, och om du ser en flaskhals kan du skala upp databasen.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Stöder du någon annan databas än Azure SQL Database som en extern metabutik?

Nej, Microsoft stöder endast Azure SQL Database som en extern anpassad metabutik.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Kan jag dela en metabutik över flera kluster?

Ja, du kan dela anpassad metabutik över flera kluster så länge de använder samma version av HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Anslutning och virtuella nätverk  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Vilka är konsekvenserna av att blockera portarna 22 och 23 i mitt nätverk?

Om du blockerar portar 22 och port 23 har du inte SSH-åtkomst till klustret. Dessa portar används inte av HDInsight-tjänsten.

Mer information finns i följande dokument:

- [Styra nätverkstrafik](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Säker inkommande trafik till HDInsight-kluster i ett virtuellt nätverk med privat slutpunkt](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [IP-adresser för HDInsight-hantering](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Kan jag distribuera ytterligare en virtuell dator i samma undernät som ett HDInsight-kluster?

Ja, du kan distribuera ytterligare en virtuell dator i samma undernät som ett HDInsight-kluster. Följande konfigurationer är möjliga:

- Kantnoder: Du kan lägga till ytterligare en kantnod i klustret, enligt beskrivningen i [Använd tomma kantnoder på Apache Hadoop-kluster i HDInsight](hdinsight-apps-use-edge-node.md).

- Fristående noder: Du kan lägga till en fristående virtuell dator i samma undernät och komma åt `https://<CLUSTERNAME>-int.azurehdinsight.net`klustret från den virtuella datorn med hjälp av den privata slutpunkten . Mer information finns i [Styra nätverkstrafik](hdinsight-plan-virtual-network-deployment.md#networktraffic).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Ska jag lagra data på den lokala disken på en kantnod?

Nej, att lagra data på en lokal disk är ingen bra idé. Om noden misslyckas kommer alla data som lagras lokalt att gå förlorade. Vi rekommenderar att du lagrar data i Azure Data Lake Storage Gen2 eller Azure Blob storage, eller genom att montera en Azure Files-resurs för lagring av data.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Kan jag lägga till ett befintligt HDInsight-kluster i ett annat virtuellt nätverk?

Nej, det kan du inte. Det virtuella nätverket bör anges vid etableringstillfället. Om inget virtuellt nätverk anges under etableringen skapar distributionen ett internt nätverk som inte är tillgängligt utifrån. Mer information finns i [Lägga till HDInsight i ett befintligt virtuellt nätverk](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Säkerhet och certifikat

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Vilka är rekommendationerna för skydd av skadlig programvara i Azure HDInsight-kluster?

Information om skydd mot skadlig programvara finns i [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Hur skapar jag en keytab för ett HDInsight ESP-kluster?

Skapa en Kerberos keytab för domänanvändarnamnet. Du kan senare använda den här keytab för att autentisera till fjärrdomänanslutna kluster utan att ange ett lösenord. Domännamnet är versaler:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Kan jag använda en befintlig Azure Active Directory-klient för att skapa ett HDInsight-kluster som har ESP?

Du måste aktivera Azure Active Directory Domain Services (Azure AD DS) innan du kan skapa ett HDInsight-kluster med ESP. Hadoop med öppen källkod förlitar sig på Kerberos för autentisering (i motsats till OAuth).

Om du vill ansluta virtuella datorer till en domän måste du ha en domänkontrollant. Azure AD DS är den hanterade domänkontrollanten och betraktas som ett tillägg av Azure Active Directory som tillhandahåller alla Kerberos-krav för att skapa ett säkert Hadoop-kluster på ett hanterat sätt. HDInsight som en hanterad tjänst integreras med Azure AD DS för att ge heltäckande säkerhet.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Kan jag använda ett självsignerat certifikat i en AAD-DS-säker LDAP-konfiguration och etablera ett ESP-kluster?

Du rekommenderas att använda ett certifikat som utfärdats av en certifikatutfärdarmyndighet, men att använda ett självsignerat certifikat stöds också på ESP. Mer information finns i:

- [Aktivera Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Självstudiekurs: Konfigurera säker LDAP för en hanterad Azure Active Directory Domain Services-domän](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Hur kan jag hämta inloggningsaktivitet som visas i Ranger?

För granskningskrav rekommenderar Microsoft att du aktiverar Azure Monitor-loggar enligt beskrivningen i [Använda Azure Monitor-loggar för att övervaka HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Kan jag inaktivera Clamscan i mitt kluster?

Clamscan är antivirusprogrammet som körs på HDInsight-klustret och används av Azure security (azsecd) för att skydda dina kluster från virusattacker. Microsoft rekommenderar starkt att användarna avstår från att göra några ändringar i standardangeljerkankonfigurationen.

Denna process stör inte eller tar några cykler bort från andra processer. Det kommer alltid att ge efter för andra processer. CPU-toppar från Clamscan bör endast ses när systemet är inaktivt.  

I scenarier där du måste styra schemat kan du använda följande steg:

1. Inaktivera automatisk körning med följande kommando:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Lägg till ett Cron-jobb som kör följande kommando som rot:
   
   `/usr/local/bin/azsecd manual -s clamav`

Mer information om hur du konfigurerar och kör ett cron-jobb finns i [Hur konfigurerar jag ett Cron-jobb?](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Varför är LLAP tillgängligt i Spark ESP-kluster?
På ESP Spark-kluster är LLAP aktiverat av säkerhetsskäl (dvs. Apache Ranger), inte prestanda. Du bör använda större virtuella nod-datorer för att hantera resursanvändningen för LLAP (t.ex. minsta D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Hur lägger jag till ytterligare AAD-grupper när jag har skapat ett ESP-kluster?
Det finns två sätt att uppnå detta: 1- Du kan återskapa klustret och lägga till ytterligare grupp när klustret skapas. Om du använder begränsad synkronisering i AAD-DS kontrollerar du att grupp B ingår i den begränsade synkroniseringen.
2- Lägg till gruppen som en kapslad undergrupp i den föregående gruppen som användes för att skapa ESP-klustret. Om du till exempel har skapat `A`ett ESP-kluster med `B` grupp kan du `A` senare lägga till grupp som en kapslad undergrupp till och efter ungefär en timme synkroniseras och är den tillgänglig i klustret automatiskt. 

## <a name="storage"></a>Lagring

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Kan jag lägga till en Azure Data Lake Storage Gen2 i ett befintligt HDInsight-kluster som ett ytterligare lagringskonto?

Nej, det är för närvarande inte möjligt att lägga till ett Azure Data Lake Storage Gen2-lagringskonto i ett kluster som har blob-lagring som primär lagring. Mer information finns i [Jämföra lagringsalternativ](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Hur hittar jag den för närvarande länkade tjänstansvarig för ett datasjölagringskonto?

Du hittar dina inställningar i **Data Lake Storage Gen1-åtkomst** under klusteregenskaperna i Azure-portalen. Mer information finns i [Verifiera klusterinställningar](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Hur beräknar jag användningen av lagringskonton och blob-behållare för mina HDInsight-kluster?

Gör något av följande:

- [Använda PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Hitta storleken på */user/hive/. Papperskorgen/mappen* i HDInsight-klustret med följande kommandorad:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Hur konfigurerar jag granskning för mitt blob storage-konto?

Om du vill granska blob-lagringskonton konfigurerar du övervakning med hjälp av proceduren på [Övervaka ett lagringskonto i Azure-portalen](../storage/common/storage-monitor-storage-account.md). En HDFS-granskningslogg innehåller endast granskningsinformation för det lokala HDFS-filsystemet (hdfs://mycluster).  Det omfattar inte åtgärder som utförs på fjärrlagring.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Hur kan jag överföra filer mellan en blob-behållare och en HDInsight-huvudnod?

Kör ett skript som liknar följande skalskript på huvudnoden:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> *Filnamnets.txt* har den absoluta sökvägen för filerna i blob-behållarna.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Finns det några Ranger plugins för lagring?

För närvarande finns ingen Ranger plugin för blob lagring och Azure Data Lake Storage Gen1 eller Gen2. För ESP-kluster bör du använda Azure Data Lake Storage, eftersom du åtminstone kan ange finkorniga behörigheter manuellt på filsystemnivå med HJÄLP AV HDFS-verktyg. När du använder Azure Data Lake Storage kommer ESP-kluster att utföra en del av filsystemåtkomstkontrollen med Hjälp av Azure Active Directory på klusternivå. 

Du kan tilldela dataåtkomstprinciper till användarnas säkerhetsgrupper med hjälp av Azure Storage Explorer. Mer information finns i:

- [Hur anger jag behörigheter för Azure AD-användare att fråga data i Data Lake Storage Gen2 med hjälp av Hive eller andra tjänster?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Ange behörigheter på fil- och katalognivå med hjälp av Azure Storage Explorer med Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Kan jag öka HDFS-lagringen i ett kluster utan att öka diskstorleken på arbetsnoder?

Nej, du kan inte öka diskstorleken för en arbetsnod, så det enda sättet att öka diskstorleken är att släppa klustret och återskapa det med större arbets-virtuella datorer. Använd inte HDFS för att lagra någon av dina HDInsight-data, eftersom data tas bort om du tar bort klustret. Spara i stället dina data i Azure. Skala upp klustret kan också lägga till ytterligare kapacitet till ditt HDInsight-kluster.

## <a name="edge-nodes"></a>Gränsnoder

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Kan jag lägga till en kantnod när klustret har skapats?

HDInsight-kluster eller till ett nytt kluster när du skapar klustret. Mer information finns i [Använda tomma gränsnoder i Apache Hadoop-kluster i HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Hur kan jag ansluta till en kantnod?

När du har skapat en kantnod kan du ansluta till den med hjälp av SSH på port 22. Du hittar namnet på kantnoden från klusterportalen. Namnen slutar vanligtvis med *-ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Varför körs inte beständiga skript automatiskt på nyskapade kantnoder?

Du använder beständiga skript för att anpassa nya arbetsnoder som lagts till i klustret genom skalningsåtgärder. Beständiga skript gäller inte för kantnoder.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Vilka är REST API-anrop för att hämta en Tez-frågevy från klustret?

Du kan använda följande REST-slutpunkter för att hämta nödvändig information i JSON-format. Använd grundläggande autentiseringshuvuden för att göra begäranden.

- Tez Query View: *https:\//\<klusternamn>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- Tez Dag View: *https:\//\<klusternamn>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Hur hämtar jag konfigurationsinformationen från HDI-klustret med hjälp av en Azure Active Directory-användare?

Om du vill förhandla om korrekta autentiseringstoken med AAD-användaren går du igenom gatewayen med hjälp av följande format:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Hur använder jag Ambari Restful API för att övervaka YARN-prestanda?

Om du anropar kommandot Curl i samma virtuella nätverk eller ett peer-virtuellt nätverk är kommandot:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Om du anropar kommandot utanför det virtuella nätverket eller från ett icke-peer-virtuellt nätverk är kommandoformatet:

- För ett kluster som inte är ESP:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- För ett ESP-kluster:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl kommer att fråga dig om ett lösenord. Du måste ange ett giltigt lösenord för användarnamnet för klusterinloggning.

## <a name="billing"></a>Fakturering

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Hur mycket kostar det att distribuera ett HDInsight-kluster?

Mer information om priser och vanliga frågor som rör fakturering finns på sidan [Azure HDInsight Pricing.](https://azure.microsoft.com/pricing/details/hdinsight/)

### <a name="when-does-hdinsight-billing-start--stop"></a>När börjar HDInsight-faktureringen & sluta?

Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Faktureringen är proportionellt per minut.

### <a name="how-do-i-cancel-my-subscription"></a>Hur säger jag upp min prenumeration?

Information om hur du avbryter prenumerationen finns i [Avbryt din Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Vad händer när jag har sagt upp min prenumeration när jag har sagt upp min prenumeration?

Information om din prenumeration när prenumerationen har avbrutits finns i [Vad händer när jag har sagt upp min prenumeration?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Varför visas Hive-versionen som 1.2.1000 istället för 2.1 i Ambari UI trots att jag kör ett HDInsight 3.6-kluster?

Även om endast 1,2 visas i Ambari UI, innehåller HDInsight 3.6 både Hive 1.2 och Hive 2.1.

## <a name="other-faq"></a>Andra vanliga frågor och svar

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Vad erbjuder HDInsight när det gäller funktioner för strömhantering i realtid?

Information om integreringsfunktionerna för dataflödesbearbetning i Azure HDInsight finns [i Välja en dataströmsbearbetningsteknik i Azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Finns det något sätt att dynamiskt avsluta huvudnoden för klustret när klustret är inaktivt under en viss period?

Du kan inte göra detta med HDInsight-kluster. Du kan använda Azure Data Factory för dessa scenarier.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Vilka efterlevnadserbjudanden erbjuder HDInsight?

Information om efterlevnad finns i [Microsoft Trust Center](https://www.microsoft.com/trust-center) och [översikten över Microsoft Azure-efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).
