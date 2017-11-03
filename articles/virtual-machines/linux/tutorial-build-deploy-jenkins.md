---
title: "CI/CD från Jenkins till Azure virtuella datorer med VSTS | Microsoft Docs"
description: "Ställ in kontinuerlig integration (KO) och kontinuerlig distribution (CD) av en Node.js-app med Jenkins till Azure virtuella datorer från versionshantering i Visual Studio Team Services VSTS () eller Microsoft Team Foundation Server (TFS)"
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
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>Distribuera din app till Linux virtuella datorer med hjälp av Jenkins och VSTS

Kontinuerlig integration (KO) och kontinuerlig distribution (CD) är en pipeline som du kan bygga, släppa och distribuera din kod. Visual Studio Team Services VSTS () innehåller en fullständig, komplett funktionalitet uppsättning av verktyg för automatisering av CI/CD för distribution till Azure. Jenkins är ett populära 3 parts CI/CD-server-baserade verktyg som också ger CI/CD-automatisering. Du kan använda båda tillsammans för att anpassa hur du levererar ditt molnapp eller tjänst.

I den här självstudiekursen kommer du använda Jenkins för att skapa en **Node.js-webbapp**, och VSTS eller Team Foundation Server (TFS) för att distribuera den till en [distributionsgruppen](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) som innehåller virtuella Linux-datorer.

Du kommer att:

> [!div class="checklist"]
> * Hämta sample-appen
> * Konfigurera Jenkins plugin-program
> * Konfigurera ett Jenkins Freestyle projekt för Node.js
> * Konfigurera Jenkins för VSTS integrering
> * Skapa en Jenkins tjänstslutpunkt
> * Skapa en distributionsgrupp för de virtuella Azure-datorerna
> * Skapa en definition för VSTS versionen
> * Köra manuell och CI utlöses distributioner

## <a name="before-you-begin"></a>Innan du börjar

* Du behöver åtkomst till en Jenkins-server. Om du inte har skapat en Jenkins server, se [och skapa ett Jenkins original på en virtuell dator i Azure](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template). 

