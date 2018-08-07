---
title: Självstudier – CI/CD från Jenkins till virtuella Azure-datorer med Team Services | Microsoft Docs
description: I dessa självstudier får du lära dig hur du med hjälp av Jenkins kan konfigurera kontinuerlig integration (CI) och kontinuerlig distribution (CD) för en Node.js-app till virtuella Azure-datorer från versionshanteringen i Visual Studio Team Services eller Microsoft Team Foundation Server
author: tomarcher
manager: jpconnock
tags: azure-resource-manager
ms.assetid: ''
ms.service: devops
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/31/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: d3a4a81f60f4e70c2c7576c3176e2b4d6de08d04
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390603"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-visual-studio-team-services"></a>Självstudier: Distribuera appar till virtuella Linux-datorer i Azure med Jenkins och Visual Studio Team Services

Kontinuerlig integration (CI) och kontinuerlig distribution (CD) utgör en pipeline som du kan använda för att bygga, släppa och distribuera din kod. Visual Studio Team Services innehåller en fullständig, komplett uppsättning CI/CD-automatiseringsverktyg för distribution till Azure. Jenkins är ett populärt CI/CD-serverbaserat verktyg från tredje part som också har funktioner för CI/CD-automatisering. Du kan använda Team Services och Jenkins tillsammans för att anpassa hur du levererar din app eller tjänst i molnet.

I dessa självstudier använder du Jenkins för att skapa en Node.js-webbapp. Sedan använder du Team Services eller Team Foundation Server för att distribuera den till en [distributionsgrupp](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) som innehåller virtuella Linux-datorer (VM). Lär dig att:

> [!div class="checklist"]
> * Hämta exempelappen.
> * Konfigurera plugin-programmet för Jenkins.
> * Konfigurera ett Jenkins Freestyle-projekt för Node.js.
> * Konfigurera Jenkins för integrering med Team Services.
> * Skapa en tjänstslutpunkt för Jenkins.
> * Skapa en distributionsgrupp för de virtuella Azure-datorerna.
> * Skapa en versionsdefinition i Team Services.
> * Kör manuella och CI-utlösta distributioner.

## <a name="before-you-begin"></a>Innan du börjar

