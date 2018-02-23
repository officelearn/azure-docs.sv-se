---
title: Distribuera mallar med Visual Studio i Azure-stacken | Microsoft Docs
description: "Lär dig mer om att distribuera mallar med Visual Studio i Azure-stacken."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: ecefa8c3695aad0593516cb9ad8812216e163192
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Distribuera mallar i Azure-stacken med Visual Studio

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Använd Visual Studio för att distribuera Azure Resource Manager-mallar i Azure-stacken development Kit.

1. [Installera och ansluta](azure-stack-install-visual-studio.md) till Azure-stacken med Visual Studio.
2. Öppna Visual Studio.
3. Klicka på **filen**, klickar du på **ny**, och i den **nytt projekt** klickar du på **Azure-resursgrupp**.
4. Ange en **namn** nytt projekt och klicka sedan på **OK**.
5. I den **Välj Azure-mall** i dialogrutan den *Visa mallar från den här platsen* listrutan **Azure Stack Snabbstart**
6. Klicka på **101-skapa-storage-konto**, och klicka sedan på **OK**.  
7. I det nya projektet visas en lista över tillgängliga mallar genom att expandera den **mallar** nod i den **Solution Explorer** fönstret.
8. I den **Solution Explorer** fönstret, högerklicka på namnet på ditt projekt, klickar du på **distribuera**, klicka på **ny distribution**.
9. I den **distribuera till resursgrupp** i dialogrutan den **prenumeration** listrutan, väljer din prenumeration på Microsoft Azure-stacken.
10. I den **resursgruppen** listan, Välj en befintlig resursgrupp eller skapa en ny.
11. I den **resursgruppsplats** listan, Välj en plats och klicka sedan på **distribuera**.
12. I den **redigera parametrar** dialogrutan Ange värden för parametrarna (som varierar beroende på mall), och klicka sedan på **spara**.

## <a name="next-steps"></a>Nästa steg
[Distribuera mallar med kommandoraden](azure-stack-deploy-template-command-line.md)

[Utveckla mallar för Azure-Stack](azure-stack-develop-templates.md)

