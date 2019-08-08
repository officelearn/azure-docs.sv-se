---
title: Installation av Azure Batchs konto för projekt akustiska konton
titlesuffix: Azure Cognitive Services
description: Den här instruktionen beskriver hur du konfigurerar ett Azure Batch-konto för användning med projekt Akustisker och Unreal-motor integreringar.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855087"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Installation av Azure Batchs konto för projekt akustiska konton
Den här instruktionen beskriver hur du konfigurerar ett Azure Batch-konto för användning med projekt Akustisker och Unreal-motor integreringar.

## <a name="get-an-azure-subscription"></a>Skaffa en Azure-prenumeration
En [Azure-prenumeration](https://azure.microsoft.com/free/) krävs innan du konfigurerar batch-och lagrings konton. Om du registrerar dig för första gången ger Azure några tidsbegränsade kostnads fria resurser och $200 kredit.

## <a name="create-azure-batch-and-storage-accounts"></a>Skapa Azure Batch-och lagrings konton
Följ sedan [de här anvisningarna](https://docs.microsoft.com/azure/batch/batch-account-create-portal) för att konfigurera Azure Batch och associerade Azure Storage-konton.

Välj standard alternativ för både batch-och lagrings konton:
  
  ![Skärm bild av Azure Batch nya konto alternativ som visar standardinställningar](media/new-batch-account-create.png)

  ![Skärm bild av Azure Storage nya konto alternativ som visar standardinställningar](media/batch-storage-account-create.png)

Det tar några minuter för Azure att distribuera kontona. Sök efter ett meddelande om slut för ande i det övre högra hörnet på portalen.
  
  ![Skärm bild av distribuerat meddelande i Azure-konton](media/batch-accounts-deploy-notification.png)

Dina konton bör nu vara synliga på din instrument panel.
  
  ![Skärm bild av Azure Portal instrument panel med en batch och ett lagrings konto](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Konfigurera akustiskt användar gränssnitt med Azure-autentiseringsuppgifter
Klicka på länken batch-konto på instrument panelen och klicka sedan på länken **nycklar** på sidan batch-konto för att komma åt dina autentiseringsuppgifter.
  
  ![Skärm bild av Azure Batch konto med länk till tangenter-sidan markerad](media/batch-access-keys.png)

  ![Skärm bild av sidan Azure Batch konto nycklar med åtkomst nycklar](media/batch-keys-info.png)

Klicka på länken **lagrings konto** på sidan för att få åtkomst till dina Azure Storage kontoautentiseringsuppgifter.
  
  ![Skärm bild av sidan Azure Storage konto nycklar med åtkomst nycklar](media/storage-keys-info.png)

Ange dessa autentiseringsuppgifter i enhets- [plugin-programmet](unity-baking.md) eller [Unreal bageri-plugin](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Nodtyper och regions stöd
För projekt akustiska datorer krävs Fsv2-och H-seriens beräknings optimerade Azure VM-noder som kanske inte stöds i alla Azure-regioner. Kontrol lera att du har rätt plats för batch-kontot genom att kontrol lera [den här tabellen](https://azure.microsoft.com/global-infrastructure/services) .
![Skärm bild som visar Azure Virtual Machines efter region](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Uppgradera din kvot
Azure Batch konton skapas vid skapande av konto med en gräns på 20 beräknings kärnor. Vi kanske vill öka den här gränsen för snabbare bageri tider eftersom du kan parallellisera din akustiska arbets belastning över flera noder, upp till antalet avsöknings punkter i din scen. Du kan begära en kvot ökning genom att klicka på länken **kvot** på sidan Azure Batch Portal och sedan klicka på **begär kvot ökning**:

![Skärm bild av sidan Azure-kvot](media/azure-quotas.png)

## <a name="next-steps"></a>Nästa steg
* Integrera plugin-programmet för projekt i ditt [Unity](unity-integration.md) -eller [Unreal](unreal-integration.md) -projekt

