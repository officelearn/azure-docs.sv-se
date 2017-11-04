---
title: "Övervaka och felsöka cloud storage-program i Azure | Microsoft Docs"
description: "Använda diagnostiska verktyg, mätvärden och aviseringar för att felsöka och övervaka ett program i molnet."
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: db88c331f79d83e0124519f8b6dbb34514b456dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Övervaka och felsöka ett moln storage-program

Den här kursen är en del fyra och den sista delen av en serie. Du lär dig att övervaka och felsöka ett moln storage-program.

I del fyra av serien får du lära dig hur du:

> [!div class="checklist"]
> * Aktivera loggning och mått
> * Aktivera aviseringar för auktorisering fel
> * Kör test-trafik med felaktigt SAS-token
> * Hämta och analysera loggar

[Azure storage analytics](../common/storage-analytics.md) tillhandahåller data för loggning och mått för ett lagringskonto. Dessa data ger insikter om hälsotillståndet för ditt lagringskonto. Innan du kan vara synlighet i ditt lagringskonto, måste du konfigurera datainsamling. Den här processen att aktivera loggning, konfigurerar mått och aktiverar aviseringar.

Aktiveras loggning och mått för storage-konton från den **diagnostik** fliken i Azure-portalen. Det finns två typer av mått. **Sammanställd** mått samla in ingång-/ utgång, tillgänglighet, svarstid och lyckade procenttal. De här måtten samman för blob, kön, tabell och Filtjänster. **Per API** samlar in samma uppsättning mätvärden för varje Lagringsåtgärden i Azure Storage service API. Loggning för lagring kan du registrera information för både slutförda och misslyckade begäranden på ditt lagringskonto. Dessa loggar kan du se detaljer för Läs-, Skriv- och delete-åtgärder mot dina Azure-tabeller, köer och blobbar. De kan du också se orsakerna till misslyckade begäranden som tidsgränser, begränsning och auktorisering fel.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Aktivera loggning och mått

I den vänstra menyn, Välj **resursgrupper**väljer **myResourceGroup**, och välj sedan ditt lagringskonto i resurslistan.

Under **diagnostik** ange **Status** till **på**. Se till att **Blob sammanställd mått**, **Blob per API-mått**, och **Blob loggar** är aktiverade.

När du är klar klickar du på **spara**

![Diagnostik-fönstret](media/storage-monitor-troubleshoot-storage-application/figure1.png)

## <a name="enable-alerts"></a>Aktivera aviseringar

Aviseringar är ett sätt att e-administratörer eller utlösa en webhook baserat på ett mått brott mot ett tröskelvärde. I det här exemplet aktiverar du en avisering om den `SASClientOtherError` mått.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Navigera till storage-konto i Azure-portalen

I den vänstra menyn, Välj **resursgrupper**väljer **myResourceGroup**, och välj sedan ditt lagringskonto i resurslistan.

Under den **övervakning** väljer **Varna regler**.

Välj **+ Lägg till avisering**under **lägga till en varningsregel**, Fyll i informationen som krävs. Välj `SASClientOtherError` från den **mått** listrutan.

![Diagnostik-fönstret](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Simulera ett fel

För att simulera en giltig avisering försök att begära en obefintlig blob från ditt lagringskonto. Om du vill göra detta måste du ersätta den `<incorrect-blob-name>` värdet med ett värde som inte finns. Kör följande kodexempel några gånger att simulera misslyckade begäranden för blob.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

Följande bild är ett exempel som baseras simulerade felet varningen med föregående exempel.

 ![Exempel avisering](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>Hämta och visa loggar

Lagring loggar lagrar data i en uppsättning blobbar i en blobbbehållare med namnet **$logs** i ditt lagringskonto. Den här behållaren visas inte om du lista alla blobbbehållare i ditt konto men du kan se innehållet om du kommer åt den direkt.

I det här scenariot kan du använda [Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) att interagera med ditt Azure storage-konto.

### <a name="download-microsoft-message-analyzer"></a>Hämta Microsoft Message Analyzer

Hämta [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) och installera programmet.

Starta programmet och välj **filen** > **öppna** > **från andra filen källor**.

I den **filen Selector** markerar **+ Lägg till Azure-anslutningen**. Ange i din **lagringskontonamnet** och **kontonyckel** och på **OK**.

![Microsoft Message Analyzer - Azure Storage anslutningsdialogrutan Lägg till](media/storage-monitor-troubleshoot-storage-application/figure3.png)

När du är ansluten, expandera behållare i trädet lagring i vyn log-BLOB. Markera den senaste och klicka på **OK**.

![Microsoft Message Analyzer - Azure Storage anslutningsdialogrutan Lägg till](media/storage-monitor-troubleshoot-storage-application/figure4.png)

På den **ny Session** dialogrutan klickar du på **starta** att visa loggen.

När loggen öppnas och kan du visa storage-händelser. Som du ser i följande bild har en `SASClientOtherError` utlöste på lagringskontot. Mer information om lagring loggning finns [Storage Analytics](../common/storage-analytics.md).

![Microsoft Message Analyzer - visa händelser](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) är ett annat verktyg som kan användas för att interagera med dina lagringskonton, inklusive den **$logs** behållare och de loggar som finns i den.

## <a name="next-steps"></a>Nästa steg

Du lärt dig hur du övervakar och felsöka ditt lagringskonto, till exempel att i en del fyra och den sista delen av serien:

> [!div class="checklist"]
> * Aktivera loggning och mått
> * Aktivera aviseringar för auktorisering fel
> * Kör test-trafik med felaktigt SAS-token
> * Hämta och analysera loggar

Den här länken om du vill se förskapad lagring prover.

> [!div class="nextstepaction"]
> [Azure storage skriptexempel](storage-samples-blobs-cli.md)