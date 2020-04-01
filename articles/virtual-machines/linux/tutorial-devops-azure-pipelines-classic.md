---
title: Självstudiekurs - Integrerad DevOps för IaaS och PaaS på Azure
description: I den här självstudien får du lära dig hur du konfigurerar kontinuerlig integrering (CI) och kontinuerlig distribution (CD) av en app till virtuella Azure-datorer med Azure Pipelines.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77912536"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Självstudiekurs: Integrerade DevOps för IaaS och PaaS på Azure

Med heltäckande lösningar på Azure kan team implementera DevOps-metoder i var och en av programmets livscykelfaser: planera, utveckla, leverera och använda. 

Nedan följer några av De Azure Services som förenklar molnarbetsbelastningar och kan kombineras för att möjliggöra otroligt kraftfulla scenarier.
Dessa tekniker, i kombination med människor och processer, gör det möjligt för team att kontinuerligt ge värde till kunderna. 

- Azure: https://portal.azure.com – Portal för att skapa molnarbetsbelastningar. Hantera och övervaka allt från enkla webbappar till komplexa molnprogram 
- Azure DevOps: https://dev.azure.com – Planera smartare, samarbeta bättre och leverera snabbare med en uppsättning moderna utvecklingstjänster 
- Azure Machine Learning https://ml.azure.com studio: - Förbered data, träna och distribuera maskininlärningsmodeller 
 

Azure DevOps är en inbyggd Azure-tjänst som automatiserar varje del av DevOps-processen med kontinuerlig integrering och kontinuerlig leverans för alla Azure-resurser.
Oavsett om din app använder virtuella datorer, webbappar, Kubernetes eller någon annan resurs kan du implementera infrastruktur som kod, kontinuerlig integrering, kontinuerlig testning, kontinuerlig leverans och kontinuerlig övervakning med Azure och Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - Konfigurera CI/CD 
Azure Pipelines tillhandahåller en komplett, komplett uppsättning CI/CD-automatiseringsverktyg för distributioner till virtuella datorer. Du kan konfigurera en kontinuerlig leveranspipeline för en Azure VM direkt från Azure-portalen. Det här dokumentet innehåller de steg som är associerade med att konfigurera en CI/CD-pipeline för distributioner med flera datorer från Azure-portalen. Konfigurera CI/CD på virtuella datorer.

Virtuella datorer kan läggas till som mål i en [distributionsgrupp](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) och kan riktas för rullande uppdateringar med flera datorer. Distributionshistorikvyer i distributionsgrupper ger spårbarhet från virtuell dator till pipelinen och sedan till commit. 
 
**Rullande uppdateringar**: En rullande distribution ersätter instanser av den tidigare versionen av ett program med instanser av den nya versionen av programmet på en fast uppsättning datorer (rullande uppsättning) i varje iteration. Låt oss gå igenom hur du kan konfigurera en rullande uppdatering till virtuella datorer.  
Du kan konfigurera rullande uppdateringar till dina "**virtuella datorer**" i Azure-portalen med alternativet kontinuerlig leverans. 

