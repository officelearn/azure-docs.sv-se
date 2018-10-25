---
title: Självstudier – Hög tillgänglighet för virtuella Windows-datorer i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du använder Azure PowerShell för att distribuera virtuella datorer med hög tillgänglighet i tillgänglighetsuppsättningar
documentationcenter: ''
services: virtual-machines-windows
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 02/09/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: bf6f74e05a788c6a6ffb88b71a2dfc27a6695a62
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49464836"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Självstudier: Skapa och distribuera virtuella datorer med hög tillgänglighet med Azure PowerShell

I den här självstudien får du lära dig hur du ökar tillgängligheten och tillförlitligheten för dina VM-lösningar i Azure med en funktion som heter ”Tillgänglighetsuppsättningar”. Tillgänglighetsuppsättningarna ser till att de virtuella datorer som du distribuerar i Azure distribueras över flera isolerade maskinvarunoder i ett kluster. Detta innebär att endast en del av de virtuella datorerna påverkas om det skulle uppstå ett maskinvaru- eller programvarufel i Azure, och att din lösning fortfarande är tillgänglig och fungerar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar
> * Kontrollera Azure Advisor

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt krävs Azure PowerShell-modulen version 5.7.0 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="availability-set-overview"></a>Översikt över tillgänglighetsuppsättning

En tillgänglighetsuppsättning är en logisk grupperingsfunktion som du kan använda i Azure för att se till att VM-resurserna du placerar i Azure är isolerade från varandra när de distribueras i ett Azure-datacenter. Azure ser till att de virtuella datorer du placerar i en tillgänglighetsuppsättning körs över flera fysiska servrar, datarack, lagringsenheter och nätverksväxlar. Om det uppstår ett maskinvarufel eller Azure-programvarufel påverkas endast en del av dina virtuella datorer. Ditt program fungerar fortfarande och är tillgängligt för dina kunder. Tillgänglighetsuppsättningar är en viktig funktion när du vill skapa tillförlitliga molnlösningar.

Nu ska vi titta en typisk VM-baserad lösning där du kan ha fyra klientdelswebbservrar och två virtuella serverdelsdatorer. När du använder Azure bör du definiera två tillgänglighetsuppsättningar innan du distribuerar dina virtuella datorer: en tillgänglighetsuppsättning för webbnivån och en tillgänglighetsuppsättning för serverdelsnivån. När du skapar en ny virtuell dator kan du sedan ange tillgänglighetsuppsättningen som en parameter för kommandot ”az vm create”. Azure ser då automatiskt till att de virtuella datorer du skapar i tillgänglighetsuppsättningen är isolerade över flera fysiska maskinvaruresurser. Om det uppstår ett problem på den fysiska maskinvaran som din webbserver eller de virtuella serverdelsdatorerna körs på fungerar fortfarande de andra instanserna av webbservern och de virtuella serverdelsdatorerna eftersom de finns på en annan maskinvara.

Använd tillgänglighetsuppsättningar när du vill distribuera tillförlitliga VM-baserade lösningar i Azure.

## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Du kan skapa en tillgänglighetsuppsättning med [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). I det här exemplet anger du antalet för både uppdaterings- och feldomäner till *2* för tillgänglighetsuppsättningen med namnet *myAvailabilitySet* i resursgruppen *myResourceGroupAvailability*.

Skapa en resursgrupp.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Skapa en hanterad tillgänglighetsuppsättning med [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) med parametern `-sku aligned`.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Skapa virtuella datorer i en tillgänglighetsuppsättning
De virtuella datorerna måste skapas i tillgänglighetsuppsättningen för att säkerställa att de distribueras i maskinvaran. Du kan inte lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning efter att den har skapats. 

Maskinvaran på en plats är uppdelad i flera uppdateringsdomäner och feldomäner. En **uppdateringsdomän** är en grupp av underliggande fysisk maskinvara som kan startas om samtidigt. De virtuella datorerna i samma **feldomän** delar samma lagring, strömkälla och nätverksväxel. 

När du skapar en ny virtuell dator med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) använder du parametern `-AvailabilitySetName` för att ange namnet på tillgänglighetsuppsättningen.

Ange först ett administratörsanvändarnamn och lösenord för den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa sedan två virtuella datorer med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) i tillgänglighetsuppsättningen.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Parametern `-AsJob` skapar den virtuella datorn som en bakgrundsaktivitet så att PowerShell-kommandotolkarna återgår till dig. Du kan visa information om bakgrundsjobb med cmdleten `Job`. Det tar några minuter att skapa och konfigurera de virtuella datorerna. När du är klar har du två virtuella datorer distribuerade över den underliggande maskinvaran. 

Om du tittar på tillgänglighetsuppsättningen i Portal (genom att gå till Resursgrupper > myResourceGroupAvailability > myAvailabilitySet) ser du hur de virtuella datorerna är distribuerade över två feldomäner och uppdateringsdomäner.

![Tillgänglighetsuppsättning i portalen](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrollera tillgängliga VM-storlekar 

Du kan lägga till fler virtuella datorer i tillgänglighetsuppsättningen senare, men du måste veta vilka VM-storlekar som är tillgängliga på maskinvaran. Använd [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) för att visa en lista över alla tillgängliga storlekar i maskinvaruklustret för tillgänglighetsuppsättningen.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Kontrollera Azure Advisor 

Du kan också använda Azure Advisor för att få mer information om hur du kan förbättra tillgängligheten för dina virtuella datorer. Azure Advisor hjälper dig att följa bästa praxis för att optimera dina Azure-distributioner. Advisor analyserar din resurskonfiguration och användningstelemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnadseffektiviteten, prestanda, tillgängligheten och säkerheten för dina Azure-resurser.

Logga in på [Azure Portal](https://portal.azure.com), välj **Alla tjänster** och skriv **Advisor**. Advisor-instrumentpanelen visar anpassade rekommendationer för den valda prenumerationen. Mer information finns i [Komma igång med Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar
> * Kontrollera Azure Advisor

Gå vidare till nästa självstudie om du vill veta mer om VM-skalningsuppsättningar.

> [!div class="nextstepaction"]
> [Skapa en VM-skalningsuppsättning](tutorial-create-vmss.md)


