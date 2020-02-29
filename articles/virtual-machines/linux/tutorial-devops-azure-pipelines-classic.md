---
title: Självstudie – integrerad DevOps för IaaS och PaaS på Azure
description: I den här självstudien får du lära dig att ställa in kontinuerlig integrering (CI) och kontinuerlig distribution (CD) för en app på virtuella Azure-datorer med hjälp av Azure-pipelines.
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912536"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Självstudie: integrerad DevOps för IaaS och PaaS på Azure

Med lösningar från slut punkt till slut punkt i Azure kan grupper implementera DevOps-metoder i varje fas i program livs cykeln: planera, utveckla, leverera och driva. 

Nedan visas några av de Azure-tjänster som fören klar moln arbets belastningar och kan kombineras för att möjliggöra fantastiska kraftfulla scenarier.
Dessa tekniker, kombinerat med personer och processer, gör det möjligt för team att kontinuerligt tillhandahålla mervärde för kunder. 

- Azure: https://portal.azure.com – portalen för att skapa moln arbets belastningar. Hantera och övervaka allt från enkla webbappar till komplexa moln program 
- Azure DevOps: https://dev.azure.com – planera smartare, samar beta bättre och leverera snabbare med en uppsättning moderna utvecklings tjänster 
- Azure Machine Learning Studio: https://ml.azure.com – prepare data, träna och distribuera maskin inlärnings modeller 
 

Azure DevOps är en inbyggd Azure-tjänst som automatiserar varje del av DevOps-processen med kontinuerlig integrering och kontinuerlig leverans för alla Azure-resurser.
Oavsett om din app använder virtuella datorer, webbappar, Kubernetes eller någon annan resurs, kan du implementera, infrastruktur som kod, kontinuerlig integrering, kontinuerlig testning, kontinuerlig leverans och kontinuerlig övervakning med Azure och Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS – konfigurera CI/CD 
Azure-pipeliner innehåller en komplett uppsättning CI/CD Automation-verktyg för distributioner till virtuella datorer. Du kan konfigurera en pipeline för kontinuerlig leverans för en virtuell Azure-dator direkt från Azure Portal. Det här dokumentet innehåller stegen för att konfigurera en CI/CD-pipeline för distributioner av flera datorer från Azure Portal. Konfigurera CI/CD på Virtual Machines.

Virtuella datorer kan läggas till som mål i en [distributions grupp](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) och kan riktas mot rullande uppdateringar med flera datorer. Vyer för distributions historik i distributions grupper ger spårning från virtuell dator till pipelinen och sedan till commit. 
 
**Rullande uppdateringar**: en rullande distribution ersätter instanser av den tidigare versionen av ett program med instanser av den nya versionen av programmet på en fast uppsättning datorer (rullande uppsättning) i varje iteration. Nu ska vi göra en genom gång av hur du kan konfigurera en rullande uppdatering på virtuella datorer.  
Du kan konfigurera rullande uppdateringar till dina "**virtuella datorer**" i Azure Portal med alternativet för kontinuerlig leverans. 

