---
title: Distribuera mallar med Visual Studio i Azure Stack | Microsoft Docs
description: Lär dig hur du distribuerar mallar med Visual Studio i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 9cbe39c018d5ba91599d8ec429430fceaaa567c5
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064635"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Distribuera mallar i Azure Stack med Visual Studio

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan använda Visual Studio för att distribuera Azure Resource Manager-mallar i Azure Stack.

## <a name="to-deploy-a-template"></a>Distribuera en mall

1. [Installera och ansluta](azure-stack-install-visual-studio.md) till Azure Stack med Visual Studio.
2. Öppna Visual Studio.
3. Välj **filen**, och välj sedan **New**. I **nytt projekt**väljer **Azure-resursgrupp**.
4. Ange en **namn** för det nya projektet och välj sedan **OK**.
5. I **Välj Azure-mall**, Välj **Azure Stack-Quickstart** från den nedrullningsbara listan.
6. Välj **101 – skapa-lagringskonto**, och välj sedan **OK**.
7. I det nya projektet, expanderar den **mallar** nod i **Solution Explorer** att se de tillgängliga mallarna.
8. I **Solution Explorer**, Välj namnet på projektet och välj sedan **distribuera**. Välj **ny distribution**.
9. I **distribuera till resursgrupp**, använda den **prenumeration** listrutan att välja Microsoft Azure Stack-prenumeration.
10. Från den **resursgrupp** , Välj en befintlig resursgrupp eller skapa en ny.
11. Från den **resursgruppsplats** listan, Välj en plats och välj sedan **distribuera**.
12. I **redigera parametrar**, ange värden för parametrarna (som varierar beroende på mall), och välj sedan **spara**.

## <a name="next-steps"></a>Nästa steg

* [Distribuera mallar med kommandoraden](azure-stack-deploy-template-command-line.md)
* [Utveckla mallar för Azure Stack](azure-stack-develop-templates.md)
