---
title: Utveckling av skriptåtgärder med HDInsight - Azure | Microsoft Docs
description: Lär dig hur du anpassar Hadoop-kluster med skriptåtgärder. Skriptåtgärder kan användas för att installera ytterligare programvara som körs på ett Hadoop-kluster eller ändra konfigurationen av program på ett kluster.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 836d68a8-8b21-4d69-8b61-281a7fe67f21
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 921da2db8e235e17611788cae7e976597bd76703
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Utveckla skriptåtgärd skript för HDInsight Windows-baserade kluster
Lär dig hur du skriver skript för skriptåtgärder för HDInsight. Information om hur du använder skriptåtgärd skript finns [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster.md). Samma artikel skrivna för Linux-baserade HDInsight-kluster, se [utveckla skriptåtgärd skript för HDInsight](hdinsight-hadoop-script-actions-linux.md).



> [!IMPORTANT]
> Stegen i det här dokumentet fungerar endast för Windows-baserade HDInsight-kluster. HDInsight är endast tillgängligt i Windows för versioner som är lägre än HDInsight 3.4. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Information om användning av skriptåtgärder med Linux-baserade kluster finns i [utveckling av skriptåtgärder med HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).
>
>



Skriptåtgärder kan användas för att installera ytterligare programvara som körs på ett Hadoop-kluster eller ändra konfigurationen av program på ett kluster. Skriptåtgärder avses skript som körs på klusternoderna när HDInsight-kluster distribueras och de utförs när noder i klustret Slutför HDInsight-konfigurationen. En skriptåtgärd körs under kontot systemadministratörsprivilegier och ger fullständig behörighet till klusternoderna. Varje kluster kan anges med en lista över skriptåtgärder ska utföras i den ordning som de har angetts.

> [!NOTE]
> Om du får följande felmeddelande:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage: Termen ”spara HDIFile' identifieras inte som namnet på en cmdlet, funktion, skriptfilen eller körbart program. Kontrollera stavningen av namnet, eller om en sökväg ingick, kontrollera att sökvägen är korrekt och försök igen.
> Det beror på att du inte inkludera helper-metoder.  Se [hjälpmetoder för anpassade skript](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).
>
>

## <a name="sample-scripts"></a>Exempelskript
För att skapa HDInsight-kluster på Windows operativsystem, är den skriptåtgärd Azure PowerShell-skript. Följande skript är ett exempel för att konfigurera platsen konfigurationsfiler:

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

Skriptet använder fyra parametrar, namnet på konfigurationsfilen, egenskapen du vill ändra värdet som du vill ange, och en beskrivning. Exempel:

    hive-site.xml hive.metastore.client.socket.timeout 90

Dessa parametrar anger hive.metastore.client.socket.timeout-värdet till 90 i filen hive-site.XML.  Standardvärdet är 60 sekunder.

