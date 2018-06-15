---
title: Lägg till ytterligare Azure storage-konton till HDInsight | Microsoft Docs
description: Lär dig hur du lägger till ytterligare Azure storage-konton till ett befintligt HDInsight-kluster.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 1404b37725362a71ccb4a0a84dff0c7c4ca591e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170109"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Lägga till ytterligare lagringskonton i HDInsight

Lär dig använda script actions för att lägga till ytterligare Azure storage-konton i HDInsight. Stegen i det här dokumentet för att lägga till ett lagringskonto till ett befintligt Linux-baserat HDInsight-kluster.

> [!IMPORTANT]
> Informationen i det här dokumentet handlar om att lägga till ytterligare lagringsutrymme i ett kluster när den har skapats. Mer information om att lägga till lagringskonton när klustret skapas finns [ställa in kluster i HDInsight Hadoop, Spark, Kafka och mycket mer](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Hur det fungerar

Det här skriptet använder följande parametrar:

* __Azure lagringskontonamnet__: namnet på lagringskontot för att lägga till HDInsight-klustret. När du har kört skriptet kan HDInsight läsa och skriva data lagras i det här lagringskontot.

* __Azure lagringskontonyckel__: en nyckel som ger åtkomst till lagringskontot.

* __-p__ (valfritt): Om nyckeln inte är krypterad och lagras i filen core-site.XML som oformaterad text.

Under bearbetning av utförs skriptet följande åtgärder:

* Om lagringskontot finns redan i core-site.xml konfigurationen för klustret, skriptet avslutas och inga ytterligare åtgärder utförs.

* Verifierar att lagringskontot finns och kan nås med hjälp av nyckeln.

* Krypterar nyckeln med hjälp av autentiseringsuppgifter för klustret.

* Lägger till lagringskontot i filen core-site.XML.

* Stoppar och startar om Oozie, YARN, MapReduce2 och HDFS-tjänster. Stoppa och starta dessa tjänster gör att de kan använda det nya lagringskontot.

> [!WARNING]
> Med hjälp av ett lagringskonto i en annan plats än HDInsight-kluster stöds inte.

## <a name="the-script"></a>Skriptet

__Script plats__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Krav för__:

* Skriptet måste tillämpas på den __huvudnoder__.

## <a name="to-use-the-script"></a>Du använder skriptet

Det här skriptet kan användas från Azure-portalen, Azure PowerShell eller Azure CLI 1.0. Mer information finns i [anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentet.

> [!IMPORTANT]
> Använd följande information när du använder finns i dokumentet anpassning för att tillämpa det här skriptet:
>
> * Ersätt alla exempel skriptåtgärd URI med URI: N för det här skriptet (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Ersätt alla exempel parametrar med Azure storage-kontonamnet och nyckeln för lagringskontot som ska läggas till klustret. Om du använder Azure portal måste parametrarna avgränsas med ett blanksteg.
> * Du behöver inte markera skriptet som __bevarade__, enligt den direkt uppdaterar Ambari-konfiguration för klustret.

## <a name="known-issues"></a>Kända problem

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Storage-konton som inte visas i Azure-portalen eller verktyg

När du visar HDInsight-kluster i Azure-portalen kan du välja den __Lagringskonton__ posten under __egenskaper__ storage-konton som lagts till via den här skriptåtgärden visas inte. Azure PowerShell och Azure CLI visas inte ytterligare storage-konto.

Storage-informationen visas inte eftersom skriptet ändras endast core-site.xml konfigurationen för klustret. Den här informationen används inte vid hämtning av klusterinformationen med hjälp av Azure management API: er.

Använd Ambari REST API för att visa information om lagringskontot läggs till klustret med det här skriptet. Använd följande kommandon för att hämta informationen för klustret:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Ange `$clusterName` till namnet på HDInsight-klustret. Ange `$storageAccountName` till namnet på lagringskontot. När du uppmanas ange klusterinloggning (admin) och lösenord.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Ange `$PASSWORD` kontolösenord för kluster-inloggning (admin). Ange `$CLUSTERNAME` till namnet på HDInsight-klustret. Ange `$STORAGEACCOUNTNAME` till namnet på lagringskontot.
>
> Det här exemplet används [curl (http://curl.haxx.se/) ](http://curl.haxx.se/) och [jq (https://stedolan.github.io/jq/) ](https://stedolan.github.io/jq/) att hämta och tolka JSON-data.

När det här kommandot ersätter __KLUSTERNAMN__ med namnet på HDInsight-klustret. Ersätt __lösenord__ med HTTP-inloggningslösenordet för klustret. Ersätt __STORAGEACCOUNT__ med namnet på lagringskontot läggs till med skriptåtgärder. Informationen som returneras från det här kommandot visas liknar följande:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Den här texten är ett exempel på en krypterad nyckel som används för att komma åt lagringskontot.

### <a name="unable-to-access-storage-after-changing-key"></a>Det går inte att komma åt lagringsutrymme när du har ändrat nyckel

Om du ändrar nyckeln för ett lagringskonto HDInsight inte längre komma åt lagringskontot. HDInsight använder en cachelagrad kopia av nyckeln i core-site.xml för klustret. Den här kopian måste uppdateras för att matcha den nya nyckeln.

Kör skriptet igen har __inte__ uppdatera nyckeln, som skriptet kontrollerar om det redan finns en post för lagringskontot. Om det finns redan en post, den inte göra några ändringar.

Om du vill undvika det här problemet måste du ta bort den befintliga posten för lagringskontot. Använd följande steg för att ta bort den befintliga posten:

1. Öppna Ambari-Webbgränssnittet för ditt HDInsight-kluster i en webbläsare. URI är https://CLUSTERNAME.azurehdinsight.net. Ersätt __CLUSTERNAME__ med namnet på klustret.

    När du uppmanas du ange HTTP-inloggning och lösenord för klustret.

2. I listan över tjänster till vänster på sidan Välj __HDFS__. Välj sedan den __konfigurationerna__ fliken i mitten på sidan.

3. I den __Filter...__  , ange ett värde för __fs.azure.account__. Detta returnerar poster för alla ytterligare lagringskonton som har lagts till i klustret. Det finns två typer av uppgifter. __keyprovider__ och __nyckeln__. Båda innehåller namnet på lagringskontot som en del av namnet.

    Följande är Exempelposter för ett lagringskonto med namnet __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. När du har identifierat nycklarna för lagringskontot som du måste ta bort, kan du använda röda '-' ikonen till höger om posten ska tas bort. Använd sedan den __spara__ för att spara ändringarna.

5. När ändringarna har sparats, Använd skriptåtgärden att lägga till lagringskontot och nya nyckelvärdet i klustret.

### <a name="poor-performance"></a>Dåliga prestanda

Om lagringskontot finns i en annan region än HDInsight-kluster, kan det uppstå sämre prestanda. Åtkomst till data i en annan region skickar nätverkstrafik utanför regionala Azure-datacentret och över det offentliga internet som kan introducera svarstid.

> [!WARNING]
> Med hjälp av ett lagringskonto i en annan region än HDInsight-kluster stöds inte.

### <a name="additional-charges"></a>Ytterligare kostnader

Om lagringskontot finns i en annan region än HDInsight-kluster, ser du ytterligare utgång avgifter på ditt Azure fakturering. En utgående kostnad tillämpas när data lämnar ett regionala datacenter. Det här tillägget används även om trafiken är avsedda för en annan Azure-datacenter i en annan region.

> [!WARNING]
> Med hjälp av ett lagringskonto i en annan region än HDInsight-kluster stöds inte.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du lägger till ytterligare lagringskonton i ett befintligt HDInsight-kluster. Mer information om skriptåtgärder finns [anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md)
