---
title: Distribuera en virtuell dator från dina virtuella hård diskar för Azure Marketplace
description: Förklarar hur du registrerar en virtuell dator från en Azure-distribuerad virtuell hård disk.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: b02fda545ac135735186885d7db597885bf6cc21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147971"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Distribuera en virtuell dator från dina virtuella hård diskar

> [!IMPORTANT]
> Från och med 13 april 2020 börjar vi flytta hanteringen av din virtuella Azure-dator till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [skapa tekniska till gång till Azure-datorer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) för att hantera dina migrerade erbjudanden.

I det här avsnittet beskrivs hur du distribuerar en virtuell dator (VM) från en Azure-distribuerad virtuell hård disk (VHD).  Den innehåller en lista över de verktyg som krävs och hur du använder dem för att skapa en användar avbildning av en virtuell dator och sedan distribuera den till Azure med hjälp av PowerShell-skript

När du har laddat upp dina virtuella hård diskar (VHD: er) – det generaliserade operativ systemet VHD och noll eller flera datadisk-VHD: er till ditt Azure Storage-konto kan du registrera dem som en VM-avbildning. Sedan kan du testa avbildningen. Eftersom operativ systemets virtuella hård disk är generaliserad kan du inte distribuera den virtuella datorn direkt genom att tillhandahålla VHD-URL: en.

Mer information om VM-avbildningar finns i följande blogg inlägg:

- [Avbildning av virtuell dator](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Den virtuella dator avbildningen PowerShell ' How to '](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Förutsättning: installera nödvändiga verktyg

Om du inte redan har gjort det installerar du Azure PowerShell och Azure CLI med hjälp av följande anvisningar:

- [Installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Distributionssteg

Du kommer att använda följande steg för att skapa och distribuera en användar avbildning av en virtuell dator:

1. Skapa avbildningen av den virtuella datorn, vilket innebär att avbildningen insamlas och generaliseras. 
2. Skapa certifikat och lagra dem i en ny Azure Key Vault. Ett certifikat krävs för att upprätta en säker WinRM-anslutning till den virtuella datorn.  En Azure Resource Manager mall och ett Azure PowerShell-skript tillhandahålls. 
3. Distribuera den virtuella datorn från en användar avbildning av en virtuell dator med hjälp av den angivna mallen och skriptet.

När den virtuella datorn har distribuerats är du redo att [certifiera din VM-avbildning](./cpp-certify-vm.md).

1. Klicka på **ny** och Sök efter **mall distribution**och välj sedan **Bygg en egen mall i redigerings programmet**.  <br/>
   ![Skapar distributions mal len för virtuella hård diskar i Azure Portal](./media/publishvm_021.png)

1. Kopiera och klistra in denna [JSON-mall](./cpp-deploy-json-template.md) i redigeraren och klicka på **Spara**. <br/>
   ![Spara distributions mal len för virtuella hård diskar i Azure Portal](./media/publishvm_022.png)

1. Ange parameter värden för de visade egenskaps sidorna för **Anpassad distribution** .

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **ProfileServiceApplicationProxy**              |   **Beskrivning**                                                            |
   |  -------------              |   ---------------                                                            |
   | Konto namn för användar lagring   | Lagrings konto namn där den generaliserade virtuella hård disken finns                    |
   | Namn på användar lagrings behållare | Container namn där den generaliserade virtuella hård disken finns                          |
   | DNS-namn för offentlig IP      | DNS-namn för offentlig IP. DNS-namnet är för den virtuella datorn. du definierar detta i Azure Portal när erbjudandet har distribuerats.  |
   | Administratörens användar namn             | Administratörs kontots användar namn för ny virtuell dator                                  |
   | Adminlösenord              | Administratörs kontots lösen ord för ny virtuell dator                                  |
   | OS-typ                     | Operativ system för virtuell `Windows` \| dator:`Linux`                                    |
   | Prenumerations-ID:t             | Identifierare för den valda prenumerationen                                      |
   | Plats                    | Distributionens geografiska plats                                        |
   | Storlek på virtuell dator                     | [Storlek på virtuell Azure-dator](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), till exempel`Standard_A2` |
   | Namn på offentlig IP-adress      | Namn på din offentliga IP-adress                                               |
   | Namn på virtuell dator                     | Namn på den nya virtuella datorn                                                           |
   | Virtual Network namn        | Namnet på det virtuella nätverk som används av den virtuella datorn                                   |
   | NIC-namn                    | Namnet på det nätverks gränssnitts kort som kör det virtuella nätverket               |
   | VHD-URL                     | Fullständigt VHD-URL för OS-disk                                                     |
   |  |  |
            
1. När du har angett dessa värden klickar du på **köp**. 

Azure påbörjar distributionen: den skapar en ny virtuell dator med den angivna ohanterade virtuella hård disken, i den angivna sökvägen för lagrings kontot.  Du kan följa förloppet i Azure Portal genom att klicka på **Virtual Machines** till vänster i portalen.  När den virtuella datorn har skapats ändras statusen från `Starting` till. `Running` 


### <a name="deploy-a-vm-from-powershell"></a>Distribuera en virtuell dator från PowerShell

Om du vill distribuera en stor virtuell dator från den generaliserade VM-avbildningen som precis har skapats, använder du följande cmdletar.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Nästa steg

Därefter [skapar du en virtuell användar avbildning](cpp-create-user-image.md) för din lösning.

