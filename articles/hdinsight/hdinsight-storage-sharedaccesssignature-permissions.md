---
title: "Begränsa åtkomst med signaturer för delad åtkomst - Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder signaturer för delad åtkomst för att begränsa HDInsight åtkomst till data som lagras i Azure storage BLOB."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 7bcad2dd-edea-467c-9130-44cffc005ff3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/11/2017
ms.author: larryfr
ms.openlocfilehash: 2e4b1a307fae06c0639d93b9804c6f0f703d5900
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Använd Azure Storage signaturer för delad åtkomst för att begränsa åtkomsten till data i HDInsight

HDInsight har fullständig åtkomst till data i Azure Storage-konton som är associerade med klustret. Du kan använda signaturer för delad åtkomst på blob-behållaren för att begränsa åtkomsten till data. Till exempel för att ge skrivskyddad åtkomst till data. Delad åtkomst signaturer (SAS) är en funktion i Azure storage-konton som gör att du kan begränsa åtkomsten till data. Till exempel ger skrivskyddad åtkomst till data.

> [!IMPORTANT]
> Överväg att använda domänanslutna HDInsight för en lösning som använder Apache Ranger. Mer information finns i [konfigurera domänanslutna HDInsight](hdinsight-domain-joined-configure.md) dokumentet.

> [!WARNING]
> HDInsight måste ha fullständig åtkomst till standardlagring för klustret.

## <a name="requirements"></a>Krav

* En Azure-prenumeration
* C# eller Python. C#-kodexempel anges som ett Visual Studio-lösning.

  * Visual Studio måste vara version 2013 eller 2015 2017
  * Python måste vara version 2.7 eller högre

