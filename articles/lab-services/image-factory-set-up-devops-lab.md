---
title: Kör en datafabrik avbildning från Azure DevOps i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du skapar en anpassad avbildning fabrik i Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 62cb8222b592660f2e7ab32d438fd4073246ee50
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58440038"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Kör en datafabrik avbildning från Azure DevOps
Den här artikeln beskriver alla produkter som behövs för att köra fabriken avbildning från Azure DevOps (tidigare Visual Studio Team Services).

> [!NOTE]
> Alla dirigeringsmotor fungerar! Azure DevOps är inte obligatoriskt. Bild-factory körs med Azure PowerShell-skript, så det kan vara köra manuellt med hjälp av Schemaläggaren i Windows, andra CI/CD-system, och så vidare.

## <a name="create-a-lab-for-the-image-factory"></a>Skapa ett labb för avbildning factory
Det första steget att konfigurera fabriken avbildning är att skapa ett labb i Azure DevTest Labs. Den här övningen är bild factory labbet där vi skapa de virtuella datorerna och spara anpassade avbildningar. Den här övningen anses som en del av den övergripande bild factory processen. När du skapar ett labb kan du se till att spara namnet eftersom du behöver senare.

## <a name="scripts-and-templates"></a>Skript och mallar
Nästa steg i att börja använda avbildningen fabriken för ditt team är att förstå vad som är tillgängligt. Bild factory skript och mallar är tillgängliga i offentligt den [DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory). Här är en översikt över delarna:

- Bild Factory. Det är rotmappen. 
    - Konfiguration. Indata till avbildningen fabriken
        - GoldenImages. Den här mappen innehåller JSON-filer som representerar definitionerna av anpassade avbildningar.
        - Labs.json. Filen där team prenumerera specifik anpassade avbildningar.
- Skript. Motorn för avbildning factory.

Artiklarna i det här avsnittet innehåller mer information om dessa skript och mallar. 

## <a name="create-an-azure-devops-team-project"></a>Skapa ett projekt för Azure DevOps-team
Azure DevOps kan du lagra källkoden genom att köra Azure PowerShell på samma ställe. Du kan schemalägga återkommande körs ska hålla avbildningar uppdaterade. Det är bra funktioner för loggning av resultaten att diagnostisera problem.  Med Azure DevOps är inte ett krav men kan du använda valfri stomme/motor som kan ansluta till Azure och kan köra Azure PowerShell.

Om du har ett befintligt DevOps konto eller projekt som du vill använda i stället kan du hoppa över det här steget.

Kom igång genom att skapa ett kostnadsfritt konto i Azure DevOps. Besök https://www.visualstudio.com/ och välj **Kom igång kostnadsfritt** direkt under **Azure DevOps** (tidigare VSTS). Du måste välja ett unikt kontonamn och se till att välja att hantera kod med Git. Spara URL: en till ditt teamprojekt när den har skapats. Här är en exempel-URL: https://<accountname>.visualstudio.com/MyFirstProject.

## <a name="check-in-the-image-factory-to-git"></a>Kontrollera i avbildningen factory Git
Alla PowerShell, mallar och konfiguration för avbildning factory finns i den [offentliga DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory). Det snabbaste sättet att få koden i det nya teamprojektet är att importera en databas. Detta hämtar hela DevTest Labs-databasen (så här får du extra docs och exempel). 

