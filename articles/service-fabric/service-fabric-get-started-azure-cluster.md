---
title: Konfigurera ett Azure Service Fabric-kluster | Microsoft Docs
description: "Snabbstart – skapa ett Service Fabric-kluster i Azure för Windows eller Linux."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 813217567c7e1fa5fc9ac11492933ad0c34553d1
ms.lasthandoff: 04/18/2017


---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Skapa ditt första Service Fabric-kluster i Azure
Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. I den här snabbstarten får du hjälp att skapa ett kluster med fem noder som körs i antingen Windows eller Linux på bara några minuter via [Azure Portal](http://portal.azure.com).  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

## <a name="create-the-cluster"></a>Skapa klustret

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.
2. Välj **Compute** från bladet **Nytt** och sedan **Service Fabric-kluster** från bladet **Compute**.
3. Fyll i Service Fabric-formuläret **Grundläggande inställningar**. För **Operativsystem** väljer du den version av Windows eller Linux som du vill köra klusternoderna i. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. Skapa en ny för **Resursgrupp**. En resursgrupp är en logisk behållare där Azure-resurser skapas och hanteras gemensamt. När du är klar klickar du på **OK**.

    ![Utdata efter klusterinstallationen][cluster-setup-basics]

4. Fyll i formuläret **Klusterkonfiguration**.  För **Antal nodtyper** anger du 1 och för [Hållbarhetsnivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) anger du Brons.

5. Välj **Konfigurera varje nodtyp** och fyll i formuläret **Konfiguration av nodtyp**. Nodtypen definierar antalet virtuella datorer och deras storlek, anpassade slutpunkter och andra inställningar för virtuella datorer av samma typ. Varje definierad nodtyp konfigureras som en separat VM-skalningsuppsättning som används till att distribuera och hantera virtuella datorer som en uppsättning. Varje nodtyp kan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet.  Den första, eller primära, nodtypen används för systemtjänsterna för Service Fabric, och den måste ha fem eller fler virtuella datorer.

    Vid distribution till en produktionsmiljö är det viktigt med [kapacitetsplanering](service-fabric-cluster-capacity.md).  I den här snabbstarten kör du däremot inga program, så välj VM-storleken *DS1_v2 Standard*.  Välj ”Silver” som [tillförlitlighetsnivå](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) och en inledande kapacitet på 5 för VM-skalningsuppsättningar.  

    Anpassade slutpunkter används till att öppna portar i Azure Load Balancer så att du kan ansluta med program som körs i klustret.  Ange ”80, 8172” så att du öppnar portarna 80 och 8172.

    Markera inte kryssrutan **Konfigurera avancerade inställningar**, som används till att anpassa slutpunkter för TCP/HTTP-hantering, portintervall för program, [placeringsbegränsningar](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) och [kapacitetsegenskaper](service-fabric-cluster-resource-manager-metrics.md).    

    Välj **OK**.

6. I formuläret **Klusterkonfiguration** ställer du in **Diagnostik** på **På**.  I den här snabbstarten behöver du inte ange några anpassade [infrastrukturinställningar](service-fabric-cluster-fabric-settings.md).  Under **Fabric-version** väljer du uppgraderingsläget **Automatiskt** så att Microsoft automatiskt uppdaterar den Fabric-kod som kör klustret.  Ställ in läget som **Manuellt** om du vill [välja en version som stöds](service-fabric-cluster-upgrade.md) och uppgradera till den. 

    ![Konfiguration av nodtyp][node-type-config]

    Välj **OK**.

7. Fyll i formuläret **Säkerhet**.  I den här snabbstarten kan du välja **Ta bort skydd**.  I produktionsmiljöer bör du endast skapa skyddade kluster eftersom alla kan ansluta anonymt till oskyddade kluster och utföra hanteringsåtgärder.  

    Certifikat används i Service Fabric till att autentisera och kryptera olika delar av ett kluster och de program som körs där. Mer information om hur du använder certifikat i Service Fabric finns i [Service Fabric cluster security scenarios](service-fabric-cluster-security.md) (Säkerhet för Service Fabric-kluster).  Om du vill aktivera autentisering av användare via Azure Active Directory eller ställa in certifikat för programsäkerhet kan du [skapa ett kluster från en Resource Manager-mall](service-fabric-cluster-creation-via-arm.md).

    Välj **OK**.

8. Granska sammanfattningen.  Om du vill ladda ned en Resource Manager-mall som bygger på de inställningar du har angett väljer du **Ladda ned mall och parametrar**.  Välj **Skapa** så att klustret skapas.

    Du kan se förloppet bland aviseringarna. (Klicka på klockikonen nära statusfältet uppe till höger på skärmen.) Om du klickade på **Fäst på startsidan** när du skapade klustret ser du **Deploying Service Fabric Cluster** (Distribuerar Service Fabric-kluster) fäst på **startsidan**.

## <a name="view-cluster-status"></a>Visa klusterstatus
När du har skapat ett kluster kan du granska det på bladet **Översikt** i portalen. Där kan du se information om klustret på instrumentpanelen, bland annat klustrets offentliga slutpunkter och en länk till Service Fabric Explorer.

![Klusterstatus][cluster-status]

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualisera klustret med hjälp av Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) är ett bra verktyg för att visualisera klustret och hantera program.  Service Fabric Explorer är en tjänst som körs i klustret.  Du kommer åt den i en webbläsare genom att klicka på länken **Service Fabric Explorer** på sidan **Översikt** för klustret i portalen.  Du kan också ange adressen direkt i webbläsaren: [http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer)

