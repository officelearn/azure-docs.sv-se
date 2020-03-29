---
title: Skapa en klustermiljö för service fabric i Azure DevTest Labs
description: Lär dig hur du skapar en miljö med ett fristående Service Fabric-kluster och startar och stoppar klustret med hjälp av scheman.
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
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170345"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Skapa en miljö med självädslutat Service Fabric-kluster i Azure DevTest Labs
Den här artikeln innehåller information om hur du skapar en miljö med ett fristående Service Fabric-kluster i Azure DevTest Labs. 

## <a name="overview"></a>Översikt
DevTest Labs kan skapa fristående testmiljöer som definieras av Azure Resource Management-mallar. Dessa miljöer innehåller både IaaS-resurser, som virtuella datorer, och PaaS-resurser, till exempel Service Fabric. DevTest Labs kan du hantera virtuella datorer i en miljö genom att tillhandahålla kommandon för att styra de virtuella datorerna. Dessa kommandon ger dig möjlighet att starta eller stoppa en virtuell dator enligt ett schema. På samma sätt kan DevTest Labs också hjälpa dig att hantera Service Fabric-kluster i en miljö. Du kan starta eller stoppa ett Service Fabric-kluster i en miljö antingen manuellt eller via ett schema.

## <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster
Service Fabric-kluster skapas med hjälp av miljöer i DevTest Labs. Varje miljö definieras av en Azure Resource Manager-mall i en Git-databas. Den [offentliga Git-databasen](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) för DevTest Labs innehåller resource manager-mallen för att skapa ett Service Fabric-kluster i mappen [ServiceFabric-Cluster.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 

1. Skapa först ett labb i Azure DevTest Labs med hjälp av instruktioner i följande artikel: [Skapa ett labb](devtest-lab-create-lab.md). Observera att alternativet **Offentliga miljöer** är **På** som standard. 
2. Bekräfta att Service Fabric-leverantören är registrerad för din prenumeration genom att följa dessa steg:
    1. Välj **Prenumerationer** på menyn till vänster och välj din **prenumeration**
    2. På sidan **Prenumeration** väljer du **Resursleverantörer** i avsnittet **Inställningar** på den vänstra menyn. 
    3. Om **Microsoft.ServiecFabric** inte är registrerat väljer du **Registrera**. 
3. På sidan **DevTest Lab** för ditt labb väljer du **+ Lägg till** i verktygsfältet. 
    
    ![Knappen Lägg till i verktygsfältet](./media/create-environment-service-fabric-cluster/add-button.png)
3. På sidan **Välj en bas** väljer du Service Fabric Lab **Cluster** i listan. 

    ![Välj Service Fabric Lab Cluster i listan](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Gör följande på sidan **Konfigurera inställningar:** 
    1. Ange ett **namn** för **klustermiljön**. Det här är namnet på resursgruppen i Azure där Service Fabric-klustret kommer att skapas. 
    2. Välj **operativsystem (OS)** för kluster virtuella datorer. Standardvärdet är: **Windows**.
    3. Ange ett namn för **administratören** för klustret. 
    4. Ange ett **lösenord** för administratören. 
    5. För **certifikatet**anger du certifikatinformationen som en Base64-kodad sträng. Så här skapar du ett certifikat:
        1. Hämta filen **Create-ClusterCertificate.ps1** från [Git-databasen](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Du kan också klona databasen på datorn. 
        2. Starta **PowerShell**. 
        3. Kör **ps1-filen** med `.\Create-ClusterCertificate.ps1`kommandot . Du ser en textfil som öppnas i anteckningar med den information du behöver för att fylla i de certifikatrelaterade fälten på den här sidan. . 
    6. Ange **lösenordet för certifikatet**.
    7. Ange **tumavtrycket** för certifikatet.
    8. Välj **Lägg till** på sidan Konfigurera **inställningar.** 

        ![Konfigurera klusterinställningar](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. När klustret har skapats visas en resursgrupp med namnet på den miljö som du angav i föregående steg. När du expanderar visas servicetygsklustret i det. Om resursgruppens status har fastnat vid **Skapa**väljer du **Uppdatera** i verktygsfältet. **Klustermiljön För Service Fabric** skapar ett 5-nod 1-nodtypskluster på antingen Linux eller Windows.

    I följande exempel är **mysfabricclusterrg** namnet på resursgruppen som har skapats specifikt för service fabric-klustret. Det är viktigt att notera att labbmiljöer är fristående inom resursgruppen där de skapas. Det innebär att mallen som definierar miljön, som endast kan komma åt resurser inom den nyskapade resursgruppen eller [virtuella nätverk som konfigurerats för att användas av labbet](devtest-lab-configure-vnet.md). Det här exemplet ovan skapar alla nödvändiga resurser i samma resursgrupp.

    ![Kluster skapat](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Starta eller stoppa klustret
Du kan starta eller stoppa klustret från sidan DevTest Lab själv eller från sidan Service Fabric Cluster som tillhandahålls av DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Från sidan DevTest Lab
Du kan starta eller stoppa klustret på devtestlabbsidan för ditt labb. 

1. Välj **tre punkter (...)** för servicetygsklustret enligt följande bild: 

    ![Start- och stoppkommandon för klustret](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Du ser två kommandon i snabbmenyn för att **starta** och **stoppa** klustret. Startkommandot startar alla noder i ett kluster. Stoppkommandot stoppar alla noder i ett kluster. När ett kluster har stoppats förblir själva Service Fabric-klustret i ett färdigt tillstånd, men inga noder är tillgängliga förrän startkommandot utfärdas på nytt i klustret i labbet.

    Det finns några saker att tänka på när du använder ett Service Fabric-kluster i en testmiljö. Det kan ta lite tid för Service Fabric-klustret att helt rehydrera efter att noderna har startats om. Om du vill behålla data från avstängning till start måste data sparas på en hanterad disk som är ansluten till den virtuella datorn. Det finns prestandakonsekvenser när du använder en ansluten hanterad disk, så det rekommenderas endast för testmiljöer. Om disken som används för datalagring inte backas upp går data förlorade när stoppkommandot utfärdas i klustret.

### <a name="from-the-service-fabric-cluster-page"></a>Från sidan Kluster för serviceinfrastruktur 
Det finns ett annat sätt att starta eller stoppa klustret. 

1. Välj ditt Service Fabric-kluster i trädvyn på sidan DevTest Lab. 

    ![Välj ditt kluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. På sidan **Service Fabric-kluster** för klustret visas kommandon i verktygsfältet för att starta eller stoppa klustret. 

    ![Starta eller stoppa kommandon på sidan Kluster för servicevävnad](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Konfigurera schema för automatisk start och automatisk avstängning
Service Fabric-kluster kan också startas eller stoppas enligt ett schema. Den här upplevelsen liknar upplevelsen för virtuella datorer i ett labb. För att spara pengar stängs som standard varje kluster som skapas i ett labb automatiskt av vid den tidpunkt som definieras av [labbavstängningsprincipen](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy). Du kan åsidosätta genom att ange om klustret ska stängas av eller genom att ange när klustret stängs av. 

![Befintliga scheman för automatisk start och automatisk avstängning](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Anmäl dig till det automatiska startschemat
Så här väljer du startschemat:

1. Välj **Autostart** på den vänstra menyn
2. Välj **På** för **Tillåt att det här serviceggetygetklustret schemaläggs för automatisk start**. Den här sidan är endast aktiverad om labbägaren har tillåtit användare att starta sina virtuella datorer eller Service Fabric-kluster automatiskt.
3. Välj **Spara** i verktygsfältet. 

    ![Sida med automatisk stjärna](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurera inställningar för automatisk avstängning 
Så här ändrar du inställningar för avstängning:

1. Välj **Automatisk avstängning** på den vänstra menyn. 
2. På den här sidan kan du välja bort automatisk avstängning genom att välja **Av** för **Aktiverad**. 
3. Om du har valt **På** för **aktiverat**gör du så här:
    1. Ange **tid** för avstängning.
    2. Ange **tidszonen.** 
    3. Ange om du vill att DevTest Labs ska skicka **meddelanden** innan du stänger av automatiskt. 
    4. Om du valde **Ja** för meddelandealternativet anger du **Webhook-URL:en** och/eller **e-postadressen** för att skicka meddelanden. 
    5. Välj **Spara** i verktygsfältet.

        ![Stäng av sidan automatiskt](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Så här visar du noder i service fabric-klustret
Klustersidan För Service Fabric som du har sett i stegen tidigare är specifik för sidan DevTest Labs. Det visar inte noderna i klustret. SÃ¥ information om klustret:

1. På sidan **DevTest Lab** för ditt labb väljer du **resursgruppen** i trädvyn i avsnittet **Mina virtuella datorer.**

    ![Välj resursgrupp](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. På sidan **Resursgrupp** visas alla resurser i resursgruppen i en lista. Välj ditt **Service Fabric-kluster** i listan. 

    ![Markera klustret i listan](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Du ser sidan **Service Fabric Cluster** för klustret. Det här är sidan som Service Fabric tillhandahåller. Du ser all information om kluster som noder, nodtyper, etc.

    ![Startsida för Service Fabric-kluster](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Nästa steg
Mer information om miljöer finns i följande artiklar: 

- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
