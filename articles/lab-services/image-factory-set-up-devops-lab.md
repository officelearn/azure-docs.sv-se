---
title: Köra en avbildningsfabrik från Azure DevOps i Azure DevTest Labs
description: Den här artikeln beskriver alla förberedelser som behövs för att köra avbildningsfabriken från Azure DevOps (tidigare Visual Studio Team Services).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758690"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Köra en avbildningsfabrik från Azure DevOps
Den här artikeln beskriver alla förberedelser som behövs för att köra avbildningsfabriken från Azure DevOps (tidigare Visual Studio Team Services).

> [!NOTE]
> Alla orkestreringsmotorer kommer att fungera! Azure DevOps är inte obligatoriskt. Avbildningsfabriken körs med Azure PowerShell-skript, så det kan köras manuellt med hjälp av Windows Schemaläggare, andra CI/CD-system och så vidare.

## <a name="create-a-lab-for-the-image-factory"></a>Skapa ett labb för bildfabriken
Det första steget i att konfigurera avbildningsfabriken är att skapa ett labb i Azure DevTest Labs. Detta labb är avbildningen fabriken labb där vi skapar virtuella maskiner och spara anpassade avbildningar. Detta labb betraktas som en del av den övergripande avbildningen fabriken processen. När du har skapat ett labb måste du spara namnet eftersom du behöver det senare.

