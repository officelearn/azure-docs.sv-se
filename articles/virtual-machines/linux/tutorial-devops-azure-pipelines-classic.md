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
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885913"
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
Azure Pipelines tillhandahåller en komplett, komplett uppsättning CI/CD-automatiseringsverktyg för distributioner till virtuella datorer. Du kan konfigurera en kontinuerlig leveranspipeline för en Azure VM direkt från Azure-portalen. Det här dokumentet innehåller de steg som är associerade med att konfigurera en CI/CD-pipeline för distributioner med flera datorer från Azure-portalen. 


**Konfigurera CI/CD på virtuella datorer**

Virtuella datorer kan läggas till som mål i en [distributionsgrupp](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) och kan riktas för uppdatering av flera datorer. Baserat på dina krav kan du välja någon av out-of-box distribution strategier, _Rullande,_ _Kanariefågel,_ _Blå-Grön_ eller kan ytterligare anpassa dem. När distributionshistoriken har distribuerats i distributionsgrupper kan du spåra från virtuell dator till pipelinen och sedan till commit. 
 
**Rullande distributioner**: En rullande distribution ersätter instanser av den tidigare versionen av ett program med instanser av den nya versionen av programmet på en fast uppsättning datorer (rullande uppsättning) i varje iteration. Låt oss gå igenom hur du kan konfigurera en rullande uppdatering till virtuella datorer.  
Du kan konfigurera rullande uppdateringar till dina "**virtuella datorer**" i Azure-portalen med alternativet kontinuerlig leverans. 

Här är steg-för-steg genomgång. 
1. Logga in på din Azure-portal och navigera till en virtuell dator. 
2. I den vänstra datorn navigerar du till menyn **för kontinuerlig leverans.**  Klicka sedan på **Konfigurera**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. På konfigurationspanelen klickar du på "Azure DevOps Organization" för att välja ett befintligt konto eller skapa ett. Välj sedan det projekt som du vill konfigurera pipelinen under.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. En distributionsgrupp är en logisk uppsättning distributionsmåldatorer som representerar de fysiska miljöerna. till exempel "Dev", "Test", "UAT" och "Produktion". Du kan skapa en ny distributionsgrupp eller välja en befintlig distributionsgrupp. 
5. Välj den byggpipeline som publicerar paketet som ska distribueras till den virtuella datorn. Observera att det publicerade paketet ska ha ett distributionsskript _deploy.ps1_ eller _deploy.sh_ i _deployscripts-mappen_ vid paketroten. Det här distributionsskriptet körs av Azure DevOps-pipelinen vid körning.
6. Välj valfri distributionsstrategi. I det här fallet kan du välja "Rullande".
7. Du kan också tagga datorn med rollen. Till exempel "web", "db" etc. Detta hjälper dig att rikta virtuella datorer som bara har en specifik roll.
8. Klicka på **OK** i dialogrutan för att konfigurera den kontinuerliga leveranspipelinen. 
9. När du är klar har du en kontinuerlig leveranspipeline konfigurerad för distribution till den virtuella datorn.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Du kommer att se att distributionen till den virtuella datorn pågår. Du kan klicka på länken för att navigera till pipelinen. Klicka på **Release-1** för att se distributionen. Eller så kan du klicka på **Redigera** för att ändra versionen pipeline definition. 
11. Om du har flera virtuella datorer som ska konfigureras upprepar du stegen 2-4 för att andra virtuella datorer ska läggas till i distributionsgruppen. Observera att om du väljer en distributionsgrupp som det redan finns en pipeline-körning för, läggs den virtuella datorn bara till i distributionsgruppen utan att skapa några nya pipelines. 
12. När du är klar klickar du på pipeline-definitionen, navigerar till Azure DevOps-organisationen och klickar på **Redigera** versionspipeline. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Klicka på länken **1 jobb, 1 uppgift** i **dev** skede. Klicka på **utplacera fasen.**
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. Från konfigurationsfönstret till höger kan du ange hur många datorer som du vill distribuera parallellt i varje iteration. Om du vill distribuera till flera datorer åt gången kan du ange det i procent med hjälp av skjutreglaget.  

15. Aktiviteten Kör distribuera skript kör som standard distributionsskriptet _deploy.ps1_ eller _deploy.sh_ i _mappen deployscripts_ i rotkatalogen för publicerat paket.
  
