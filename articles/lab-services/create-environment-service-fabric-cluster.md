---
title: Skapa en miljö med Service Fabric-kluster i Azure DevTest Labs | Microsoft Docs
description: Lär dig mer om att skapa en miljö med en fristående Service Fabric-kluster och starta och stoppa klustret med scheman.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318791"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Skapa en miljö med fristående Service Fabric-kluster i Azure DevTest Labs
Den här artikeln innehåller information om hur du skapar en miljö med en fristående Service Fabric-kluster i Azure DevTest Labs. 

## <a name="overview"></a>Översikt
DevTest Labs kan skapa fristående testmiljöer som definieras av Azure Resource Management-mallar. De här miljöerna innehålla både IaaS-resurser, t.ex. virtuella datorer och PaaS-resurser, som Service Fabric. DevTest Labs kan du hantera virtuella datorer i en miljö genom att tillhandahålla kommandon för att styra de virtuella datorerna. Dessa kommandon ger dig möjlighet att starta eller stoppa en virtuell dator på ett schema. På samma sätt DevTest Labs kan också hjälpa dig att hantera Service Fabric-kluster i en miljö. Du kan starta eller stoppa Service Fabric-kluster i en miljö, antingen manuellt eller via ett schema.

## <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster
Service Fabric-kluster skapas med hjälp av miljöer i DevTest Labs. Varje miljö definieras av en Azure Resource Manager-mall i en Git-lagringsplats. Den [offentlig Git-lagringsplats](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) för DevTest Labs innehåller Resource Manager-mall för att skapa ett Service Fabric-kluster i den [Service fabric-kluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) mapp. 

1. Skapa först ett labb i Azure DevTest Labs med hjälp av anvisningarna i följande artikel: [Skapa ett labb](devtest-lab-create-lab.md). Observera att den **offentliga miljöer** alternativet är **på** som standard. 
2. Bekräfta att Service Fabric-providern är registrerad för din prenumeration genom att följa dessa steg:
    1. Välj **prenumerationer** på menyn på vänster navigering och välj din **prenumeration**
    2. På den **prenumeration** väljer **resursprovidrar** i den **inställningar** avsnitt i den vänstra menyn. 
    3. Om **Microsoft.ServiecFabric** inte är registrerade, Välj **registrera**. 
3. På den **labb** för labbet, väljer **+ Lägg till** i verktygsfältet. 
    
    ![Lägg till knapp i verktygsfältet](./media/create-environment-service-fabric-cluster/add-button.png)
