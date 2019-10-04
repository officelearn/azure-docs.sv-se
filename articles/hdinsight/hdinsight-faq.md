---
title: Vanliga frågor och svar om Azure HDInsight
description: Vanliga frågor och svar om HDInsight
keywords: Vanliga frågor och svar om vanliga frågor och svar
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 6bff2210e77f7af98c1289b08159a89f42f2a3bd
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827609"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Vanliga frågor och svar

Den här artikeln innehåller svar på några av de vanligaste frågorna om hur du kör [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Skapa eller ta bort HDInsight-kluster

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Hur gör jag för att etablera ett HDInsight-kluster?

Om du vill granska de typer av HDInsight-kluster som är tillgängliga och etablerings metoderna, se [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Hur gör jag för att ta bort ett befintligt HDInsight-kluster?

Om du vill veta mer om hur du tar bort ett kluster när det inte längre används, se [ta bort ett HDInsight-kluster](hdinsight-delete-cluster.md).

Lämna minst 30 till 60 minuter mellan åtgärderna skapa och ta bort. Annars kan åtgärden Miss lyckas med följande fel meddelande:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Hur gör jag för att välja rätt antal kärnor eller noder för min arbets belastning?

Lämpligt antal kärnor och andra konfigurations alternativ beror på olika faktorer.

Mer information finns i [kapacitets planering för HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Vad kan jag göra när kluster etableringen Miss lyckas på grund av ett kapacitets problem?

Vanliga fel och lösningar för kapacitets problem finns i det här avsnittet.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Fel: Distributionen skulle överskrida kvoten på 800

Azure har en kvot på 800 distributioner per resursgrupp. Olika kvoter tillämpas per resurs grupp, prenumeration, konto eller andra omfång. Din prenumeration kan till exempel konfigureras så att antalet kärnor för en region begränsas. Om du försöker distribuera en virtuell dator som har fler kärnor än vad som tillåts visas ett fel meddelande som anger att kvoten överskreds.

Lös problemet genom att ta bort de distributioner som inte längre behövs med hjälp av Azure Portal, CLI eller PowerShell.

Mer information finns i [Åtgärda fel med resurskvoter](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Fel: Den maximala noden överskred de tillgängliga kärnorna i den här regionen

Din prenumeration kanske har konfigurerats så att antalet kärnor för en region begränsas. Om du försöker distribuera en resurs som har fler kärnor än vad som tillåts visas ett fel meddelande som anger att kvoten överskreds.

Följ dessa steg om du vill begära en kvotökning:

1. Gå till [Azure Portal](https://portal.azure.com)och välj **Hjälp + Support**.
   
1. Välj **Ny supportbegäran**.
   
1. På fliken **grundläggande** på sidan **ny supportbegäran** anger du följande information:
   
   - **Typ av problem:** Välj **tjänst-och prenumerations gränser (kvoter)** .
   - **Prenumeration:** Välj den prenumeration som du vill ändra.
   - **Kvot typ:** Välj **HDInsight**.

Mer information finns i [Skapa ett supportärende för att öka antalet kärnor](hdinsight-capacity-planning.md#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Vilka är de olika typerna av noder i ett HDInsight-kluster?

Azure HDInsight-kluster har olika typer av virtuella datorer eller noder. Varje nodtyp spelar en roll i systemets funktion.

Mer information finns i [resurs typer i Azure HDInsight-kluster](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Enskilda komponenter

### <a name="can-i-install-additional-components-on-my-cluster"></a>Kan jag installera ytterligare komponenter i mitt kluster?

Ja. Om du vill installera ytterligare komponenter eller anpassa kluster konfigurationen använder du:

- Skript under eller efter skapandet. Skript anropas via [skript åtgärd](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), vilket är ett konfigurations alternativ som du kan använda från Azure Portal, HDInsight Windows PowerShell-cmdletar eller HDInsight .NET SDK. Det här konfigurations alternativet kan användas från Azure Portal, HDInsight Windows PowerShell-cmdletar eller HDInsight .NET SDK.

- `sudo`eller andra metoder när du har etablerat klustret.
  
- [HDInsight-program plattform](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) för att installera eko system program.

Microsoft Support Team kan dock endast erbjuda support i följande situationer:

- Problem eller fel som uppstår vid inläsning av skriptet. Eventuella fel som uppstår under körningen av anpassade skript ligger utanför omfånget för ett support ärende.

- Ytterligare program som är en del av klustrets skapande process. 

En lista över komponenter som stöds finns i [vilka Apache Hadoop-komponenter och versioner som är tillgängliga med HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

Stöd för enskilda komponenter kan också variera beroende på klustertyp. Till exempel stöds inte spark i ett Kafka-kluster och vice versa.

Kontakta leverantören eller tjänst leverantören för att få support för program och tjänster utanför klustrets skapande process. Du kan också använda många community-support webbplatser. Exempel är [MSDN-forumet för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) och [Stack Overflow](https://stackoverflow.com/). Apache-projekt har också projekt webbplatser på [Apache-webbplatsen](https://apache.org/). Ett exempel är [Hadoop](https://hadoop.apache.org/). 

Mer information om support för Azure finns i [vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq/).

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Kan jag uppgradera de enskilda komponenterna som är förinstallerade i klustret?

Om du uppgraderar inbyggda komponenter eller program som är förinstallerade i klustret kommer den resulterande konfigurationen inte att stödjas av Microsoft. Dessa system konfigurationer har inte testats av Microsoft. Försök att använda en annan version av HDInsight-klustret som redan har den uppgraderade versionen av komponenten som är installerad.

Det går till exempel inte att uppgradera Hive som en enskild komponent. HDInsight är en hanterad tjänst och många tjänster är integrerade med Ambari-servern och testas. Att uppgradera en Hive på egen hand leder till att indexerade binärfiler för andra komponenter ändras och kan orsaka komponent integrerings problem i klustret.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Kan Spark och Kafka köras i samma HDInsight-kluster?

Nej, det går inte att köra Apache Kafka och Apache Spark i samma HDInsight-kluster. Skapa separata kluster för Kafka och Spark för att undvika problem med resurs konflikter.

### <a name="how-do-i-change-timezone-in-ambari"></a>Hur gör jag för att ändra tidszon i Ambari?

1. Öppna Ambari-webbgränssnittet https://CLUSTERNAME.azurehdinsight.net på, där kluster namn är namnet på klustret.
2. I det övre högra hörnet väljer du admin | Autentiseringsinställningar. 

   ![Ambari-inställningar](media/hdinsight-faq/ambari-settings.png)

3. I fönstret användar inställningar väljer du den nya tids zonen från List rutan tidszon och klickar sedan på Spara.

   ![Ambari användar inställningar](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metaarkiv

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Hur kan jag migrera från det befintliga metaarkiv till Azure SQL Server? 

Information om hur du migrerar från SQL Server till [Azure SQL Server finns i Självstudier: Migrera SQL Server till en enskild databas eller en databas i pooler i Azure SQL Database offline med](../dms/tutorial-sql-server-to-azure-sql.md)DMS.

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Tas Hive-metaarkiv bort när klustret tas bort?

Det beror på vilken typ av metaarkiv som klustret är konfigurerat för att använda.

För en standard metaarkiv: Standard-metaarkiv är en del av kluster livs cykeln. När du tar bort ett kluster raderas även motsvarande metaarkiv och metadata.

För en anpassad metaarkiv: Livs cykeln för metaarkiv är inte kopplad till ett klusters livs cykel. Därför kan du skapa och ta bort kluster utan att förlora metadata. Metadata, till exempel Hive-scheman, behålls även efter att du tagit bort och återskapat HDInsight-klustret.

Mer information finns i [använda externa metadata butiker i Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Migrerar en Hive-metaarkiv även standard principerna för Ranger-databasen?

Nej, princip definitionen finns i Ranger-databasen, så migreringen av Ranger-databasen migrerar sin princip.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Kan du migrera en Hive-metaarkiv från ett Enterprise Security Package-kluster (ESP) till ett icke-ESP-kluster och vice versa?

Ja, du kan migrera en Hive-metaarkiv från en ESP till ett icke-ESP-kluster.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hur kan jag uppskatta storleken på en Hive-metaarkiv databas?

En Hive-metaarkiv används för att lagra metadata för data källor som används av Hive-servern. Storleks kraven beror delvis på antalet och komplexiteten för dina Hive-datakällor och kan inte uppskattas överst. Som beskrivs i [Hive-metaarkiv bästa praxis](hdinsight-use-external-metadata-stores.md#hive-metastore-best-practices)kan du starta med en S2-nivå, vilket ger 50 DTU och 250 GB lagring, och om du ser en Flask hals kan du skala upp databasen.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Stöder du andra databaser än Azure SQL Database som externa metaarkiv?

Nej, Microsoft stöder bara Azure SQL Database som en extern anpassad metaarkiv.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Kan jag dela en metaarkiv över flera kluster?

Ja, du kan dela anpassade metaarkiv över flera kluster så länge de använder samma version av HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Anslutningar och virtuella nätverk  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Vad är konsekvenserna av att blockera portarna 22 och 23 i mitt nätverk?

Om du blockerar portarna 22 och port 23 kommer du inte att ha SSH-åtkomst till klustret. Dessa portar används inte av HDInsight-tjänsten.

Mer information finns i följande dokument:

- [Kontrol lera nätverks trafik](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Skydda inkommande trafik till HDInsight-kluster i ett virtuellt nätverk med privat slut punkt](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [Hanterings-IP-adresser för HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Kan jag distribuera en ytterligare virtuell dator i samma undernät som ett HDInsight-kluster?

Ja, du kan distribuera en ytterligare virtuell dator i samma undernät som ett HDInsight-kluster. Följande konfigurationer är möjliga:

- Edge-noder: Du kan lägga till ytterligare en Edge-nod i klustret, enligt beskrivningen i [använda tomma Edge-noder i Apache Hadoop kluster i HDInsight](hdinsight-apps-use-edge-node.md).

- Fristående noder:  Du kan lägga till en fristående virtuell dator i samma undernät och komma åt klustret från den virtuella datorn med hjälp av den privata slut `https://<CLUSTERNAME>-int.azurehdinsight.net`punkten. Mer information finns i [kontrol lera nätverks trafik](hdinsight-plan-virtual-network-deployment.md#networktraffic).

### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Kan jag lägga till ett befintligt HDInsight-kluster i ett annat virtuellt nätverk?

Nej, du kan inte. Det virtuella nätverket ska anges vid tidpunkten för etableringen. Om inget virtuellt nätverk anges under etableringen skapar distributionen ett internt nätverk som inte är tillgängligt utanför. Mer information finns i [lägga till HDInsight i ett befintligt virtuellt nätverk](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Säkerhet och certifikat

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Vilka är rekommendationerna för skydd mot skadlig kod på Azure HDInsight-kluster?

Information om skydd mot skadlig kod finns i [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Hur gör jag för att skapa en keytab för ett HDInsight ESP-kluster?

Skapa en Kerberos-keytab för ditt domän användar namn. Du kan senare använda den här keytab för att autentisera till fjärranslutna domänanslutna kluster utan att ange ett lösen ord. Domän namnet är versaler:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Kan jag använda en befintlig Azure Active Directory-klient för att skapa ett HDInsight-kluster med ESP?

Du måste aktivera Azure Active Directory Domain Services (Azure AD DS) innan du kan skapa ett HDInsight-kluster med ESP. Hadoop med öppen källkod förlitar sig på Kerberos för autentisering (till skillnad från OAuth).

Om du vill ansluta virtuella datorer till en domän måste du ha en domänkontrollant. Azure AD DS är den hanterade domänkontrollanten och betraktas som en utökning av Azure Active Directory som innehåller alla Kerberos-krav för att bygga ett säkert Hadoop-kluster på ett hanterat sätt. HDInsight som en hanterad tjänst integreras med Azure AD DS för att tillhandahålla säkerhet från slut punkt till slut punkt.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Kan jag använda ett självsignerat certifikat i en AAD-DS säker LDAP-installation och etablera ett ESP-kluster?

Du rekommenderas att använda ett certifikat som utfärdats av en certifikat utfärdare, men det går också att använda ett självsignerat certifikat på ESP. Mer information finns i:

- [Aktivera Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Självstudier: Konfigurera säker LDAP för en Azure Active Directory Domain Services hanterad domän](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Hur kan jag hämta inloggnings aktivitet som visas i Ranger?

För gransknings krav rekommenderar Microsoft att du aktiverar Azure Monitor loggar enligt beskrivningen i [använda Azure Monitor loggar för att övervaka HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Kan jag inaktivera clamscan i mitt kluster?

Clamscan är antivirus programmet som körs i HDInsight-klustret och används av Azure Security (azsecd) för att skydda dina kluster från virus angrepp. Microsoft rekommenderar starkt att användarna inte gör några ändringar i standard konfigurationen för clamscan.

Den här processen stör inte eller tar inga cykler bort från andra processer. Den kommer alltid att ge till gång till andra processer. PROCESSOR toppar från clamscan bör endast visas när systemet är inaktivt.  

I scenarier där du måste kontrol lera schemat kan du använda följande steg:

1. Inaktivera automatisk körning med följande kommando:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Lägg till ett cron-jobb som kör följande kommando som rot:
   
   `/usr/local/bin/azsecd manual -s clamav`

Mer information om hur du konfigurerar och kör ett cron-jobb finns i [Hur gör jag för att konfigurera ett cron-jobb](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Kan jag lägga till en Azure Data Lake Storage Gen2 till ett befintligt HDInsight-kluster som ett ytterligare lagrings konto?

Nej, det är för närvarande inte möjligt att lägga till ett Azure Data Lake Storage Gen2 lagrings konto till ett kluster som har blob-lagring som primär lagring. Mer information finns i [jämföra lagrings alternativ](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Hur hittar jag den för närvarande länkade tjänstens huvud namn för ett Data Lake lagrings konto?

Du kan hitta inställningarna i **data Lake Storage gen1 åtkomst** under kluster egenskaperna i Azure Portal. Mer information finns i [Verifiera kluster konfiguration](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Hur kan jag beräkna användningen av lagrings konton och blob-behållare för mina HDInsight-kluster?

Gör något av följande:

- [Använda PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Hitta storleken på */User/Hive/. Pappers korgen/* mapp i HDInsight-klustret med följande kommando rad:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Hur kan jag konfigurera granskning för mitt Blob Storage-konto?

Om du vill granska Blob Storage-konton konfigurerar du övervakning med hjälp av proceduren i [övervaka ett lagrings konto i Azure Portal](../storage/common/storage-monitor-storage-account.md). En HDFS – Gransknings logg innehåller bara gransknings information för det lokala HDFS-filsystemet (hdfs://mycluster).  Den omfattar inte åtgärder som utförs på Fjärrlagring.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Hur kan jag överföra filer mellan en BLOB-behållare och en HDInsight-Head-nod?

Kör ett skript som liknar följande Shell-skript på Head-noden:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Filen *filenames. txt* kommer att ha den absoluta sökvägen till filerna i BLOB-behållarna.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Finns det några Ranger-plugin-program för lagring?

För närvarande finns det inget Ranger-plugin-program för Blob Storage och Azure Data Lake Storage Gen1 eller Gen2. För ESP-kluster bör du använda Azure Data Lake Storage, eftersom du kan minst ange detaljerade behörigheter manuellt på fil system nivå med HDFS-verktyg. När du använder Azure Data Lake Storage kommer ESP-kluster också att göra en del av åtkomst kontrollen för fil system med Azure Active Directory på kluster nivå. 

Du kan tilldela data åtkomst principer till dina användares säkerhets grupper med hjälp av Azure Storage Explorer. Mer information finns i:

- [Hur gör jag för att ställer du in behörigheter för Azure AD-användare att fråga efter data i Data Lake Storage Gen2 med hjälp av Hive eller andra tjänster?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Ange behörigheter för fil-och katalog nivå med Azure Storage Explorer med Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Kan jag öka HDFS-lagringen i ett kluster utan att öka disk storleken för arbetsnoder?

Nej, du kan inte öka disk storleken på en arbetsnoden, så det enda sättet att öka disk storleken är att släppa klustret och återskapa det med större virtuella datorer i arbets gruppen. Använd inte HDFS för att lagra någon av dina HDInsight-data eftersom data tas bort om du tar bort klustret. Lagra dina data i stället i Azure. Att skala upp klustret kan också lägga till ytterligare kapacitet i ditt HDInsight-kluster.

## <a name="edge-nodes"></a>Gränsnoder

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Kan jag lägga till en Edge-nod efter att klustret har skapats?

HDInsight-kluster eller till ett nytt kluster när klustret skapas. Mer information finns i [Använda tomma gränsnoder i Apache Hadoop-kluster i HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Hur kan jag ansluta till en Edge-nod?

När du har skapat en Edge-nod kan du ansluta till den med hjälp av SSH på port 22. Du kan hitta namnet på Edge-noden från kluster portalen. Namnen slutar vanligt vis med *-Ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Varför körs inte de sparade skripten automatiskt på nyskapade Edge-noder?

Du använder sparade skript för att anpassa nya arbetsnoder som läggs till i klustret genom skalnings åtgärder. Bestående skript gäller inte för Edge-noder.

## <a name="rest-api"></a>REST-API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Vad är REST API anrop för att hämta en Tez från klustret?

Du kan använda följande REST-slutpunkter för att hämta nödvändig information i JSON-format. Använd grundläggande autentiseringsscheman för att göra begär Anden.

- Tez-frågevy: *https: \/ @ no__t-2 @ no__t-3cluster name >. azurehdinsight. net/WS/v1/Timeline/HIVE_QUERY_ID/*
- Tez dag vy: *https: \/ @ no__t-2 @ no__t-3cluster namn >. azurehdinsight. net/WS/v1/Timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Hur gör jag för att hämta konfigurations information från HDI-kluster med hjälp av en Azure Active Directory användare?

Om du vill förhandla rätt autentiserings-token med din AAD-användare går du igenom gatewayen med hjälp av följande format:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Hur gör jag för att använda Ambari RESTful API för att övervaka garn prestanda?

Om du anropar kommandot spiral i samma virtuella nätverk eller ett peer-kopplat virtuellt nätverk är kommandot:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Om du anropar kommandot från utanför det virtuella nätverket eller från ett icke-peer-kopplat virtuellt nätverk är kommando formatet:

- För ett icke-ESP-kluster:
  
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
> Du uppmanas att ange ett lösen ord. Du måste ange ett giltigt lösen ord för användar namnet för kluster inloggning.

## <a name="billing"></a>Fakturering

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Hur mycket kostar det att distribuera ett HDInsight-kluster?

Mer information om priser och vanliga frågor och svar om fakturering finns på pris sidan för [Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) .

### <a name="when-does-hdinsight-billing-start--stop"></a>När ska HDInsight-faktureringen starta & stoppa?

Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Faktureringen beräknas proportionellt per minut.

### <a name="how-do-i-cancel-my-subscription"></a>Hur gör jag för att avbryta min prenumeration?

Information om hur du avbryter din prenumeration finns i [avbryta din Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Vad händer om du betalar per användning, vad händer när jag har avbrutit min prenumeration?

Information om din prenumeration när den har avbrutits finns i [Vad händer när jag har avbrutit min prenumeration?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Varför visas Hive-versionen som 1.2.1000 i stället för 2,1 i Ambari-ANVÄNDARGRÄNSSNITTET trots att jag kör ett HDInsight 3,6-kluster?

Även om 1,2 visas i Ambari-ANVÄNDARGRÄNSSNITTET innehåller HDInsight 3,6 både Hive 1,2 och Hive 2,1.

## <a name="other-faq"></a>Andra vanliga frågor och svar

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Vad är HDInsight-erbjudandet vad gäller real tids data ström bearbetnings funktioner?

Information om integrations funktionerna i Stream-bearbetning i Azure HDInsight finns i [välja en teknik för ström bearbetning i Azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Finns det något sätt att dynamiskt avsluta klustrets huvud nod när klustret är inaktivt under en viss period?

Du kan inte göra detta med HDInsight-kluster. Du kan använda Azure Data Factory för dessa scenarier.