1. Besök Azure DevOps-projektet som du skapade i föregående steg (URL-Adressen liknar **https://<accountname>.visualstudio.com/MyFirstProject**).
2. Välj **importera en databas**.
3. Ange den **klona URL** för DevTest Labs lagringsplats: `https://github.com/Azure/azure-devtestlab`.
4. Välj **Import**.

    ![Importera Git-lagringsplats](./media/set-up-devops-lab/import-git-repo.png)

Om du väljer att bara checka in exakt vad som behövs (bildfiler factory), följer du stegen [här](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) klona Git-lagringsplats och push-överför endast de filer som finns i den **skript/ImageFactory** directory.

## <a name="create-a-build-and-connect-to-azure"></a>Skapa en version och ansluta till Azure
Du har nu källfilerna som lagras i en Git-lagringsplats i Azure DevOps. Nu kan behöva du konfigurera en pipeline för att köra Azure PowerShell. Det finns många olika alternativ för att utföra de här stegen. I den här artikeln får du använda byggesdefinition för enkelhetens skull, men den fungerar med Build-DevOps, DevOps-versionen (en eller flera miljöer), andra körning motorer som Schemaläggaren i Windows eller andra stomme som kan köra Azure PowerShell.

> [!NOTE]
> En viktig sak att tänka på att vissa av de PowerShell-filerna ta lång tid att köra när det finns mycket (10 +) anpassade bilder och skapa. Kostnadsfria värdbaserade agenter för DevOps version och utgåva har en tidsgräns på 30 minuter så att du inte kan använda ledigt värdbaserade agenten när du börjar bygga många bilder. Denna timeout-utmaning gäller att det skydd som du vill använda, det är bra att direkt kontrollerar du att du kan utöka den typiska tidsgränser för Azure PowerShell-skript som körs under lång tid. När det gäller Azure DevOps kan du använda betalda värdbaserade agenter eller använda din egen skapandeagent.

1. För att starta, markerar **konfigurera Build** på startsidan i ditt DevOps-projekt:

    ![Build-knappen Inställningar](./media/set-up-devops-lab/setup-build-button.png)
2. Ange en **namn** för versionen (till exempel: Bygg och leverera avbildningar till labb). 
3. Välj en **tom** build-definition och välj **tillämpa** att skapa din version. 
4. I det här skedet kan du välja **värdbaserad** för build-agenten. 
5. **Spara** build-definition.

    ![Build-definition](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Konfigurera build-variabler
Kapsla in nyckelvärden som driver fabriken avbildning till en uppsättning Skapa variabler för att förenkla kommandoradsparametrar. Välj den **variabler** fliken och du ser en lista över flera standard-variabler. Här är listan över variabler för att ange i Azure devops:


| Variabelnamn | Värde | Anteckningar |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Det här är den fullständiga sökvägen i lagringsplatsen till den **Configuration** mapp. Om du har importerat hela lagringsplatsen ovan är värdet till vänster korrekt. Uppdatera annars punkt till plats för konfiguration. |
| DevTestLabName | MyImageFactory | Namnet på labb i Azure DevTest Labs användes eftersom fabriken för att skapa avbildningar. Om du inte har något, skapa en. Se till att laboratoriet är i samma prenumeration som tjänsteslutpunkt har åtkomst till. |
| ImageRetention | 1 | Antalet avbildningar som du vill spara av varje typ. Ange standardvärdet 1. |
| MachinePassword | ******* | Den inbyggda administratörslösenordet för de virtuella datorerna. Detta är ett tillfälligt konto, så se till att de är skyddade. Välj den lilla låsikonen till höger så att den är en säker sträng. |
| MachineUserName | ImageFactoryUser | Inbyggt konto administratörsanvändarnamnet för de virtuella datorerna. Detta är ett tillfälligt konto. |
| StandardTimeoutMinutes | 30 | Den tidsgräns som vi ska vänta tills normal drift för Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | ID för den prenumeration där labbet finns och att tjänsteslutpunkt har åtkomst till. |
| VMSize | Standard_A3 | Storleken på den virtuella datorn ska användas för den **skapa** steg. De virtuella datorerna som skapas är tillfälligt. Storleken måste vara som är [aktiverad för labbet](devtest-lab-set-lab-policy.md). Bekräfta att det finns tillräckligt [kärnor prenumerationskvot](../azure-subscription-service-limits.md). 

![Skapa variabler](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Anslut till Azure
Nästa steg är att konfigurera tjänsten huvudnamn. Det här är en identitet i Azure Active Directory som gör DevOps-skapandeagent ska fungera i Azure för användarens räkning. Om du vill konfigurera, börjar du med att lägga till dig Azure PowerShell skapar först.

1. Välj **Lägg till aktivitet**.
2. Sök efter **Azure PowerShell**. 
3. När du har hittat Välj **Lägg till** att lägga till uppgiften i bygget. När du gör detta visas uppgiften visas till vänster som har lagts till.

![Konfigurera PowerShell steg](./media/set-up-devops-lab/set-up-powershell-step.png)

Det snabbaste sättet att konfigurera tjänsten huvudnamn är att låta Azure DevOps gör det åt oss. 

1. Välj den **uppgift** du just har lagt till.
2. För **Azure anslutningstypen**, Välj **Azure Resource Manager**. 
3. Välj den **hantera** länk till konfigurera tjänstens huvudnamn. 

Mer information finns i den här [blogginlägget](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). När du väljer den **hantera** länken, kommer du att hamna på rätt plats i DevOps (andra skärmbilden i blogginlägget) att konfigurera anslutningen till Azure. Se till att välja **tjänstslutpunkt för Azure Resource Manager** när du konfigurerar detta.

## <a name="complete-the-build-task"></a>Slutför denna build task
Om du väljer denna build task visas all information i den högra rutan bör fyllas i. 

1. Förnamn denna build task: **Skapa virtuella datorer**. 
2. Välj den **tjänstens huvudnamn** du skapade genom att välja **Azure Resource Manager**
3. Välj den **tjänstslutpunkt**. 
4. För **skriptets sökväg**väljer **... (tre punkter)**  till höger.
5. Gå till **MakeGoldenImageVMs.ps1** skript. 
6. Skriptparametrar bör se ut så här: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Slutför build-definition](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Kön versionen
Nu ska vi Kontrollera att du har allt korrekt konfigurerat genom att Köa en ny version. När bygget körs, växla till den [Azure-portalen](https://portal.azure.com) och välj på **alla virtuella datorer** i labbet bild factory för att bekräfta att allt fungerar korrekt. Du bör se tre virtuella datorer skapas i labbet.

![Virtuella datorer i labbet](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Nästa steg 
Det första steget i att konfigurera avbildningen fabriken baserat på Azure DevTest Labs är klar. I nästa artikel i serien får du de virtuella datorer generaliserad och sparas på anpassade avbildningar. Sedan kan låta du dem distribueras till alla andra övningar. Se nästa artikel i serien: [Spara anpassade avbildningar och distribuera till flera labs](image-factory-save-distribute-custom-images.md).
