---
title: Distribuera mallar med Visual Studio i Azure-stacken | Microsoft Docs
description: Lär dig mer om att distribuera mallar med Visual Studio i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4101567eff68789fe5d46a01de26f6a873b519fa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Distribuera mallar i Azure-stacken med Visual Studio

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda Visual Studio för att distribuera Azure Resource Manager-mallar till Azure-stacken.

Att distribuera en mall:

1. [Installera och ansluta](azure-stack-install-visual-studio.md) till Azure-stacken med Visual Studio.
2. Öppna Visual Studio.
3. Välj **filen**, och välj sedan **ny**. I **nytt projekt**väljer **Azure-resursgrupp**.
4. Ange en **namn** för nytt projekt och välj sedan **OK**.
5. I **Välj Azure-mall**, Välj **Azure Stack Quickstart** från den nedrullningsbara listan.
6. Välj **101-skapa-storage-konto**, och välj sedan **OK**.
7. I det nya projektet expanderar den **mallar** nod i **Solution Explorer** att se tillgängliga mallar.
8. I **Solution Explorer**, Välj namnet på ditt projekt och välj sedan **distribuera**. Välj **ny distribution**.
9. I **distribuera till resursgrupp**, använda den **prenumeration** listrutan för att välja din prenumeration på Microsoft Azure-stacken.
10. Från den **resursgruppen** listan, Välj en befintlig resursgrupp eller skapa en ny.
11. Från den **resursgruppsplats** listan, Välj en plats och väljer sedan **distribuera**.
12. I **redigera parametrar**, ange värden för parametrarna (som varierar beroende på mall), och välj sedan **spara**.

## <a name="next-steps"></a>Nästa steg

* [Distribuera mallar med kommandoraden](azure-stack-deploy-template-command-line.md)
* [Utveckla mallar för Azure-Stack](azure-stack-develop-templates.md)
