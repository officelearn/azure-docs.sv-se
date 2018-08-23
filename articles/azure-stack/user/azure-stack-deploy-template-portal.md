---
title: Distribuera mallar med portalen i Azure Stack | Microsoft Docs
description: Lär dig hur du kan distribuera mallar med Azure Stack-portal.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 931c3d8beb9f2ed12228c74f09f84bbdee1798b8
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057373"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Distribuera mallar med hjälp av Azure Stack-portalen

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan använda portalen för att distribuera Azure Resource Manager-mallar i Azure Stack.

## <a name="to-deploy-a-template"></a>Distribuera en mall

1. Logga in på portalen väljer **New**, och välj sedan **anpassad**.
2. Välj **malldistributionen**.
3. Välj **redigera mallen**, och sedan klistra in koden JSON-mall i code-fönstret. Välj **Spara**.
4. Välj **redigera parametrar**, ange värden för parametrarna som visas och välj sedan **OK**.
5. Välj **prenumeration**. Välj den prenumeration som du vill använda och välj sedan **OK**.
6. Välj **resursgrupp**. Välj en befintlig resursgrupp eller skapa en ny och välj sedan **OK**.
7. Välj **Skapa**. En ny panel på instrumentpanelen spårar förloppet för malldistributionen av.

## <a name="next-steps"></a>Nästa steg

* [Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)
