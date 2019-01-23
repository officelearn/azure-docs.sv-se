---
title: Storage-konton i Azure Stack | Microsoft Docs
description: Lär dig hur du skapar ett Azure Stack-lagringskonto.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/18/2019
ms.author: mabrigg
ms.openlocfilehash: c3b1c0e6ed0850e6346d385dc3e86afdcb78fe39
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478543"
---
# <a name="storage-accounts-in-azure-stack"></a>Lagringskonton i Azure Stack
Lagringskonton omfattar Blob- och tabelltjänster och det unika namnområdet för dina lagrade dataobjekt. Som standard är data i ditt konto endast tillgängliga för dig, kontoägaren.

1. Logga in på Azure Stack POC-datorn till `https://adminportal.local.azurestack.external` som [administratör](azure-stack-connect-azure-stack.md), och klicka sedan på **+ skapa en resurs** > **Data + lagring**  >  **Lagringskonto**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. I den **skapa lagringskonto** bladet anger du ett namn för ditt lagringskonto. Skapa en ny **resursgrupp**, eller en befintlig och sedan klicka på **skapa** att skapa lagringskontot.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Om du vill se det nya kontot, klickar du på **alla resurser**, och Sök efter lagringskontot och klicka på dess namn.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Nästa steg
[Använda Azure Resource Manager-mallar](user/azure-stack-arm-templates.md)

[Lär dig mer om Azure storage-konton](../storage/common/storage-create-storage-account.md)

[Ladda ned guiden för Azure Stack Azure-konsekvent verifiering](https://aka.ms/azurestacktp1doc)
