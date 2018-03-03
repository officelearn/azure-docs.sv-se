---
title: Storage-konton i Azure-stacken | Microsoft Docs
description: "Lär dig hur du skapar ett lagringskonto i Azure-stacken."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a599d809ba3da8487a6c5d115bf04922a546e6ad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="storage-accounts-in-azure-stack"></a>Lagringskonton i Azure Stack
Lagringskonton omfattar Blob- och tabelltjänster och det unika namnområdet för dina lagrade dataobjekt. Som standard är data i ditt konto endast tillgängliga för dig, kontoägaren.

1. Logga in på Azure-stacken POC-dator i `https://adminportal.local.azurestack.external` som [administratör](azure-stack-connect-azure-stack.md), och klicka sedan på **ny** > **Data + lagring**  >  **Lagringskonto**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. I den **skapa lagringskonto** bladet, ange ett namn för ditt lagringskonto. Skapa en ny **resursgruppen**, eller välj en befintlig, och klicka sedan på **skapa** att skapa lagringskontot.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Klicka för att visa det nya kontot **alla resurser**Sök sedan efter storage-konto och klicka på dess namn.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Nästa steg
[Använda Azure Resource Manager-mallar](user/azure-stack-arm-templates.md)

[Lär dig mer om Azure storage-konton](../storage/common/storage-create-storage-account.md)

[Hämta Azure Stack konsekvent Azure Storage-verifiering Guide](http://aka.ms/azurestacktp1doc)
