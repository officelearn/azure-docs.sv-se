---
title: Konfigurera azure-batchkonto för projekt akustik
titlesuffix: Azure Cognitive Services
description: Den här inställningen beskriver hur du konfigurerar ett Azure Batch-konto för användning med Project Acoustics Unity och Unreal-motorintegrationer.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855087"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Konfigurera azure-batchkonto för projekt akustik
Den här inställningen beskriver hur du konfigurerar ett Azure Batch-konto för användning med Project Acoustics Unity och Unreal-motorintegrationer.

## <a name="get-an-azure-subscription"></a>Skaffa en Azure-prenumeration
En [Azure-prenumeration](https://azure.microsoft.com/free/) krävs innan du konfigurerar batch- och lagringskonton. Om du registrerar dig för första gången tillhandahåller Azure några tidsbegränsade kostnadsfria resurser och 200 USD-kredit.

## <a name="create-azure-batch-and-storage-accounts"></a>Skapa Azure Batch- och lagringskonton
Följ sedan [dessa instruktioner](https://docs.microsoft.com/azure/batch/batch-account-create-portal) för att konfigurera dina Azure Batch- och associerade Azure Storage-konton.

Välj standardalternativ för både batch- och lagringskonton:
  
  ![Skärmbild av azure batch nya konton alternativ som visar standardinställningar](media/new-batch-account-create.png)

  ![Skärmbild av azure storage nya konton alternativ som visar standardinställningar](media/batch-storage-account-create.png)

Det tar några minuter för Azure att distribuera kontona. Leta efter ett slutförandemeddelande i det övre högra hörnet på portalen.
  
  ![Skärmbild av azure-konton som distribuerats](media/batch-accounts-deploy-notification.png)

Dina konton ska nu visas på instrumentpanelen.
  
  ![Skärmbild av Azure Portal-instrumentpanelen som visar ett batch- och lagringskonto](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Konfigurera akustik baka användargränssnitt med Azure-autentiseringsuppgifter
Klicka på länken Batch-konto på instrumentpanelen och klicka sedan på länken **Nycklar** på sidan Batch-konto för att komma åt dina autentiseringsuppgifter.
  
  ![Skärmbild av Azure Batch-konto med länk till sidan Nycklar markerad](media/batch-access-keys.png)

  ![Skärmbild av sidan Azure Batch-kontonycklar med åtkomstnycklar](media/batch-keys-info.png)

Klicka på länken **Lagringskonto** på sidan för att komma åt dina Azure Storage-kontouppgifter.
  
  ![Skärmbild av sidan Azure Storage-kontonycklar med åtkomstnycklar](media/storage-keys-info.png)

Ange dessa referenser i [Unity baka plugin](unity-baking.md) eller [Unreal baka plugin](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Stöd för nodtyper och region
Project Acoustics kräver Fsv2- och H-seriens beräkningsoptimerade Azure VM-noder som kanske inte stöds i alla Azure-regioner. Kontrollera [den här tabellen](https://azure.microsoft.com/global-infrastructure/services) för att se till att du väljer rätt plats för ditt Batch-konto.
![Skärmbild som visar virtuella Azure-datorer efter region](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Uppgradera din kvot
Azure Batch-konton etableras när kontot skapas med en gräns på 20 beräkningskärnor. Vi kanske vill öka den här gränsen för snabbare bakningstider, eftersom du kan parallellisera din akustikarbetsbelastning över många noder, upp till antalet avsökningspunkter i din scen. Du kan begära en kvotökning genom att klicka på länken **Kvot** på portalsidan för Azure Batch och sedan klicka på **Ökning av begärkvot:**

![Skärmbild av sidan Azure-kvot](media/azure-quotas.png)

## <a name="next-steps"></a>Nästa steg
* Integrera project acoustics-insticksprogrammet i ditt [Unity-](unity-integration.md) eller [Unreal-projekt](unreal-integration.md)

