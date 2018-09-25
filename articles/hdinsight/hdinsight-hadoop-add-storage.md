---
title: Lägga till ytterligare Azure storage-konton i HDInsight
description: Lär dig hur du lägger till ytterligare Azure storage-konton i ett befintligt HDInsight-kluster.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: bdf4c7527e2c0056b2de2321fbae280e9008cb1b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990685"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Lägga till ytterligare lagringskonton till HDInsight

Lär dig hur du använder skriptåtgärder för att lägga till ytterligare Azure storage-konton i HDInsight. Stegen i det här dokumentet för att lägga till ett lagringskonto till ett befintligt Linux-baserade HDInsight-kluster.

> [!IMPORTANT]
> Informationen i det här dokumentet handlar om att lägga till ytterligare lagringsutrymme i ett kluster när den har skapats. Information om att lägga till lagringskonton när klustret skapas finns i [Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Hur det fungerar

Det här skriptet använder följande parametrar:

* __Azure storage-kontonamn__: namnet på lagringskontot för att lägga till i HDInsight-klustret. När skriptet har körts, HDInsight läser och skriver data lagras i det här lagringskontot.

* __Azure storage-kontonyckel__: en nyckel som ger åtkomst till lagringskontot.

* __-p__ (valfritt): Om nyckeln är inte krypterat och lagras i filen core-site.xml som oformaterad text.

Under bearbetning utför skriptet följande åtgärder:

* Om lagringskontot finns redan i core-site.xml-konfigurationen för klustret, skriptet avslutas och inga ytterligare åtgärder utförs.

* Verifierar att lagringskontot finns och kan nås med hjälp av nyckeln.

* Krypterar nyckeln med hjälp av autentiseringsuppgifter för klustret.

* Lägger till lagringskontot i filen core-site.xml.

* Stoppar och startar om tjänsten Oozie, YARN, MapReduce2 och HDFS. Stoppa och starta dessa tjänster gör att de kan använda det nya lagringskontot.

> [!WARNING]
> Med ett storage-konto i en annan plats än HDInsight-kluster stöds inte.

## <a name="the-script"></a>Skriptet

__Skriptplats__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Krav för__:

* Skriptet måste tillämpas på den __huvudnoder__.

## <a name="to-use-the-script"></a>Du använder skriptet

Det här skriptet kan användas från Azure-portalen, Azure PowerShell eller den klassiska Azure-CLI. Mer information finns i den [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentet.

> [!IMPORTANT]
> När du använder stegen i dokumentet anpassning, kan du använda följande information för att tillämpa det här skriptet:
>
> * Ersätt alla exempel skriptåtgärd URI med URI: N för det här skriptet (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Ersätt alla exempel parametrar med Azure storage-kontonamnet och nyckeln för lagringskontot som ska läggas till i klustret. Om du använder Azure-portalen, måste parametrarna avgränsas med ett blanksteg.
> * Du behöver inte att markera det här skriptet som __bevarade__, som uppdateras direkt Ambari-konfigurationen för klustret.

## <a name="known-issues"></a>Kända problem

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Storage-konton som inte visas i Azure-portalen eller verktyg

När du visar HDInsight-kluster i Azure portal, välja den __Lagringskonton__ posten under __egenskaper__ visas inte storage-konton som har lagts till via den här skriptåtgärden. Azure PowerShell och Azure klassiskt CLI visas inte ytterligare storage-konto.

Information om visas inte eftersom skriptet ändras endast core-site.xml-konfigurationen för klustret. Den här informationen används inte vid hämtning av klusterinformationen med hjälp av API: er för Azure hantering.

Använda Ambari REST API för att visa information på lagringskontot läggs till klustret med det här skriptet. Använd följande kommandon för att hämta den här informationen för klustret:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Ange `$clusterName` till namnet på HDInsight-klustret. Ange `$storageAccountName` till namnet på lagringskontot. När du uppmanas ange klusterinloggningen (administratör) och lösenord.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Ange `$PASSWORD` att lösenordet för klusterinloggning (administratör)-konto. Ange `$CLUSTERNAME` till namnet på HDInsight-klustret. Ange `$STORAGEACCOUNTNAME` till namnet på lagringskontot.
>
> Det här exemplet används [curl (http://curl.haxx.se/) ](http://curl.haxx.se/) och [jq (https://stedolan.github.io/jq/) ](https://stedolan.github.io/jq/) att hämta och parsa JSON-data.

När det här kommandot ersätter __CLUSTERNAME__ med namnet på HDInsight-klustret. Ersätt __lösenord__ med HTTP-inloggningslösenordet för klustret. Ersätt __STORAGEACCOUNT__ med namnet på det lagringskonto som har lagts till med skriptåtgärd. Information som returneras från det här kommandot ser ut som följande text:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Den här texten är ett exempel på en krypterad nyckel som används för att komma åt lagringskontot.

### <a name="unable-to-access-storage-after-changing-key"></a>Det går inte att komma åt lagring när du har ändrat nyckel

Om du ändrar nyckeln för ett lagringskonto, HDInsight inte längre komma åt lagringskontot. HDInsight använder en cachelagrad kopia av nyckeln i core-site.xml för klustret. Den här cachelagrade kopian måste uppdateras så att den matchar den nya nyckeln.

Kör skriptet igen har __inte__ uppdatera nyckeln, eftersom skriptet kontrollerar om det redan finns en post för lagringskontot. Om det finns redan en post, den inte göra några ändringar.

Om du vill undvika det här problemet måste du ta bort den befintliga posten för lagringskontot. Använd följande steg för att ta bort den befintliga posten:

1. Öppna Ambari-Webbgränssnittet för ditt HDInsight-kluster i en webbläsare. URI: N är https://CLUSTERNAME.azurehdinsight.net. Ersätt __CLUSTERNAME__ med namnet på klustret.

    När du uppmanas, anger du http-logga in användare och lösenord för klustret.

2. I listan över tjänster till vänster på sidan Välj __HDFS__. Välj sedan den __Peeringkonfigurationer__ fliken i mitten av sidan.

3. I den __Filter...__  fältet, anger du värdet __fs.azure.account__. Det här returnerar poster för alla ytterligare lagringskonton som har lagts till i klustret. Det finns två typer av uppgifter. __keyprovider__ och __nyckeln__. Båda innehåller namnet på lagringskontot som en del av nyckelnamnet.

    Följande är Exempelposter för ett lagringskonto med namnet __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. När du har identifierat nycklarna för lagringskontot som du vill ta bort kan du använda röda '-' ikonen till höger i posten för att ta bort den. Använd sedan den __spara__ för att spara dina ändringar.

5. När ändringar har sparats, kan du använda skriptåtgärden för att lägga till lagringskontot och det nya nyckelvärdet i klustret.

### <a name="poor-performance"></a>Sämre prestanda

Om lagringskontot är i en annan region än HDInsight-kluster kan uppstå det sämre prestanda. Åtkomst till data i en annan region skickar nätverkstrafik utanför regionala Azure-datacentret och över offentliga internet, vilket kan medföra svarstid.

> [!WARNING]
> Med ett storage-konto i en annan region än HDInsight-kluster stöds inte.

### <a name="additional-charges"></a>Ytterligare avgifter

Om lagringskontot är i en annan region än HDInsight-kluster kan märka du kostnader för ytterligare utgående trafik på din Azure-fakturering. En utgående avgift tillämpas när data lämnar ett regionala datacenter. Den här avgiften tillämpas även om trafiken som är avsedd för en annan Azure-datacenter i en annan region.

> [!WARNING]
> Med ett storage-konto i en annan region än HDInsight-kluster stöds inte.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du lägger till ytterligare lagringskonton i ett befintligt HDInsight-kluster. Mer information om skriptåtgärder finns i [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md)
