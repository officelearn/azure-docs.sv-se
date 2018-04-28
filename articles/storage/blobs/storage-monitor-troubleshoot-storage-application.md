---
title: Övervaka och felsöka ett molnlagringsprogram i Azure | Microsoft Docs
description: Använda diagnostiska verktyg, mått och aviseringar för att felsöka och övervaka ett molnprogram.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: eb58104309802125a8424cbbf8a1bef3d1c5e79c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Övervaka och felsöka ett molnlagringsprogram

Den här kursen är den fjärde och sista delen i en serie. Du får lära dig hur du övervakar och felsöker ett molnlagringsprogram.

I del fyra i serien lär du dig att:

> [!div class="checklist"]
> * Aktivera loggning och mått
> * Aktivera aviseringar för auktoriseringsfel
> * Köra testtrafik med felaktiga SAS-token
> * Ladda ned och analysera loggar

Med [Azure Storage Analytics](../common/storage-analytics.md) kan du logga och visa statistik för ett lagringskonto. Dessa data ger insikter om hälsotillståndet för ditt lagringskonto. Innan du kan få insyn i ditt lagringskonto måste du konfigurera datainsamling. Den här processen innefattar att aktivera loggning, konfigurera mått och aktivera aviseringar.

Loggning och mått från lagringskonton aktiveras på fliken **Diagnostik** i Azure Portal. Det finns två typer av mått. **Aggregering** samlar in data om ingående/utgående trafik (ingress/egress), tillgänglighet, svarstid och framgångsprocent. De här måtten sammanställs för blob-, kö-, tabell- och filtjänster. **Per API** samlar in samma uppsättning mått för varje lagringsåtgärd i API:t för Azure Storage-tjänsten. Med loggningen i Storage kan du registrera information för både lyckade och misslyckade begäranden i ditt lagringskonto. I loggarna kan du se information om läs-, skriv- och borttagningsåtgärder för dina tabeller, köer och blobar i Azure. Du kan också se orsaker till varför begäranden har misslyckats, till exempel tidsgränser som uppnåtts, nätverksbegränsning och auktoriseringsfel.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Aktivera loggning och mått

På menyn till vänster väljer du **Resursgrupper**, **myResourceGroup** och sedan ditt lagringskonto i resurslistan.

Under **Diagnostik** anger du **På** för **Status**. Kontrollera att alla alternativ under **Blobegenskaper** är aktiverade.

Klicka på **Spara** när du är klar

![Fönstret Diagnostik](media/storage-monitor-troubleshoot-storage-application/contoso.png)

## <a name="enable-alerts"></a>Aktivera aviseringar

E-postadministratörer kan använda aviseringar för att utlösa en webhook när ett tröskelvärde uppnås för ett mått. I det här exemplet aktiverar du en avisering för måttet `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Navigera till lagringskontot i Azure Portal

På menyn till vänster väljer du **Resursgrupper**, **myResourceGroup** och sedan ditt lagringskonto i resurslistan.

Välj **Aviseringsregler** under **Övervakning**.

Välj **+ Lägg till avisering** och fyll i nödvändig information under **Lägg till en varningsregel**. Välj `SASClientOtherError` i listrutan **Mått**.

![Fönstret Diagnostik](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Simulera ett fel

För att simulera en giltig avisering kan du försöka begära en blob som inte finns från ditt lagringskonto. Det gör du genom att ersätta värdet `<incorrect-blob-name>` med ett värde som inte finns. Kör följande kodexempel ett par gånger för att simulera misslyckade blobbegäranden.

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

På bilden nedan visas ett exempel på hur det kan se ut när ett sådant simulerat fel körs.

 ![Exempelavisering](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>Ladda ned och visa loggar

Storage-loggar lagrar data i en uppsättning blobar i en blobbehållare med namnet **$logs** i ditt lagringskonto. Den här behållaren visas inte om du visar alla blobbehållare i ditt konto i en lista, men du kan se innehållet om du går direkt till behållaren.

I det här scenariot använder du [Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) för att interagera med ditt Azure-lagringskonto.

### <a name="download-microsoft-message-analyzer"></a>Ladda ned Microsoft Message Analyzer

Ladda ned [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) och installera programmet.

Starta programmet och välj **File** (Fil)  > **Open** (Öppna) > **From Other File Sources** (Från andra filkällor).

I dialogrutan **File Selector** (Filväljaren) väljer du **+ Add Azure Connection** (Lägg till Azure-anslutning). Ange **namnet på ditt lagringskonto** och **kontonyckeln** och klicka sedan på **OK**.

![Microsoft Message Analyzer – Dialogrutan för Azure Storage-anslutning](media/storage-monitor-troubleshoot-storage-application/figure3.png)

När du är ansluten expanderar du behållarna i lagringsträdvyn så att loggblobarna visas. Markera den senaste loggen och klicka på **OK**.

![Microsoft Message Analyzer – Dialogrutan för Azure Storage-anslutning](media/storage-monitor-troubleshoot-storage-application/figure4.png)

I dialogrutan **New Session** (Ny session) klickar du på **Start** för att visa loggen.

När loggen har öppnats kan du se lagringshändelserna. Som du ser på följande bild utlöstes `SASClientOtherError` på lagringskontot. Mer information om lagringsloggning finns i [Lagringsanalys](../common/storage-analytics.md).

![Microsoft Message Analyzer – Visa händelser](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) är ett annat verktyg som du kan använda för att interagera med dina lagringskonton, till exempel **$logs**-behållaren och de loggar som finns i behållaren.

## <a name="next-steps"></a>Nästa steg

I den fjärde och sista delen i serien har du lärt dig hur du övervakar och felsöker ditt lagringskonto, till exempel hur du gör för att:

> [!div class="checklist"]
> * Aktivera loggning och mått
> * Aktivera aviseringar för auktoriseringsfel
> * Köra testtrafik med felaktiga SAS-token
> * Ladda ned och analysera loggar

Följ den här länken om du vill se inbyggda lagringsexempel.

> [!div class="nextstepaction"]
> [Azure Storage-skriptexempel](storage-samples-blobs-cli.md)