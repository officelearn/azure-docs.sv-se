---
title: Snabbstart:Skapa en profil för hög tillgänglighet av program - Azure PowerShell - Azure Traffic Manager
description: I den här snabbstartsartikeln beskrivs hur du skapar en Traffic Manager-profil för att skapa ett webbprogram med hög tillgänglig tillgång.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: rohink
ms.openlocfilehash: 0ab7392b4fa6e248d51392706fedaed156344a99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76934808"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Snabbstart: Skapa en Traffic Manager-profil för ett webbprogram med högtillgänge med Azure PowerShell

I den här snabbstarten beskrivs hur du skapar en Traffic Manager-profil som ger hög tillgänglighet för din webbapp.

I den här snabbstarten ska du skapa två instanser av ett webbprogram. Var och en av dem körs i olika Azure-regioner. Du skapar Traffic Manager-profil baserat på [slutpunktsprioritet](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profilen dirigerar användartrafik till den primära plats som kör webbappen. Traffic Manager övervakar kontinuerligt webbappen. Om den primära platsen inte är tillgänglig ger den automatisk redundans till säkerhetskopieringsplatsen.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic [Manager-profil med New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) som dirigerar användartrafik baserat på slutpunktsprioritet.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Skapa webbappar

För den här snabbstarten behöver du två instanser av ett webbprogram som distribueras i två olika Azure-regioner (*västra USA* och *östra USA*). Var och en av dessa kommer att fungera som primär- och redundansslutpunkter för Traffic Manager.

### <a name="create-web-app-service-plans"></a>Skapa planer för webbapptjänst
Skapa web app-tjänstplaner med [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) för de två instanserna av webbprogrammet som du ska distribuera i två olika Azure-regioner.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Skapa en webbapp i apptjänstplanen
Skapa två instanser webbprogrammet med [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) i App Service-planerna i regionerna *Västra USA* och *östra USA* Azure.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter
Lägg till de två webapparna som Traffic Manager-slutpunkter med [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) i Traffic Manager-profilen enligt följande:
- Lägg till webbappen som finns i regionen *Västra USA* Azure som den primära slutpunkten för att dirigera all användartrafik. 
- Lägg till webbappen som finns i regionen *Östra USA* Azure som redundansslutpunkt. När den primära slutpunkten inte är tillgänglig dirigeras trafiken automatiskt till redundansslutpunkten.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil

I det här avsnittet kontrollerar domännamnet för Traffic Manager-profilen. Du kan även konfigurera den primära slutpunkten till att inte vara tillgänglig. Slutligen ser du att webbappen fortfarande är tillgänglig. Det beror på att Traffic Manager skickar trafiken till redundansslutpunkten.

### <a name="determine-the-dns-name"></a>Bestämma DNS-namnet

Fastställ DNS-namnet på Traffic [Manager-profilen med Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Kopiera **relativednsName-värdet.** DNS-namnet på traffic manager-profilen är *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken
1. I en webbläsare anger du DNS-namnet på Traffic Manager-profilen (*http://<* relativednsname *>.trafficmanager.net)* för att visa webbappens standardwebbplats.

    > [!NOTE]
    > I det här snabbstartsscenariot dirigeras alla begäranden till den primära slutpunkten. Den är inställd på **Prioritet 1**.
2. Om du vill visa Failover i Traffic Manager inaktiverar du den primära platsen med [Inaktivera-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Kopiera DNS-namnet på Traffic Manager-profilen (*http://<* relativednsname *>.trafficmanager.net*) för att visa webbplatsen i en ny webbläsarsession.
4. Kontrollera att webbappen fortfarande är tillgänglig.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar tar du bort resursgrupper, webbprogram och alla relaterade resurser med [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en Traffic Manager-profil som ger hög tillgänglighet för webbprogrammet. Om du vill veta mer om att dirigera trafik kan du gå vidare till Traffic Manager-självstudierna.

> [!div class="nextstepaction"]
> [Självstudier för Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
