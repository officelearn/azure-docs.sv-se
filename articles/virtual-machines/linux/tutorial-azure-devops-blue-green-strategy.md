---
title: Självstudie – konfigurera Kanarie-distributioner för Azure Virtuella Linux-datorer
description: I den här självstudien får du lära dig hur du konfigurerar en pipeline för kontinuerlig distribution (CD) som uppdaterar en grupp med virtuella Azure-datorer med hjälp av den blå gröna distributions strategin
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120494"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Självstudie – konfigurera en blå-grön distributions strategi för Azure Virtuella Linux-datorer


## <a name="iaas---configure-cicd"></a>IaaS – konfigurera CI/CD 
Azure-pipeliner innehåller en komplett uppsättning CI/CD Automation-verktyg för distributioner till virtuella datorer. Du kan konfigurera en pipeline för kontinuerlig leverans för en virtuell Azure-dator direkt från Azure Portal. Det här dokumentet innehåller stegen för att konfigurera en CI/CD-pipeline som använder den blå gröna strategin för distributioner av flera datorer. Du kan också ta en titt på andra strategier som [rullande](https://aka.ms/AA7jlh8) och [Kanarie](https://aka.ms/AA7jdrz), som stöds direkt från Azure Portal. 

 
 **Konfigurera CI/CD på Virtual Machines**

Virtuella datorer kan läggas till som mål i en [distributions grupp](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) och kan användas för uppdateringar av flera datorer. När du har distribuerat visar **distributions historiken** i en distributions grupp spårnings möjlighet från virtuell dator till pipelinen och sedan till commit. 
 
  
**Blå-grön distributioner**: en blå-grön-distribution minskar stillestånds tiden genom att ha en identisk vänte miljö. När som helst är en av miljöerna i real tid. När du förbereder en ny version slutför du den slutliga test fasen i den gröna miljön. När program varan fungerar i den gröna miljön byter du trafiken så att alla inkommande begär Anden går till den gröna miljön – den blå miljön är nu inaktiv.
Du kan konfigurera Blue-grönt-distributioner till dina "**virtuella datorer**" från Azure Portal med alternativet för kontinuerlig leverans. 

Här är steg för steg-beskrivningen.

1. Logga in på Azure Portal och navigera till en virtuell dator 
2. I det vänstra fönstret i den virtuella datorn går du till **kontinuerlig leverans**. Klicka sedan på **Konfigurera**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. I konfigurations panelen klickar du på **Azure DevOps Organization** för att välja ett befintligt konto eller skapa ett. Välj sedan det projekt som du vill konfigurera pipelinen för.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. En distributions grupp är en logisk uppsättning distributions mål datorer som representerar de fysiska miljöerna. till exempel "dev", "test", "UAT" och "produktion". Du kan skapa en ny distributions grupp eller välja en befintlig distributions grupp. 
5. Välj den build-pipeline som publicerar paketet som ska distribueras till den virtuella datorn. Observera att det publicerade paketet måste ha ett distributions skript _distribuera. ps1_ eller _Deploy.sh_ i `deployscripts` mappen på paket roten. Det här distributions skriptet körs av Azure DevOps pipeline vid körning.
6. Välj den distributions strategi du önskar. Välj **blått-grönt**.
7. Lägg till taggen "blå" eller "grön" till de virtuella datorer som ska ingå i Blue-grönt-distributioner. Om den virtuella datorn är för en vänte läges roll ska du tagga den som "grön", annars tagga den som "blå".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. Klicka på **OK** för att konfigurera pipeline för kontinuerlig leverans. Nu har du en pipeline för kontinuerlig leverans som kon figurer ATS för distribution till den virtuella datorn.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Klicka på **Redigera** release pipeline i Azure DevOps för att se pipeline-konfigurationen. Pipelinen består av tre faser. Den första fasen är en distributions grupps fas och distribueras till virtuella datorer som är taggade som _gröna_ (standby VM). I den andra fasen pausas pipelinen och väntar på manuell åtgärd för att återuppta körningen. När en användare är nöjd med att distributionen är stabil kan de nu omdirigera trafiken till _gröna_ virtuella datorer och återuppta pipelinen som sedan kommer att växla _blå_ och _gröna_ Taggar i de virtuella datorerna. Detta säkerställer att de virtuella datorerna med äldre program versioner märks som _gröna_ och distribueras till i nästa pipeline-körning.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. Åtgärden kör distribuera skript kör som standard distributions skriptet _Deploy. ps1_ eller _Deploy.sh_ i `deployscripts` mappen i det publicerade paketets rot Katalog. Kontrol lera att den valda Bygg pipelinen publicerar detta i rotmappen för paketet.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Andra distributions strategier
- [Konfigurera strategi för löpande distribution](https://aka.ms/AA7jlh8)
- [Konfigurera strategin för Kanarie-distribution](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Azure DevOps-projekt 
Kom igång med Azure enklare än någonsin.
 
Med DevOps Projects börjar du köra ditt program på valfri Azure-tjänst i tre steg: Välj ett program språk, en körning och en Azure-tjänst.
 
[Läs mer](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Ytterligare resurser 
- [Distribuera till Azure Virtual Machines med DevOps-projekt](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementera en kontinuerlig distribution av din app till en skalnings uppsättning för en virtuell Azure-dator](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
