---
title: Självstudie – konfigurera löpande distributioner för Azure Virtuella Linux-datorer
description: I den här självstudien får du lära dig hur du konfigurerar en pipeline för kontinuerlig distribution (CD) som stegvis uppdaterar en grupp med Azure Virtuella Linux-datorer med hjälp av strategin för löpande distribution
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113499"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Självstudie – konfigurera strategi för löpande distribution för Azure Virtuella Linux-datorer

Azure DevOps är en inbyggd Azure-tjänst som automatiserar varje del av DevOps-processen med kontinuerlig integrering och kontinuerlig leverans för alla Azure-resurser.
Oavsett om din app använder virtuella datorer, webbappar, Kubernetes eller någon annan resurs, kan du implementera infrastruktur som kod, kontinuerlig integrering, kontinuerlig testning, kontinuerlig leverans och kontinuerlig övervakning med Azure och Azure DevOps.  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS – konfigurera CI/CD 
Azure-pipeliner innehåller en komplett uppsättning CI/CD Automation-verktyg för distributioner till virtuella datorer. Du kan konfigurera en pipeline för kontinuerlig leverans för en virtuell Azure-dator direkt från Azure Portal. Det här dokumentet innehåller stegen som associeras med att ställa in en CI/CD-pipeline för att återställa distributioner av flera datorer från Azure Portal. Du kan också ta en titt på andra strategier, t. ex. [Kanarie](https://aka.ms/AA7jdrz) och [blå – grönt](https://aka.ms/AA83fwu), som stöds direkt från Azure Portal. 


**Konfigurera CI/CD på Virtual Machines**

Virtuella datorer kan läggas till som mål i en [distributions grupp](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) och kan användas för uppdatering av flera datorer. När **distributions historiken** i en distributions grupp har distribuerats ger det möjlighet att spåra från virtuell dator till pipelinen och sedan till commit. 
 

**Löpande distributioner**: en rullande distribution ersätter instanser av den tidigare versionen av ett program med instanser av den nya versionen av programmet på en fast uppsättning datorer (rullande uppsättning) i varje iteration. Nu ska vi göra en genom gång av hur du kan konfigurera en rullande uppdatering på virtuella datorer.  
Du kan konfigurera rullande uppdateringar till dina "**virtuella datorer**" i Azure Portal med alternativet för kontinuerlig leverans. 

Här är steg för steg-beskrivningen. 
1. Logga in på Azure Portal och navigera till en virtuell dator. 
2. I det vänstra fönstret i den virtuella datorn navigerar du till menyn för **kontinuerlig leverans** . Klicka sedan på **Konfigurera**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. I konfigurations panelen klickar du på "Azure DevOps Organization" för att välja ett befintligt konto eller skapa ett. Välj sedan det projekt som du vill konfigurera pipelinen för.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. En distributions grupp är en logisk uppsättning distributions mål datorer som representerar de fysiska miljöerna. till exempel "dev", "test", "UAT" och "produktion". Du kan skapa en ny distributions grupp eller välja en befintlig distributions grupp. 
5. Välj den build-pipeline som publicerar paketet som ska distribueras till den virtuella datorn. Observera att det publicerade paketet måste ha ett distributions skript _distribuera. ps1_ eller _Deploy.sh_ i `deployscripts` mappen vid paket roten. Det här distributions skriptet kommer att köras av Azure DevOps pipeline vid körning.
6. Välj den distributions strategi du önskar. I det här fallet kan du välja "rullande".
7. Du kan också tagga datorn med rollen. Till exempel "Web", "DB" osv. Detta hjälper dig att rikta virtuella datorer som har specifika roller.
8. Klicka på **OK** i dialog rutan för att konfigurera pipeline för kontinuerlig leverans. 
9. När du är färdig har du en pipeline för kontinuerlig leverans som är konfigurerad för distribution till den virtuella datorn.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Du kommer att se att distributionen till den virtuella datorn pågår. Du kan klicka på länken för att navigera till pipelinen. Klicka på **Release-1** om du vill visa distributionen. Du kan också klicka på **Redigera** om du vill ändra definitionen för release pipeline. 
11. Om du har flera virtuella datorer som ska konfigureras upprepar du steg 2-4 för andra virtuella datorer som ska läggas till i distributions gruppen. Observera att om du väljer en distributions grupp för en pipeline-körning som redan finns, läggs den virtuella datorn bara till i distributions gruppen utan att skapa några nya pipeliner. 
12. När du är färdig klickar du på pipeline-definitionen, navigerar till Azure DevOps-organisationen och klickar på **Redigera** versions pipeline. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Klicka på länk **1-jobbet, 1 uppgift** i **dev** -fasen. Klicka på **distributions** fasen.
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. I konfigurations rutan till höger kan du ange antalet datorer som du vill distribuera parallellt i varje iteration. Om du vill distribuera till flera datorer i taget kan du ange den i procent med hjälp av skjutreglaget.  

15. Kör skript för att distribuera skript kör som standard distributions skriptet _Deploy. ps1_ eller _Deploy.sh_ i mappen deployscripts i rot katalogen för det publicerade paketet.  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andra distributions strategier

- [Konfigurera strategin för Kanarie-distribution](https://aka.ms/AA7jdrz)
- [Konfigurera en blå-grön distributions strategi](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Azure DevOps-projekt 
Kom igång med Azure enklare än någonsin.
 
Med DevOps Projects börjar du köra ditt program på valfri Azure-tjänst i tre steg: Välj ett program språk, en körning och en Azure-tjänst.
 
[Läs mer](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Ytterligare resurser 
- [Distribuera till Azure Virtual Machines med DevOps-projekt](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementera en kontinuerlig distribution av din app till en skalnings uppsättning för en virtuell Azure-dator](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
