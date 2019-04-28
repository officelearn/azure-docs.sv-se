---
title: Projektkonfiguration Akustik Azure Batch-konto
titlesuffix: Azure Cognitive Services
description: Den här anvisningen beskriver hur du konfigurerar ett Azure Batch-konto för användning med projekt Akustik Unity och Unreal engine-integreringar.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 7a7f5f6738b4bc96b6248deb062c7b3f63048148
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335759"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Projektkonfiguration Akustik Azure Batch-konto
Den här anvisningen beskriver hur du konfigurerar ett Azure Batch-konto för användning med projekt Akustik Unity och Unreal engine-integreringar.

## <a name="get-an-azure-subscription"></a>Skaffa en Azure-prenumeration
En [Azure-prenumeration](https://azure.microsoft.com/free/) krävs innan du konfigurerar Batch och Storage-konton. Om du loggar för första gången, ger Azure några tidsbegränsad kostnadsfria resurser och en kredit på 200 USD.

## <a name="create-azure-batch-and-storage-accounts"></a>Skapa Azure Batch och storage-konton
Följ sedan [dessa instruktioner](https://docs.microsoft.com/azure/batch/batch-account-create-portal) att ställa in din Azure Batch och tillhörande Azure Storage-konton.

Välj standardalternativen för Batch- och Storage-konton:
  
  ![Skärmbild av Azure Batch nya konton alternativ som visar standardinställningarna](media/new-batch-account-create.png)

  ![Skärmbild av Azure Storage nya konton alternativ som visar standardinställningarna](media/batch-storage-account-create.png)

Det tar några minuter för Azure för att distribuera kontona. Leta efter ett meddelande för slutförande i det övre högra hörnet på portalen.
  
  ![Skärmbild av Azure-konton distribueras meddelande](media/batch-accounts-deploy-notification.png)

Dina konton ska nu visas på instrumentpanelen.
  
  ![Skärmbild av Azure portal instrumentpanelen som visar ett Batch- och Storage-konto](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Konfigurera Akustik ändamålet Användargränssnittet med autentiseringsuppgifter för Azure
Klicka på länken för Batch-konto på instrumentpanelen, och klicka sedan på den **nycklar** länken på sidan Batch-konto för att få åtkomst till dina autentiseringsuppgifter.
  
  ![Skärmbild av Azure Batch-konto med länk till sidan nycklar markerat](media/batch-access-keys.png)

  ![Skärmbild av Azure Batch-konto nycklar sida med åtkomstnycklar](media/batch-keys-info.png)

Klicka på den **Lagringskonto** länk på sidan för att komma åt autentiseringsuppgifterna för ditt Azure Storage-konto.
  
  ![Skärmbild av Azure Storage-konto nycklar sida med åtkomstnycklar](media/storage-keys-info.png)

Ange autentiseringsuppgifterna i den [Unity ändamålet plugin-programmet](unity-baking.md) eller [Unreal ändamålet plugin-programmet](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Nodtyper och regionsstöd
Projektet Akustik kräver Fsv2 - och H-serien compute optimized Azure VM-noder som inte kanske stöds i alla Azure-regioner. Kontrollera [den här tabellen](https://azure.microsoft.com/global-infrastructure/services) att se till att du väljer rätt plats för Batch-kontot.
![Skärmbild som visar Azure-datorer per Region](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Uppgradera din kvot
Azure Batch-konton har etablerats för kontot som skapas med en gräns på 20 compute-kärnor. Vi kanske vill öka gränsen för ändamålet snabbare, eftersom du kan parallellisera Akustik arbetsbelastningen över flera noder, upp till antalet avsökningen punkter i din scen. Du kan begära en kvot genom att klicka på den **kvot** länka på portalsidan Azure Batch och klicka sedan på **begäran kvot öka**:

![Skärmbild av Azure-kvoten sidan](media/azure-quotas.png)

## <a name="next-steps"></a>Nästa steg
* Integrera projekt Akustik plugin-programmet i din [Unity](unity-integration.md) eller [Unreal](unreal-integration.md) projekt