Här är steg för steg-beskrivningen. 
1. Logga in på Azure Portal och navigera till en virtuell dator. 
2. I det vänstra fönstret i den virtuella datorn går du till menyn för **kontinuerlig leverans** . Klicka sedan på **Konfigurera**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. I konfigurations panelen klickar du på "Azure DevOps Organization" för att välja ett befintligt konto eller skapa ett. Välj sedan det projekt som du vill konfigurera pipelinen för.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. En distributions grupp är en logisk uppsättning distributions mål datorer som representerar de fysiska miljöerna. till exempel "dev", "test", "UAT" och "produktion". Du kan skapa en ny distributions grupp eller välja en befintlig distributions grupp. Du kan välja att tagga datorn med rollen. Till exempel "Web", "DB" osv.  
5. Klicka på **OK** i dialog rutan för att konfigurera pipeline för kontinuerlig leverans. 
6. När du är färdig har du en pipeline för kontinuerlig leverans som är konfigurerad för distribution till den virtuella datorn.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Du kommer att se att distributionen till den virtuella datorn pågår. Du kan klicka på länken för att navigera till pipelinen. Klicka på **Release-1** om du vill visa distributionen. Du kan också klicka på **Redigera** om du vill ändra definitionen för release pipeline. 
8. Om du har flera virtuella datorer som ska konfigureras upprepar du stegen 2-5 för andra virtuella datorer som ska läggas till i distributions gruppen. 
9. När du är färdig klickar du på pipeline-definitionen, navigerar till Azure DevOps-organisationen och klickar på **Redigera** versions pipeline. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Klicka på länk **1-jobbet, 1 uppgift** i **dev** -fasen. Klicka på **distributions** fasen.  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. I konfigurations rutan till höger kan du se att pipelinen är konfigurerad att göra en rullande uppdatering av alla mål parallellt. Du kan konfigurera distributionerna så att de inträffar antingen i taget eller i procent med hjälp av skjutreglaget.  
  
  
**Kanarie** minskar risken genom att sakta ner ändringen till en liten del av användarna. När du blir mer säker i den nya versionen kan du börja släppa den på fler servrar i infrastrukturen och dirigera fler användare till den. Du kan konfigurera Kanarie-distributioner till dina "**virtuella datorer**" med Azure Portal med alternativet för kontinuerlig leverans. Här är steg för steg-beskrivningen. 
1. Logga in på Azure Portal och navigera till en virtuell dator 
2. Följ stegen 2-5 i föregående avsnitt om du vill lägga till flera virtuella datorer i distributions gruppen. 
3. Lägg till en anpassad tagg till de virtuella datorer som ska ingå i Kanarie-distributioner. Till exempel "Kanarie".
4. När pipelinen har kon figurer ATS för de virtuella datorerna klickar du på pipelinen, startar Azure DevOps Organization, **redigerar** pipelinen och navigerar till **dev** -fasen. Lägg till tagg till filtret "Kanarie". 
5. Lägg till en annan distributions grupp fas, konfigurera fasen med taggarna för att rikta in återstående virtuella datorer i distributions gruppen.  
6. Du kan också konfigurera ett manuellt validerings steg som kan höja eller avvisa Kanarie-distributionerna. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**Blått-grönt** minskar drift stopps driften med en identisk vänte miljö. När som helst är en av miljöerna i real tid. När du förbereder en ny version gör du ditt slutliga test steg i den gröna miljön. När program varan fungerar i den gröna miljön byter du trafiken så att alla inkommande begär Anden går till den gröna miljön – den blå miljön är nu inaktiv.
Du kan konfigurera Blue-grönt-distributioner till dina "**virtuella datorer**" från Azure Portal med alternativet för kontinuerlig leverans. 

Här är steg för steg-beskrivningen. 

1. Logga in på Azure Portal och navigera till en virtuell dator 
2. Följ stegen 2-5 under avsnittet **rullande uppdateringar** om du vill lägga till flera virtuella datorer i distributions gruppen. Lägg till en anpassad tagg till de virtuella datorer som ska ingå i Blue-grönt-distributioner. Till exempel "blått" eller "grönt" för de virtuella datorer som är för den aktuella rollen. 
3. När pipelinen har kon figurer ATS för de virtuella datorerna klickar du på pipelinen, startar Azure DevOps Organization, **redigerar** pipelinen, navigerar till **dev** -fasen. Lägg till taggen i filtret "grön". 
4. Lägg till en agent-mindre fas, konfigurera fasen med manuellt validerings steg och ett Invoke-REST API-steg för att växla taggarna. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Azure DevOps-projekt 
Kom igång med Azure enklare än någonsin.
 
Med DevOps Projects börjar du köra ditt program på valfri Azure-tjänst i tre steg: Välj ett program språk, en körning och en Azure-tjänst.
 
[Läs mer](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Ytterligare resurser 
- [Distribuera till Azure Virtual Machines med DevOps-projekt](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementera en kontinuerlig distribution av din app till en skalnings uppsättning för en virtuell Azure-dator](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
