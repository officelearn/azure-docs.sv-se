---
title: "Använda skalningsuppsättningar i virtuella datorer i Azure-stacken"
description: "Lär dig hur molnadministratören lägger till virtuella datorn till stacken Azure Marketplace"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
keywords: 
ms.openlocfilehash: 31aeb963bdf4fd32712bc6f29f64060ec1c77cb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Använda skalningsuppsättningar i virtuella datorer i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Skaluppsättningar för den virtuella datorn är en Azure-stacken beräkningsresurser. Du kan använda dem för att distribuera och hantera en uppsättning identiska virtuella datorer. Med alla virtuella datorer konfigureras på samma sätt, skaluppsättningar behöver före etablering av virtuella datorer. Det är enklare att bygga storskaliga tjänster som är riktade till stor beräkning och stordata av arbetsbelastningar.

Det här avsnittet hjälper dig att skalningsuppsättningar i stacken Azure Marketplace. När du har slutfört den här proceduren kan användarna lägga till virtuella skalningsuppsättningarna för sina prenumerationer.

Skaluppsättningar för virtuell dator på Azure-stacken är som skalningsuppsättningar i virtuella datorer i Azure. Mer information finns i följande videoklipp:
* [Mark Russinovich berättar om Azure-skalningsuppsättningar](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Skaluppsättningar för virtuell dator med Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Skaluppsättningar för den virtuella datorn stöder inte Autoskala på Azure-stacken. Du kan lägga till flera instanser på en skala som anges med Azure Stack-portalen, Resource Manager-mallar eller PowerShell.

## <a name="prerequisites"></a>Krav
* **PowerShell och verktyg**

   Installera och konfigurerade PowerShell för Azure-stacken och Azure Stack-verktyg. Se [komma igång med PowerShell i Azure-stacken](azure-stack-powershell-configure-quickstart.md).

   När du har installerat Azure Stack-verktyg, se till att du importera följande PowerShell-modulen (sökväg i förhållande till den. \ComputeAdmin mappen i mappen AzureStack verktyg master):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Avbildning av operativsystemet**

   Om du inte har lagt till en operativsystemavbildning till din Azure-stacken Marketplace, se [lägga till Windows Server 2016 VM-avbildning i Azure Stack-marketplace](azure-stack-add-default-image.md).

   För Linux-support, hämta Ubuntu Server 16.04 och lägga till den med hjälp av ```Add-AzsVMImage``` med följande parametrar: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.

## <a name="add-the-virtual-machine-scale-set"></a>Lägg till virtuella datorns skaluppsättning

Redigera följande PowerShell-skript för din miljö och sedan köra den att lägga till en virtuell dator skala inställd på Azure Stack-Marketplace. 

``$User``är det konto som används för att ansluta administratörsportalen. Till exempel serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>Ta bort en virtuella datorns skaluppsättning

Skala set galleriobjektet om du vill ta bort en virtuell dator, kör följande PowerShell-kommando:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> Galleriobjektet kan inte tas bort omedelbart. Du kan behöva uppdatera portalen flera gånger innan den tas bort från Marketplace.


## <a name="next-steps"></a>Nästa steg
[Vanliga frågor om Azure-stacken](azure-stack-faq.md)

