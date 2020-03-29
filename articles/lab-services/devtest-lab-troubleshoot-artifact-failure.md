---
title: Diagnostisera artefaktfel i en virtuell Azure DevTest Labs-dator
description: DevTest Labs innehåller information som du kan använda för att diagnostisera ett artefaktfel. Den här artikeln visar hur du felsöker artefaktfel.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 7229f1ee4061eb38b7c6da09df21102ab302ab42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760325"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostisera artefaktfel i labbet 
När du har skapat en artefakt kan du kontrollera om den lyckades eller misslyckades. Artefaktloggar i Azure DevTest Labs innehåller information som du kan använda för att diagnostisera ett artefaktfel. Du har ett par alternativ för att visa artefaktlogginformation för en Windows VM:

* I Azure-portalen
* I den virtuella datorn

> [!NOTE]
> För att säkerställa att fel identifieras och förklaras korrekt är det viktigt att artefakten har rätt struktur. Information om hur du konstruerar en artefakt korrekt finns i [Skapa anpassade artefakter](devtest-lab-artifact-author.md). Om du vill se ett exempel på en korrekt strukturerad artefakt finns i [testparametertypens](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefakt.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Felsöka artefaktfel med hjälp av Azure-portalen

1. Välj ditt labb i Azure-portalen i listan över resurser.
2. Välj den virtuella datorn för Windows som innehåller den artefakt som du vill undersöka.
3. Välj **Artefakter**under **ALLMÄNT**på den vänstra panelen. En lista över artefakter som är associerade med den virtuella datorn visas. Namnet på artefakten och artefaktstatusen anges.

   ![Artefaktstatus](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Välj en artefakt som visar en **misslyckad** status. Artefakten öppnas. Ett tilläggsmeddelande som innehåller information om artefaktens fel visas.

   ![Felmeddelande om artefakt](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Felsöka artefaktfel inifrån den virtuella datorn

1. Logga in på den virtuella datorn som innehåller den artefakt som du vill diagnostisera.
2. Gå till C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, där *1.9* är versionsnumret för Azure Custom Script Extension.

   ![Statusfilen](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Öppna **statusfilen.**

Instruktioner om hur du hittar loggfilerna på en **virtuell Linux-dator** finns i följande artikel: [Använd Azure Custom Script Extension Version 2 med virtuella Linux-datorer](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Ansluta en virtuell dator till en befintlig Active Directory-domän med hjälp av en Resource Manager-mall i DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Nästa steg
* Läs om hur du [lägger till en Git-databas i ett labb](devtest-lab-add-artifact-repo.md).