3. På den **väljer bas** väljer **Service Fabric-kluster i labbet** i listan. 

    ![Välj Service Fabric-kluster i labb i listan](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. På den **konfigurera inställningar för** gör du följande steg: 
    1. Ange en **namn** för klustret **miljö**. Det här är namnet på resursgruppen i Azure där Service Fabric-klustret ska skapas. 
    2. Välj den **operativsystem (OS)** för de virtuella datorerna i klustret. Standardvärdet är: **Windows**.
    3. Ange ett namn för den **administratör** för klustret. 
    4. Ange en **lösenord** för administratören. 
    5. För den **certifikat**, ange ditt certifikat som en Base64-kodad sträng. Om du vill skapa ett certifikat, gör du följande:
        1. Ladda ned den **skapa ClusterCertificate.ps1** fil från den [Git-lagringsplats](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Du kan också klona databasen på din dator. 
        2. Starta **PowerShell**. 
        3. Kör den **ps1** filen med hjälp av kommandot `.\Create-ClusterCertificate.ps1`. Du ser en textfil som öppnas i anteckningar med den information som du måste fylla i fälten certifikatrelaterade på den här sidan. . 
    6. Ange den **lösenordet för certifikatet**.
    7. Ange den **tumavtryck** för ditt certifikat.
    8. Välj **Lägg till** på den **konfigurera inställningarna för** sidan. 

        ![Konfigurera klusterinställningar](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. När klustret har skapats kan se du en resursgrupp med namnet på den miljö som du angav i föregående steg. När du expanderar se du Service Fabric-kluster i den. Om status för resursgrupp har fastnat på **skapa**väljer **uppdatera** i verktygsfältet. Den **Service Fabric-kluster** miljö skapar en 5-nod 1-nodetype-kluster på Linux eller Windows.

    I följande exempel **mysfabricclusterrg** är namnet på resursgruppen som har skapats specifikt för Service Fabric-klustret. Det är viktigt att Observera att labbmiljöer är självständig inom den resursgrupp där de skapas. Det innebär att mallen som definierar den miljö som endast kan komma åt resurser inom resursgruppen du skapade eller [virtuella nätverk som konfigurerats för användning av labbet](devtest-lab-configure-vnet.md). Det här exemplet ovan skapar alla nödvändiga resurser i samma resursgrupp.

    ![Klustret skapas](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Starta eller stoppa klustret
Du kan starta eller stoppa klustret från antingen labb själva sidan eller från sidan Service Fabric-kluster som tillhandahålls av DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Från sidan DevTest Lab
Du kan starta eller stoppa klustret på sidan labb för övningen. 

1. Välj **tre punkter (...)**  för Service Fabric-kluster som du ser i följande bild: 

    ![Starta och stoppa kommandon för klustret](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Du ser två kommandon i snabbmenyn för att **starta** och **stoppa** klustret. Startkommandot startar alla noder i ett kluster. Stoppkommandot stoppar alla noder i ett kluster. När ett kluster har stoppats kan själva Service Fabric-klustret förblir i tillståndet klar, men inga noder är tillgängliga förrän startkommandot återutfärdas på klustret i laboratoriet.

    Det finns några saker att tänka på när du använder Service Fabric-kluster i en testmiljö. Det kan ta lite tid för Service Fabric-klustret till fullständigt rehydrate när noderna har startats om. Om du vill behålla data från avstängning till Start måste data sparas på en hanterad disk som är ansluten till den virtuella datorn. Det finns prestanda när du använder en ansluten hanterad disk, så det rekommenderas endast för testmiljöer. Om den disk som används för lagring av data inte säkerhetskopieras, sedan går data förlorade när stoppkommandot utfärdas på klustret.

### <a name="from-the-service-fabric-cluster-page"></a>Från sidan Service Fabric-kluster 
Det finns ett annat sätt att starta eller stoppa klustret. 

1. Välj Service Fabric-klustret i trädvyn på sidan labb. 

    ![Välj ditt kluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. På den **Service Fabric-kluster** sidan för klustret, se kommandon i verktygsfältet för att starta eller stoppa klustret. 

    ![Starta eller stoppa kommandon i Service Fabric-kluster-sidan](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Konfigurera automatisk start och schemat för automatisk avstängning
Service Fabric-kluster kan också startas eller stoppas enligt ett schema. Detta liknar upplevelsen för virtuella datorer i ett labb. Om du vill spara pengar och som standard varje kluster som skapas i ett labb automatiskt stängs av vid den tidpunkt som definieras av labbet [avstängning princip](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown). Du kan åsidosätta genom att ange om klustret ska stängas eller genom att ange den tid som klustret är avstängd. 

![Befintliga scheman för automatisk start- och automatisk avstängning](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Välj att schemat för automatisk start
Om du vill anmäla dig till Start-schema, gör du följande:

1. Välj **automatisk start** på den vänstra menyn
2. Välj **på** för **Tillåt det här service fabric-kluster som ska schemaläggas för automatisk start**. Den här sidan är bara aktiverad om lab ägaren inte har tillåtelse användare autostart sina virtuella datorer eller Service Fabric-kluster.
3. Välj **Spara** i verktygsfältet. 

    ![Automatisk star-sidan](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurera inställningar för automatisk avstängning 
Om du vill ändra inställningarna för avstängning, gör du följande:

1. Välj **automatisk avstängning** på den vänstra menyn. 
2. På den här sidan som du kan välja bort automatiska avstängningsregler genom att välja **av** för **aktiverad**. 
3. Om du har valt **på** för **aktiverad**, Följ dessa steg:
    1. Ange den **tid** för avstängning.
    2. Ange den **tidszon** för tiden. 
    3. Ange om du vill DevTest Labs för att skicka **meddelanden** innan automatisk avstängning. 
    4. Om du har valt **Ja** meddelandealternativ, ange den **Webhooksadressen** och/eller **e-postadress** att skicka meddelanden. 
    5. Välj **Spara** i verktygsfältet.

        ![Automatisk stänga sidan](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Visa noder i Service Fabric-kluster
På sidan för Service Fabric-kluster som du tidigare har sett i steg är specifik för DevTest Labs-sidan. Den visar inte du noderna i klustret. Följ dessa steg om du vill se mer information om klustret:

1. På den **labb** för labbet, väljer den **resursgrupp** i trädvyn i det **Mina virtuella datorer** avsnittet.

    ![Välj resursgrupp](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. På den **resursgrupp** kan du se alla resurser i resursgruppen i en lista. Välj din **Service Fabric-kluster** i listan. 

    ![Välj ditt kluster i listan](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Du ser den **Service Fabric-kluster** för klustret. Det här är den sida som innehåller Service Fabric. Du kan se all information om kluster noder, nodtyper, t.ex.

    ![Startsida för Service Fabric-kluster](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för ytterligare information om miljöer: 

- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
