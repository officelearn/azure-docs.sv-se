---
title: Regional haveriberedskap för Azure Databricks
description: I den här artikeln beskrivs en metod för att göra haveriberedskap i Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 2604d5b357feacce3493b4a4ded971144262611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161944"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regional haveriberedskap för Azure Databricks-kluster

I den här artikeln beskrivs en arkitektur för haveriberedskap som är användbar för Azure Databricks-kluster och stegen för att utföra den designen.

## <a name="azure-databricks-architecture"></a>Azure Databricks-arkitektur

På en hög nivå, när du skapar en Azure Databricks-arbetsyta från Azure-portalen, distribueras en [hanterad installation](../azure-resource-manager/managed-applications/overview.md) som en Azure-resurs i din prenumeration, i den valda Azure-regionen (till exempel västra USA). Den här installationen distribueras i ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md) med en [nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md) och ett Azure Storage-konto som är tillgängligt i din prenumeration. Det virtuella nätverket ger perimeternivåsäkerhet till Databricks arbetsyta och skyddas via nätverkssäkerhetsgrupp. Inom arbetsytan kan du skapa Databricks-kluster genom att tillhandahålla den virtuella typen av arbets- och drivrutinstyp och Databricks runtime-version. Beständiga data är tillgängliga i ditt lagringskonto, som kan vara Azure Blob Storage eller Azure Data Lake Storage. När klustret har skapats kan du köra jobb via anteckningsböcker, REST-API:er, ODBC/JDBC-slutpunkter genom att koppla dem till ett visst kluster.

Databricks styrplan hanterar och övervakar Databricks arbetsytemiljö. Alla hanteringsåtgärder som skapa kluster initieras från kontrollplanet. Alla metadata, till exempel schemalagda jobb, lagras i en Azure-databas med geo-replikering för feltolerans.

![Databricks arkitektur](media/howto-regional-disaster-recovery/databricks-architecture.png)

En av fördelarna med den här arkitekturen är att användare kan ansluta Azure Databricks till alla lagringsresurser i sitt konto. En viktig fördel är att både beräkning (Azure Databricks) och lagring kan skalas oberoende av varandra.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Så här skapar du en regional katastrofåterställningstopologi

Som du märker i föregående arkitekturbeskrivning finns det ett antal komponenter som används för en Big Data-pipeline med Azure Databricks: Azure Storage, Azure Database och andra datakällor. Azure Databricks är *beräkningen* för Big Data-pipelinen. Det är *efemärt* till sin natur, vilket innebär att medan dina data fortfarande är tillgängliga i Azure Storage, kan *beräkningen* (Azure Databricks-klustret) avslutas så att du inte behöver betala för beräkning när du inte behöver den. *Beräknings-(Azure* Databricks) och lagringskällor måste finnas i samma region så att jobben inte får hög svarstid.  

Så här skapar du en egen regional katastrofåterställningstopologi:

   1. Etablera flera Azure Databricks-arbetsytor i separata Azure-regioner. Skapa till exempel den primära Azure Databricks-arbetsytan i östra US2. Skapa den sekundära azure databricks-arbetsytan för katastrofåterställning i en separat region, till exempel västra USA.

   2. Använd [geo-redundant lagring](../storage/common/storage-redundancy.md). De data associerade Azure Databricks lagras som standard i Azure Storage. Resultaten från Databricks-jobb lagras också i Azure Blob Storage, så att de bearbetade data är varaktiga och förblir mycket tillgängliga när klustret har avslutats. Eftersom lagrings- och databricks-klustret finns samtidigt måste du använda Geo-redundant lagring så att data kan nås i sekundär region om den primära regionen inte längre är tillgänglig.

   3. När den sekundära regionen har skapats måste du migrera användare, användarmappar, anteckningsböcker, klusterkonfiguration, jobbkonfiguration, bibliotek, lagring, init-skript och konfigurera om åtkomstkontroll. Ytterligare information beskrivs i följande avsnitt.

## <a name="detailed-migration-steps"></a>Detaljerade migreringssteg

