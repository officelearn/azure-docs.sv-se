---
title: Utveckling av skriptåtgärder med HDInsight - Azure
description: Lär dig hur du anpassar Hadoop-kluster med skriptåtgärd. Skriptåtgärd kan användas för att installera ytterligare programvara som körs på ett Hadoop-kluster eller ändra konfigurationen för program som har installerats på ett kluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: d8f7808401b2e11a38b239a353e3b7af2ffcffb3
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361310"
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Utveckla skriptåtgärder skript för HDInsight Windows-baserade kluster
Lär dig hur du skriver skript för skriptåtgärd för HDInsight. Information om hur du använder skriptåtgärd skript finns i [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md). Samma artikel skrivna för Linux-baserade HDInsight-kluster finns i [utveckla skriptåtgärder skript till HDInsight](hdinsight-hadoop-script-actions-linux.md).


> [!IMPORTANT]  
> Stegen i det här dokumentet fungerar endast för Windows-baserade HDInsight-kluster. HDInsight är endast tillgänglig på Windows för versioner lägre än HDInsight 3.4. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Information om hur du använder skriptåtgärder med Linux-baserade kluster finns i [utveckling av skriptåtgärder med HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).


Skriptåtgärd kan användas för att installera ytterligare programvara som körs på ett Apache Hadoop-kluster eller ändra konfigurationen för program som har installerats på ett kluster. Skriptåtgärder är skript som körs på noderna i klustret när HDInsight-kluster distribueras, och de utförs när noder i klustret Slutför konfiguration av HDInsight. En skriptåtgärd körs under kontot för systemadministratörsprivilegier och ger fullständiga åtkomsträttigheter till klusternoderna. Varje kluster kan anges med en lista över skriptåtgärder som ska köras i den ordning som de anges.

