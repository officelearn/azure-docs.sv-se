---
title: Begränsa åtkomst med signaturer för delad åtkomst - Azure HDInsight
description: Lär dig hur du använder signaturer för delad åtkomst för att begränsa HDInsight åtkomst till data som lagras i Azure storage-blobbar.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.openlocfilehash: 76e40f5178457f366ed386dba7a1817ddde3c8ac
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090236"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Använd Azure Storage signaturer för delad åtkomst för att begränsa åtkomsten till data i HDInsight

HDInsight har fullständig åtkomst till data i Azure Storage-konton som är associerade med klustret. Du kan använda signaturer för delad åtkomst för blob-behållaren för att begränsa åtkomsten till data. Signaturer för delad åtkomst (SAS) är en funktion i Azure storage-konton där du kan begränsa åtkomsten till data. Till exempel konfigurera skrivskyddad åtkomst till data.

> [!IMPORTANT]
> Överväg att använda domänanslutna HDInsight för en lösning som använder Apache Ranger. Mer information finns i den [konfigurera domänanslutna HDInsight](./domain-joined/apache-domain-joined-configure.md) dokumentet.

> [!WARNING]
> HDInsight måste ha fullständig åtkomst till standardlagringen för klustret.

## <a name="requirements"></a>Krav

* En Azure-prenumeration
* C# eller Python. C#-kodexempel tillhandahålls som en Visual Studio-lösning.

  * Visual Studio måste vara version 2013, 2015 eller 2017
  * Python måste vara version 2.7 eller senare

