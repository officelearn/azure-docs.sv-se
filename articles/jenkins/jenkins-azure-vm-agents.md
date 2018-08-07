---
title: Skala Jenkins-distributioner med Azure VM-agenter.
description: Lägg till ytterligare kapacitet i dina Jenkins-pipelines med hjälp av virtuella Azure-datorer med Jenkins-plugin-programmet för Azure VM-agenter.
ms.topic: tutorial
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/31/2018
ms.openlocfilehash: 7f3facbc1bca51061d49ca99778c60d58c525144
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391282"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Skala dina Jenkins-distributioner för att uppfylla behovet med Azure VM-agenter

Den här självstudien visar hur du använder Jenkins-plugin-programmet för [agenter för virtuell Azure-dator](https://plugins.jenkins.io/azure-vm-agents) för att lägga till kapacitet på begäran med virtuella Linux-datorer som körs i Azure.

I den här kursen ska du:

> [!div class="checklist"]
> * Installera plugin-programmet för Azure VM-agenter
> * Konfigurera plugin-programmet för att skapa resurser i din Azure-prenumeration
> * Ange de beräkningsresurser som är tillgängliga för varje agent
> * Ange det operativsystem och de verktyg som installeras på varje agent
> * Skapa ett nytt Jenkins-freestylejobb
> * Kör jobbet på en Azure VM-agent

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration
* En Jenkins-huvudserver. Om du inte har någon använder du [snabbstarten](install-jenkins-solution-template.md) för att konfigurera en i Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Installera Plugin-program för Azure VM-agenter

> [!TIP]
> Om du distribuerade Jenkins på Azure med hjälp av [lösningsmallen](install-jenkins-solution-template.md), har plugin-programmet för Azure VM-agenter redan installerats.

1. Från Jenkins-instrumentpanelen väljer du **Manage Jenkins** (Hantera Jenkins) och sedan **Manage Plugins** (Hantera plugin-program).
2. Välj fliken **Tillgängligt** och söka sedan efter **Azure VM Agents** (Azure VM-agenter). Markera kryssrutan intill posten för plugin-programmet och välj **Install without restart** (Installera utan omstart) längst ned på instrumentpanelen.

## <a name="configure-the-azure-vm-agents-plugin"></a>Konfigurera plugin-programmet för Azure VM-agenter

1. På Jenkins-instrumentpanelen väljer du **Manage Jenkins** (Hantera Jenkins) och sedan **Configure System** (Konfigurera systemet).
2. Rulla längst ned på sidan och leta rätt på avsnittet **Moln** med listrutan **Add new cloud** (Lägg till nytt moln) och välj **Microsoft Azure VM Agents** (Microsoft Azure VM-agenter).
3. Välj ett befintligt tjänsthuvudnamn från listrutan **Lägg till** i avsnittet **Azure Credentials** (Azure-autentiseringsuppgifter). Om inget anges utför du följande steg för att [skapa ett tjänsthuvudnamn](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) för ditt Azure-konto och lägga till det i din Jenkins-konfiguration:   

    a. Välj **Lägg till** intill **Azure Credentials** (Azure-autentiseringsuppgifter) och välj **Jenkins**.   
    b. I dialogrutan **Add Credentials** (Lägg till autentiseringsuppgifter) väljer du **Microsoft Azure Service Principal** (Tjänstens huvudnamn för Microsoft Azure) från listrutan **Kind** (Variant).   
    c. Skapa ett Active Directory-tjänsthuvudnamn från Azure CLI eller [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Ange autentiseringsuppgifterna från tjänstens huvudnamn i dialogrutan **Add credentials** (Lägg till autentiseringsuppgifter). Om du inte känner till ditt Azure-prenumerations-ID kan du fråga efter det från CLI:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    Det slutförda tjänsthuvudnamnet ska använda fältet `id` för **prenumerations-ID**, värdet `appId` för **klient-ID**, `password` för **klienthemlighet** och `tenant` för **klientorganisations-ID**. Välj **Lägg till** för att lägga till tjänstens huvudnamn och konfigurera sedan plugin-programmet för att använda den nyligen skapade autentiseringsuppgiften.

    ![Konfigurera tjänstens huvudnamn för Azure](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

4. I avsnittet **Resource Group Name** (Resursgruppsnamn) lämnar du **Skapa nytt** markerat och anger `myJenkinsAgentGroup`.
5. Välj **Verify configuration** (Verifiera konfiguration) för att ansluta till Azure för att testa profilinställningarna.
6. Välj **Applicera** för att uppdatera konfigurationen av plugin-programmet.

## <a name="configure-agent-resources"></a>Konfigurera agentresurser

Konfigurera en mall som ska användas för att definiera en Azure VM-agent. Den här mallen definierar de beräkningsresurser som varje agent har när de skapas.

1. Välj **Lägg till** intill **Add Azure Virtual Machine Template** (Lägg till mall för virtuell Azure-dator).
2. Ange `defaulttemplate` som **Namn**
3. Ange `ubuntu` som **Etikett**
4. Välj önskad [Azure-region](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) från kombinationsrutan.
5. Välj en [VM-storlek](/azure/virtual-machines/linux/sizes) i listrutan i **Storlek för virtuell dator**. En allmän `Standard_DS1_v2`-storlek går bra för den här självstudien.   
6. Lämna **kvarhållningstiden** på `60`. Den här inställningen anger hur många minuter Jenkins kan vänta innan det frigör inaktiva agent. Ange 0 om du inte vill att inaktiva agenter ska tas bort automatiskt.

   ![Allmän konfiguration av virtuella datorer](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Konfigurera operativsystem och verktyg för agent

I avsnittet **Bildkonfiguration** i konfigurationen av plugin-programmet väljer du **Ubuntu 16.04 LTS**. Markera kryssrutorna intill **Install Git (Latest)** (Installera Git (senaste)), **Install Maven (V3.5.0)** (Installera Maven (V3.5.0)) och **Install Docker** (Installera Docker) för att installera verktygen på nyligen skapade agenter.

![Konfigurera operativsystem och verktyg för virtuella datorer](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Välj **Lägg till** intill **Admin Credentials** (Autentiseringsuppgifter för administratör) och välj sedan **Jenkins**. Ange ett användarnamn och lösenord som används för att logga in på agenter. Se till att de överensstämmer med [principen för användarnamn och lösenord](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) för administrativa konton på virtuella Azure-datorer.

Välj **Verify Template** (Verifiera mall) för att kontrollera konfigurationen och välj sedan **Spara** för att spara dina ändringar och återgå till Jenkins-instrumentpanelen.

## <a name="create-a-job-in-jenkins"></a>Skapa ett jobb i Jenkins

1. I instrumentpanelen för Jenkins klickar du på **Nytt objekt**. 
2. Ange `demoproject1` som namn och välj **Freestyle-projekt**. Välj sedan **OK**.
3. På fliken **Allmänt** väljer du **Begränsa var projekt kan köras** och typen `ubuntu` i **etikettuttrycket**. Du ser ett meddelande som bekräftar att etiketten hanteras av den molnkonfiguration som skapades i föregående steg. 
   ![Konfigurera jobb](./media/jenkins-azure-vm-agents/job-config.png)
4. På fliken **Source Code Management** (Källkodshantering) väljer du **Git** och lägger till följande URL i fältet **Repository URL** (URL för lagringsplats): `https://github.com/spring-projects/spring-petclinic.git`
5. På fliken **Build** (Bygg) väljer du **Add build step** (Lägg till byggsteg) och sedan **Invoke top-level Maven targets** (Anropa Maven-mål på toppnivå). Ange `package` i fältet **Mål**.
6. Välj **Spara** för att spara jobbdefinitionen.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Bygg det nya jobbet på en Azure VM-agent

1. Gå tillbaka till Jenkins-instrumentpanelen.
2. Välj det jobb som du skapade i föregående steg och klicka sedan på **Build now** (Skapa nu). En ny version ställs i kö men startar inte förrän en VM-agent har skapats i din Azure-prenumeration.
3. När versionen har slutförts, går du till **Konsolutdata**. Du ser att versionen skapades via en fjärranslutning på en Azure-agent.

![Konsolutdata](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [CI/CD till Azure App Service](java-deploy-webapp-tutorial.md)