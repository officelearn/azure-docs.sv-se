---
title: Regional haveri beredskap för Azure Databricks
description: I den här artikeln beskrivs en metod för att utföra haveri beredskap i Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 8f68bbb4e73758e44e775e1c0c23ad007ca60aa2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84016941"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regional haveri beredskap för Azure Databricks kluster

I den här artikeln beskrivs en katastrof återställnings arkitektur som är användbar för Azure Databricks kluster och stegen för att utföra den här designen.

## <a name="azure-databricks-architecture"></a>Azure Databricks arkitektur

När du skapar en Azure Databricks arbets yta från Azure Portal på hög nivå distribueras en [hanterad](../azure-resource-manager/managed-applications/overview.md) installation som en Azure-resurs i din prenumeration, i den valda Azure-regionen (till exempel västra USA). Den här installationen är distribuerad i en [Azure-Virtual Network](../virtual-network/virtual-networks-overview.md) med en [nätverks säkerhets grupp](../virtual-network/manage-network-security-group.md) och ett Azure Storage konto som är tillgängligt i din prenumeration. Det virtuella nätverket tillhandahåller gräns nivå säkerhet till arbets ytan Databricks och skyddas via nätverks säkerhets gruppen. I arbets ytan kan du skapa Databricks-kluster genom att ange arbets-och driv Rutinens VM-typ och Databricks runtime-version. De sparade data är tillgängliga i ditt lagrings konto, som kan vara Azure Blob Storage eller Azure Data Lake Storage. När klustret har skapats kan du köra jobb via antecknings böcker, REST-API: er, ODBC/JDBC-slutpunkter genom att koppla dem till ett enskilt kluster.

Databricks kontroll planet hanterar och övervakar arbets ytan Databricks. Alla hanterings åtgärder, till exempel skapa kluster, kommer att initieras från kontroll planet. Alla metadata, till exempel schemalagda jobb, lagras i en Azure Database med geo-replikering för fel tolerans.

![Databricks-arkitektur](media/howto-regional-disaster-recovery/databricks-architecture.png)

En av fördelarna med den här arkitekturen är att användarna kan ansluta Azure Databricks till valfri lagrings resurs i sitt konto. En viktig fördel är att både beräkning (Azure Databricks) och lagring kan skalas oberoende av varandra.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Så här skapar du en regional haveri beredskaps topologi

Som du ser i föregående Beskrivning av arkitekturen, finns det ett antal komponenter som används för en stor data pipeline med Azure Databricks: Azure Storage, Azure Database och andra data källor. Azure Databricks är *beräkningen* för stor data pipelinen. Det är en *tillfällig* natur, vilket innebär att medan dina data fortfarande är tillgängliga i Azure Storage, kan *beräkningen* (Azure Databricks-kluster) avbrytas så att du inte behöver betala för beräkning när du inte behöver det. Data *bearbetningen* (Azure Databricks) och lagrings källorna måste vara i samma region så att jobben inte får hög latens.  

Följ dessa krav för att skapa en egen regional haveri beredskaps topologi:

   1. Etablera flera Azure Databricks arbets ytor i separata Azure-regioner. Skapa till exempel den primära Azure Databricks arbets ytan i öst-2; USA. Skapa den sekundära haveri återställnings Azure Databricks arbets ytan i en separat region, till exempel västra USA.

   2. Använd [Geo-redundant lagring](../storage/common/storage-redundancy.md). De data som associeras Azure Databricks lagras som standard i Azure Storage. Resultaten från Databricks-jobb lagras också i Azure Blob Storage, så att de bearbetade data är varaktiga och förblir hög tillgängliga när klustret har avslut ATS. Eftersom lagrings-och Databricks-klustret är samplacerade måste du använda Geo-redundant lagring så att data kan nås i den sekundära regionen om den primära regionen inte längre är tillgänglig.

   3. När den sekundära regionen har skapats måste du migrera användare, användarmappar, bärbara datorer, kluster konfiguration, jobb konfiguration, bibliotek, lagring, init-skript och konfigurera om åtkomst kontroll. Ytterligare information beskrivs i följande avsnitt.

## <a name="detailed-migration-steps"></a>Detaljerade migreringssteg

