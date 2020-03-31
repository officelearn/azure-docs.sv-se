---
title: Lägga till en Git-databas i ett labb i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du lägger till en GitHub- eller Azure DevOps Services Git-databas för din anpassade artefaktkälla i Azure DevTest Labs.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1e7587c60e180fb35e1a2bed735b053b6b0c388a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294613"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Lägga till en Git-databas för att lagra egna artefakter och Resource Manager-mallar

Du kan [skapa anpassade artefakter](devtest-lab-artifact-author.md) för de virtuella datorerna i labbet eller använda Azure Resource [Manager-mallar för att skapa en anpassad testmiljö](devtest-lab-create-environment-from-arm.md). Du måste lägga till en privat Git-databas för de artefakter eller Resource Manager-mallar som teamet skapar. Databasen kan finnas på [GitHub](https://github.com) eller [på Azure DevOps Services](https://visualstudio.com).

Vi erbjuder en [GitHub-lagringsplats med artefakter](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) som du kan distribuera som de är, eller så kan du anpassa dem för dina labb. När du anpassar eller skapar en artefakt kan du inte lagra artefakten i den offentliga databasen. Du måste skapa en egen privat repo för anpassade artefakter och för artefakter som du skapar. 

När du skapar en virtuell dator kan du spara Resource Manager-mallen, anpassa den om du vill och sedan använda den senare för att skapa fler virtuella datorer. Du måste skapa en egen privat databas för att kunna lagra dina anpassade Resource Manager-mallar.  

* Mer information om hur du skapar en GitHub-databas finns i [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Mer information om hur du skapar ett Azure DevOps Services-projekt som har en Git-databas finns i [Anslut till Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Följande bild är ett exempel på hur en databas med artefakter kan se ut i GitHub:  

![Exempel på GitHub-artefakter repo](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Hämta databasinformation och autentiseringsuppgifter
Om du vill lägga till en databas i labbet får du först viktig information från databasen. I följande avsnitt beskrivs hur du får nödvändig information för databaser som finns på GitHub eller Azure DevOps Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Hämta GitHub-databasklonadressen och den personliga åtkomsttoken

1. Gå till startsidan för GitHub-databasen som innehåller malldefinitionerna för artefakt eller Resurshanteraren.
2. Välj **Klona eller ladda ned**.
3. Om du vill kopiera URL:en till Urklipp markerar du **url-knappen för HTTPS-klon.** Spara webbadressen för senare användning.
4. I det övre högra hörnet av GitHub markerar du profilbilden och väljer sedan **Inställningar**.
5. Välj **Personliga åtkomsttokens**på menyn **Personliga inställningar** .
6. Välj **Generera ny token**.
7. Ange en beskrivning under **Token beskrivning**på sidan Ny **personlig åtkomsttoken.** Acceptera standardobjekten under **Välj scope**och välj sedan **Generera token**.
8. Spara den genererade token. Du använder token senare.
9. Stäng GitHub.   
10. Fortsätt till avsnittet [Anslut labbet till databasavsnittet.](#connect-your-lab-to-the-repository)

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Hämta URL:en för Klon av Azure Repos och token för personlig åtkomst

1. Gå till startsidan för gruppsamlingen `https://contoso-web-team.visualstudio.com`(till exempel ) och välj sedan projektet.
2. Välj **Kod**på projektets startsida .
3. Om du vill visa klon-URL:en väljer du **Klona**på sidan **Projektkod.**
4. Spara webbadressen. Du använder webbadressen senare.
5. Om du vill skapa en personlig åtkomsttoken väljer du **Min profil**i den nedrullningsbara menyn Användarkonto .
6. På sidan med profilinformation väljer du **Säkerhet**.
7. Välj **Lägg till**på fliken **Säkerhet** .
8. På sidan **Skapa en personlig åtkomsttoken:**
   1. Ange en **beskrivning** för token.
   2. Välj **180 dagar**i listan **Upphör att gälla** i .
   3. I listan **Konton** väljer du **Alla tillgängliga konton**.
   4. Välj alternativet **Skrivskyddad.**
   5. Välj **Skapa token**.
9. Den nya token visas i listan **Token för personlig åtkomst.** Välj **Kopiera token**och spara sedan tokenvärdet för senare användning.
10. Fortsätt till avsnittet [Anslut labbet till databasavsnittet.](#connect-your-lab-to-the-repository)

## <a name="connect-your-lab-to-the-repository"></a>Ansluta labbet till databasen
1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **Fler tjänster**och välj sedan **DevTest Labs** i listan över tjänster.
3. Välj ditt labb i listan över labb. 
4. Välj **Konfiguration och principer** > **Databaser** > **+ Lägg till**.

    ![Knappen Lägg till databas](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. På den andra sidan **Databaser** anger du följande information:
   1. **Namn**. Ange ett namn på databasen.
   2. **Git Klon Url**. Ange Git HTTPS-klon-URL:en som du kopierade tidigare från GitHub eller Azure DevOps Services.
   3. **Gren**. Ange grenen för att hämta definitionerna.
   4. **Token för personlig åtkomst**. Ange den personliga åtkomsttoken som du fick tidigare från antingen GitHub eller Azure DevOps Services.
   5. **Mappsökvägar**. Ange minst en mappsökväg i förhållande till klon-URL:en som innehåller malldefinitionerna för artefakten eller Resurshanteraren. När du anger en underkatalog kontrollerar du att du inkluderar det vidarebefordrade snedstrecket i mappsökvägen.

      ![Området Databaser](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Välj **Spara**.

### <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Felsöka felaktiga artefakter i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Ansluta en virtuell dator till en befintlig Active Directory-domän med hjälp av en Resource Manager-mall i DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat din privata Git-databas kan du göra något av följande, beroende på dina behov:
* Lagra dina [egna artefakter](devtest-lab-artifact-author.md). Du kan använda dem senare för att skapa nya virtuella datorer.
* [Skapa miljöer med flera virtuella datorer och PaaS-resurser med hjälp av Resource Manager-mallar](devtest-lab-create-environment-from-arm.md). Sedan kan du lagra mallarna i din privata repo.

När du skapar en virtuell dator kan du kontrollera att artefakterna eller mallarna läggs till i Git-databasen. De är omedelbart tillgängliga i listan över artefakter eller mallar. Namnet på din privata repa visas i kolumnen som anger källan. 