Det här exempelskriptet finns även på [ https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1 ](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight tillhandahåller flera skript för att installera ytterligare komponenter i HDInsight-kluster:

| Namn | Skript |
| --- | --- |
| **Installera Spark** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Se [installera och använda Spark i HDInsight-kluster][hdinsight-install-spark]. |
| **Installera R** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Se [installera och använda R i HDInsight-kluster] [hdinsight-r-skript]. |
| **Installera Solr** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Se [installerar och använder Solr på HDInsight-kluster](hdinsight-hadoop-solr-install.md). |
| - **Installera Giraph** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Se [installerar och använder Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install.md). |

Skriptåtgärder kan distribueras från Azure-portalen, Azure PowerShell eller med hjälp av HDInsight .NET SDK.  Mer information finns i [anpassa HDInsight-kluster med skriptåtgärder][hdinsight-cluster-customize].

> [!NOTE]
> Exempel på skript fungerar bara med HDInsight-kluster av version 3.1 eller senare. Mer information om HDInsight-kluster-versioner finns [HDInsight-kluster-versioner](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Hjälpmetoder för anpassade skript
Skriptet åtgärd helper metoder är verktyg som du kan använda vid skrivning till anpassade skript. Dessa metoder är definierade i [ https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1 ](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1), och kan ingå i ditt skript med hjälp av följande exempel:

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

Här följer hjälpmetoder som tillhandahålls av skriptet:

| Hjälpmetod | Beskrivning |
| --- | --- |
| **Spara HDIFile** |Hämta en fil från den angivna identifieraren URI (Uniform Resource) till en plats på den lokala disken som associeras med den Virtuella Azure-noden som är tilldelade till klustret. |
| **Expandera HDIZippedFile** |Packa upp ZIP. |
| **Anropa HDICmdScript** |Köra ett skript från cmd.exe. |
| **Skriv HDILog** |Skriva utdata från skriptet som används för en skriptåtgärd. |
| **Get-tjänster** |Hämta en lista över tjänster som körs på datorn där skriptet körs. |
| **Get-Service** |Med specifika tjänstnamnet som indata, får du detaljerad information för en specifik tjänst (namn på tjänst, process-ID, tillstånd, etc.) på datorn där skriptet körs. |
| **Get-HDIServices** |Hämta en lista över HDInsight-tjänster som körs på datorn där skriptet körs. |
| **Get-HDIService** |Med specifika HDInsight tjänstnamnet som indata, får du detaljerad information för en specifik tjänst (namn på tjänst, process-ID, tillstånd, etc.) på datorn där skriptet körs. |
| **Get-ServicesRunning** |Hämta en lista över tjänster som körs på datorn där skriptet körs. |
| **Get-ServiceRunning** |Kontrollera om en specifik tjänst (efter namn) körs på datorn där skriptet körs. |
| **Get-HDIServicesRunning** |Hämta en lista över HDInsight-tjänster som körs på datorn där skriptet körs. |
| **Get-HDIServiceRunning** |Kontrollera om en specifik HDInsight-tjänst (efter namn) körs på datorn där skriptet körs. |
| **Get-HDIHadoopVersion** |Hämta versionen av Hadoop som är installerad på datorn där skriptet körs. |
| **Testa IsHDIHeadNode** |Kontrollera om den dator där skriptet körs är en huvudnod. |
| **Testa IsActiveHDIHeadNode** |Kontrollera om den dator där skriptet körs är en aktiv huvudnod. |
| **Testa IsHDIDataNode** |Kontrollera om den dator där skriptet körs är en datanod. |
| **Redigera HDIConfigFile** |Redigera config filer hive-site.xml, core-site.xml, hdfs-site.xml, mapred site.xml eller yarn-site.xml. |

## <a name="best-practices-for-script-development"></a>Metodtips för skriptutveckling av
Finns flera bästa praxis att tänka på när du utvecklar ett anpassat skript för ett HDInsight-kluster:

* Sök efter Hadoop-version

    Endast HDInsight version 3.1 (Hadoop 2.4) och högre support med skriptåtgärder installera anpassade komponenter på ett kluster. I ett skript, måste du använda den **Get-HDIHadoopVersion** hjälpmetod Kontrollera Hadoop-versionen innan du fortsätter med att utföra andra uppgifter i skriptet.
* Ange stabil länkar till skriptresurser

    Användare bör se till att alla skript och andra artefakter som används i anpassning av ett kluster förblir tillgängliga under hela livslängden för klustret och att versionerna av dessa filer inte ändrar under. Dessa resurser krävs om de återställning av avbildning av noder i klustret måste anges. Det bästa sättet är att hämta och arkivera allt innehåll i ett lagringskonto som användaren anger. Det här kontot kan vara standardkontot för lagring eller någon av de ytterligare lagringskonton som anges vid tidpunkten för distribution av anpassade kluster.
    Anpassade kluster prover i Spark och R anges i dokumentationen, till exempel finns en lokal kopia av resurser i det här lagringskontot: https://hdiconfigactions.blob.core.windows.net/.
* Se till att klustret anpassning skriptet idempotent

    Förväntat att noder i ett HDInsight-kluster är avbildade under klustrets livslängd. Klustret anpassning skript körs när ett kluster avbildade. Det här skriptet måste utformas ska vara idempotent i den mening att efter återställning av avbildning, skriptet bör se till att klustret returneras till samma anpassade tillståndet den var i när skriptet kördes för första gången när klustret skapades. Till exempel om ett anpassat skript har installerat ett program på D:\AppLocation första körs på varje efterföljande körning vid återställning av avbildning, skriptet ska kontrollera om programmet redan finns på plats D:\AppLocation innan du fortsätter med andra steg i den skript.
* Installera komponenter för anpassade optimala plats

    Om klusternoderna är avbildade kan C:\ resurs enheten och D:\ systemenhet formateras om, vilket resulterar i förlust av data och program som har installerats på dessa enheter. Den här förlusten kan också inträffa om en virtuell Azure-dator (VM)-nod som ingår i klustret slutar fungera och ersätts av en ny nod. Du kan installera komponenterna på D:\-enhet eller på C:\apps plats på klustret. Alla andra platser på enhet C:\ är reserverade. Ange platsen för program eller bibliotek som ska installeras i klustret anpassning skriptet.
* Garantera hög tillgänglighet för kluster-arkitektur

    HDInsight har ett aktivt-passivt arkitektur för hög tillgänglighet som en huvudnod är i aktivt läge (där HDInsight-tjänsterna körs) och andra Huvudnoden är i vänteläge (i vilken HDInsight tjänster inte körs). Noderna växla mellan lägena aktiva och passiva om HDInsight tjänster avbryts. Om en skriptåtgärd används för att installera tjänster på båda huvudnoderna för hög tillgänglighet, Observera att mekanismen för HDInsight-redundans inte kan automatiskt växla över dessa användare installerade tjänster. Så användaren installerade tjänster på HDInsight huvudnoderna som förväntas ha hög tillgänglighet måste antingen ha sina egna mekanism för växling vid fel om i aktivt-passivt läge eller vara i läget för aktiv-aktiv.

    Ett HDInsight-skriptåtgärder kommando som körs på båda huvudnoderna när rollen head-noden har angetts som ett värde i den *ClusterRoleCollection* parameter. Så när du skapar ett anpassat skript, se till att skriptet är medveten om den här installationen. Du bör inte köra problem där samma tjänster är installerade och igång på både huvudnoderna och hamnar konkurrerar med varandra. Tänk också på att data går förlorade vid återställning av avbildning, så att programvaran via skriptåtgärd måste vara motståndskraftiga mot sådana händelser. Program bör utformas för att arbeta med hög tillgänglighet data som distribueras över flera noder. Upp till 1/5 noder i ett kluster kan vara avbildade på samma gång.
* Konfigurera anpassade komponenter om du vill använda Azure Blob storage

    De anpassade komponenter som installeras på klusternoderna kan ha en standardkonfiguration lagringsmedier Hadoop Distributed File System (HDFS). Du bör ändra konfigurationen för att använda Azure Blob storage i stället. På ett kluster avbildningsåterställning HDFS hämtar filsystemet och du skulle förlora alla data som lagras där. Azure Blob storage i stället garanterar att dina data bevaras.

## <a name="common-usage-patterns"></a>Vanliga användningsmönster
Det här avsnittet ger vägledning om att implementera några av de vanliga användningsmönster som kan uppstå vid skrivning till ett eget skript.

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler
I skriptutveckling, kan du ofta anser att behöva ange miljövariabler. Exempelvis är mest sannolika scenariot när du hämtar en binär från en extern plats, installera den på klustret och Lägg till platsen för där den är installerad 'PATH'-miljövariabeln. Följande utdrag visar hur du anger miljövariabler i skriptet.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Den här instruktionen anger miljövariabeln **MDS_RUNNER_CUSTOM_CLUSTER** med värdet 'true' och även anger omfånget för den här variabeln ska vara datoromfattande. Det är viktigt att miljövariablerna anges på en lämplig omfattning – dator eller användare. Se [här] [ 1] för mer information om hur du anger miljövariabler.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Åtkomst till platser där anpassade skript lagras
Skript som används för att anpassa ett kluster måste antingen vara i standardkontot för lagring för klustret eller i en offentlig skrivskyddad behållare för andra storage-konto. Om skriptet har åtkomst till resurser som finns någon annanstans måste resurser läsas offentligt. Du kanske exempelvis vill komma åt en fil och spara den med hjälp av kommandot SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

I det här exemplet måste du säkerställa att behållaren `somecontainer` i lagringskonto `somestorageaccount` är allmänt tillgänglig. Annars genereras ett undantag 'Gick inte att hitta' skriptet och misslyckas.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Skicka parametrar för cmdleten Add-AzureRmHDInsightScriptAction
Om du vill lägga till flera parametrar för cmdleten Add-AzureRmHDInsightScriptAction, måste du formatera strängvärde så att den innehåller alla parametrar för skriptet. Exempel:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

eller

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Utlös undantag för misslyckade Klusterdistribution
Om du vill hämta exakt ett meddelande om att kluster anpassning slutfördes inte som förväntat, är det viktigt att utlösa ett undantag och kunde inte skapa ett kluster. Du kanske exempelvis vill bearbeta en fil om den finns och hantera fel fallet där filen inte finns. Detta skulle se till att skriptet avslutas utan problem och kallas korrekt tillstånd för klustret. Följande kodavsnitt ger ett exempel på hur du kan åstadkomma detta:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

I det här kodstycket om filen inte finns, skulle det leda till ett tillstånd där skriptet faktiskt avslutas korrekt efter utskrift felmeddelandet och klustret når körtillstånd under förutsättning att den ”” slutförts anpassningsprocess av klustret. Om du vill meddelas korrekt att klustret anpassning i stort sett slutfördes inte som förväntat på grund av en fil som saknas, det är mer lämpligt att utlösa ett undantag och misslyckas steget klustret anpassning. För att åstadkomma detta måste du använda följande exempel kodfragment i stället.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Checklista för distribution av en skriptåtgärd
Här följer de steg som vi har tagit när du förbereder att distribuera dessa skript:

1. Placera de filer som innehåller anpassade skript på en plats som kan nås av klusternoder under distributionen. Detta kan vara någon av standard eller ytterligare lagringskonton som anges vid tidpunkten för distribution eller andra offentligt tillgänglig lagringsbehållaren.
2. Lägga till kontroller i skript för att se till att de kör idempotently, så att skriptet kan köras flera gånger på samma nod.
3. Använd den `Write-Output` Azure PowerShell-cmdlet för att skriva ut till STDOUT och STDERR. Använd inte `Write-Host`.
4. Använda en tillfällig mapp, till exempel `$env:TEMP`, för att hålla den hämta filen som används av skripten och sedan rensa dem efter skript har körts.
5. Installera anpassade programvara på D:\ eller C:\apps. Andra platser på enhet C: ska inte användas som de är reserverade. Installation av filer på enhet C: utanför mappen C:\apps kan resultera i installationsfel under reimages för noden.
6. I händelse av att inställningarna för OS-nivå eller Hadoop service configuration-filer har ändrats, kan du vill starta om HDInsight-tjänster så att de kan hämta OS-nivå inställningar, till exempel miljövariabler som anges i skripten.

## <a name="debug-custom-scripts"></a>Felsöka anpassade skript
Felloggarna skriptet lagras tillsammans med andra utdata i standardkontot för lagring som angetts för klustret när skapandet. Loggfilerna lagras i en tabell med namnet *u < \cluster-name-fragment >< \time-stamp > setuplog*. Det här är sammanställda loggar som har poster från alla noder (huvudnod och arbetarnoder) där skriptet körs i klustret.

Ett enkelt sätt att kontrollera loggarna är att använda HDInsight Tools för Visual Studio. För att installera verktygen finns [komma igång med Visual Studio Hadoop-verktyg för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio)

**Kontrollera loggen med hjälp av Visual Studio**

1. Öppna Visual Studio.
2. Klicka på **visa**, och klicka sedan på **Server Explorer**.
3. Högerklicka på ”Azure”, klicka på Anslut till **Microsoft Azure-prenumerationer**, och ange dina autentiseringsuppgifter.
4. Expandera **lagring**, expandera Azure storage-konto som används som standardfilsystem, expandera **tabeller**, och dubbelklicka sedan på namnet på tabellen.

Du kan också fjärråtkomst till klusternoderna finns både STDOUT och STDERR för anpassade skript. Loggar på varje nod bara gäller den noden och loggas i **C:\HDInsightLogs\DeploymentAgent.log**. Dessa loggfilerna registrerar alla utdata från skriptet. Ett exempel loggen kodstycke för en Spark skriptåtgärd ser ut så här:

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


I den här loggen är det klart att skriptåtgärd Spark har körts på den virtuella datorn med namnet HEADNODE0 och att inga undantag utlöstes under körningen.

I händelse av att ett körningsfel inträffar ingå utdata som beskriver den också i den här loggfilen. Informationen i dessa loggar är användbar vid felsökning av problem med skript som kan uppstå.

## <a name="see-also"></a>Se också
* [Anpassa HDInsight-kluster med skriptåtgärder][hdinsight-cluster-customize]
* [Installera och använda Spark på HDInsight-kluster][hdinsight-install-spark]
* [Installera och använda Solr på HDInsight-kluster](hdinsight-hadoop-solr-install.md).
* [Installera och använda Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
