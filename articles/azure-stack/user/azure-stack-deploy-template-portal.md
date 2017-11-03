---
title: Distribuera mallar med portalen i Azure-stacken | Microsoft Docs
description: "Lär dig hur du använder Azure Stack-portal för att distribuera mallar."
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 630d86ed7e3558ae2c8a62f4e1a94f10ca11812d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Distribuera mallar med hjälp av Azure Stack-portalen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Använda portalen för att distribuera Azure Resource Manager-mallar i Azure-stacken development Kit.

Resource Manager-mallar distribuera och Tillhandahåll alla resurser i programmet i en enda, samordnad åtgärd.

1. Logga in på portalen klickar du på **ny**, klickar du på **anpassad**, och klicka sedan på **malldistribution**.
2. Klicka på **redigera mallen**klistra in koden JSON-mall i bladet och klicka sedan på **spara**.
3. Klicka på **redigera parametrar**ange värden för parametrarna i listan och klicka sedan på **OK**.
4. Klicka på **prenumeration**, Välj den prenumeration som du vill använda och klicka sedan på **OK**.
5. Klicka på **resursgruppen**, Välj en befintlig resursgrupp eller skapa en ny och klicka sedan på **OK**.
6. Klicka på **Skapa**. En ny panel på instrumentpanelen spårar förloppet för distributionen av mallen.

## <a name="next-steps"></a>Nästa steg
[Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)

