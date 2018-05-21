---
title: Distribuera mallar med hjälp av portalen i Azure-stacken | Microsoft Docs
description: Lär dig hur du använder Azure Stack-portal för att distribuera mallar.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 278f15271d3a5443102f5e387d3db1adb53fe7db
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Distribuera mallar med hjälp av Azure Stack-portalen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda portalen för att distribuera Azure Resource Manager-mallar till Azure-stacken.

Att distribuera en mall:

1. Logga in på portalen, Välj **ny**, och välj sedan **anpassad**.
2. Välj **malldistribution**.
3. Välj **redigera mallen**, och sedan klistra in koden JSON-mall i kodfönstret. Välj **Spara**.
4. Välj **redigera parametrar**, ange värden för parametrarna som visas och välj sedan **OK**.
5. Välj **prenumeration**. Välj den prenumeration som du vill använda och välj sedan **OK**.
6. Välj **resursgruppen**. Välj en befintlig resursgrupp eller skapa en ny och välj sedan **OK**.
7. Välj **Skapa**. En ny panel på instrumentpanelen spårar förloppet för distributionen av mallen.

## <a name="next-steps"></a>Nästa steg

[Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)
