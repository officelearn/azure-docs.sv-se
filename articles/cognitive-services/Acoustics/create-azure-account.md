---
title: Konfigurera Azure-konton för Project Akustik
titlesuffix: Azure Cognitive Services
description: Följ den här guiden för att konfigurera Azure Batch och Storage-konton som är nödvändiga för att arbeta med Akustik.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b8735c0c5d05f2ee4bd17dc41fc90d1f5aa5128a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876699"
---
# <a name="create-an-azure-batch-account"></a>Skapa ett Azure Batch-konto
Följ den här guiden för att konfigurera Azure Batch och Storage-konton som är nödvändiga för att arbeta med Akustik. Läs om hur Unity-plugin-programmet som har utvecklats som en del av projektet Akustik [vad är Akustik](what-is-acoustics.md). Information om hur du införlivar Akustik till ditt Unity-projekt finns i [komma igång](getting-started.md).  

## <a name="get-an-azure-subscription"></a>Skaffa en Azure-prenumeration
En [Azure-prenumeration](https://azure.microsoft.com/free/) krävs innan du konfigurerar Batch och Storage-konton. Om du loggar för första gången, ger Azure några tidsbegränsad kostnadsfria resurser och en kredit på 200 USD.

## <a name="create-azure-batch-and-storage-accounts"></a>Skapa Azure Batch och storage-konton
Följ sedan [dessa instruktioner](https://docs.microsoft.com/azure/batch/batch-account-create-portal) att ställa in din Azure Batch och tillhörande Azure Storage-konton.

Välj standardalternativen för Batch- och Storage-konton:
  
  ![Nytt Batch-konto](media/NewBatchAccountCreate.png)

  ![Nytt Lagringskonto](media/BatchStorageAccountCreate.png)

Det tar några minuter för Azure för att distribuera kontona. Leta efter ett meddelande för slutförande i det övre högra hörnet på portalen.
  
  ![Konton som har distribuerats](media/BatchAccountsDeployNotification.png)

Dina konton ska nu visas på instrumentpanelen.
  
  ![Portalens instrumentpanel](media/AzurePortalDashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Konfigurera Akustik ändamålet Användargränssnittet med autentiseringsuppgifter för Azure
Klicka på länken för Batch-konto på instrumentpanelen, och klicka sedan på den **nycklar** länken på sidan Batch-konto för att få åtkomst till dina autentiseringsuppgifter.
  
  ![Länk för batch-nycklar](media/BatchAccessKeys.png)

  ![Autentiseringsuppgifterna för batch-konto](media/BatchKeysInfo.png)

Klicka på den **Lagringskonto** länk på sidan för att komma åt autentiseringsuppgifterna för ditt Azure Storage-konto.
  
  ![Autentiseringsuppgifter för lagringskonto](media/StorageKeysInfo.png)

Ange autentiseringsuppgifterna i fliken ändamålet enligt beskrivningen i den [skapa Användargränssnittet genomgången](bake-ui-walkthrough.md).

## <a name="node-types-and-region-support"></a>Nodtyper och regionsstöd
Projektet Akustik kräver F - och H-serien compute optimized Azure VM-noder som inte kanske stöds i alla Azure-regioner. Kontrollera [den här tabellen](https://azure.microsoft.com/global-infrastructure/services) att se till att du väljer rätt plats för Batch-kontot. I det här tillfället H-serien stöds virtuella datorer i östra USA, norra centrala USA, södra centrala USA, västra USA, västra USA 2, Norra Europa, västra Europa och västra Japan.

## <a name="upgrading-your-quota"></a>Uppgradera din kvot
Azure Batch-konton har etablerats för kontot som skapas med en gräns på 20 compute-kärnor. Du kanske vill öka gränsen för ändamålet snabbare, eftersom du kan parallellisera Akustik arbetsbelastningen över flera noder, upp till antalet avsökningen punkter i din scen. Du kan begära en kvot genom att klicka på den **kvot** länka på portalsidan Azure Batch och klicka sedan på **begäran kvot öka**:

![Azure-kvot](media/azurequotas.png)

## <a name="next-steps"></a>Nästa steg
* Kom igång [integrera Akustik i dina Unity-projekt](getting-started.md)
* Utforska den [exempel scen](sample-walkthrough.md)