> [!NOTE]  
> Om du får följande felmeddelande visas:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage : Termen ”spara HDIFile' identifieras inte som namnet på en cmdlet, funktion, skriptfil eller ett körbart program. Kontrollera stavningen av namnet, eller om en sökväg har inkluderats, kontrollera att sökvägen är korrekt och försök igen.
> 
> Det beror på att du skickat helper-metoder.  Se [hjälpkomponentmetoder för anpassade skript](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sample-scripts"></a>Exempelskript
För att skapa HDInsight-kluster på Windows-operativsystem, är den skriptåtgärd Azure PowerShell-skript. Följande skript är ett exempel för att konfigurera site konfigurationsfiler:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

Skriptet använder fyra parametrar, namnet på konfigurationsfilen, egenskapen som du vill ändra värdet du vill använda, och en beskrivning. Exempel:

    hive-site.xml hive.metastore.client.socket.timeout 90

Dessa parametrar värdet hive.metastore.client.socket.timeout till 90 i filen hive-site.xml.  Standardvärdet är 60 sekunder.

Det här exempelskriptet finns även på [ https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1 ](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight innehåller flera skript för att installera ytterligare komponenter i HDInsight-kluster:

| Namn | Skript |
| --- | --- |
| **Installera Spark** | `https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1`. Se [installera och använda Apache Spark i HDInsight-kluster][hdinsight-install-spark]. |
| **Installera R** | `https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1`. Se [installera och använda R i HDInsight-kluster](r-server/r-server-hdinsight-manage.md#install-additional-r-packages-on-the-cluster). |
| **Installera Giraph** | `https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1`. Se [installera och använda Apache Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install.md). |
| **Förhandsladda Hive-bibliotek** | `https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1`. Se [lägga till Apache Hive-bibliotek på HDInsight-kluster](hdinsight-hadoop-add-hive-libraries.md) |


Skriptåtgärd kan distribueras från Azure-portalen, Azure PowerShell eller med hjälp av HDInsight .NET SDK.  Mer information finns i [anpassa HDInsight-kluster med skriptåtgärd] [hdinsight-kluster – anpassa].

> [!NOTE]  
> Exempel på skript fungerar bara med HDInsight-kluster av version 3.1 eller senare. Läs mer på HDInsight-klusterversioner [HDInsight-klusterversioner](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Hjälpkomponentmetoder för anpassade skript
Skriptet åtgärd hjälpmetoder är verktyg som du kan använda när du skriver anpassade skript. Dessa metoder definieras i [ https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1 ](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1), och kan tas med i dina skript med hjälp av följande exempel:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Här följer hjälpmetoder som tillhandahålls av det här skriptet:

| Hjälpmetoden | Beskrivning |
| --- | --- |
| **Save-HDIFile** |Hämta en fil från den angivna identifieraren URI (Uniform Resource) till en plats på den lokala disken som är associerad med den Virtuella Azure-noden som tilldelats i klustret. |
| **Expand-HDIZippedFile** |Packa upp ZIP-filer. |
| **Invoke-HDICmdScript** |Köra ett skript från cmd.exe. |
| **Write-HDILog** |Skriva utdata från det anpassade skriptet som används för en skriptåtgärd. |
| **Get-tjänster** |Hämta en lista över tjänster som körs på datorn där skriptet körs. |
| **Get-tjänst** |Med specifika tjänstnamnet som indata, får du detaljerad information för en specifik tjänst (namn på tjänst, process-ID, tillstånd och så vidare) på den dator där skriptet körs. |
| **Get-HDIServices** |Hämta en lista över HDInsight-tjänster som körs på datorn där skriptet körs. |
| **Get-HDIService** |Med specifika HDInsight tjänstnamnet som indata, får du detaljerad information för en specifik tjänst (namn på tjänst, process-ID, tillstånd och så vidare) på den dator där skriptet körs. |
| **Get-ServicesRunning** |Hämta en lista över tjänster som körs på datorn där skriptet körs. |
| **Get-ServiceRunning** |Kontrollera om en specifik tjänst (efter namn) körs på datorn där skriptet körs. |
| **Get-HDIServicesRunning** |Hämta en lista över HDInsight-tjänster som körs på datorn där skriptet körs. |
| **Get-HDIServiceRunning** |Kontrollera om en specifik HDInsight-tjänst (efter namn) körs på datorn där skriptet körs. |
| **Get-HDIHadoopVersion** |Hämta versionen av Hadoop som är installerad på den dator där skriptet körs. |
| **Test-IsHDIHeadNode** |Kontrollera om den dator där skriptet körs är en huvudnod. |
| **Test-IsActiveHDIHeadNode** |Kontrollera om den dator där skriptet körs är en aktiv huvudnod. |
| **Test-IsHDIDataNode** |Kontrollera om den dator där skriptet körs är en datanod. |
| **Edit-HDIConfigFile** |Redigera config filer hive-site.xml, core-site.xml, hdfs-site.xml, mapred site.xml eller yarn-site.xml. |

## <a name="best-practices-for-script-development"></a>Metodtips för skriptutveckling av
När du utvecklar ett anpassat skript för ett HDInsight-kluster kan finns det flera bästa praxis att tänka på:

* Söka efter den Hadoop-versionen.

    Endast HDInsight version 3.1 (Hadoop 2.4) och senare support med skriptåtgärder för att installera anpassade komponenter i ett kluster. I ett skript, måste du använda den **Get-HDIHadoopVersion** helper-metoden för att kontrollera Hadoop-versionen innan du fortsätter att utföra andra uppgifter i skriptet.
* Ange stabil länkar till skriptresurser.

    Användare bör se till att alla skript och andra artefakter som används i anpassningen av ett kluster förblir tillgängliga under hela klustrets livslängd och att versionerna av dessa filer inte ändrar för hela. De här resurserna krävs om återställningen av avbildningen av noder i klustret måste anges. Det bästa sättet är att hämta och arkivera allt i ett lagringskonto som du styr. Det här kontot kan vara standardkontot för lagring eller någon av de ytterligare lagringskonton som anges vid tidpunkten för distribution för ett anpassade kluster.
    Anpassade kluster-exempel i Spark- och R angivna i dokumentationen, exempelvis det är en lokal kopia av resurserna i det här lagringskontot: https://hdiconfigactions.blob.core.windows.net/.
* Kontrollera att klustret anpassning skriptet är idempotent.

    Du får räkna med att noderna i ett HDInsight-kluster är avbildning återställs under klustrets livstid. Klustret anpassning skript körs när ett kluster återställs. Det här skriptet måste utformas ska vara idempotenta i den mening att efter återställningen av avbildningen, skriptet bör se till att klustret returneras till samma anpassade tillstånd som den var i bara när skriptet kördes för första gången när klustret skapades från början. Till exempel om ett anpassat skript har installerat ett program vid D:\AppLocation på den första körningen, sedan på varje efterföljande körning vid återställning av avbildning, skriptet ska kontrollera om programmet finns på D:\AppLocation plats innan du fortsätter med andra steg i den skript.
* Installera anpassade komponenter i den optimala platsen.

    Om klusternoderna är avbildning kan C:\ resource enhet och D:\ systemenhet formateras om, vilket resulterar i förlust av data och program som har installerats på dessa enheter. Den här förlusten kan också inträffa om en Azure-dator (VM)-nod som är en del av klustret slutar fungera och ersätts av en ny nod. Du kan installera komponenter på D:\-enhet eller på plats C:\apps på klustret. Alla andra platser på enhet C:\ är reserverade. Ange platsen där program eller bibliotek ska installeras i klustret anpassning skriptet.
* Garantera hög tillgänglighet för klusterarkitekturen.

    HDInsight har en aktiv-passiv arkitektur för hög tillgänglighet, där en huvudnod är i aktivt läge (där HDInsight-tjänsterna körs) och andra Huvudnoden är i vänteläge (i vilken HDInsight tjänsterna inte körs). Noderna växla mellan lägena aktiva och passiva om HDInsight tjänster avbryts. Om en skriptåtgärd används för att installera tjänster på både huvudnoderna för hög tillgänglighet, Observera att mekanismen för HDInsight-redundans inte kan automatiskt växla över tjänsterna användarinstallerade. Så användarinstallerade tjänster på HDInsight huvudnoder som förväntas ha hög tillgänglighet måste antingen ha sina egna redundans sätt om du är i aktivt-passivt läge eller vara i aktivt-aktivt läge.

    Ett HDInsight-skriptåtgärder-kommando som körs på både huvudnoderna när rollen huvudnod anges som ett värde i den *ClusterRoleCollection* parametern. Så när du utformar ett anpassat skript, se till att skriptet är medveten om den här installationen. Du bör inte stöter på problem där samma tjänster har installerats och startats på både huvudnoderna och de hamnar konkurrerar med varandra. Tänk också på att data går förlorad under återställningen av avbildningen, så att programvaran via skriptåtgärd måste vara motståndskraftiga mot sådana händelser. Program bör utformas för att arbeta med med hög tillgänglighet data som är fördelade på flera noder. Upp till 1/5 noder i ett kluster kan att återställa avbildning på samma gång.
* Konfigurera anpassade komponenter om du vill använda Azure Blob storage.

    Med anpassade komponenter som du installerar på klusternoderna kan ha en standardkonfiguration för att använda Hadoop Distributed File System (HDFS) lagring. Du bör ändra konfigurationen för att använda Azure Blob storage i stället. På en kluster-reimage HDFS-filsystemets hämtar formaterad och du förlorar alla data som lagras där. Med Azure Blob storage i stället säkerställer att dina data finns kvar.

## <a name="common-usage-patterns"></a>Vanliga mönster
Det här avsnittet innehåller anvisningar om hur du implementerar några av de vanliga användningsmönster som kan uppstå vid skrivning till ett eget skript.

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler
I skriptåtgärdsutveckling, kan du ofta anser att behöva ange miljövariabler. Exempelvis är mest sannolika scenariot när du hämtar en binär från en extern webbplats, installera den på klustret och Lägg till platsen för där det har installerats ”sökväg”-miljövariabeln. Följande kodfragment visar hur du ställer in miljövariabler i det anpassade skriptet.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Den här instruktionen anger miljövariabeln **MDS_RUNNER_CUSTOM_CLUSTER** till värdet ”true” och även anger omfattningen för den här variabeln ska vara hela datorn. Det är viktigt att miljövariabler är inställda på lämplig omfattning – dator eller användare. Se [här] [ 1] för mer information om hur du anger miljövariabler.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Åtkomst till platser där de anpassade skript lagras
Skript som används för att anpassa ett kluster måste antingen vara i standardkontot för lagring för klustret eller i en offentlig skrivskyddad behållare för andra storage-konto. Om skriptet har åtkomst till resurser som finns någon annanstans måste resurserna läsas offentligt. Du kanske exempelvis vill få åtkomst till en fil och spara den med hjälp av kommandot SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

I det här exemplet måste du kontrollera att behållaren `somecontainer` i storage-konto `somestorageaccount` är allmänt tillgänglig. I annat fall skriptet genererar ett ”kunde inte hittas”-undantag och misslyckas.

### <a name="pass-parameters-to-the-add-azhdinsightscriptaction-cmdlet"></a>Skicka parametrar till cmdleten Add-AzHDInsightScriptAction
Om du vill skicka flera parametrar till cmdleten Add-AzHDInsightScriptAction, måste du formatera strängvärde så att den innehåller alla parametrar för skriptet. Exempel:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

eller

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Undantag för misslyckade Klusterdistribution
Om du vill meddelas korrekt av det faktum att klusteranpassning lyckades inte som förväntat, är det viktigt att ett undantag och skapa ett kluster att misslyckas. Du kanske exempelvis vill bearbeta en fil om den finns och hantera felsituation där filen inte finns. Detta skulle se till att skriptet avslutas utan problem och kallas korrekt tillstånd för klustret. Följande kodavsnitt ger ett exempel på hur du gör det:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

I det här kodfragmentet om att filen inte finns, skulle det leda till ett tillstånd där skriptet faktiskt avslutas ett smidigt sätt efter att skriva ut ett felmeddelande och klustret når körtillstånd förutsatt att den ”” slutförts anpassningsprocess av klustret. Om du vill meddelas korrekt om det faktum att klusteranpassning i stort sett lyckades inte som förväntat på grund av en saknad fil, är det mer effektivt att ett undantag och misslyckas kluster anpassning steg. Om du vill åstadkomma detta måste du använda följande exempel kodfragment i stället.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Checklista för distribution av en skriptåtgärd
Här är de steg som vi tog när du förbereder att distribuera dessa skript:

1. Placera de filer som innehåller anpassade skript på en plats som kan nås av noderna i klustret under distributionen. Detta kan vara standard eller ytterligare Storage-konton som anges vid tidpunkten för distribution av kluster eller annan offentligt tillgänglig storage-behållare.
2. Lägga till kontroller i skript för att se till att de kör idempotently, så att skriptet kan köras flera gånger på samma nod.
3. Använd den `Write-Output` Azure PowerShell-cmdlet för att skriva ut till STDOUT och STDERR. Använd inte `Write-Host`.
4. Använda en tillfällig mapp, till exempel `$env:TEMP`, för att hålla den hämta filen som används av skripten och sedan rensa dem när skript har körts.
5. Installera anpassad programvara endast på D:\ eller C:\apps. Andra platser på C:-enheten ska inte användas som de är reserverade. Installerar filer på C:-enheten utanför mappen C:\apps kan resultera i installationsfel under avbildningen av noden.
6. I händelse av att OS-inställningar eller konfigurationsfiler för Hadoop-tjänsten har ändrats, kan du starta om HDInsight-tjänster så att de kan ta över alla inställningar för OS-nivå, till exempel miljövariabler som anges i skripten.

## <a name="debug-custom-scripts"></a>Felsök anpassade skript
Felloggarna för skriptet lagras tillsammans med andra utdata i Storage-kontot som angetts för klustret när den har skapandet. Loggfilerna lagras i en tabell med namnet *u < \cluster-name-fragment >< \time-stamp > setuplog*. Det här är sammanställda loggfiler som har poster från alla noder (huvudnoden och arbetsnoder) som skriptet körs i klustret.

Ett enkelt sätt att kontrollera loggarna är att använda HDInsight Tools för Visual Studio. Installera verktyg, finns i [Kom igång med Visual Studio Hadoop-verktyg för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio)

**Kontrollera loggen med hjälp av Visual Studio**

1. Öppna Visual Studio.
2. Klicka på **visa**, och klicka sedan på **Server Explorer**.
3. Högerklicka på ”Azure”, klicka på Anslut till **Microsoft Azure-prenumerationer**, och ange dina autentiseringsuppgifter.
4. Expandera **Storage**, expandera Azure storage-kontot som används som standardfilsystem, expandera **tabeller**, och dubbelklicka sedan på namnet på tabellen.

Du kan även fjärransluta till noderna i klustret för att se både STDOUT och STDERR för anpassade skript. Loggarna på varje nod som är specifika för noden och är inloggad på **C:\HDInsightLogs\DeploymentAgent.log**. Dessa loggfiler registrera alla utdata från det anpassade skriptet. Ett exempel log kodfragmentet för en Spark-skriptåtgärd ser ut så här:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


I den här loggen är det tydligt att instruktionen Spark-skriptet har körts på den virtuella datorn med namnet HEADNODE0 och att inga undantag utlöstes vid körningen.

I händelse av att ett körningsfel inträffar ingå utdata som beskriver den också i den här loggfilen. Informationen i de här loggarna ska vara användbara vid felsökning av problem med skript som kan uppstå.

## <a name="see-also"></a>Se också
* [Anpassa HDInsight-kluster med skriptåtgärd] [hdinsight-kluster – anpassa]
* [Installera och använda Apache Spark på HDInsight-kluster][hdinsight-install-spark]
* [Installera och använda Apache Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
