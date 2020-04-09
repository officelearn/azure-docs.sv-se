---
title: Självstudiekurs - Hög tillgänglighet för virtuella Windows-datorer i Azure
description: I den här självstudiekursen lär du dig hur du använder Azure PowerShell för att distribuera virtuella datorer med hög tillgänglighet i tillgänglighetsuppsättningar
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 59bf06d2b279bad792bdc42a7c3b6acc2bc304b8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985719"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Självstudier: Skapa och distribuera virtuella datorer med hög tillgänglighet med Azure PowerShell

I den här självstudien lär du dig att öka tillgängligheten och tillförlitligheten för dina virtuella datorer (VM) med hjälp av tillgänglighetsuppsättningar. Tillgänglighetsuppsättningarna ser till att de virtuella datorer som du distribuerar i Azure distribueras över flera isolerade maskinvarunoder i ett kluster. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar
> * Kontrollera Azure Advisor


## <a name="availability-set-overview"></a>Översikt över tillgänglighetsuppsättning

En tillgänglighetsuppsättning är en logisk grupperingsfunktion för att isolera vm-resurser från varandra när de distribueras. Azure ser till att de virtuella datorer som du placerar i en tillgänglighetsuppsättning körs över flera fysiska servrar, datarack, lagringsenheter och nätverksswitchar. Om ett maskinvaru- eller programvarufel inträffar påverkas endast en delmängd av dina virtuella datorer. Den övergripande lösningen fortsätter att fungera. Tillgänglighetsuppsättningar är viktiga för skapandet av tillförlitliga molnlösningar.

Nu ska vi titta en typisk VM-baserad lösning där du kan ha fyra klientdelswebbservrar och två virtuella serverdelsdatorer. Med Azure bör du definiera två tillgänglighetsuppsättningar innan du distribuerar dina virtuella datorer: en för webbnivån och en för serverdelsnivån. När du skapar en ny virtuell dator anger du tillgänglighetsuppsättningen som en parameter. Azure ser till att de virtuella datorerna isoleras över flera fysiska maskinvaruresurser. Om det uppstår ett problem på den fysiska maskinvara som dina servrar körs på fortsätter de andra instanserna av servrarna att köras eftersom de finns på annan maskinvara.

Använd tillgänglighetsuppsättningar när du vill distribuera tillförlitliga VM-baserade lösningar i Azure.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i [https://shell.azure.com/powershell](https://shell.azure.com/powershell)en separat webbläsarflik genom att gå till . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Maskinvaran på en plats är uppdelad i flera uppdateringsdomäner och feldomäner. En **uppdateringsdomän** är en grupp av underliggande fysisk maskinvara som kan startas om samtidigt. De virtuella datorerna i samma **feldomän** delar samma lagring, strömkälla och nätverksväxel.  

Du kan skapa en tillgänglighetsuppsättning med hjälp av [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). I det här exemplet är antalet för både uppdateringsdomäner och feldomäner *2*, och tillgänglighetsuppsättningen heter *myAvailabilitySet*.

Skapa en resursgrupp.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Skapa en hanterad tillgänglighetsuppsättning med hjälp av [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) med parametern `-sku aligned`.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Skapa virtuella datorer i en tillgänglighetsuppsättning
De virtuella datorerna måste skapas i tillgänglighetsuppsättningen för att säkerställa att de distribueras i maskinvaran. Du kan inte lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning efter att den har skapats. 


När du skapar en virtuell dator med hjälp av [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) använder du parametern `-AvailabilitySetName` för att ange namnet på tillgänglighetsuppsättningen.

Ange först ett administratörsanvändarnamn och lösenord för den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa nu två virtuella datorer med hjälp av [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) i tillgänglighetsuppsättningen.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
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

Det tar några minuter att skapa och konfigurera de virtuella datorerna. När du är klar har du två virtuella datorer distribuerade över den underliggande maskinvaran. 

Om du tittar på tillgängligheten som anges i portalen genom att gå till **Resursgrupper** > **myResourceGroupAvailability** > **myAvailabilitySet,** bör du se hur de virtuella datorerna distribueras över de två fel- och uppdateringsdomänerna.

![Tillgänglighetsuppsättning i portalen](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrollera tillgängliga VM-storlekar 

När du skapar en virtuell dator i en tillgänglighetsuppsättning måste du veta vilka vm-storlekar som är tillgängliga på maskinvaran. Använd [kommandot Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) för att hämta alla tillgängliga storlekar för virtuella datorer som du kan distribuera i tillgänglighetsuppsättningen.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Kontrollera Azure Advisor 

Du kan även använda Azure Advisor för att få mer information om hur du kan förbättra tillgängligheten för dina virtuella datorer. Azure Advisor analyserar din konfiguration och användningstelemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnadseffektiviteten, prestanda, tillgängligheten och säkerheten för dina Azure-resurser.

Logga in på [Azure Portal](https://portal.azure.com), välj **Alla tjänster** och skriv **Advisor**. Advisor-instrumentpanelen visar anpassade rekommendationer för den valda prenumerationen. Mer information finns i [Komma igång med Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar
> * Kontrollera Azure Advisor

Gå vidare till nästa kurs vill veta mer om VM-skalningsuppsättningar.

> [!div class="nextstepaction"]
> [Skapa en vm-skalningsuppsättning](tutorial-create-vmss.md)


