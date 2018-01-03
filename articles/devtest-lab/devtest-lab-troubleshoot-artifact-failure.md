---
title: "Diagnostisera artefakt fel på en virtuell dator i Azure DevTest Labs | Microsoft Docs"
description: "Lär dig hur du felsöker artefakt fel i Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: v-craic
ms.openlocfilehash: 6c03ce8f91ec688e32e379f1284767db9a45920c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostisera artefakt fel i labbet 
När du har skapat en artefakt, kan du kontrollera om det har lyckats eller misslyckats. Artefakt loggar i Azure DevTest Labs innehåller information som du kan använda för att diagnostisera ett artefakt-fel. Du har ett antal alternativ för att visa artefakt logginformation för en virtuell Windows-dator:

* I Azure-portalen
* I den virtuella datorn

> [!NOTE]
> För att säkerställa att fel identifieras korrekt och förklaras, är det viktigt att artefakten har rätt struktur. Information om hur du skapar en artefakt korrekt finns [skapa anpassade artefakter](devtest-lab-artifact-author.md). Om du vill se ett exempel på en korrekt strukturerad artefakt checka ut den [testa parametertyper](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefakt.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Felsöka artefakt fel med hjälp av Azure portal

1. Välj ditt labb i Azure-portalen i din lista över resurser.
2. Välj den Windows-VM som innehåller den artefakt som du vill undersöka.
3. I den vänstra rutan under **allmänna**väljer **artefakter**. En lista över artefakter som är associerade med den virtuella datorn visas. Namnet på artefakten och artefakt status anges.

   ![Artefakt status](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Välj en artefakt som visar en **misslyckades** status. Artefakten öppnas. Ett tillägg som innehåller information om felet för artefakten meddelande.

   ![Artefakt felmeddelande](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Felsöka artefakt fel från den virtuella datorn

1. Logga in på den virtuella datorn som innehåller den artefakt som du vill felsöka.
2. Gå till C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1,9*\Status, där *1,9* versionsnummer Azure-tillägget för anpassat skript.

   ![Filen med](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Öppna den **status** fil.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Anslut en virtuell dator till en befintlig Active Directory-domän med hjälp av en Resource Manager-mall i DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägga till en Git-lagringsplats i ett labb](devtest-lab-add-artifact-repo.md).

