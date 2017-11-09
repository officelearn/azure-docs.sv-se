---
title: "CI/CD: N från Jenkins för virtuella Azure-datorer med Team Services | Microsoft Docs"
description: "Ställ in kontinuerlig integration (KO) och kontinuerlig distribution (CD) för en Node.js-app genom att använda Jenkins till Azure virtuella datorer från versionshantering i Visual Studio Team Services eller Microsoft Team Foundation Server"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: c96aafeb05293ccdc4c30c2b828cead1dfdb157c
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="deploy-your-app-to-linux-vms-by-using-jenkins-and-team-services"></a>Distribuera din app till Linux virtuella datorer med hjälp av Jenkins och Team Services

Kontinuerlig integration (KO) och kontinuerlig distribution (CD) utgör en pipeline som du kan bygga, släppa och distribuera din kod. Visual Studio Team Services innehåller en fullständig, komplett funktionalitet uppsättning av verktyg för automatisering av CI/CD för distribution till Azure. Jenkins är en populära från tredje part CI/CD-server-baserade verktyg som ger också CI/CD-automatisering. Du kan använda Team Services och Jenkins tillsammans för att anpassa hur du levererar ditt molnapp eller tjänst.

I den här kursen använder du Jenkins för att skapa en Node.js-webbapp. Du sedan använda Team Services eller Team Foundation Server för att distribuera den till en [distributionsgruppen](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) som innehåller virtuella Linux-datorer (VM).

Du kommer att:

> [!div class="checklist"]
> * Hämta sample-appen.
> * Konfigurera Jenkins plugin-program.
> * Konfigurera ett Jenkins Freestyle projekt för Node.js.
> * Konfigurera Jenkins för integrering med Team Services.
> * Skapa en Jenkins tjänstslutpunkt.
> * Skapa en distributionsgrupp för de virtuella Azure-datorerna.
> * Skapa en definition för Team Services-versionen.
> * Köra manuell och CI-utlösta distributioner.

## <a name="before-you-begin"></a>Innan du börjar

* Du behöver åtkomst till en Jenkins-server. Om du inte har skapat en Jenkins server, se [skapa ett Jenkins original på en virtuell dator i Azure](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template). 