**Kanariefågeldistributioner:** En kanariefågeldistribution minskar risken genom att långsamt distribuera ändringen till en liten delmängd av användare. När du får mer förtroende för den nya versionen kan du börja släppa den till fler servrar i infrastrukturen och dirigera fler användare till den. Du kan konfigurera kanariedistributioner till dina "**virtuella datorer**" med Azure-portalen med alternativet kontinuerlig leverans. Här är steg-för-steg genomgång. 
1. Logga in på din Azure-portal och navigera till en virtuell dator 
2. Följ stegen 2-7 under avsnittet **Rullande distributioner** för att lägga till flera virtuella datorer i distributionsgruppen. Välj "Kanariefågel" för listrutan distributionsstrategi.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. Lägg till en canary-tagg i de virtuella datorer som ska ingå i kanariefågeldistributioner och en prod-tagg till de virtuella datorerna som ingår i distributioner efter kanariefågeldistributionen som lyckades.
4. När du är klar har du en kontinuerlig leveranspipeline konfigurerad för distribution till den virtuella datorn.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. Precis som i avsnittet **Rullande distributioner** kan du klicka på **Redigera** versionspipeline i Azure DevOps för att se pipeline-konfigurationen. Pipelinen består av 3 faser - första fasen är en GD-fas och distribuerar till virtuella datorer som är taggade som _kanariefågel_. Den andra fasen, pausar rörledningen och väntar på manuell intervention för att återuppta körningen. När en användare är övertygad om att kanariefågeldistributionen är stabil kan han återuppta pipelinekörningen som sedan kör den tredje fasen som distribuerar till virtuella datorer som har taggats som _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**Blå-gröna distributioner:** En blå-grön distribution minskar driftstopp genom att ha en identisk standby-miljö. När som helst en av miljöerna är levande. När du förbereder dig för en ny version gör du ditt sista steg i testningen i den gröna miljön. När programvaran fungerar i den gröna miljön, växla trafik så att alla inkommande förfrågningar går till den gröna miljön - den blå miljön är nu inaktiv.
Du kan konfigurera Blågröna distributioner till dina "**virtuella datorer**" från Azure-portalen med alternativet kontinuerlig leverans. 

Här är steg-för-steg genomgång.

1. Logga in på din Azure-portal och navigera till en virtuell dator 
2. Följ stegen 2-7 under avsnittet **Rullande distributioner** för att lägga till flera virtuella datorer i distributionsgruppen. Lägg till en "blå" eller "grön" tagg i de virtuella datorer som ska ingå i blågröna distributioner. Om den virtuella datorn är för en standby-roll, bör du tagga den som "grön".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. När du är klar har du en kontinuerlig leveranspipeline konfigurerad för distribution till den virtuella datorn.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. Precis som med **rullande distributioner**kan du klicka på **Redigera** versionspipeline i Azure DevOps för att se pipeline-konfigurationen. Pipelinen består av 3 faser - första fasen är en GD-fas och distribuerar till virtuella datorer som är taggade som _gröna_ (vänteläge virtuella datorer) . Den andra fasen pausar pipelinen och väntar på manuella åtgärder för att återuppta körningen. När en användare är övertygad om att distributionen är stabil, kan han nu omdirigera trafiken till _gröna_ virtuella datorer och återuppta pipeline-körningen som sedan kommer att byta _blå_ och _gröna_ taggar i de virtuella datorerna. Detta säkerställer att de virtuella datorer som har äldre programversion är taggade som _gröna_ och distribueras till i nästa pipeline-körning.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. Observera att den här distributionsstrategin kräver att det måste finnas minst en virtuell dator som taggats som blå och grön vardera. Kontrollera att du har minst en virtuell dator som taggats som _blå_innan du återupptar pipelinekörningen i steg med manuell intervention.





 
## <a name="azure-devops-project"></a>Azure DevOps-projektet 
Kom igång med Azure enklare än någonsin.
 
Med DevOps-projekt börjar du köra programmet på valfri Azure-tjänst i bara tre steg: välj ett programspråk, en körning och en Azure-tjänst.
 
[Läs mer](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Ytterligare resurser 
- [Distribuera till virtuella Azure-datorer med DevOps-projektet](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementera kontinuerlig distribution av din app till en Azure Virtual Machine Scale Set](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