* Logga in på ditt konto i VSTS (`https://{youraccount}.visualstudio.com`). 
  Du kan få en [kostnadsfritt konto VSTS](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Mer information finns i [Anslut till VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  Du behöver en Linux-dator för ett mål för distribution.  Mer information finns i [skapa och hantera virtuella Linux-datorer med Azure CLI](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm).

*  Öppna inkommande port 80 för den virtuella datorn.  Mer information finns i [skapa nätverkssäkerhetsgrupper med hjälp av Azure portal](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Hämta sample-appen

Du måste distribuera en app lagras i en Git-lagringsplats.
Den här kursen rekommenderar vi du använder [denna sample-appen som är tillgängliga från GitHub](https://github.com/azooinmyluggage/fabrikam-node).  Den här självstudiekursen innehåller ett exempelskript som används för att installera Node.js och ett program.  Om du vill arbeta med dina egna databasen bör du konfigurera ett liknande exempel.

1. Skapa en duplicering av den här appen och Anteckna platsen (URL) för användning i senare steg i den här kursen.  Mer information finns i [duplicera en repo](https://help.github.com/articles/fork-a-repo/)    

> [!NOTE]
> Appen har skapats med [Yeoman](http://yeoman.io/learning/index.html); används **Express**, **bower**, och **grunt**; och har några **npm** paket som beroenden.
> Exemplet innehåller också ett skript som konfigurerar Nginx och distribuerar appen. Den körs på virtuella datorer. Mer specifikt installerar skriptet nod, Nginx och PM2; konfigurerar Nginx och PM2; Därefter startar appen nod.

## <a name="configure-jenkins-plugins"></a>Konfigurera Jenkins plugin-program

Först måste du konfigurera två Jenkins plugin-program för **NodeJS** och **VS Team Services kontinuerlig distribution**.

1. Öppna Jenkins-konto och välj **hantera Jenkins**.
2. I den **hantera Jenkins** väljer **hantera plugin-program**.
3. Filtrera listan för att hitta den **NodeJS** plugin-programmet och välj den **installera utan att starta om** alternativet.
    ![Att lägga till plugin-programmet för NodeJS Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrera listan efter den **VS Team Services kontinuerlig distribution** plugin-programmet och välj den **installera utan att starta om** alternativet.
5. Gå tillbaka till instrumentpanelen Jenkins och välj **hantera Jenkins**.
6. Välj **globala verktyget Configuration**.  Hitta **NodeJS** och på **NodeJS installationer**.
7. Aktivera den **installeras automatiskt** alternativet och ange sedan en **namn** värde.
8. Klicka på **Spara**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Konfigurera ett Jenkins Freestyle projekt för Node.js

1. Klicka på **nytt objekt**.  Ange en **objektnamnet**.
2. Välj **Freestyle projekt**.  Klicka på **OK**.
3. I den **källa kod Management** väljer **Git** och ange information om databasen och grenen med Appkod.    
    ![Lägg till en repo i din version](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. I den **Skapa utlösare** väljer **avsökning SCM** och ange schemat `H/03 * * * *` avsöker Git-lagringsplats för ändringar i alla tre minuter. 
5. I den **kompileringsmiljö** väljer **ange nod &amp; npm bin / mappen SÖKVÄGEN** och välj den **NodeJS Installation** värde. Lämna **npmrc filen** inställd på ”Använd systemstandard”.
6. I den **skapa** , Välj **köra shell** och ange kommandot `npm install` att se till att alla beroenden är uppdaterade.


## <a name="configure-jenkins-for-vsts-integration"></a>Konfigurera Jenkins för VSTS integrering

  > [!NOTE]
  Kontrollera den personliga åtkomsttoken (PATRIK) som du använder för att göra följande innehåller den **versionen (läsa, skriva, köra och hantera) behörighet i VSTS**.
 
1.  Skapa en PATRIK i VSTS-konto om du inte redan har ett. Jenkins kräver den här informationen för att komma åt ditt konto VSTS.  Se till att du **lagra** tokeninformation för kommande steg i det här avsnittet.
  Läs [hur skapar jag en personlig åtkomsttoken för VSTS och TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) att lära dig hur du skapar en.
2. I den **efter build åtgärder** klickar du på **Lägg till efter build åtgärd**. Välj **Arkivera artefakter**.
3. För **filer att arkivera**, ange `**/*` att inkludera alla filer.
4. Klicka för att skapa en annan åtgärd **Lägg till efter build åtgärd**.
5. Välj **utlösa version i TFS/Team Services**, anger du uri för ditt konto i VSTS t.ex: `https://{your-account-name}.visualstudio.com`.
6. Ange den **grupprojekt** namn.
7. Välj ett namn för den **släpper definition** (du skapar den här versionen definitionen senare i VSTS).
8. Välj autentiseringsuppgifter för att ansluta till VSTS eller TFS-miljön.  Lämna den **användarnamn** tomma om du använder VSTS.
   Ange en **användarnamn och lösenord** om du använder en lokal version av TFS.    
    ![Konfigurera Jenkins efter build-åtgärder](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. **Spara** jenkins projektet.

## <a name="create-a-jenkins-service-endpoint"></a>Skapa en Jenkins tjänstslutpunkt

En tjänstslutpunkt kan VSTS att ansluta till Jenkins.

1. Öppna den **Services** sida i VSTS, öppna den **nya tjänstslutpunkten** listan, och välj **Jenkins**.
    ![Lägg till en Jenkins slutpunkt](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Ange ett namn för anslutningen.
3. Ange Webbadressen till din Jenkins server och skalstreck den **accepterar ej betrodda certifikat för SSL** alternativet.  Ett exempel är URL:`http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. Ange den **användarnamn och lösenord** för ditt konto för Jenkins.
5. Välj **verifiera anslutning** att kontrollera att informationen är korrekt.
6. Välj **OK** att skapa tjänsteslutpunkt.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Skapa en distributionsgrupp för virtuella datorer i Azure

Du behöver en [distributionsgruppen](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) att registrera VSTS agenten så att versionen definitionen kan distribuera till den virtuella datorn.  Distributionsgrupper gör det lättare att definiera logiska grupper med måldatorer för distribution och installera nödvändiga agenten på varje dator.

   > [!NOTE]
   > Kontrollera i följande steg ska du installera nödvändiga komponenter och **inte köra skriptet med sudo-behörighet.**

1. Öppna den **versioner** för den **skapa &amp; versionen** hubben, öppna **distributionsgrupper**, och välj **+ ny**.
2. Ange ett namn för distributionsgruppen och en valfri beskrivning.
   Välj **skapa**.
3. Välj operativsystem för din distribution av virtuella måldatorn.  Till exempel välja **Ubuntu 16.04 +**.
4. Skalstreck den **använder en personlig åtkomsttoken i skriptet för autentisering**.
5. Välj den **systemkraven** länk.  Installera förutsättningar för ditt operativsystem.
6. Välj den **kopiera skriptet till Urklipp** att kopiera skriptet.
7. **Logga in** till virtuella för distribution måldatorn och **köra** skriptet.  **Inte** kör skriptet med sudo-behörighet.
8. Efter installationen kan uppmanas du distribution taggar.  Godkänn standardinställningarna.
9. I VSTS, söka efter den nya virtuella datorn i **mål** under **distributionsgrupper**.

## <a name="create-a-vsts-release-definition"></a>Skapa en definition för VSTS versionen

En definition av versionen anger processen VSTS kör om du vill distribuera appen.  I det här exemplet kan du köra ett kommandoskript.

Skapa definition för versionen i VSTS:

1. Öppna den **versioner** på den **skapa &amp; släpper** hubben, och välj **skapa versionen definition**. 
2. Välj den **tom** mall genom att välja Starta med en **tom process**.
3. I den **artefakter** klickar du på **+ Lägg till artefakt** och välj **Jenkins** för **typ av datakälla**. Välj Jenkins tjänst endpoint-anslutningen. Sedan väljer du det Jenkins källa och välj **Lägg till**.
4.  Klicka på knappen bredvid **miljö 1**.  Klicka på **Lägg till grupp distributionsfasen**.
5.  Välj din **distributionsgruppen**.
5. Klicka på  **+**  att lägga till en aktivitet för den **distribution grupp fas**.
6. Välj den **kommandoskript** och på **Lägg till**.    
    Den **kommandoskript** aktivitet används för att ange konfigurationen för ett skript köras på varje server för att installera Node.js och starta appen. Konfigurera enligt följande:
8. **Script sökvägen**:     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  Klicka på **Avancerat**, och sedan aktivera **ange Working Directory**.
10.  **Arbetskatalog**:`$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. Redigera namnet på definitionen versionen till det namn du angav i den **efter build åtgärder** för versionen i Jenkins. Jenkins kräver det här namnet för att kunna utlösa en ny version när artefakter källa har uppdaterats.
12. Klicka på **spara**, och klicka på **OK** att spara versionen-definitionen.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Köra manuell och CI utlöses distributioner

1. Välj **+ versionen** och välj **skapa versionen**.
2. Välj build du slutfört i den markerade listrutan och välj **kön**.
3. Välj länken versionen i popup-meddelande. Till exempel ”: versionen **Release 1** har skapats”.
4. Öppna den **loggar** fliken kan du titta på utmatningen versionen.
5. Öppna URL-Adressen till en av de servrar som du har lagt till i gruppen för distribution i din webbläsare. Ange till exempel`http://{your-server-ip-address}`
6. Gå till Git-lagerkälla och ändra innehållet i den **h1** rubriken i filen app/views/index.jade med ändrade text.
7. **Genomför** ändringen.
8. Efter några minuter ser du en ny utgåva som skapats i den **versioner** i VSTS eller TFS. Öppna versionen om du vill se distributionen äger rum. Grattis!

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen automatiserad distribution av en app på Azure med hjälp av Jenkins bygg- och VSTS för versionen. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa din app i Jenkins
> * Konfigurera Jenkins för VSTS integrering
> * Skapa en distributionsgrupp för de virtuella Azure-datorerna
> * Skapa en definition av versionen som konfigurerar de virtuella datorerna och distribuerar appen

Gå till nästa kurs vill veta mer om hur du distribuerar ett ljus (Linux, Apache, MySQL och PHP) stacken.

> [!div class="nextstepaction"]
> [Distribuera LAMP-stack](tutorial-lamp-stack.md)