* Logga in på ditt Team Services-konto (**https://{youraccount}.visualstudio.com**). 
  Du kan få en [kostnadsfritt konto Team Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Mer information finns i [Anslut till Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  Du behöver en Linux-dator för ett mål för distribution.  Mer information finns i [skapa och hantera virtuella Linux-datorer med Azure CLI](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm).

*  Öppna inkommande port 80 för den virtuella datorn. Mer information finns i [skapa nätverkssäkerhetsgrupper med hjälp av Azure portal](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Hämta sample-appen

Du behöver en app som ska distribueras, lagras i en Git-lagringsplats.
Vi rekommenderar att du använder den här självstudiekursen [denna sample-appen som är tillgängliga från GitHub](https://github.com/azooinmyluggage/fabrikam-node). Den här självstudiekursen innehåller ett exempelskript som används för att installera Node.js och ett program. Om du vill arbeta med dina egna databasen bör du konfigurera ett liknande exempel.

Skapa en duplicering av den här appen och Anteckna platsen (URL) för användning i senare steg i den här kursen. Mer information finns i [duplicera en repo](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> Appen har skapats via [Yeoman](http://yeoman.io/learning/index.html). Snabb och bower grunt används. Och det finns vissa npm-paket som beroenden.
> Exemplet innehåller också ett skript som konfigurerar Nginx och distribuerar appen. Den körs på virtuella datorer. Mer specifikt skript:
> 1. Installerar nod och Nginx PM2.
> 2. Konfigurerar Nginx och PM2.
> 3. Startar appen nod.

## <a name="configure-jenkins-plug-ins"></a>Konfigurera Jenkins plugin-program

Först måste du konfigurera två Jenkins plugin-program: **NodeJS** och **VS Team Services kontinuerlig distribution**.

1. Öppna Jenkins-konto och välj **hantera Jenkins**.
2. På den **hantera Jenkins** väljer **hantera plugin-program**.
3. Filtrera listan för att hitta den **NodeJS** plugin-programmet och välj den **installera utan att starta om** alternativet.
    ![Att lägga till plugin-programmet för NodeJS Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrera listan efter den **VS Team Services kontinuerlig distribution** plugin-programmet och välj den **installera utan att starta om** alternativet.
5. Gå tillbaka till Jenkins instrumentpanelen och välj **hantera Jenkins**.
6. Välj **globala verktyget Configuration**. Hitta **NodeJS** och välj **NodeJS installationer**.
7. Välj den **installeras automatiskt** alternativet och ange sedan en **namn** värde.
8. Välj **Spara**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Konfigurera ett Jenkins Freestyle projekt för Node.js

1. Välj **nytt objekt**. Ange ett objektnamn.
2. Välj **Freestyle projekt**. Välj **OK**.
3. På den **källa kod Management** väljer **Git** och ange information om databasen och grenen som innehåller din Appkod.    
    ![Lägg till en repo i din version](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. På den **Skapa utlösare** väljer **avsökning SCM** och ange schemat `H/03 * * * *` avsöker Git-lagringsplats för ändringar i alla tre minuter. 
5. På den **kompileringsmiljö** väljer **ange nod &amp; npm bin / mappen SÖKVÄGEN** och välj den **NodeJS Installation** värde. Lämna **npmrc filen** inställd på **Använd systemstandard**.
6. På den **skapa** väljer **köra shell** och ange kommandot `npm install` så att alla beroenden är uppdaterade.


## <a name="configure-jenkins-for-team-services-integration"></a>Konfigurera Jenkins för integrering med Team Services

> [!NOTE]
> Se till att den personliga åtkomsttoken (PATRIK) som du använder för att göra följande innehåller den *versionen* (läsa, skriva, köra och hantera) behörighet i Team Services.
 
1.  Skapa en PATRIK i ditt Team Services-konto om du inte redan har ett. Jenkins kräver den här informationen för att få åtkomst till ditt Team Services-konto. Se till att lagra tokeninformation för kommande steg i det här avsnittet.
  
    Om du vill lära dig mer om att generera en token, läsa [hur skapar jag en personlig åtkomsttoken för VSTS och TFS?](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).
2. I den **efter build åtgärder** väljer **Lägg till efter build åtgärd**. Välj **Arkivera artefakter**.
3. För **filer att arkivera**, ange `**/*` att inkludera alla filer.
4. Om du vill skapa en annan åtgärd **Lägg till efter build åtgärd**.
5. Välj **utlösa version i TFS/Team Services**. Ange URI för ditt Team Services-konto som **https://{your-account-name}.visualstudio.com**.
6. Ange den **grupprojekt** namn.
7. Välj ett namn för versionen definitionen. (Du skapar den här versionen definitionen senare i Team Services.)
8. Välj autentiseringsuppgifter för att ansluta till ditt Team Services eller Team Foundation Server-miljö:
   - Lämna **användarnamn** tomma om du använder Team Services. 
   - Ange ett användarnamn och lösenord om du använder en lokal version av Team Foundation Server.    
   ![Konfigurera Jenkins efter build-åtgärder](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Spara projektet Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Skapa en Jenkins tjänstslutpunkt

En tjänstslutpunkt kan Team Services att ansluta till Jenkins.

1. Öppna den **Services** i Team Services, öppna den **nya tjänstslutpunkten** och väljer **Jenkins**.
   ![Lägg till en Jenkins slutpunkt](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Ange ett namn för anslutningen.
3. Ange URL-Adressen till din Jenkins och välj den **accepterar ej betrodda certifikat för SSL** alternativet. Ett exempel som URL: en är **http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com**.
4. Ange användarnamnet och lösenordet för kontot för Jenkins.
5. Välj **verifiera anslutning** att kontrollera att informationen är korrekt.
6. Välj **OK** att skapa tjänsteslutpunkt.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Skapa en distributionsgrupp för virtuella Azure-datorer

Du behöver en [distributionsgruppen](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) att registrera Team Services-agenten så versionen definitionen kan distribueras till den virtuella datorn. Distributionsgrupper gör det lättare att definiera logiska grupper med måldatorer för distribution och installera nödvändiga agenten på varje dator.

   > [!NOTE]
   > I följande procedur, måste du installera nödvändiga komponenter och *inte köra skriptet med sudo-behörighet.*

1. Öppna den **versioner** för den **skapa &amp; versionen** hubben, öppna **distributionsgrupper**, och välj **+ ny**.
2. Ange ett namn för distributionsgruppen och en valfri beskrivning. Välj sedan **skapa**.
3. Välj operativsystem för din distribution av virtuella måldatorn. Välj exempelvis **Ubuntu 16.04 +**.
4. Välj **använder en personlig åtkomsttoken i skriptet för autentisering**.
5. Välj den **systemkraven** länk. Installera förutsättningar för ditt operativsystem.
6. Välj **kopiera skriptet till Urklipp** att kopiera skriptet.
7. Logga in på din distribution av den virtuella måldatorn och kör skriptet. Inte köra skriptet med sudo-behörighet.
8. Efter installationen kan uppmanas du distribution taggar. Godkänn standardinställningarna.
9. Sök efter den nya virtuella datorn i i Team Services **mål** under **distributionsgrupper**.

## <a name="create-a-team-services-release-definition"></a>Skapa en definition för Team Services version

En definition av versionen anger processen som Team Services använder för att distribuera appen. I det här exemplet kan du köra ett kommandoskript.

Skapa definition för versionen i Team Services:

1. Öppna den **versioner** för den **skapa &amp; släpper** NAV- och välj **skapa versionen definition**. 
2. Välj den **tom** mall genom att välja börja med en **tom process**.
3. I den **artefakter** väljer **+ Lägg till artefakt** och välj **Jenkins** för **typ av datakälla**. Välj Jenkins tjänst endpoint-anslutningen. Sedan väljer du det Jenkins källa och välj **Lägg till**.
4. Välj tre punkter bredvid **miljö 1**. Välj **Lägg till grupp distributionsfasen**.
5. Välj din distributionsgruppen.
5. Välj  **+**  lägga till en aktivitet till **distribution grupp fas**.
6. Välj den **kommandoskript** uppgift och välj **Lägg till**. Den **kommandoskript** ger konfigurationen för ett skript köras på varje server för att kunna installera Node.js och starta appen.
8. För **skriptsökvägen**, ange **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Välj **Avancerat**, och sedan aktivera **ange Working Directory**.
10. För **Working Directory**, ange **$(System.DefaultWorkingDirectory) / Fabrikam-nod**.
11. Redigera namnet på definitionen versionen till det namn som du angav i den **efter build åtgärder** för versionen i Jenkins. Jenkins kräver det här namnet för att kunna utlösa en ny version när artefakter källa har uppdaterats.
12. Välj **spara** och välj **OK** att spara versionen-definitionen.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Köra manuell och CI-utlösta distributioner

1. Välj **+ versionen** och välj **skapa versionen**.
2. Välj den version som du slutfört i den markerade listrutan och välj **kön**.
3. Välj länken versionen i popup-meddelandet. Till exempel ”: versionen **Release 1** har skapats”.
4. Öppna den **loggar** fliken kan du titta på utmatningen versionen.
5. Öppna URL-Adressen till en av de servrar som du lagt till din distributionsgrupp i din webbläsare. Ange till exempel **http://{your-server-ip-address}**.
6. Gå till Git-lagerkälla och ändra innehållet i den **h1** rubriken i filen app/views/index.jade med ändrade text.
7. Genomför ändringarna.
8. Efter några minuter ser du en ny version skapas på den **versioner** i Team Services eller Team Foundation Server. Öppna versionen om du vill se distributionen äger rum. Grattis!

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen automatiserad distribution av en app till Azure med hjälp av Jenkins för bygg- och Team Services för versionen. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa din app i Jenkins.
> * Konfigurera Jenkins för integrering med Team Services.
> * Skapa en distributionsgrupp för de virtuella Azure-datorerna.
> * Skapa en definition av versionen som konfigurerar de virtuella datorerna och distribuerar appen.

Mer information om hur du distribuerar ett ljus (Linux, Apache, MySQL och PHP) stacken, gå vidare till nästa kurs.

> [!div class="nextstepaction"]
> [Distribuera LAMP-stack](tutorial-lamp-stack.md)
