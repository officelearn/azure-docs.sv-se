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
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: eea4f568f21693764222c8fdbe3316bf6008cc05
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604241"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Distribuera mallar med hjälp av Azure Stack-portalen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda portalen för att distribuera Azure Resource Manager-mallar till Azure-stacken.

## <a name="to-deploy-a-template"></a>Att distribuera en mall

1. Logga in på portalen, Välj **ny**, och välj sedan **anpassad**.
2. Välj **malldistribution**.
3. Välj **redigera mallen**, och sedan klistra in koden JSON-mall i kodfönstret. Välj **Spara**.
4. Välj **redigera parametrar**, ange värden för parametrarna som visas och välj sedan **OK**.
5. Välj **prenumeration**. Välj den prenumeration som du vill använda och välj sedan **OK**.
6. Välj **resursgruppen**. Välj en befintlig resursgrupp eller skapa en ny och välj sedan **OK**.
7. Välj **Skapa**. En ny panel på instrumentpanelen spårar förloppet för distributionen av mallen.

## <a name="next-steps"></a>Nästa steg

* [Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)