## <a name="scripts-and-templates"></a>Skript och mallar
Nästa steg i att anta bildfabriken för ditt team är att förstå vad som är tillgängligt. Skript och mallar för avbildningsfabriken är tillgängliga för allmänheten i [DevTest Labs GitHub Repo](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Här är en översikt över bitarna:

- Bildfabrik. Det är rotmappen.
    - Konfiguration. Ingångarna till bildfabriken
        - GoldenImages. Den här mappen innehåller JSON-filer som representerar definitionerna av anpassade bilder.
        - Labs.json. Fil där team registrerar sig för att ta emot specifika anpassade bilder.
- Skript. Motorn för bildfabriken.

Artiklarna i det här avsnittet innehåller mer information om dessa skript och mallar.

## <a name="create-an-azure-devops-team-project"></a>Skapa ett Azure DevOps-teamprojekt
Med Azure DevOps kan du lagra källkoden, köra Azure PowerShell på ett ställe. Du kan schemalägga återkommande körningar för att hålla bilderna uppdaterade. Det finns bra faciliteter för att logga resultaten för att diagnostisera eventuella problem.  Att använda Azure DevOps är dock inte ett krav, du kan använda vilken sele/motor som helst som kan ansluta till Azure och kan köra Azure PowerShell.

Om du har ett befintligt DevOps-konto eller projekt som du vill använda i stället hoppar du över det här steget.

För att komma igång, skapa ett kostnadsfritt konto i Azure DevOps. Besök https://www.visualstudio.com/ och välj **Kom igång gratis direkt** under Azure **DevOps** (tidigare VSTS). Du måste välja ett unikt kontonamn och se till att välja att hantera kod med Git. När detta har skapats sparar du webbadressen i gruppprojektet. Här är ett `https://<accountname>.visualstudio.com/MyFirstProject`exempel URL: .

## <a name="check-in-the-image-factory-to-git"></a>Checka in bildfabriken till Git
Alla PowerShell, mallar och konfiguration för avbildningsfabriken finns i den [offentliga DevTest Labs GitHub repo](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Det snabbaste sättet att få in koden i det nya teamprojektet är att importera en databas. Detta drar i hela DevTest Labs-arkivet (så att du får extra dokument och exempel).

1. Besök Azure DevOps-projektet som du skapade i föregående steg (URL ser ut som **\//\<https: accountname>.visualstudio.com/MyFirstProject**).
2. Välj **Importera en databas**.
3. Ange **klon-URL:en** för DevTest `https://github.com/Azure/azure-devtestlab`Labs Repo: .
4. Välj **Importera**.

    ![Importera Git-repo](./media/set-up-devops-lab/import-git-repo.png)

Om du bestämmer dig för att bara checka in exakt vad som behövs (bilden fabriksfiler), följ stegen [här](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) för att klona Git repo och tryck bara de filer som finns i **skript / ImageFactory** katalogen.

## <a name="create-a-build-and-connect-to-azure"></a>Skapa en version och ansluta till Azure
Nu har du källfilerna lagrade i en Git-repo i Azure DevOps. Nu måste du konfigurera en pipeline för att köra Azure PowerShell. Det finns många alternativ för att göra dessa steg. I den här artikeln använder du byggdefinition för enkelhet, men det fungerar med DevOps Build, DevOps Release (en eller flera miljöer), andra körningsmotorer som Windows Schemaläggare eller någon annan sele som kan köra Azure PowerShell.

> [!NOTE]
> En viktig punkt att komma ihåg att vissa av PowerShell-filer tar lång tid att köra när det finns många (10 +) anpassade bilder att skapa. Gratis värd DevOps Build / Release agenter har en timeout på 30 min, så du kan inte använda den fria värd agent när du börjar bygga många bilder. Den här timeout-utmaningen gäller oavsett vilken sele du väljer att använda, är det bra att kontrollera på framsidan att du kan utöka de vanliga tidsgränsen för tidskrävande Azure PowerShell-skript. När det gäller Azure DevOps kan du antingen använda betalda värdagenter eller använda din egen byggagent.

1. Börja med att konfigurera **Bygg upp** på startsidan för ditt DevOps-projekt:

    ![Knappen Skapa installation](./media/set-up-devops-lab/setup-build-button.png)
2. Ange ett **namn** för bygget (till exempel: Skapa och leverera avbildningar till DevTest Labs).
3. Välj en **tom** byggdefinition och välj **Använd** för att skapa din version.
4. I det här skedet kan du välja **Värd värd** för byggagenten.
5. **Spara** byggdefinitionen.

    ![Build-definition](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Konfigurera byggvariablerna
Om du vill förenkla kommandoradsparametrarna kapslar du in de nyckelvärden som driver avbildningsfabriken till en uppsättning byggvariabler. Välj fliken **Variabler** så visas en lista över flera standardvariabler. Här är listan över variabler som ska anges i Azure DevOps:


| Variabelnamn | Värde | Anteckningar |
| ------------- | ----- | ----- |
| KonfigurationPlats | /Scripts/ImageFactory/Configuration | Det här är den fullständiga sökvägen i databasen till **mappen Konfiguration.** Om du har importerat hela reporäntan ovan är värdet till vänster korrekt. Uppdatera annars för att peka på konfigurationsplatsen. |
| DevTestLabName (Olikartade) | MyImageFactory | Namnet på labbet i Azure DevTest Labs som används som fabrik för att producera avbildningar. Om du inte har en, skapa en. Kontrollera att labbet finns i samma prenumeration som tjänstslutpunkten har åtkomst till. |
| BildRetention | 1 | Antalet bilder som du vill spara av varje typ. Ange standardvärdet till 1. |
| Maskinpassord | ******* | Det inbyggda administratörskontots lösenord för de virtuella datorerna. Detta är ett tillfälligt konto, så se till att det är säkert. Välj den lilla låsikonen till höger för att säkerställa att det är en säker sträng. |
| MachineUserName | ImageFactoryUser | Användarnamnet för det inbyggda administratörskontot för de virtuella datorerna. Det här är ett övergående konto. |
| StandardTimeoutMinutes | 30 | Tidsgränsen bör vi vänta på vanliga Azure-åtgärder. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | ID:t för prenumerationen där labbet finns och som tjänstslutpunkten har åtkomst till. |
| VMSize (VMSize) | Standard_A3 | Storleken på den virtuella datorn som ska användas för steget **Skapa.** De virtuella datorer som skapas är övergående. Storleken måste vara den som är [aktiverad för labbet](devtest-lab-set-lab-policy.md). Bekräfta att det finns tillräckligt med kvot för [prenumerationskärtor](../azure-resource-manager/management/azure-subscription-service-limits.md).

![Skapa variabler](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Anslut till Azure
Nästa steg är att ställa in tjänstens huvudnamn. Det här är en identitet i Azure Active Directory som gör att DevOps-byggagenten kan fungera i Azure för användarens räkning. Om du vill konfigurera det börjar du med att lägga till dig första Azure PowerShell Build Step.

1. Välj **Lägg till aktivitet**.
2. Sök efter **Azure PowerShell**.
3. När du har hittat den väljer du **Lägg till** för att lägga till aktiviteten i versionen. När du gör det ser du uppgiften visas på vänster sida som tillagd.

![Konfigurera PowerShell-steg](./media/set-up-devops-lab/set-up-powershell-step.png)

Det snabbaste sättet att konfigurera ett tjänsthuvudnamn är att låta Azure DevOps göra det åt oss.

1. Markera den **uppgift** som du just har lagt till.
2. För **Azure-anslutningstyp**väljer du **Azure Resource Manager**.
3. Välj länken **Hantera** för att ställa in tjänstens huvudnamn.

Se det här [blogginlägget](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) för mer information. När du väljer länken **Hantera** hamnar du på rätt plats i DevOps (andra skärmbilden i blogginlägget) för att konfigurera anslutningen till Azure. Se till att välja **Azure Resource Manager Service Slutpunkt** när du ställer in detta.

## <a name="complete-the-build-task"></a>Slutför bygguppgiften
Om du väljer bygguppgiften visas all information i den högra rutan som ska fyllas i.

1. Först namnge bygguppgiften: **Skapa virtuella datorer**.
2. Välj **det tjänsthuvudnamn** som du har skapat genom att välja **Azure Resource Manager**
3. Välj **tjänstens slutpunkt**.
4. För **skriptsökväg**väljer du **... (ellips)** till höger.
5. Navigera till **skriptet MakeGoldenImageVMs.ps1.**
6. Skriptparametrar ska se ut så här:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Slutför byggdefinitionen](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Köa bygget
Låt oss kontrollera att du har allt korrekt konfigurerat genom att köa en ny version. Medan bygget körs växlar du till [Azure-portalen](https://portal.azure.com) och väljer på **alla virtuella datorer** i avbildningsfabrikslabbet för att bekräfta att allt fungerar som det ska. Du bör se tre virtuella datorer skapas i labbet.

![Virtuella datorer i labbet](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Nästa steg
Det första steget i att konfigurera avbildningsfabriken baserat på Azure DevTest Labs är slutfört. I nästa artikel i serien får du dessa virtuella datorer generaliserade och sparade i anpassade bilder. Sedan får du dem distribuerade till alla dina andra labb. Se nästa artikel i serien: [Spara anpassade bilder och distribuera till flera labb](image-factory-save-distribute-custom-images.md).
