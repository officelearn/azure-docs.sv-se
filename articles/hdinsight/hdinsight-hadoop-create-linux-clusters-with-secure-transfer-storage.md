---
title: Apache Hadoop & säkert överföringslagringsutrymme – Azure HDInsight
description: Lär dig hur du skapar HDInsight-kluster med Azure-lagringskonton som använder säker överföring.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560090"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop-kluster med konton för säker överföring av lagring i Azure HDInsight

Funktionen [Säker överföring krävs](../storage/common/storage-require-secure-transfer.md) förbättrar säkerheten för ditt Azure Storage-konto genom att kräva att alla förfrågningar till ditt konto görs via en säker anslutning. Den här funktionen och wasbs-schemat stöds endast av HDInsight-kluster med version 3.6 eller senare.

> [!IMPORTANT]
> Aktivera säker lagringsöverföring när du har skapat ett kluster kan resultera i fel med ditt lagringskonto och rekommenderas inte. Det är bättre att skapa ett nytt kluster med ett lagringskonto med säker överföring redan aktiverat.

## <a name="storage-accounts"></a>Lagringskonton

### <a name="azure-portal"></a>Azure Portal

Som standard aktiveras egenskapen säker överföring som krävs när du skapar ett lagringskonto i Azure-portalen.

Information om hur du uppdaterar ett befintligt lagringskonto med Azure-portal finns i [Kräv säker överföring med Azure-portalen](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

För PowerShell [cmdlet New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) `-EnableHttpsTrafficOnly` är se `1`till att parametern är inställd på .

Information om hur du uppdaterar ett befintligt lagringskonto med PowerShell finns i [Kräv säker överföring med PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Azure CLI

För Azure CLI-kommandot [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create), ensure parameter `--https-only` is set to `true`.

Information om hur du uppdaterar ett befintligt lagringskonto med Azure CLI finns i [Kräv säker överföring med Azure CLI](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Lägga till fler lagringskonton

Det finns flera alternativ för att lägga till ytterligare lagringskonton som använder säker överföring:

* Ändra Azure Resource Manager-mallen i det sista avsnittet.
* Skapa ett kluster med hjälp av [Azure-portalen](https://portal.azure.com) och ange ett länkat lagringskonto.
* Använd skriptåtgärder om du vill lägga till fler lagringskonton som använder säker överföring i ett befintligt HDInsight-kluster. Mer information finns i [Add additional storage accounts to HDInsight](hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="next-steps"></a>Nästa steg

* Användningen av Azure Storage (WASB) i stället för [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) som standarddatalag
* Mer information om hur HDInsight använder Azure Storage finns i [Använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Mer information om hur du överför data till HDInsight finns i [Överföra data till HDInsight](hdinsight-upload-data.md).
