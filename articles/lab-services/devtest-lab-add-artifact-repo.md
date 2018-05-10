---
title: Lägg till en Git-lagringsplats i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till en GitHub eller Visual Studio Team Services Git-lagringsplats för anpassade artefakter-källan i Azure DevTest Labs.
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
ms.openlocfilehash: 80724a7d8d2b5cec19bdbce27cdafd4a9c09eb47
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Lägg till en Git-lagringsplats för att lagra anpassade artefakter och Resource Manager-mallar

Du kan [skapa anpassade artefakter](devtest-lab-artifact-author.md) för de virtuella datorerna i ditt labb eller [använda Azure Resource Manager-mallar för att skapa en anpassad testmiljö](devtest-lab-create-environment-from-arm.md). Du måste lägga till en privat Git-lagringsplats för artefakter eller Resource Manager-mallar som skapas av ditt team. Databasen kan finnas på [GitHub](https://github.com) eller på [Visual Studio Team Services](https://visualstudio.com).

Vi erbjuder en [GitHub-lagringsplatsen för artefakter](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) som du kan distribuera som-är, eller anpassa dem för din testlabb. När du anpassa eller skapa en artefakt, kan inte du lagra artefakten i offentliga lagringsplats. Du måste skapa en egen privata lagringsplatsen för anpassade artefakter och artefakter som du skapar. 

När du skapar en virtuell dator kan du spara Resource Manager-mallen och anpassa den om du vill ha och sedan använda det för att skapa flera virtuella datorer. Du måste skapa egna privata lagringsplats för dina egna Resource Manager-mallar.  

* Information om hur du skapar en GitHub-databas finns [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Information om hur du skapar ett Team Services-projekt som har en Git-lagringsplats finns [Anslut till Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Följande bild är ett exempel på hur en databas som har artefakter kan se ut i GitHub:  

![Exempel på GitHub-repo-artefakter](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Hämta information om databas och autentiseringsuppgifter
Om du vill lägga till en databas i ditt labb först hämta viktig information från databasen. I följande avsnitt beskrivs hur du hämtar information som krävs för databaser som finns på GitHub eller Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Hämta klon-URL för GitHub-lagringsplatsen och personlig åtkomst-token

1. Gå till startsidan för GitHub-lagringsplats som innehåller artefakten eller Malldefinitioner av Resource Manager.
2. Välj **kloning eller hämta**.
3. URL: en till Urklipp och markera den **HTTPS klona url** knappen. Spara URL: en för senare användning.
4. Välj den profil avbildningen i det övre högra hörnet i GitHub och välj sedan **inställningar**.
5. I den **personliga inställningar** menyn till vänster, Välj **personlig åtkomsttoken**.
6. Välj **Skapa ny token**.
7. På den **ny personliga åtkomsttoken** sidan under **Token beskrivning**, ange en beskrivning. Acceptera standardvärdet artiklarna under **väljer du**, och välj sedan **generera Token**.
8. Spara den genererade token. Du använder token senare.
9. Stäng GitHub.   
10. Fortsätta att den [ansluta ditt labb till databasen](#connect-your-lab-to-the-repository) avsnitt.

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Hämta Visual Studio Team Services databasen klon-URL och personlig åtkomst-token

1. Gå till startsidan för team-samling (t.ex, https://contoso-web-team.visualstudio.com), och välj sedan projektet.
2. På startsidan projektet väljer **kod**.
3. Visa klon-URL på projektet **kod** väljer **klona**.
4. Spara URL: en. Du använda Webbadressen senare.
5. Om du vill skapa en personlig åtkomsttoken i menyn för användarkonton listrutan, Välj **min profil**.
6. Välj på sidan profil information **säkerhet**.
7. På den **säkerhet** väljer **Lägg till**.
8. På den **skapa en personlig åtkomsttoken** sidan:
   1. Ange en **beskrivning** för token.
   2. I den **upphör att gälla i** väljer **180 dagar**.
   3. I den **konton** väljer **alla tillgängliga konton**.
   4. Välj den **alla scope** alternativet.
   5. Välj **skapa Token**.
9. Den nya token som visas i den **personlig åtkomsttoken** lista. Välj **kopiera Token**, och spara token-värde för senare användning.
10. Fortsätta att den [ansluta ditt labb till databasen](#connect-your-lab-to-the-repository) avsnitt.

## <a name="connect-your-lab-to-the-repository"></a>Ansluta ditt labb till databasen
1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan över tjänster.
3. Välj ditt labb i listan över övningar. 
4. Välj **konfiguration och principer** > **databaser** > **+ Lägg till**.

    ![Knappen Lägg till databasen](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. På andra **databaser** anger du följande information:
  1. **Namn**. Ange ett namn för databasen.
  2. **Url för Git-klonen**. Ange URL för Git HTTPS-klon som du kopierade tidigare från GitHub eller Visual Studio Team Services.
  3. **Branch**. Ange grenen för att få definitionerna.
  4. **Personlig åtkomsttoken**. Ange den personliga åtkomsttoken som du tidigare fått från GitHub eller Visual Studio Team Services.
  5. **Mappsökvägar**. Ange minst en mappsökväg i förhållande till klon-URL som innehåller din artefakt eller Malldefinitioner av Resource Manager. När du anger en underkatalog, kontrollera att du inkluderar snedstreck i mappsökvägen.

     ![Databaser område](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Välj **Spara**.

### <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Felsöka misslyckas artefakter i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Anslut en virtuell dator till en befintlig Active Directory-domän med hjälp av en Resource Manager-mall i DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat ditt privata Git-lagringsplatsen, kan du göra något eller båda av följande, beroende på dina behov:
* Lagra dina [anpassade artefakter](devtest-lab-artifact-author.md). Du kan använda dem senare för att skapa nya virtuella datorer.
* [Skapa flera Virtuella miljöer och PaaS-resurser med hjälp av Resource Manager-mallar](devtest-lab-create-environment-from-arm.md). Sedan kan du lagra mallarna i ditt privata lagringsplatsen.

När du skapar en virtuell dator måste kontrollera du att artefakter eller mallar läggs till Git-lagringsplatsen. De är omedelbart tillgängligt i listan över artefakter eller mallar. Namnet på din privata lagringsplatsen visas i kolumnen som anger källa. 