Här är steg-för-steg genomgång. 
1. Logga in på din Azure-portal och navigera till en virtuell dator. 
2. I den vänstra datorn navigerar du till menyn **för kontinuerlig leverans.**  Klicka sedan på **Konfigurera**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. På konfigurationspanelen klickar du på "Azure DevOps Organization" för att välja ett befintligt konto eller skapa ett. Välj sedan det projekt som du vill konfigurera pipelinen under.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. En distributionsgrupp är en logisk uppsättning distributionsmåldatorer som representerar de fysiska miljöerna. till exempel "Dev", "Test", "UAT" och "Produktion". Du kan skapa en ny distributionsgrupp eller välja en befintlig distributionsgrupp. Du kan också tagga datorn med rollen. Till exempel "web", "db" etc.  
5. Klicka på **OK** i dialogrutan för att konfigurera den kontinuerliga leveranspipelinen. 
6. När du är klar har du en kontinuerlig leveranspipeline konfigurerad för distribution till den virtuella datorn.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Du kommer att se att distributionen till den virtuella datorn pågår. Du kan klicka på länken för att navigera till pipelinen. Klicka på **Release-1** för att se distributionen. Eller så kan du klicka på **Redigera** för att ändra versionen pipeline definition. 
8. Om du har flera virtuella datorer som ska konfigureras upprepar du stegen 2-5 för att andra virtuella datorer ska läggas till i distributionsgruppen. 
9. När du är klar klickar du på pipeline-definitionen, navigerar till Azure DevOps-organisationen och klickar på **Redigera** versionspipeline. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Klicka på länken **1 jobb, 1 uppgift** i **dev** skede. Klicka på **utplacera fasen.**  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. Från konfigurationsfönstret till höger kan du se att pipelinen som standard är konfigurerad för att göra en rullande uppdatering till alla mål parallellt. Du kan konfigurera distributionerna så att de sker antingen en i taget eller i procent med hjälp av skjutreglaget.  
  
  
**Kanariefågel** minskar risken genom att långsamt rulla ut förändringen till en liten delmängd av användarna. När du får mer förtroende för den nya versionen kan du börja släppa den till fler servrar i infrastrukturen och dirigera fler användare till den. Du kan konfigurera kanariedistributioner till dina "**virtuella datorer**" med Azure-portalen med alternativet kontinuerlig leverans. Här är steg-för-steg genomgång. 
1. Logga in på din Azure-portal och navigera till en virtuell dator 
2. Följ stegen 2-5 i föregående avsnitt för att lägga till flera virtuella datorer i distributionsgruppen. 
3. Lägg till en anpassad tagg i de virtuella datorer som ska ingå i kanariedistributioner. Till exempel "kanariefågel".
4. När pipelinen har konfigurerats för de virtuella datorerna klickar du på pipelinen, startar Azure DevOps-organisationen, **Redigera** pipelinen och navigerar till **utvecklingsfasen.** Lägg till taggen i filtret "kanariefågel". 
5. Lägg till ytterligare en distributionsgruppfas, konfigurera fasen med taggarna för att rikta återstående virtuella datorer i distributionsgruppen.  
6. Du kan också konfigurera ett manuellt valideringssteg som kan befordra/avvisa kanariefågeldistributionerna. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**Blue-Green** minskar driftstoppet genom att ha en identisk standby-miljö. När som helst en av miljöerna är levande. När du förbereder dig för en ny version gör du ditt sista steg i testningen i den gröna miljön. När programvaran fungerar i den gröna miljön, växla trafik så att alla inkommande förfrågningar går till den gröna miljön - den blå miljön är nu inaktiv.
Du kan konfigurera Blågröna distributioner till dina "**virtuella datorer**" från Azure-portalen med alternativet kontinuerlig leverans. 

Här är steg-för-steg genomgång. 

1. Logga in på din Azure-portal och navigera till en virtuell dator 
2. Följ stegen 2-5 under avsnittet **Rullande uppdateringar** för att lägga till flera virtuella datorer i distributionsgruppen. Lägg till en anpassad tagg i de virtuella datorer som ska ingå i blågröna distributioner. Till exempel "blå" eller "grön" för de virtuella datorer som är för stand-by-rollen. 
3. När pipelinen har konfigurerats för de virtuella datorerna klickar du på pipelinen, startar Azure DevOps-organisationen, **Redigera** pipelinen, navigerar till **utvecklingsfasen.** Lägg till taggen i filtret "grön". 
4. Lägg till en agentlös fas, konfigurera fasen med manuell valideringssteg och ett anropa-REST api-steg för att byta taggar. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Azure DevOps-projektet 
Kom igång med Azure enklare än någonsin.
 
Med DevOps-projekt börjar du köra programmet på valfri Azure-tjänst i bara tre steg: välj ett programspråk, en körning och en Azure-tjänst.
 
[Läs mer](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Ytterligare resurser 
- [Distribuera till virtuella Azure-datorer med DevOps-projektet](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementera kontinuerlig distribution av din app till en Azure Virtual Machine Scale Set](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