* Ett Linux-baserade HDInsight-kluster eller [Azure PowerShell] [ powershell] -om du har ett befintligt Linux-baserade kluster kan du använda Ambari att lägga till en signatur för delad åtkomst till klustret. Om inte, du kan använda Azure PowerShell för att skapa ett kluster och lägga till en signatur för delad åtkomst när klustret skapas.

    > [!IMPORTANT]
    > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* I exempel-filer från [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Den här lagringsplatsen innehåller följande objekt:

  * Ett Visual Studio-projekt som kan skapa en lagringsbehållare, lagrade principer och SAS för användning med HDInsight
  * Python-skriptet som kan skapa en lagringsbehållare, lagrade principer och SAS för användning med HDInsight
  * Ett PowerShell-skript som kan skapa ett HDInsight-kluster och konfigurera den att använda SAS.

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Det finns två typer av signaturer för delad åtkomst:

* Ad hoc-: starttid, förfallotiden och behörigheter för SAS har angetts för SAS-URI.

* Lagras åtkomstprincip: en lagrade princip har definierats för en resurs-behållare, till exempel en blob-behållare. En princip kan användas för att hantera begränsningar för en eller flera signaturer för delad åtkomst. När du kopplar en SAS med en lagrad till ärver SAS begränsningarna - starttid, förfallotiden och behörigheter - har definierats för den lagrade åtkomstprincipen.

Skillnaden mellan de två formulär är viktigt för ett key-scenario: återkallade certifikat. En SAS är en URL så att alla som erhåller SAS kan använda det, oavsett vem som har begärt det börja med. Om en SAS publiceras offentligt, kan den användas av vem som helst i världen. En SAS som distribueras är giltig förrän något av följande händer:

1. Förfallotiden som anges på SAS har nåtts.

2. Förfallotiden som angetts på den lagrade åtkomstprincip som refereras av SAS har nåtts. Följande scenarier orsaka förfallotiden att nå:

    * Tidsintervallet har gått ut.
    * Den lagrade åtkomstprincipen ändras till att ha en förfallotiden tidigare. Förfallotiden är ett sätt att återkalla SAS.

3. Lagrade åtkomstprincipen som refereras av SAS tas bort, vilket är ett annat sätt att återkalla SAS. Om du återskapa den lagrade åtkomstprincipen med samma namn, gäller alla SAS-token för den föregående principen (förfallotiden på SAS inte har passerats). Om du vill återkalla SAS måste du använda ett annat namn om du återskapa åtkomstprincipen med ett förfallodatum i framtiden.

4. Nyckeln för kontot som används för att skapa SAS genereras. Återskapa nyckeln gör att alla program som använder den tidigare nyckeln Avbryt autentisering. Uppdatera alla komponenter till den nya nyckeln.

> [!IMPORTANT]
> En signatur för delad åtkomst URI som är associerad med kontonyckel som används för att skapa signaturen och den associerade lagras åtkomstprincip (eventuella). Om inga lagrade åtkomstprincip anges är det enda sättet att återkalla en signatur för delad åtkomst att ändra nyckeln för kontot.

Vi rekommenderar att du alltid använder lagrade åtkomstprinciper. När du använder lagrade principer, kan du återkalla signaturer eller utöka utgångsdatum efter behov. Stegen i det här dokumentet används lagras åtkomstprinciper generera SAS.

Mer information om signaturer för delad åtkomst finns [förstå SAS-modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Skapa en princip för lagrade och SAS med hjälp av C\#

1. Öppna lösningen i Visual Studio.

2. I Solution Explorer högerklickar du på den **SASToken** projektet och välj **egenskaper**.

3. Välj **inställningar** och lägga till värden för följande uppgifter:

   * StorageConnectionString: Anslutningssträngen för lagringskontot som du vill skapa en princip för lagrade och SAS för. Formatet ska vara `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` där `myaccount` är namnet på ditt lagringskonto och `mykey` är nyckeln för lagringskontot.

   * ContainerName: Behållare i storage-konto som du vill begränsa åtkomst till.

   * SASPolicyName: Namnet att använda lagrade principen för att skapa.

   * FileToUpload: Sökvägen till en fil som överförs till behållaren.

4. Kör projektet. När SAS har skapats visas information som liknar följande:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Spara SAS-token för principen, lagringskontonamnet och behållarnamn. Dessa värden används när du associerar storage-konto med ditt HDInsight-kluster.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Skapa en princip för lagrade och SAS använder Python

1. Öppna filen SASToken.py och ändra följande värden:

   * principen\_namn: namnet som ska använda för att principen för att skapa.

   * lagring\_konto\_name: namnet på ditt lagringskonto.

   * lagring\_konto\_nyckel: nyckeln för lagringskontot.

   * lagring\_behållare\_name: storage-konto som du vill begränsa åtkomst till behållaren.

   * exempel\_filen\_sökväg: sökväg till en fil som överförs till behållaren.

2. Kör skriptet. När skriptet har slutförts visas SAS-token som liknar följande:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Spara SAS-token för principen, lagringskontonamnet och behållarnamn. Dessa värden används när du associerar storage-konto med ditt HDInsight-kluster.

## <a name="use-the-sas-with-hdinsight"></a>Använd SAS med HDInsight

När du skapar ett HDInsight-kluster, måste du ange en primär storage-konto och du kan också ange ytterligare lagringskonton. Båda dessa metoder för att lägga till lagring kräver fullständig åtkomst till storage-konton och behållare som används.

Om du vill använda en signatur för delad åtkomst för att begränsa åtkomsten till en behållare, lägger du till en anpassad post i **core-plats** konfiguration för klustret.

* För **Windows-baserade** eller **Linux-baserade** HDInsight-kluster, kan du lägga till posten när klustret skapas med hjälp av PowerShell.
* För **Linux-baserade** HDInsight-kluster, ändra konfigurationen efter att skapa ett kluster med Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Skapa ett kluster som använder SAS

Ett exempel på hur du skapar ett HDInsight-kluster som använder SAS ingår i den `CreateCluster` för databasen. Använd följande steg för att använda den:

1. Öppna den `CreateCluster\HDInsightSAS.ps1` filen i en textredigerare och ändra följande värden i början av dokumentet.

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

    Till exempel ändra `'mycluster'` till namnet på det kluster du vill skapa. SAS-värden ska matcha värden från föregående steg när du skapar ett lagringskonto och SAS-token.

    När du har ändrat värdena, spara filen.

2. Öppna en ny Azure PowerShell-kommandotolk. Om du inte känner till Azure PowerShell eller inte har installerat den, se [installera och konfigurera Azure PowerShell][powershell].

1. Använder du följande kommando för autentisering till din Azure-prenumeration från Kommandotolken:

    ```powershell
    Login-AzureRmAccount
    ```

    När du uppmanas logga in med kontot för din Azure-prenumeration.

    Om ditt konto är kopplat till flera Azure-prenumerationer, kan du behöva använda `Select-AzureRmSubscription` till väljer du den prenumeration som du vill använda.

4. Från Kommandotolken, ändra kataloger till den `CreateCluster` katalog som innehåller filen HDInsightSAS.ps1. Sedan använder du följande kommando för att köra skriptet

    ```powershell
    .\HDInsightSAS.ps1
    ```

    När skriptet körs, loggas utdata till PowerShell-Kommandotolken eftersom den skapar resursen grupp och storage-konton. Du uppmanas att ange HTTP-användare för HDInsight-kluster. Det här kontot används för att skydda HTTP/s-åtkomst till klustret.

    Om du skapar ett Linux-baserade kluster, efterfrågas ett SSH-konto användarnamn och lösenord. Det här kontot används för att logga in via en fjärranslutning till klustret.

   > [!IMPORTANT]
   > När du uppmanas att ange HTTP/s eller SSH-användarnamn och lösenord, måste du ange ett lösenord som uppfyller följande kriterier:
   >
   > * Måste vara minst 10 tecken
   > * Måste innehålla minst en siffra
   > * Måste innehålla minst ett icke-alfanumeriska tecken
   > * Måste innehålla minst en versal eller gemen bokstav

Det tar en stund innan det här skriptet att slutföra, vanligtvis cirka 15 minuter. När skriptet har slutförts utan fel, har klustret skapats.

### <a name="use-the-sas-with-an-existing-cluster"></a>Använd SAS med ett befintligt kluster

Om du har ett befintligt Linux-baserade kluster kan du lägga till SA till den **core-plats** konfiguration med hjälp av följande steg:

1. Öppna Ambari webbgränssnittet för klustret. Adressen för den här sidan är https://YOURCLUSTERNAME.azurehdinsight.net. När du uppmanas, autentisera för klustret med hjälp av admin-namnet (admin) och lösenord som du använde när du skapar klustret.

2. Vänster sida av Ambari-webbgränssnittet, Välj **HDFS** och välj sedan den **konfigurationerna** fliken i mitten på sidan.

3. Välj den **Avancerat** , och Bläddra tills du hittar den **anpassad core-plats** avsnitt.

4. Expandera den **anpassad core-plats** avsnitt och rulla till slutet och välj sedan den **Lägg till egenskap...**  länk. Använd följande värden för den **nyckeln** och **värdet** fält:

   * **Nyckeln**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Värdet**: den SAS som returneras av C# eller Python-programmet som du tidigare körde

     Ersätt **CONTAINERNAME** med behållarens namn används med C# eller SAS-programmet. Ersätt **STORAGEACCOUNTNAME** med lagringskontots namn du använde.

5. Klicka på den **Lägg till** knappen om du vill spara den här nyckeln och värdet och klicka sedan på den **spara** för att spara ändringar i konfigurationen. När du uppmanas, lägga till en beskrivning av ändringen (”lägger till SAS-lagringsåtkomst” till exempel) och klicka sedan på **spara**.

    Klicka på **OK** när ändringarna har utförts.

   > [!IMPORTANT]
   > Du måste starta om flera tjänster innan ändringen börjar gälla.

6. Markera i Ambari webbgränssnittet **HDFS** i listan till vänster och välj sedan **starta om alla** från den **tjänståtgärder** listrutan till höger. När du uppmanas, Välj **aktivera underhållsläge** och sedan väljer __Conform starta om alla ”.

    Upprepa processen för MapReduce2 och YARN.

7. När tjänsten har startats om, välja respektive och inaktivera underhållsläge från den **tjänståtgärder** listrutan.

## <a name="test-restricted-access"></a>Testa begränsad åtkomst

Kontrollera att du har begränsad åtkomst genom att använda följande metoder:

* För **Windows-baserade** HDInsight-kluster, använda Fjärrskrivbord för att ansluta till klustret. Mer information finns i [Anslut till HDInsight med RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    När du är ansluten, Använd den **Hadoop kommandoradsverktyget** ikon på skrivbordet för att öppna en kommandotolk.

* För **Linux-baserade** HDInsight-kluster, använda SSH för att ansluta till klustret. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

När du är ansluten till klustret, Använd följande steg för att verifiera att du kan endast Läs- och objekten på lagringskontot SAS:

1. Om du vill visa innehållet i behållaren, använder du följande kommando i Kommandotolken: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Ersätt **SASCONTAINER** med namnet på den behållare som skapats för SAS-lagringskontot. Ersätt **SASACCOUNTNAME** med namnet på lagringskontot används för SAS.

    Listan innehåller filen när behållare och SAS skapades.

2. Använd följande kommando för att kontrollera att du kan läsa innehållet i filen. Ersätt den **SASCONTAINER** och **SASACCOUNTNAME** som i föregående steg. Ersätt **FILENAME** med namnet på den fil som visas i föregående kommando:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Det här kommandot visar innehållet i filen.

3. Använd följande kommando för att hämta filen till det lokala filsystemet:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Det här kommandot laddar ned filen till en lokal fil med namnet **testfile.txt**.

4. Använd följande kommando för att överföra den lokala filen till en ny fil med namnet **testupload.txt** på SAS-lagring:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Du får ett meddelande som liknar följande:

        put: java.io.IOException

    Detta fel uppstår eftersom lagringsplatsen är skrivskyddade + endast lista över tillåtna. Använd följande kommando för att placera data på lagringsutrymme som standard för klustret, som är skrivbara:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Den här gången ska åtgärden slutföras.

## <a name="troubleshooting"></a>Felsökning

### <a name="a-task-was-canceled"></a>En uppgift har avbrutits

**Symptom**: när du skapar ett kluster med PowerShell-skript får följande felmeddelande:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Orsak**: det här felet kan inträffa om du använder ett lösenord för admin/http-användaren för klustret, eller (för Linux-baserade kluster) för SSH-användare.

**Lösning**: Använd ett lösenord som uppfyller följande kriterier:

* Måste vara minst 10 tecken
* Måste innehålla minst en siffra
* Måste innehålla minst ett icke-alfanumeriska tecken
* Måste innehålla minst en versal eller gemen bokstav

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du lägger till lagring med begränsad åtkomst till ditt HDInsight-kluster, lär du dig andra sätt att arbeta med data på ditt kluster:

* [Använda Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
