---
title: Skala Jenkins distribution med Virtuella Azure-agenter.
description: "Lägga till ytterligare kapacitet i din Jenkins pipelines som använder virtuella Azure-datorer med Jenkins Azure VM-agenten plugin-programmet."
services: multiple
documentationcenter: 
author: rloutlaw
manager: justhe
ms.service: multiple
ms.workload: multiple
ms.topic: article
ms.date: 8/25/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: dbb30809ab68079666ecfa81a896c1d5101fb6fb
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Skala din Jenkins distributioner för att uppfylla begäran med Virtuella Azure-agenter

Den här kursen visar hur du använder Jenkins [plugin-program för Azure VM agenter](https://plugins.jenkins.io/azure-vm-agents) att lägga till kapacitet på begäran med Linux virtuella datorer som körs i Azure.

I den här kursen ska du:

> [!div class="checklist"]
> * Installera Azure VM agenter plugin-programmet
> * Konfigurera plugin-programmet för att skapa resurser i din Azure-prenumeration
> * Ange beräkningsresurserna som är tillgängliga för varje agent
> * Ange operativsystem och verktyg som installerats på varje agent
> * Skapa ett nytt Jenkins freestyle jobb
> * Kör jobbet på en virtuell dator i Azure-agent

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration
* Jenkins huvudserver. Om du inte har någon kan visa den [quickstart](install-jenkins-solution-template.md) att ställa in en i Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Installera Plugin-program för Azure VM-agenter

> [!TIP]
> Om du har distribuerat Jenkins på Azure med hjälp av [lösningsmall](install-jenkins-solution-template.md), plugin-program för Azure VM-agenten är installerad.

1. Infopanelen Jenkins Välj **hantera Jenkins**och välj **hantera plugin-program**.
2. Välj den **tillgänglig** fliken, och sök sedan efter **Azure VM agenter**. Markera kryssrutan bredvid posten för plugin-programmet och välj **installera utan att starta om** längst ned i instrumentpanelen.

## <a name="configure-the-azure-vm-agents-plugin"></a>Konfigurera Azure VM agenter plugin-programmet

1. Infopanelen Jenkins Välj **hantera Jenkins**, sedan **konfigurera systemet**.
2. Bläddra till längst ned på sidan och hitta det **moln** avsnittet med den **lägga till nya molntjänster** listrutan och välj **Microsoft Azure VM agenter**.
3. Välj ett befintligt huvudnamn för tjänsten från **Lägg till** listrutan i den **Azure-autentiseringsuppgifterna** avsnitt. Om inget anges, utför följande steg för att [skapa ett huvudnamn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) för din Azure-kontot och lägga till den Jenkins konfigurationen:   

    a. Välj **Lägg till** bredvid **Azure-autentiseringsuppgifterna** och välj **Jenkins**.   
    b. I den **lägga till autentiseringsuppgifter** markerar **Microsoft Azure Service Principal** från den **typ** listrutan.   
    c. Skapa en Active Directory-tjänstens huvudnamn från Azure CLI eller [moln Shell](/azure/cloud-shell/overview).
    
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
    d. Ange autentiseringsuppgifterna från tjänsten huvudnamn i den **lägga till autentiseringsuppgifter** dialogrutan. Om du inte vet Azure prenumerations-ID, kan den fråga från CLI:
     
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

    Slutförda tjänstens huvudnamn ska använda den `id` för **prenumerations-ID**, `appId` värde för **klient-ID**, `password` för **Klienthemlighet**, och en Webbadressen för **OAuth 2.0-Token för slutpunkt** av `https://login.windows.net/<tenant_value>`. Välj **Lägg till** lägga till tjänstens huvudnamn och sedan konfigurera plugin-programmet för att använda den nyligen skapade referensen.

    ![Konfigurera Azure tjänstens huvudnamn](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

4. I den **resursgruppens namn** och lämna **Skapa nytt** markerad och ange `myJenkinsAgentGroup`.
5. Välj **verifiera konfigurationen** att ansluta till Azure för att testa profilinställningarna.
6. Välj **tillämpa** att uppdatera konfigurationen av plugin-programmet.

## <a name="configure-agent-resources"></a>Konfigurera agenten resurser

Konfigurera en mall som används för att definiera en Azure VM-agent. Den här mallen definierar de beräkningsresurser som varje agent har när skapas.

1. Välj **Lägg till** bredvid **Lägg till mall för virtuell dator i Azure**.
2. Ange `defaulttemplate` för den **namn**
3. Ange `ubuntu` för den **etikett**
4. Välj den önskade [Azure-region](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) från kombinationsrutan.
5. Välj en [VM-storlek](/azure/virtual-machines/linux/sizes) listrutan under **storlek för virtuell dator**. En generell `Standard_DS1_v2` storleken är bra för den här självstudiekursen.   
6. Lämna den **kvarhållningstiden** på `60`. Den här inställningen definierar hur många minuter Jenkins kan vänta innan den har frigjorts inaktiv agenter. Ange 0 om du inte vill att inaktiva agenter som ska tas bort automatiskt.

   ![Allmänna VM-konfiguration](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Konfigurera agenten för operativsystemet och verktyg

I den **avbildningen Configuration** avsnittet i plugin-konfigurationen väljer **Ubuntu 16.04 LTS**. Markera kryssrutorna bredvid **installera Git (senaste)**, **installerar Maven (V3.5.0)**, och **installera Docker** att installera verktygen på nyskapade agenter.

![Konfigurera VM OS och verktyg](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Välj **Lägg till** bredvid **administratörsautentiseringsuppgifter**och välj **Jenkins**. Ange ett användarnamn och lösenord som används för att logga in på agenter, kontrollera att de uppfyller de [användarnamn och lösenord princip](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) för administratörskonton på Azure Virtual Machines.

Välj **Kontrollera mallen** att kontrollera konfigurationen och välj sedan **spara** att spara ändringarna och återgå till instrumentpanelen Jenkins.

## <a name="create-a-job-in-jenkins"></a>Skapa ett jobb i Jenkins

1. I instrumentpanelen för Jenkins klickar du på **Nytt objekt**. 
2. Ange `demoproject1` för namn och välj **Freestyle projektet**och välj **OK**.
3. I den **allmänna** , Välj **begränsa där du kan köra project** och skriv `ubuntu` i **etikettuttrycket**. Du ser ett meddelande som bekräftar att etiketten hanteras av konfigurationen för molnet skapade i föregående steg. 
   ![Ange jobb](./media/jenkins-azure-vm-agents/job-config.png)
4. I den **källa kod Management** väljer **Git** och Lägg till följande URL till den **databasen URL** fält:`https://github.com/spring-projects/spring-petclinic.git`
5. I den **skapa** väljer **Lägg till build steg**, sedan **anropa översta Maven mål**. Ange `package` i den **mål** fältet.
6. Välj **spara** spara jobbdefinitionen.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Skapa nytt jobb på en virtuell dator i Azure-agent

1. Gå tillbaka till Jenkins-instrumentpanelen.
2. Välj det jobb som du skapade i föregående steg och klicka sedan på **skapa nu**. En ny version är i kö men startar inte förrän en agent VM skapas i din Azure-prenumeration.
3. När versionen har slutförts, går du till **Konsolutdata**. Du ser att bygga utfördes via fjärranslutning på en Azure-agenten.

![Konsolutdata](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [CI/CD: N för Azure App Service](java-deploy-webapp-tutorial.md)