1. **Konfigurera databricks-kommandoradsgränssnittet på datorn**

   Den här artikeln visar ett antal kodexempel som använder kommandoradsgränssnittet för de flesta av de automatiserade stegen, eftersom det är ett lättanvänt omslag över Azure Databricks REST API.

   Innan du utför några migreringssteg installerar du databricks-cli på din stationära dator eller en virtuell dator där du planerar att utföra arbetet. Mer information finns i [Installera Databricks CLI](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Alla pythonskript som finns i den här artikeln förväntas fungera med Python 2.7+ < 3.x.

2. **Konfigurera två profiler.**

   Konfigurera en för den primära arbetsytan och en annan för den sekundära arbetsytan:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Kodblocken i den här artikeln växlar mellan profiler i varje efterföljande steg med motsvarande arbetsytas kommando. Se till att namnen på de profiler du skapar ersätts med varje kodblock.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Du kan växla manuellt på kommandoraden om det behövs:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrera Azure Active Directory-användare**

   Lägg manuellt till samma Azure Active Directory-användare på den sekundära arbetsytan som finns på den primära arbetsytan.

4. **Migrera användarmappar och anteckningsböcker**

   Använd följande python-kod för att migrera användarmiljöer i begränsat läge, som innehåller den kapslade mappstrukturen och anteckningsböcker per användare.

   > [!NOTE]
   > Bibliotek kopieras inte över i det här steget, eftersom det underliggande API:et inte stöder dessa.

   Kopiera och spara följande pythonskript i en fil och kör det på kommandoraden Databricks. Till exempel `python scriptname.py`.

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

5. **Migrera klusterkonfigurationerna**

   När anteckningsböcker har migrerats kan du också migrera klusterkonfigurationerna till den nya arbetsytan. Det är nästan ett helt automatiserat steg med databricks-cli, om du inte vill göra selektiv kluster config migrering snarare än för alla.

   > [!NOTE]
   > Tyvärr finns det ingen skapa kluster config slutpunkt, och det här skriptet försöker skapa varje kluster direkt. Om det inte finns tillräckligt med kärnor som är tillgängliga i prenumerationen kan klusterskapandet misslyckas. Felet kan ignoreras så länge konfigurationen har överförts.

   Följande skript som innehåller utskrifter en mappning från gamla till nya kluster-ID: er, som kan användas för jobbmigrering senare (för jobb som är konfigurerade för att använda befintliga kluster).

   Kopiera och spara följande pythonskript i en fil och kör det på kommandoraden Databricks. Till exempel `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json, os

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE])
   clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids
   clusters_list = []
   ##for cluster_info in clusters_info_list: clusters_list.append(cluster_info.split(None, 1)[0])

   for cluster_info in clusters_info_list: 
      if cluster_info != '':
         clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf","node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","spark_env_vars","autotermination_minutes","enable_elastic_disk"]

   print(str(len(clusters_list)) + " clusters found in the primary site" )

   print ("---------------------------------------------------------")
   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {}
   i = 0
   for cluster in clusters_list:
      i += 1
      print("Checking cluster " + str(i) + "/" + str(len(clusters_list)) + " : " + cluster)
      cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
      print ("Got cluster config from old workspace")

       # Remove extra content from the config, as we need to build create request with allowed elements only
      cluster_req_json = json.loads(cluster_get_out)
      cluster_json_keys = cluster_req_json.keys()

      #Don't migrate Job clusters
      if cluster_req_json['cluster_source'] == u'JOB' : 
         print ("Skipping this cluster as it is a Job cluster : " + cluster_req_json['cluster_id'] )
         print ("---------------------------------------------------------")
         continue

      for key in cluster_json_keys:
         if key not in cluster_req_elems:
            cluster_req_json.pop(key, None)

      # Create the cluster, and store the mapping from old to new cluster ids

      #Create a temp file to store the current cluster info as JSON
      strCurrentClusterFile = "tmp_cluster_info.json" 

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

      fClusterJSONtmp = open(strCurrentClusterFile,"w+")
      fClusterJSONtmp.write(json.dumps(cluster_req_json))
      fClusterJSONtmp.close()

      #cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE])
      cluster_create_out = check_output(["databricks", "clusters", "create", "--json-file", strCurrentClusterFile , "--profile", IMPORT_PROFILE])
      cluster_create_out_json = json.loads(cluster_create_out)
      cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

      print ("Cluster create request sent to secondary site workspace successfully")
      print ("---------------------------------------------------------")

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

   print ("Cluster mappings: " + json.dumps(cluster_old_new_mappings))
   print ("All done")
   print ("P.S. : Please note that all the new clusters in your secondary site are being started now!")
   print ("       If you won't use those new clusters at the moment, please don't forget terminating your new clusters to avoid charges")
   ```

6. **Migrera jobbkonfigurationen**

   Om du har migrerat klusterkonfigurationer i föregående steg kan du välja att migrera jobbkonfigurationer till den nya arbetsytan. Det är ett helt automatiserat steg med databricks-cli, om du inte vill göra selektiv jobb config migrering snarare än att göra det för alla jobb.

   > [!NOTE]
   > Konfigurationen för ett schemalagt jobb innehåller också "schemainformation", så som standard börjar den fungera enligt konfigurerad tid så snart den har migrerats. Därför tar följande kodblock bort all schemainformation under migreringen (för att undvika dubblettkörningar över gamla och nya arbetsytor). Konfigurera scheman för sådana jobb när du är redo för cutover.

   Jobbkonfigurationen kräver inställningar för ett nytt kluster eller ett befintligt kluster. Om du använder befintligt kluster försöker skriptet /koden nedan att ersätta det gamla kluster-ID:t med nytt kluster-ID.

   Kopiera och spara följande pythonskript i en fil. Ersätt värdet `old_cluster_id` för `new_cluster_id`och , med utdata från klustermigrering gjort i föregående steg. Kör den i din databricks-cli kommandorad, `python scriptname.py`till exempel .

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

   Det finns för närvarande inget enkelt sätt att migrera bibliotek från en arbetsyta till en annan. Installera i stället om biblioteken i den nya arbetsytan manuellt. Det är möjligt att automatisera med hjälp av en kombination av [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) för att ladda upp anpassade bibliotek till arbetsytan och [bibliotek CLI](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrera Azure blob storage och Azure Data Lake Storage-fästen**

   Montera om alla [Azure Blob-lagring](/azure/databricks/data/data-sources/azure/azure-storage) och [Azure Data Lake Storage (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) montera punkter manuellt med hjälp av en anteckningsboksbaserad lösning. Lagringsresurserna skulle ha monterats på den primära arbetsytan och som måste upprepas på den sekundära arbetsytan. Det finns inget externt API för fästen.

9. **Migrera kluster init-skript**

   Alla klusterinitiseringsskript kan migreras från gammal till ny arbetsyta med [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Kopiera först de skript `dbfs:/dat abricks/init/..` som behövs från till det lokala skrivbordet eller den virtuella datorn. Kopiera sedan skripten till den nya arbetsytan på samma sökväg.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Konfigurera om och tillämpa åtkomstkontrollen manuellt.**

    Om din befintliga primära arbetsyta är konfigurerad för att använda Premium-nivån (SKU) är det troligt att du också använder [funktionen Åtkomstkontroll](/azure/databricks/administration-guide/access-control/index).

    Om du använder funktionen Snabbkontroll använder du åtkomstkontrollen manuellt på resurserna (anteckningsböcker, kluster, jobb, tabeller).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Haveriberedskap för ditt Azure-ekosystem

Om du använder andra Azure-tjänster, se till att implementera metodtips för katastrofåterställning för dessa tjänster också. Om du till exempel väljer att använda en extern Hive-metastore-instans bör du överväga haveriberedskap för [Azure SQL Server](../sql-database/sql-database-disaster-recovery.md), Azure [HDInsight](../hdinsight/hdinsight-high-availability-linux.md)och/eller [Azure Database for MySQL](../mysql/concepts-business-continuity.md). Allmän information om haveriberedskap finns i [Haveriberedskap för Azure-program](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Azure Databricks-dokumentationen](index.yml).