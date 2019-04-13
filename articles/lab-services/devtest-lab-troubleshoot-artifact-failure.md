---
title: Diagnostisera fel i artefakter i en virtuell dator i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du felsöker fel i artefakter i Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2019
ms.author: spelluru
ms.openlocfilehash: 29af70a2713e7b4aebf611d8f2b547e38c6c5d3d
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546373"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostisera fel i artefakter i laboratoriet 
När du har skapat en artefakt, kan du kontrollera om du vill se om den lyckades eller misslyckades. Artefakt-loggarna i Azure DevTest Labs ger information som du kan använda för att diagnostisera ett artefakt-fel. Du har ett par alternativ för att visa artefakten logginformation för en virtuell Windows-dator:

* I Azure-portalen
* I den virtuella datorn

> [!NOTE]
> För att säkerställa att fel identifieras korrekt och förklaras, är det viktigt att artefakten har rätt struktur. Information om hur du skapar en artefakt korrekt finns i [skapa anpassade artefakter](devtest-lab-artifact-author.md). Om du vill se ett exempel på en korrekt strukturerad artefakt, Kolla in den [testa parametertyper](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefakten.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Felsöka fel i artefakter med hjälp av Azure portal

1. Välj ditt labb i Azure-portalen, i listan över resurser.
2. Välj den Windows-VM som innehåller den artefakt som du vill undersöka.
3. I den vänstra panelen under **Allmänt**väljer **artefakter**. En lista över artefakter som associeras med den virtuella datorn visas. Namnet på artefakten och artefakt status anges.

   ![Artefakten status](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Välj en artefakt som visar en **misslyckades** status. Artefakten öppnas. Ett tillägg som innehåller information om fel på artefakten meddelande.

   ![Artefakten felmeddelande](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Felsöka fel i artefakter från den virtuella datorn

1. Logga in på den virtuella datorn som innehåller den artefakt som du vill diagnostisera.
2. Gå till C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, där *1.9* är det anpassade Skripttillägget Azure-versionsnumret.

   ![Filen med](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Öppna den **status** fil.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Ansluta en dator till en befintlig Active Directory-domän med hjälp av Resource Manager-mall i DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägga till en Git-lagringsplats i ett labb](devtest-lab-add-artifact-repo.md).

