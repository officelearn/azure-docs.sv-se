---
title: Regional haveriberedskap för Azure Databricks
description: Den här artikeln beskriver en metod för att göra katastrofåterställning i Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/27/2018
ms.openlocfilehash: a42d2c75913b2c9fdfa0d2b7c3ec2742525a4c97
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806104"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regional haveriberedskap för Azure Databricks-kluster

Den här artikeln beskrivs en disaster recovery-arkitekturen användbart för Azure Databricks-kluster och de steg du utför den designen.

## <a name="azure-databricks-architecture"></a>Azure Databricks-arkitektur

På en hög nivå när du skapar en Azure Databricks-arbetsyta från Azure-portalen en [hanterade installation](../managed-applications/overview.md) distribueras som en Azure-resurs i din prenumeration på den valda Azure-regionen (till exempel USA, västra). Den här installationen har distribuerats i en [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) med en [Nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md) och ett Azure Storage-konto, som finns i din prenumeration. Det virtuella nätverket innehåller nivå perimetersäkerhet till Databricks-arbetsytan och skyddas via nätverkssäkerhetsgruppen. Du kan skapa Databricks-kluster genom att tillhandahålla worker och driver typ av virtuell dator och Databricks körningsversion i arbetsytan. Beständiga data är tillgängliga i ditt storage-konto som kan vara Azure Blob Storage eller Azure Data Lake Store. När klustret har skapats kan köra du jobb via notebooks, REST API: er, ODBC-/ JDBC slutpunkter genom att koppla dem till ett specifikt kluster.

Databricks kontrollplanet hanterar och övervakar miljön för Databricks-arbetsyta. Alla management-åtgärder som till exempel när du skapar klustret kommer att initieras från kontrollplanet. Alla metadata, till exempel schemalagda jobb lagras i en Azure-databas med geo-replikering för feltolerans.

![Databricks-arkitektur](media/howto-regional-disaster-recovery/databricks-architecture.png)

En av fördelarna med den här arkitekturen är att användare kan ansluta Azure Databricks till en lagringsresurs i sitt konto. En viktig fördel är att båda compute (Azure Databricks) och lagring skalas oberoende av varandra.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Så här skapar du en regionala disaster recovery-topologi

Som du ser i föregående arkitektur beskrivning, finns det ett antal komponenter som används för en Big Data-pipeline med Azure Databricks:  Azure Storage, Azure-databas och andra datakällor. Azure Databricks är den *compute* Big Data i pipeline. Det är *tillfälliga* sin natur, vilket innebär att även om dina data finns kvar i Azure Storage, den *compute* (Azure Databricks-kluster) kan avbrytas så att du inte behöver betala för compute när du behövs inte. Den *compute* (Azure Databricks) och molnlagringskällor måste vara i samma region så att jobben inte drabbas av fördröjningar.  

