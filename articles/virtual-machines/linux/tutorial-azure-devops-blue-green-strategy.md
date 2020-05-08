---
title: Självstudie – konfigurera Kanarie-distributioner för virtuella Azure Linux-datorer
description: I den här självstudien får du lära dig hur du konfigurerar en pipeline för kontinuerlig distribution (CD). Den här pipelinen uppdaterar en grupp med virtuella Azure Linux-datorer med hjälp av den blå gröna distributions strategin.
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
ms.openlocfilehash: a98989ed48e515cafeca27ae492c83efca6002c4
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871611"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Självstudie – konfigurera den blå gröna distributions strategin för virtuella Azure Linux-datorer

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>IaaS (Infrastructure as a Service) – Konfigurera CI/CD

Azure-pipeliner ger en helt aktuell uppsättning CI/CD Automation-verktyg för distribution till virtuella datorer. Du kan konfigurera en pipeline för kontinuerlig leverans för en virtuell Azure-dator från Azure Portal.

Den här artikeln visar hur du konfigurerar en CI/CD-pipeline som använder den blå gröna strategin för MultiMachine-distributioner. Azure Portal stöder också andra strategier som [rullande](https://aka.ms/AA7jlh8) och [Kanarie](https://aka.ms/AA7jdrz).

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurera CI/CD på virtuella datorer

Du kan lägga till virtuella datorer som mål i en [distributions grupp](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups). Du kan sedan rikta dem till MultiMachine-uppdateringar. När du har distribuerat till datorer, Visa **distributions historik** i en distributions grupp. I den här vyn kan du spåra från virtuell dator till pipelinen och sedan till commit.

### <a name="blue-green-deployments"></a>Blå-grön distributioner

En blå-grön-distribution minskar stillestånds tiden genom att ha en identisk vänte miljö. Endast en miljö är Live när som helst.

När du förbereder en ny version måste du slutföra den slutliga test fasen i den gröna miljön. När program varan fungerar i den gröna miljön kan du växla trafik så att alla inkommande begär Anden går till den gröna miljön. Den blå miljön är inaktiv.

Med alternativet för kontinuerlig leverans kan du konfigurera blå-grönt-distributioner till dina virtuella datorer från Azure Portal. Här är stegvisa steg-för-steg-anvisningar:

1. Logga in på Azure Portal och navigera till en virtuell dator.
1. I rutan längst till vänster i VM-inställningarna väljer du **kontinuerlig leverans**. Välj sedan **Konfigurera**.

   ![Fönstret kontinuerlig leverans med knappen Konfigurera](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. I konfigurations panelen väljer du **Azure DevOps-organisation** för att välja ett befintligt konto eller skapa ett nytt. Välj sedan det projekt som du vill konfigurera pipelinen för.  

   ![Panelen kontinuerlig leverans](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. En distributions grupp är en logisk uppsättning distributions mål datorer som representerar de fysiska miljöerna. Utveckling, testning, UAT och produktion är exempel. Du kan skapa en ny distributions grupp eller välja en befintlig.
1. Välj den build-pipeline som publicerar paketet som ska distribueras till den virtuella datorn. Det publicerade paketet måste ha ett distributions skript med namnet Deploy. ps1 eller deploy.sh i mappen deployscripts i paketets rotmapp. Pipelinen kör det här distributions skriptet.
1. I **distributions strategi**väljer du **blått-grönt**.
1. Lägg till taggen "blå" eller "grön" till de virtuella datorer som ska ingå i Blue-grönt-distributioner. Tagga det som "grönt" om en virtuell dator är för en vänte läges roll. Annars kan du tagga det som "blått".

   ![Panelen kontinuerlig leverans med distributions strategi svärdet blått – grönt valt](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Välj **OK** om du vill konfigurera en pipeline för kontinuerlig leverans som ska distribueras till den virtuella datorn.

   ![Den blå gröna pipelinen](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. Distributions informationen för den virtuella datorn visas. Du kan välja länken för att gå till versions pipelinen i Azure DevOps. I versions pipeline väljer du **Redigera** för att Visa pipeline-konfigurationen. Pipelinen har följande tre faser:

   1. Den här fasen är en distributions grupps fas. Program distribueras till virtuella datorer i vänte läge, som är märkta som "gröna".
   1. I den här fasen pausar och väntar på manuella åtgärder för att återuppta körningen. Användare kan återuppta pipeline-körningen när de har manuellt försäkrat stabiliteten vid distribution till virtuella datorer märkta som "gröna".
   1. Den här fasen byter taggarna "blå" och "gröna" i de virtuella datorerna. Detta säkerställer att virtuella datorer med äldre program versioner nu är märkta som "gröna". Under nästa pipeline-körning kommer programmen att distribueras till dessa virtuella datorer.

      ![Fönstret distributions grupp för aktiviteten distribuera blått-grönt](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. Åtgärden kör distribuera skript kör som standard distributions skriptet Deploy. ps1 eller deploy.sh. Skriptet finns i mappen deployscripts i rotmappen för det publicerade paketet. Se till att den valda Bygg pipelinen publicerar distributionen i paketets rotmapp.

   ![Fönster rutan artefakter som visar deploy.sh i mappen deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andra distributions strategier

- [Konfigurera strategin för löpande distribution](https://aka.ms/AA7jlh8)
- [Konfigurera strategin för Kanarie-distribution](https://aka.ms/AA7jdrz)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Du kan enkelt komma igång med Azure. Med Azure DevOps Projects börjar du köra ditt program på valfri Azure-tjänst i tre steg genom att välja:

- Ett program språk
- En körning
- En Azure-tjänst

[Läs mer](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Ytterligare resurser

- [Distribuera till virtuella Azure-datorer med hjälp av Azure DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementera en kontinuerlig distribution av din app till en skalnings uppsättning för en virtuell Azure-dator](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