* Du behöver åtkomst till en Jenkins-server. Om du inte har skapat en Jenkins-server kan du läsa informationen om hur du [skapar ett Jenkins-original på en virtuell Azure-dator](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Logga in på ditt Team Services-konto (**https://{dittkonto}.visualstudio.com**). 
  Du kan få ett [kostnadsfritt Team Services-konto](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Mer information finns i avsnittet om att [ansluta till Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  Du behöver en virtuell Linux-dator som distributionsmål.  Mer information finns i [Skapa och hantera virtuella Linux-datorer med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Öppna inkommande port 80 för den virtuella datorn. Mer information finns i avsnittet om att [skapa nätverkssäkerhetsgrupper med hjälp av Azure Portal](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-the-sample-app"></a>Hämta exempelappen

Du behöver en app som ska distribueras och lagras på en Git-lagringsplats.
Vi rekommenderar att du för dessa självstudier använder [denna exempelapp, som finns tillgänglig från GitHub](https://github.com/azooinmyluggage/fabrikam-node). Dessa självstudier innehåller ett exempelskript som används för att installera Node.js och ett program. Om du vill arbeta med en egen lagringsplats bör du konfigurera ett liknande exempel.

Skapa en förgrening av den här appen och anteckna platsen (URL) för användning i senare steg i den här kursen. Läs mer i informationen om hur du [skapar en förgrening av en lagringsplats](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> Appen har skapats med [Yeoman](http://yeoman.io/learning/index.html). Den använder Express, Bower och Grunt. Den har även vissa npm-paket som beroenden.
> Exempelappen innehåller också ett skript som konfigurerar Nginx och distribuerar appen. Det körs på de virtuella datorerna. Vad skriptet gör:
> 1. Installerar Node, Nginx och PM2.
> 2. Konfigurerar Nginx och PM2.
> 3. Startar Node-appen.

## <a name="configure-jenkins-plug-ins"></a>Konfigurera plugin-programmet för Jenkins

Först måste du konfigurera två Jenkins plugin-program: **NodeJS** och **VS Team Services Continuous Deployment**.

1. Öppna ditt Jenkins-konto och välj **Hantera Jenkins**.
2. På sidan **Hantera Jenkins** väljer du **Hantera plugin-program**.
3. Filtrera listan för att hitta **NodeJS**- plugin-programmet och välj **Installera utan att starta om**.
    ![Lägga till plugin-programmet NodeJS till Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrera listan för att hitta plugin-programmet **VS Team Services Continuous Deployment** och välj **Installera utan att starta om**.
5. Gå tillbaka till instrumentpanelen i Jenkins och välj **Hantera Jenkins**.
6. Välj **Global Tool Configuration** (Global verktygskonfiguration). Hitta **NodeJS** och välj **NodeJS-installationer**.
7. Välj **Installera automatiskt** och ange ett värde för **Namn**.
8. Välj **Spara**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Konfigurera ett Jenkins Freestyle-projekt för Node.js

1. Välj **Nytt objekt**. Ange ett objektnamn.
2. Välj **Freestyle-projekt**. Välj **OK**.
3. På fliken **Source Code Management** (Källkodshantering) väljer du **Git** och anger information om lagringsplatsen och grenen som innehåller din appkod.    
    ![Lägga till en lagringsplats i din version](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. På fliken för att **skapa utlösare** väljer du **Poll SCM** (Avsök SCM) och anger schemat `H/03 * * * *` för att avsöka Git-lagringsplatsen för ändringar var tredje minut. 
5. På fliken **Build Environment** (Kompileringsmiljö) väljer du **Provide Node &amp; npm bin / folder PATH** (Ange nod och npm bim/folder SÖKVÄG) och sedan **NodeJS Installation** (NodeJS-installation). Låt **npmrc-filen** vara inställd för att **använda systemstandarden**.
6. På fliken **Build** (Version) väljer du **Execute shell** (Kör gränssnitt) och anger kommandot `npm install` så att alla beroenden är uppdaterade.


## <a name="configure-jenkins-for-team-services-integration"></a>Konfigurera Jenkins för integrering med Team Services

> [!NOTE]
> Kontrollera att den personliga åtkomsttoken (PAT) som du använder för följande steg har behörigheten *Release* (läsa, skriva, köra och hantera) i Team Services.
 
1.  Skapa en PAT i ditt Team Services-konto om du inte redan har ett. Jenkins behöver den här informationen för att få åtkomst till ditt Team Services-konto. Se till att spara tokeninformation för kommande steg i det här avsnittet.
  
    Om du vill lära dig mer om att generera en token läser du avsnittet om [hur du skapar en personlig åtkomsttoken för VSTS och TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).
2. På fliken **Post-build Actions** (Åtgärder efter skapandet) väljer du **Add post-build action** (Lägg till åtgärd efter skapandet). Välj **Archive the artifacts** (Arkivera artefakter).
3. För **Files to archive** (Filer att arkivera) anger du `**/*` för att inkludera alla filer.
4. Om du vill skapa en åtgärd till väljer du **Add post-build action** (Lägg till åtgärd efter skapandet).
5. Välj **Trigger release in TFS/Team Services** (Utlös version i TFS/Team Services). Ange URI för ditt Team Services-konto, t.ex. **https://{ditt-konto-namn}.visualstudio.com**.
6. Ange namnet för **grupprojektet**.
7. Välj ett namn för versionsdefinitionen. (Du skapar den här versionsdefinitionen senare i Team Services.)
8. Välj autentiseringsuppgifter för att ansluta till din Team Services- eller Team Foundation Server-miljö:
   - Låt **Username** (Användarnamn) vara tomt om du använder Team Services. 
   - Ange användarnamn och lösenord om du använder en lokal version av Team Foundation Server.    
   ![Konfigurera åtgärder efter skapandet i Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Spara Jenkins-projektet.


## <a name="create-a-jenkins-service-endpoint"></a>Skapa en tjänstslutpunkt för Jenkins

En tjänstslutpunkt gör att Team Services kan ansluta till Jenkins.

1. Öppna sidan **Services** (Tjänster) i Team Services, öppna sedan listan **New Service Endpoint** (Ny tjänstslutpunkt) och välj **Jenkins**.
   ![Lägga till en slutpunkt i Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Ange ett namn för anslutningen.
3. Ange URL-adressen till din Jenkins-server och välj alternativet **Accept untrusted SSL certificates** (Acceptera ej betrodda SSL-certifikat). Ett exempel på en URL är **http://{dinjenkinsURL}.westcentralus.cloudapp.azure.com**.
4. Ange användarnamnet och lösenordet för ditt Jenkins-konto.
5. Välj **Verify connection** (Verifiera anslutning) för att kontrollera att informationen är korrekt.
6. Välj **OK** för att skapa tjänstslutpunkten.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Skapa en distributionsgrupp för virtuella Azure-datorer

Du behöver en [distributionsgrupp](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) för att registrera Team Services-agenten så versionsdefinitionen kan distribueras till den virtuella datorn. Distributionsgrupper gör det lättare att definiera logiska grupper med måldatorer för distribution och installera den nödvändiga agenten på varje dator.

   > [!NOTE]
   > I följande procedur måste du installera nödvändiga komponenter. *Kör inte skriptet med sudo-privilegier.*

1. Öppna fliken **Releases** (Versioner) i hubben **Build &amp; Release** (Skapa version). Öppna **Deployment groups** (Distributionsgrupper) och välj **+ New** (+ Ny).
2. Ange ett namn för distributionsgruppen och valfri beskrivning. Välj sedan **Skapa**.
3. Välj operativsystem för din virtuella måldator för distribution. Välj t.ex. **Ubuntu 16.04+**.
4. Välj **Use a personal access token in the script for authentication** (Använd en personlig åtkomsttoken i skriptet för autentisering).
5. Välj länken för **systemkrav**. Installera förutsättningarna för ditt operativsystem.
6. Välj **Kopiera skriptet till Urklipp** för att kopiera skriptet.
7. Logga in på din virtuella måldator för distribution och kör skriptet. Kör inte skriptet med sudo-privilegier.
8. Efter installationen kan du uppmanas att distribuera grupptaggar. Acceptera alla standardinställningar.
9. Sök efter den nya virtuella datorn i Team Services, i **Targets** (Mål) under **Deployment Groups** (Distributionsgrupper).

## <a name="create-a-team-services-release-definition"></a>Skapa en versionsdefinition i Team Services

En versionsdefinition anger hur Team Services ska distribuera appen. I det här exemplet kör du ett kommandoskript.

Så här skapar du en versionsdefinition i Team Services:

1. Öppna fliken **Releases** (Versioner) i hubben **Build &amp; Release**  (Skapa version) och välj **Create release definition** (Skapa versionsdefinition). 
2. Välj den **tomma** mallen genom att välja att börja med en **tom process**.
3. I avsnittet **Artefakter** väljer du **+ Lägg till artefakt** och **Jenkins** som **källtyp**. Välj din tjänstslutpunkt i Jenkins-anslutningen. Sedan väljer du Jenkins-källjobbet och **Lägg till**.
4. Välj ellipsen bredvid **Environment 1** (Miljö 1). Välj **Add deployment group phase** (Lägg till distributionsgruppsfas).
5. Välj distributionsgrupp.
5. Välj **+** för att lägga till en uppgift till **Add deployment group phase** (Lägg till distributionsgruppsfas).
6. Välj uppgiften för **kommandoskriptet** och välj sedan **Lägg till**. Uppgiften för **kommandoskriptet** ger konfigurationen för ett skript som ska köras på varje server för att kunna installera Node.js och starta appen.
8. För **skriptsökvägen** anger du **$(System.StandardArbetsKatalog)/Fabrikam-Node/deployscript.sh**.
9. Välj **Avancerat** och aktivera **Ange arbetskatalog**.
10. För **arbetskatalog** anger du **$(System.StandardArbetsKatalog)/Fabrikam-Node**.
11. Redigera namnet på versionsdefinitionen till det namn du angav på fliken **Post-build Actions** (Åtgärder efter skapandet) för versionen i Jenkins. Jenkins behöver det här namnet för att kunna utlösa en ny version när artefakter för källor uppdateras.
12. Välj **Spara** och **OK** för att spara versionsdefinitionen.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Köra manuella och CI-utlösta distributioner

1. Välj **+ Release** (+ Version) och sedan **Create Release** (Skapa version).
2. Välj den version du slutförde i den markerade listrutan och välj **Queue** (Kö).
3. Välj versionslänken i popup-meddelandet. Till exempel: "Release **Release-1** has been created." (Version Version-1 har skapats).
4. Öppna fliken med **loggar** för att se på utmatningen av versionen.
5. Öppna URL-adressen till en av de servrar som du lagt till i din distributionsgrupp i en webbläsare. Ange till exempel **http://{din-server-ip-adress}**.
6. Gå till källagringsplatsen i Git och ändra innehållet i rubriken **h1** för filen app/views/index.jade.
7. Checka in ändringarna.
8. Efter några minuter ser du att en ny version har skapats på sidan med **versioner** i Team Services eller Team Foundation Server. Öppna versionen om du vill se distributionen. Grattis!

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

I dessa självstudier har du automatiserat distributionen av en app till Azure med hjälp av Jenkins för att bygga och Team Services för att publicera. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en app i Jenkins.
> * Konfigurera Jenkins för integrering med Team Services.
> * Skapa en distributionsgrupp för de virtuella Azure-datorerna.
> * Skapa en versionsdefinition som konfigurerar de virtuella datorerna och distribuerar appen.

Mer information om hur du distribuerar en LAMP-stack (Linux, Apache, MySQL och PHP) får du i nästa kurs.

> [!div class="nextstepaction"]
> [Distribuera LAMP-stack](tutorial-lamp-stack.md)
