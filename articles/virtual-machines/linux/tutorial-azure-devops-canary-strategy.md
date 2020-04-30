---
title: Självstudie – konfigurera Kanarie-distributioner för Azure Virtuella Linux-datorer
description: I den här självstudien får du lära dig hur du konfigurerar en pipeline för kontinuerlig distribution (CD) som uppdaterar en grupp av Azure-Virtuella Linux-datorer med hjälp av Kanarie-distributions strategin
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
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120480"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Självstudie – konfigurera en strategi för Kanarie-distribution för Azure Virtuella Linux-datorer


## <a name="iaas---configure-cicd"></a>IaaS – konfigurera CI/CD 
Azure-pipeliner innehåller en komplett uppsättning CI/CD Automation-verktyg för distributioner till virtuella datorer. Du kan konfigurera en pipeline för kontinuerlig leverans för en virtuell Azure-dator direkt från Azure Portal. Det här dokumentet innehåller stegen för att konfigurera en CI/CD-pipeline som använder Kanarie strategin för distributioner av flera datorer. Du kan också ta en titt på andra strategier, t. ex. [rullande](https://aka.ms/AA7jlh8) och [blå grönt](https://aka.ms/AA83fwu), som stöds direkt från Azure Portal. 


**Konfigurera CI/CD på Virtual Machines**

Virtuella datorer kan läggas till som mål i en [distributions grupp](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) och kan användas för uppdateringar av flera datorer. När **distributions historiken** i distributions gruppen har distribuerats ger det möjlighet att spåra från virtuell dator till pipelinen och sedan till commit. 
 
  
**Kanarie-distributioner**: en Kanarie-distribution minskar riskerna genom att sakta ner ändringen till en liten del av användarna. När du blir mer säker i den nya versionen kan du börja släppa den på fler servrar i infrastrukturen och dirigera fler användare till den. Du kan konfigurera Kanarie-distributioner till dina virtuella datorer med Azure Portal med alternativet för kontinuerlig leverans. Här är steg för steg-beskrivningen. 
1. Logga in på Azure Portal och navigera till en virtuell dator 
2. I det vänstra fönstret i den virtuella datorn navigerar du till menyn för **kontinuerlig leverans** . Klicka på **Konfigurera**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. I konfigurations panelen klickar du på **Azure DevOps Organization** för att välja ett befintligt konto eller skapa ett. Välj sedan det projekt som du vill konfigurera pipelinen för.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. En distributions grupp är en logisk uppsättning distributions mål datorer som representerar de fysiska miljöerna. till exempel "dev", "test", "UAT" och "produktion". Du kan skapa en ny distributions grupp eller välja en befintlig distributions grupp. 
5. Välj den build-pipeline som publicerar paketet som ska distribueras till den virtuella datorn. Observera att det publicerade paketet måste ha ett distributions skript _distribuera. ps1_ eller _Deploy.sh_ i `deployscripts` mappen på paket roten. Det här distributions skriptet körs av Azure DevOps pipeline vid körning.
6. Välj den distributions strategi du önskar. Välj **Kanarie**.
7. Lägg till en "Kanarie"-tagg till de virtuella datorer som ska ingå i Kanarie-distributioner och en "Prod"-tagg till de virtuella datorer som är en del av distributionerna när Kanarie-distributionen har slutförts. Med taggar kan du rikta virtuella datorer som har specifika roller.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. Klicka på **OK** i dialog rutan för att konfigurera pipeline för kontinuerlig leverans. Nu har du en pipeline för kontinuerlig leverans som kon figurer ATS för distribution till den virtuella datorn.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. Klicka på **Redigera** release pipeline i Azure DevOps för att se pipeline-konfigurationen. Pipelinen består av tre faser. Den första fasen är en distributions grupps fas och distribueras till virtuella datorer som är märkta som _Kanarie_. Den andra fasen pausar pipelinen och väntar på manuell åtgärd för att återuppta körningen. När en användare är nöjd med att Kanarie-distributionen är stabil kan de återuppta pipelinen och sedan köra den tredje fasen som distribueras till virtuella datorer som är märkta som _Prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. Innan du återupptar pipeline-körningen måste du kontrol lera att minst en virtuell dator är märkt som _Prod_. I den tredje fasen av pipelinen distribueras programmet enbart till de virtuella datorer som har en _Prod_ -tagg.

11. Kör skript för att distribuera skript kör som standard distributions skriptet _Deploy. ps1_ eller _Deploy.sh_ i mappen deployscripts i rot katalogen för det publicerade paketet. Kontrol lera att den valda Bygg pipelinen publicerar detta i rotmappen för paketet. 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Andra distributions strategier
- [Konfigurera strategi för löpande distribution](https://aka.ms/AA7jlh8)
- [Konfigurera en blå-grön distributions strategi](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Azure DevOps-projekt 
Kom igång med Azure enklare än någonsin.
 
Med DevOps Projects börjar du köra ditt program på valfri Azure-tjänst i tre steg: Välj ett program språk, en körning och en Azure-tjänst.
 
[Läs mer](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Ytterligare resurser 
- [Distribuera till Azure Virtual Machines med DevOps-projekt](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementera en kontinuerlig distribution av din app till en skalnings uppsättning för en virtuell Azure-dator](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