* Ett Linux-baserade HDInsight-kluster eller [Azure PowerShell] [ powershell] -om du har ett befintligt kluster för Linux-baserade, du kan använda Ambari att lägga till en signatur för delad åtkomst i klustret. Om inte, du kan använda Azure PowerShell för att skapa ett kluster och lägga till en signatur för delad åtkomst när klustret skapas.

    > [!IMPORTANT]
    > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* I exempel-filer från [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Den här lagringsplatsen innehåller följande objekt:

  * Ett Visual Studio-projekt som kan skapa en lagringsbehållare och lagrade princip SAS för användning med HDInsight
  * Ett Python-skript som kan skapa en lagringsbehållare och lagrade princip SAS för användning med HDInsight
  * Ett PowerShell-skript som kan skapa ett HDInsight-kluster och konfigurera den att använda SAS.

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Det finns två typer av signaturer för delad åtkomst:

* Ad hoc-: starttid, förfallotid och behörigheter för SAS har angetts på SAS-URI.

* Lagrad åtkomstprincip: en lagrad åtkomstprincip har definierats för en resurs-behållare, till exempel en blob-behållare. En princip kan användas för att hantera begränsningar för en eller flera signaturer för delad åtkomst. När du kopplar en SAS med en lagrad åtkomstprincip, ärver SAS begränsningarna - starttid, förfallotid och behörigheter - har definierats för lagrad åtkomstprincip.

Skillnaden mellan de två formulär är viktigt för ett viktiga scenario: återkallade certifikat. En SAS är en URL så att alla som erhåller SAS kan använda det, oavsett vem som begärde att. Om en SAS publiceras offentligt, kan den användas av vem som helst i världen. En SAS som distribueras är giltig tills något av följande händer:

1. Förfallotiden som anges för SAS har nåtts.

2. Förfallotiden som anges på lagrad åtkomstprincip som refereras av SAS har nåtts. Följande scenarier orsaka förfallotiden att nå:

    * Tidsintervallet har gått ut.
    * Lagrad åtkomstprincip ändras så att den har en förfallotid tidigare. Ändra förfallotiden är ett sätt att återkalla signaturen för delad åtkomst.

3. Lagrad åtkomstprincip som refereras av SAS tas bort, vilket är ett annat sätt att återkalla signaturen för delad åtkomst. Om du återskapar lagrad åtkomstprincip med samma namn, är alla SAS-token för den föregående principen giltiga (om förfallotiden för SAS inte har passerats). Om du planerar att återkalla signaturen för delad åtkomst, måste du använda ett annat namn om du återskapar åtkomstprincip med en förfallotid i framtiden.

4. Den kontonyckel som används för att skapa SAS återskapas. Återskapar nyckeln gör att alla program som använder den tidigare nyckeln Avbryt autentisering. Uppdatera alla komponenter till den nya nyckeln.

> [!IMPORTANT]
> En signatur för delad åtkomst URI: N är associerad med den kontonyckel som används för att skapa signaturen och den associerade lagras åtkomstprincip (om sådan finns). Om ingen lagrad åtkomstprincip anges, är det enda sättet att återkalla en signatur för delad åtkomst för att ändra kontonyckeln.

Vi rekommenderar att du alltid använder lagrade åtkomstprinciper. När du använder lagrade principer, kan du återkalla signaturer eller utöka utgångsdatum efter behov. Stegen i det här dokumentet använder lagrade åtkomstprinciper för att generera SAS.

Mer information om signaturer för delad åtkomst finns i [förstå SAS-modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Skapa en princip för lagrade och SAS med hjälp av C\#

1. Öppna lösningen i Visual Studio.

2. I Solution Explorer högerklickar du på den **SASToken** projektet och välj **egenskaper**.

3. Välj **inställningar** och lägga till värden för följande poster:

   * StorageConnectionString: Anslutningssträngen för lagringskontot som du vill skapa en princip för lagrade och SAS för. Formatet ska vara `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` där `myaccount` är namnet på ditt lagringskonto och `mykey` är nyckeln till lagringskontot.

   * ContainerName: Behållare i lagringskontot som du vill begränsa åtkomst till.

   * SASPolicyName: Namnet på den lagrade principen för att skapa.

   * FileToUpload: Sökvägen till en fil som har överförts till behållaren.

4. Kör projektet. När signaturen för delad åtkomst har skapats visas information liknande följande text:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Spara SAS-token för principen, lagringskontonamn och behållarnamn. De här värdena används när du associerar storage-konto med ditt HDInsight-kluster.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Skapa en princip för lagrade och SAS med hjälp av Python

1. Öppna filen SASToken.py och ändra följande värden:

   * principen\_name: namnet på den lagrade principen för att skapa.

   * Storage\_konto\_name: namnet på ditt lagringskonto.

   * Storage\_konto\_nyckel: nyckeln för lagringskontot.

   * Storage\_behållare\_namn: behållare i lagringskontot som du vill begränsa åtkomst till.

   * exempel\_filen\_path: sökvägen till en fil som har överförts till behållaren.

2. Kör skriptet. När skriptet har körts visas den SAS-token som liknar följande text:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Spara SAS-token för principen, lagringskontonamn och behållarnamn. De här värdena används när du associerar storage-konto med ditt HDInsight-kluster.

## <a name="use-the-sas-with-hdinsight"></a>Använda SAS med HDInsight

När du skapar ett HDInsight-kluster, måste du ange en primär lagring-konto och Alternativt kan du ange ytterligare lagringskonton. Båda dessa metoder för att lägga till lagring kräver fullständig åtkomst till lagringskonton och behållare som används.

Om du vill använda en signatur för delad åtkomst för att begränsa åtkomsten till en behållare, lägger du till en anpassad post i **core-site** konfigurationen för klustret.

* För **Windows-baserade** eller **Linux-baserade** HDInsight-kluster kan du lägga till posten när klustret skapas med hjälp av PowerShell.
* För **Linux-baserade** HDInsight-kluster kan ändra konfigurationen när klustret har skapats med hjälp av Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Skapa ett kluster som använder SAS

Ett exempel på hur du skapar ett HDInsight-kluster som använder SAS som ingår i den `CreateCluster` för databasen. Använd följande steg för att använda den:

1. Öppna den `CreateCluster\HDInsightSAS.ps1` -filen i en textredigerare och ändra följande värden i början av dokumentet.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Till exempel ändra `'mycluster'` till namnet på det kluster du vill skapa. SAS-värden måste matcha värdena från föregående steg när du skapar ett lagringskonto och SAS-token.

    När du har ändrat värdena kan du spara filen.

2. Öppna en ny Azure PowerShell-kommandotolk. Om du inte är bekant med Azure PowerShell eller inte har installerat den, se [installera och konfigurera Azure PowerShell][powershell].

1. Använd kommandot för att autentisera till Azure-prenumerationen från Kommandotolken:

    ```powershell
    Connect-AzureRmAccount
    ```

    När du uppmanas logga in med kontot för din Azure-prenumeration.

    Om ditt konto är associerad med flera Azure-prenumerationer, kan du behöva använda `Select-AzureRmSubscription` att välja den prenumeration som du vill använda.

4. Från Kommandotolken, ändrar du katalog till den `CreateCluster` katalog som innehåller filen HDInsightSAS.ps1. Sedan använder du följande kommando för att köra skriptet

    ```powershell
    .\HDInsightSAS.ps1
    ```

    När skriptet har körts, loggar det utdata i PowerShell-Kommandotolken eftersom den skapar resursen grupp och storage-konton. Du uppmanas att ange HTTP-användare för HDInsight-klustret. Det här kontot används för att säkra HTTP/s-åtkomst till klustret.

    Om du skapar ett Linux-baserade kluster, efterfrågas ett SSH-användarens kontonamn och lösenord. Det här kontot används för att logga in via en fjärranslutning till klustret.

   > [!IMPORTANT]
   > När du tillfrågas om HTTP/s eller SSH-användarnamn och lösenord, måste du ange ett lösenord som uppfyller följande kriterier:
   >
   > * Måste vara minst 10 tecken långt
   > * Måste innehålla minst en siffra
   > * Måste innehålla minst ett icke-alfanumeriska tecken
   > * Måste innehålla minst en versal eller gemen bokstav

Det tar en stund innan det här skriptet att slutföra, vanligen cirka 15 minuter. När skriptet har slutförts utan fel har klustret skapats.

### <a name="use-the-sas-with-an-existing-cluster"></a>Använda SAS med ett befintligt kluster

Om du har ett befintligt Linux-baserade kluster kan du lägga till SAS till den **core-site** konfiguration med hjälp av följande steg:

1. Öppna Ambari-webbgränssnittet för klustret. Adress för den här sidan är https://YOURCLUSTERNAME.azurehdinsight.net. När du uppmanas, autentisera till klustret med administratörsnamn (admin) och lösenordet som du använde när du skapar klustret.

2. Från vänster sida av Ambari-webbgränssnittet väljer **HDFS** och välj sedan den **Peeringkonfigurationer** fliken mitt på sidan.

3. Välj den **Avancerat** fliken och rullar tills du hittar den **anpassad core-site** avsnittet.

4. Expandera den **anpassad core-site** avsnittet och rulla till slutet och välj sedan den **Lägg till egenskap...**  länk. Använd följande värden för den **nyckel** och **värdet** fält:

   * **Nyckeln**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Värdet**: Signaturen som returneras av C# eller Python-programmet som du körde tidigare

     Ersätt **CONTAINERNAME** med behållarens namn som du använde med C# eller SAS-programmet. Ersätt **STORAGEACCOUNTNAME** med namnet du använde.

5. Klicka på den **Lägg till** knappen för att spara den här nyckeln och värdet och klicka sedan på den **spara** för att spara konfigurationsändringarna. När du uppmanas, lägga till en beskrivning av ändringen (”att lägga till SAS-lagringsåtkomst” till exempel) och klicka sedan på **spara**.

    Klicka på **OK** när ändringarna har utförts.

   > [!IMPORTANT]
   > Du måste starta om flera tjänster innan ändringen börjar gälla.

6. Ambari-webbgränssnittet, Välj **HDFS** i listan till vänster och välj sedan **starta om alla påverkade** från den **tjänståtgärder** listan till höger. När du uppmanas, väljer __bekräfta starta om alla__.

    Upprepa processen för MapReduce2 och YARN.

7. När tjänsterna har startats om, markerar du var och en och inaktivera underhållsläge från den **tjänståtgärder** nedrullningsbar listruta.

## <a name="test-restricted-access"></a>Testa begränsad åtkomst

Kontrollera att du har begränsad åtkomst genom att använda följande metoder:

* För **Windows-baserade** HDInsight-kluster kan använda Fjärrskrivbord för att ansluta till klustret. Mer information finns i [Anslut till HDInsight med hjälp av RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    När du är ansluten, Använd den **Hadoop kommandoradsverktyget** ikon på skrivbordet för att öppna en kommandotolk.

* För **Linux-baserade** HDInsight-kluster kan använda SSH för att ansluta till klustret. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

När du är ansluten till klustret, Använd följande steg för att verifiera att du kan endast Läs- och objekt för SAS-lagringskontot:

1. Använd följande kommando från prompten för att visa innehållet i behållaren: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Ersätt **SASCONTAINER** med namnet på behållaren som skapades för kontot i SAS-lagring. Ersätt **SASACCOUNTNAME** med namnet på lagringskontot som används för signaturen för delad åtkomst.

    Listan innehåller filen när behållaren och SAS har skapats.

2. Använd följande kommando för att kontrollera att du kan läsa innehållet i filen. Ersätt den **SASCONTAINER** och **SASACCOUNTNAME** som i föregående steg. Ersätt **FILENAME** med namnet på den fil som visas i föregående kommando:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Det här kommandot visar innehållet i filen.

3. Använd följande kommando för att ladda ned filen till det lokala filsystemet:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Det här kommandot laddar ned filen till en lokal fil med namnet **testfile.txt**.

4. Använd följande kommando för att ladda upp den lokala filen till en ny fil med namnet **testupload.txt** på SAS-lagring:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Du får ett meddelande som liknar följande text:

        put: java.io.IOException

    Det här felet uppstår eftersom lagringsplatsen är skrivskyddade + endast lista över. Använd följande kommando för att publicera den på standardlagringen för klustret, som är skrivbar:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Den här gången bör åtgärden slutföras utan problem.

## <a name="troubleshooting"></a>Felsökning

### <a name="a-task-was-canceled"></a>En uppgift avbröts

**Symptom**: när du skapar ett kluster med PowerShell-skriptet kan du få följande felmeddelande visas:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Orsak**: det här felet kan inträffa om du använder ett lösenord för admin/HTTP-användare för klustret, eller (för Linux-baserade kluster) för SSH-användaren.

**Lösning**: använda ett lösenord som uppfyller följande kriterier:

* Måste vara minst 10 tecken långt
* Måste innehålla minst en siffra
* Måste innehålla minst ett icke-alfanumeriska tecken
* Måste innehålla minst en versal eller gemen bokstav

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du lägger till lagring med begränsad åtkomst till ditt HDInsight-kluster kan läsa andra sätt att arbeta med data i ditt kluster:

* [Använda Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