Instrumentpanelen för klustret innehåller en översikt över klustret, inklusive en sammanfattning av program- och nodhälsan. Nodvyn visar klustrets fysiska layout. För en viss nod kan du inspektera vilka program som har kod distribuerad på noden.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="connect-to-the-cluster-using-powershell"></a>Ansluta till klustret med PowerShell
Kontrollera att klustret körs genom att ansluta med PowerShell.  Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](service-fabric-get-started.md).  Cmdleten [Connect-ServiceFabricCluster](/powershell/module/ServiceFabric/Connect-ServiceFabricCluster) upprättar en anslutning till klustret.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Fler exempel på hur du ansluter till ett kluster finns i [Ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md). När du har anslutit till klustret använder du cmdleten [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) för att visa en lista över noder i klustret och statusinformation för varje nod. **HealthState** bör vara *OK* för varje nod.

```powershell
PS C:\> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
```

## <a name="remove-the-cluster"></a>Ta bort klustret
Ett Service Fabric-kluster består av andra Azure-resurser förutom själva klusterresursen. Så om du vill ta bort ett Service Fabric-kluster helt måste du också ta bort alla resurser det består av. Det enklaste sättet att ta bort klustret och alla dess resurser är att ta bort resursgruppen. Andra sätt att ta bort ett kluster eller borttagning av vissa (men inte alla) resurser i en resursgrupp beskrivs i [Ta bort ett kluster](service-fabric-cluster-delete.md)

Ta bort en resursgrupp på Azure Portal:
1. Navigera till det Service Fabric-kluster du vill ta bort.
2. Klicka på **Resursgrupp** på sidan med klusterinformation.
3. I den **Resource Group Essentials** (Information om resursgrupp) klickar du på **Ta bort** och följer sedan anvisningarna för borttagning av resursgruppen.
    ![Ta bort resursgruppen][cluster-delete]

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat ett fristående utvecklingskluster provar du följande:
* [Create a secure cluster in the portal](service-fabric-cluster-creation-via-portal.md) (Skapa ett säkert kluster i portalen)
* [Create a cluster from a template](service-fabric-cluster-creation-via-arm.md) (Skapa ett kluster från en mall) 
* [Distribuera appar med hjälp av PowerShell](service-fabric-deploy-remove-applications.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
