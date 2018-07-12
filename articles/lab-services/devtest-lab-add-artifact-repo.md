---
title: Lägga till en Git-lagringsplats i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till en GitHub eller Visual Studio Team Services Git-lagringsplats för din anpassade artefakter källa i Azure DevTest Labs.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452506"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Lägg till en Git-lagringsplats för att lagra anpassade artefakter och Resource Manager-mallar

Du kan [skapa anpassade artefakter](devtest-lab-artifact-author.md) för virtuella datorer i labbet, eller [Använd Azure Resource Manager-mallar för att skapa en anpassad testmiljö](devtest-lab-create-environment-from-arm.md). Du måste lägga till en privata Git-lagringsplats för artefakter eller Resource Manager-mallar som ditt team skapar. Databasen kan finnas på [GitHub](https://github.com) eller på [Visual Studio Team Services](https://visualstudio.com).

Vi erbjuder en [GitHub-lagringsplats för artefakter](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) som du kan distribuera som – är, eller du kan anpassa dem efter dina labb. När du anpassa eller skapa en artefakt, kan inte du lagra artefakten i den offentliga databasen. Du måste skapa din egen privata lagringsplats för anpassade artefakter och artefakter som du skapar. 

När du skapar en virtuell dator kan du spara Resource Manager-mallen, anpassa den om du vill ha och sedan använda den senare för att skapa fler virtuella datorer. Du måste skapa din egen privata lagringsplatsen för att lagra dina anpassade Resource Manager-mallar.  

* Läs hur du skapar en GitHub-lagringsplats i [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Läs hur du skapar ett Team Services-projekt som har en Git-lagringsplats i [Anslut till Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Följande bild är ett exempel på hur en databas som har artefakter kan se i GitHub:  

![Artefakter GitHub-exempellager](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Hämta information för databasen och autentiseringsuppgifter
Om du vill lägga till en lagringsplats i labbet, först få viktig information från databasen. I följande avsnitt beskrivs hur du hämtar information som behövs för databaser som finns på GitHub eller Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Hämta GitHub-lagringsplatsen klon-URL och personlig åtkomst-token

1. Gå till startsidan för GitHub-lagringsplatsen som innehåller artefakten eller Malldefinitioner för Resource Manager.
2. Välj **klona eller ladda ned**.
3. URL: en till Urklipp och markera den **url för HTTPS-klon** knappen. Spara URL: en för senare användning.
4. Välj en avbildning med profilen i det övre högra hörnet på GitHub och välj sedan **inställningar**.
5. I den **personliga inställningar** menyn till vänster, Välj **personliga åtkomsttoken**.
6. Välj **Generera ny token**.
7. På den **ny personlig åtkomsttoken** sidan under **Tokenbeskrivning**, ange en beskrivning. Acceptera standardobjekten under **Välj områden**, och välj sedan **generera Token**.
8. Spara den genererade token. Du använder token senare.
9. Stäng GitHub.   
10. Fortsätta att den [ansluta ditt labb till databasen](#connect-your-lab-to-the-repository) avsnittet.

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Hämta Visual Studio Team Services databasen klon-URL och personlig åtkomst-token

1. Gå till startsidan för din team-samling (till exempel https://contoso-web-team.visualstudio.com), och välj sedan ditt projekt.
2. Välj på projektets startsida, **kod**.
3. Visa klon-URL på projektet **kod** väljer **klona**.
4. Spara URL: en. Du använder URL: en senare.
5. Om du vill skapa en personlig åtkomsttoken i användarens konto nedrullningsbara menyn, Välj **min profil**.
6. På sidan profil information väljer **Security**.
7. På den **Security** fliken **Lägg till**.
8. På den **skapa en personlig åtkomsttoken** sidan:
   1. Ange en **beskrivning** för token.
   2. I den **upphör att gälla i** väljer **180 dagar**.
   3. I den **konton** väljer **alla tillgängliga konton**.
   4. Välj den **alla omfattningar** alternativet.
   5. Välj **skapa Token**.
9. Den nya token visas i den **personliga åtkomsttoken** lista. Välj **kopiera Token**, och spara token-värde för senare användning.
10. Fortsätta att den [ansluta ditt labb till databasen](#connect-your-lab-to-the-repository) avsnittet.

## <a name="connect-your-lab-to-the-repository"></a>Ansluta ditt labb till databasen
1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan över tjänster.
3. Listan över labbar, väljer du ditt labb. 
4. Välj **konfiguration och principer** > **databaser** > **+ Lägg till**.

    ![Knappen Lägg till databasen](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. På andra **databaser** anger du följande information:
  1. **Namn**. Ange ett namn för databasen.
  2. **Url för Git-klonen**. Ange URL för Git HTTPS-klonen som du kopierade tidigare från GitHub eller Visual Studio Team Services.
  3. **Gren**. Ange grenen för att få definitionerna.
  4. **Personlig åtkomsttoken**. Ange din personliga åtkomsttoken som du tidigare fått från GitHub eller Visual Studio Team Services.
  5. **Mappsökvägar**. Ange minst en mappsökväg i förhållande till den klon-URL som innehåller din artefakt eller Malldefinitioner för Resource Manager. När du anger en underkatalog måste du kontrollera att du inkluderar snedstreck i mappsökvägen.

     ![Lagringsplatser området](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Välj **Spara**.

### <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Felsöka misslyckas artefakter i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Ansluta en dator till en befintlig Active Directory-domän med hjälp av Resource Manager-mall i DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat din privata Git-lagringsplats kan göra du en eller båda av följande, beroende på dina behov:
* Store din [anpassade artefakter](devtest-lab-artifact-author.md). Du kan använda dem senare för att skapa nya virtuella datorer.
* [Skapa miljöer för flera virtuella datorer och PaaS-resurser med hjälp av Resource Manager-mallar](devtest-lab-create-environment-from-arm.md). Sedan kan du lagra mallarna i din privata lagringsplats.

När du skapar en virtuell dator kan kontrollera du att artefakter och mallar har lagts till Git-lagringsplatsen. De är omedelbart tillgängliga i listan över artefakter och mallar. Namnet på din privata lagringsplats visas i kolumnen som anger källan. 