Om du vill skapa egna regionalt haveri recovery topologi, följer du dessa krav:

   1. Etablera flera Azure Databricks-arbetsytor i separata Azure-regioner. Skapa till exempel den primära Azure Databricks-arbetsytan i östra usa2. Skapa Azure Databricks-arbetsytan sekundära haveriberedskap i en separat region, till exempel USA, västra.

   2. Använd [Geo-redundant lagring](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Data associerade Azure Databricks lagras som standard i Azure Storage. Resultaten från Databricks-jobb lagras också i Azure Blob Storage, så att bearbetade data är beständiga och förblir mycket tillgängliga när klustret avslutas. Eftersom lagrings- och Databricks-kluster finns på samma plats, måste du använda Geo-redundant lagring så att data kan användas i andra regionen, om den primära regionen är inte längre tillgänglig.

   3. När den sekundära regionen har skapats kan du migrera användare, arbetsmappar för användare, anteckningsböcker, klusterkonfiguration, konfiguration av jobb, bibliotek, lagring, init skript och konfigurera om åtkomstkontroll. I följande avsnitt beskrivs ytterligare information.

## <a name="detailed-migration-steps"></a>Detaljerade steg

1. **Konfigurera kommandoradsgränssnittet Databricks på datorn**

   Den här artikeln visar ett antal kodexempel som använder kommandoradsgränssnittet för de flesta av de automatiserade stegen, eftersom det är en enkel-till-användare-omslutning över Azure Databricks REST API.

   Innan du utför migreringssteg att installera databricks-cli på din dator eller en virtuell dator som du planerar att göra jobbet. Mer information finns i [installera Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Alla python-skript i den här artikeln förväntas att arbeta med Python 2.7 + < 3.x.

2. **Konfigurera två profiler.**

   Konfigurera en för den primära arbetsytan, och en annan för den sekundära arbetsytan:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   De kodblock i den här artikeln växla mellan profiler i varje efterföljande steg med kommandot motsvarande arbetsyta. Var noga med att namnen på profilerna som du skapar ersätts i varje kodblock.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Du kan manuellt växla på kommandoraden om det behövs:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrera Azure Active Directory-användare**

   Lägga till samma Azure Active Directory-användare manuellt till arbetsytan sekundära som finns i den primära arbetsytan.

4. **Migrera användarmappar och anteckningsböcker**

   Använd följande python-kod för att migrera i begränsat läge användaren miljöer, bland annat kapslade mappstrukturen och bärbara datorer per användare.

   > [!NOTE]
   > Bibliotek kopieras inte över i det här steget eftersom det underliggande API: T inte stöder de.

   Kopiera och spara följande python-skriptet i en fil och kör det i din Databricks-kommandoraden. Till exempel `python scriptname.py`.

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **Migrera klusterkonfigurationer**

   När bärbara datorer har migrerats, kan du om du vill migrera klusterkonfigurationerna till den nya arbetsytan. Det är nästan ett helt automatiserade steg med databricks cli om du vill göra selektiv config migrering i stället för alla.

   > [!NOTE]
   > Det finns tyvärr ingen skapa klustret config slutpunkt och det här skriptet försöker skapa varje kluster direkt. Om det finns inte tillräckligt många kärnor i prenumerationen, kan skapa ett kluster misslyckas. Felet kan ignoreras så länge som överförs konfigurationen har.

   Följande skript som skriver ut en mappning från den gamla till nya kluster-ID, som skulle kunna användas för jobbet migrering senare (för jobb som är konfigurerade för att använda befintliga kluster).

   Kopiera och spara följande python-skriptet i en fil och kör det i din Databricks-kommandoraden. Till exempel `python scriptname.py`.

   ```python
   from subprocess import call, check_output import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace 
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE]) clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids 
   clusters_list = [] for cluster_info in clusters_info_list:   clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements 
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf"," node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","sp ark_env_vars","autotermination_minutes","enable_elastic_disk"]

   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {} for cluster in clusters_list:   print "Trying to migrate cluster " + cluster

   cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
   print "Got cluster config from old workspace"

   # Remove extra content from the config, as we need to build create request with allowed elements only
   cluster_req_json = json.loads(cluster_get_out)    
   cluster_json_keys = cluster_req_json.keys()   

   for key in cluster_json_keys:     
      if key not in cluster_req_elems:       
         cluster_req_json.pop(key, None)
  
   # Create the cluster, and store the mapping from old to new cluster ids
   cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE]) 
   cluster_create_out_json = json.loads(cluster_create_out)   
   cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

   print "Sent cluster create request to new workspace successfully"

   print "Cluster mappings: " + json.dumps(cluster_old_new_mappings)
   print "All done"
   ```

6. **Migrera konfigurationen av jobb**

   Om du har migrerat klusterkonfigurationer i föregående steg, kan du välja om du vill migrera jobbkonfigurationer till den nya arbetsytan. Det är en helt automatiserad steget med databricks cli, om du vill utföra selektiv jobbet config-migrering i stället för att göra det för alla jobb.

   > [!NOTE]
   > Konfigurationen för ett schemalagt jobb innehåller ”” schemainformation, så som standard som kommer att börja arbeta enligt konfigurerade tidpunkten när den har migrerats. Följande kodblock tar därför bort alla schemainformation under migreringen (för att undvika dubbla körningarna över gamla och nya arbetsytor). Konfigurera scheman för sådana jobb när du är redo för snabb lösning.

   Jobbkonfigurationen kräver inställningar för ett nytt eller ett befintligt kluster. Om du använder befintligt kluster i skriptet /code nedan kommer att försöka att ersätta det gamla klustra-ID med nytt kluster-ID.

   Kopiera och spara följande python-skriptet till en fil. Ersätt värdet för `old_cluster_id` och `new_cluster_id`, med utdata från klustermigrering som i föregående steg. Kör det i din databricks cli från kommandoraden, till exempel `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **Migrera bibliotek**

   Det finns för närvarande inget enkelt sätt migrera bibliotek från en arbetsyta till en annan. I stället installera om dessa bibliotek i den nya arbetsytan manuellt. Det är möjligt att automatisera kombination av [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) att ladda upp anpassade bibliotek till arbetsytan och [bibliotek CLI](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrera Azure blob storage och Azure Data Lake Store monterar**

   Manuellt montera om alla [Azure Blob storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html) och [Azure Data Lake Store (Gen 1)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html) monteringspunkter med hjälp av en notebook-baserad lösning. Lagringsresurserna skulle har monterats på den primära arbetsytan, och som har ska upprepas på arbetsytan sekundär. Det finns inga externa API: T för monterar.

9. **Migrera kluster init skript**

   Initieringsskript några kluster kan migreras från den gamla till ny arbetsyta med hjälp av den [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Först kopiera de nödvändiga skript från `dbfs:/dat abricks/init/..` till din lokala dator eller virtuell dator. Kopiera dessa skript till den nya arbetsytan på samma sökväg.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Manuellt omkonfigurera och tillämpa åtkomstkontroll.**

   Om din befintliga primära arbetsytan är konfigurerad för att använda Premium-nivån (SKU), är det troligt att du även använder den [Access Control-funktionen](https://docs.azuredatabricks.net/administration-guide/admin-settings/index.html#manage-access-control).

   Om du använder funktionen Access Control, manuellt tillämpa åtkomstkontrollen till resurser (anteckningsböcker, kluster, jobb, tabeller).

## <a name="next-steps"></a>Nästa steg
Mer information finns i [dokumentation för Azure Databricks](https://docs.azuredatabricks.net/user-guide/index.html).