1. **Konfigurera kommando rads gränssnittet för Databricks på datorn**

   I den här artikeln visas ett antal kod exempel som använder kommando rads gränssnittet för de flesta automatiserade steg, eftersom det är en lätt att användare i Azure Databricks REST API.

   Innan du utför några steg i migreringen installerar du databricks-CLI på din station ära dator eller på en virtuell dator där du planerar att utföra arbetet. Mer information finns i [Installera DATABRICKS CLI](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Alla Python-skript som anges i den här artikeln förväntas fungera med python 2.7 + < 3. x.

2. **Konfigurera två profiler.**

   Konfigurera en för den primära arbets ytan och en annan för den sekundära arbets ytan:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Kod blocken i den här artikeln växlar mellan profiler i varje efterföljande steg med motsvarande arbets ytans kommando. Se till att namnen på profilerna som du skapar ersätts med varje kodblock.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Du kan manuellt växla till kommando raden vid behov:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrera Azure Active Directory användare**

   Lägg manuellt till samma Azure Active Directory användare till den sekundära arbets ytan som finns på den primära arbets ytan.

4. **Migrera användarens mappar och antecknings böcker**

   Använd följande python-kod för att migrera de begränsade användar miljöerna, som inkluderar den kapslade mappstrukturen och antecknings böckerna per användare.

   > [!NOTE]
   > Bibliotek kopieras inte över i det här steget eftersom det underliggande API: t inte stöder dem.

   Kopiera och spara följande Python-skript till en fil och kör det i din Databricks-kommando rad. Till exempel `python scriptname.py`.

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

   När antecknings böckerna har migrerats kan du också migrera klusterkonfigurationer till den nya arbets ytan. Det är nästan ett helt automatiserat steg med databricks-CLI, om du inte vill göra en selektiv kluster konfigurations migrering i stället för alla.

   > [!NOTE]
   > Det finns tyvärr ingen skapa kluster konfigurations slut punkt och det här skriptet försöker skapa varje kluster direkt. Om det inte finns tillräckligt många tillgängliga kärnor i din prenumeration kan det hända att klustret inte kan skapas. Felen kan ignoreras så länge konfigurationen har överförts.

   Följande skript tillhandahöll skriver ut en mappning från gamla till nya kluster-ID: n, som kan användas för att migrera jobb senare (för jobb som är konfigurerade att använda befintliga kluster).

   Kopiera och spara följande Python-skript till en fil och kör det i din Databricks-kommando rad. Till exempel `python scriptname.py`.

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

6. **Migrera jobb konfigurationen**

   Om du har migrerat klusterkonfigurationer i föregående steg kan du välja att migrera jobb konfigurationerna till den nya arbets ytan. Det är ett helt automatiserat steg med databricks-CLI, om du inte vill göra en selektiv jobb konfigurations migrering i stället för att göra det för alla jobb.

   > [!NOTE]
   > Konfigurationen för ett schemalagt jobb innehåller även "schema"-informationen, så som standard börjar arbetet enligt konfigurationen per konfigurerad tid så snart den migreras. Därför tar följande kodblock bort eventuell schema information under migreringen (för att undvika dubbla körningar i gamla och nya arbets ytor). Konfigurera scheman för sådana jobb när du är redo för start punkt.

   Jobb konfigurationen kräver inställningar för ett nytt eller ett befintligt kluster. Om du använder ett befintligt kluster försöker skriptet/Code nedan att ersätta det gamla kluster-ID: t med nytt kluster-ID.

   Kopiera och spara följande Python-skript till en fil. Ersätt värdet för `old_cluster_id` och `new_cluster_id` , med utdata från kluster migreringen som utfördes i föregående steg. Kör det på kommando raden för databricks-CLI, till exempel `python scriptname.py` .

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

   Det finns för närvarande inget enkelt sätt att migrera bibliotek från en arbets yta till en annan. Installera i stället dessa bibliotek i den nya arbets ytan manuellt. Det är möjligt att automatisera användningen av [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) för att ladda upp anpassade bibliotek till arbets ytan och [biblioteks-CLI](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrera Azure Blob Storage och Azure Data Lake Storage monteras**

   Montera manuellt alla [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage) och [Azure Data Lake Storage (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) monterings punkter med en Notebook-baserad lösning. Lagrings resurserna skulle ha monterats på den primära arbets ytan och måste upprepas på den sekundära arbets ytan. Det finns inget externt API för monteringar.

9. **Migrera kluster init-skript**

   Eventuella kluster initierings skript kan migreras från gammal till ny arbets yta med hjälp av [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Kopiera först de skript som krävs från `dbfs:/dat abricks/init/..` till ditt lokala skriv bord eller virtuell dator. Kopiera sedan dessa skript till den nya arbets ytan på samma sökväg.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Konfigurera om och tillämpa åtkomst kontroll manuellt.**

    Om din befintliga primära arbets yta har kon figurer ATS för att använda Premium nivån (SKU), är det troligt att du också använder [Access Control funktionen](/azure/databricks/administration-guide/access-control/index).

    Om du använder funktionen Access Control, tillämpa manuellt åtkomst kontrollen på resurserna (antecknings böcker, kluster, jobb, tabeller).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Haveri beredskap för ditt Azure-eko system

Om du använder andra Azure-tjänster bör du även implementera metod tips för haveri beredskap för dessa tjänster. Om du till exempel väljer att använda en extern Hive-metaarkiv-instans bör du överväga haveri beredskap för [Azure SQL Database](../azure-sql/database/disaster-recovery-guidance.md), [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)och/eller [Azure Database for MySQL](../mysql/concepts-business-continuity.md). Allmän information om haveri beredskap finns i [haveri beredskap för Azure-program](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Azure Databricks-dokumentationen](index.yml).
