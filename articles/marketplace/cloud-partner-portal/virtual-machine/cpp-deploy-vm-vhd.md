---
title: Distribuera en virtuell dator från dina virtuella hårddiskar för Azure Marketplace
description: Förklarar hur du registrerar en virtuell dator från en Azure-distribuerad virtuell hårddisk.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: 5263d24c411ef8de4187c2fd750013374d779f04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277947"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Distribuera en virtuell dator från dina virtuella hårddiskar

I det här avsnittet beskrivs hur du distribuerar en virtuell dator (VM) från en Azure-distribuerad virtuell hårddisk (VHD).  Den listar de verktyg som krävs och hur du använder dem för att skapa en användare VM-avbildning och distribuera den sedan till Azure med PowerShell-skript.

När du har laddat upp dina virtuella hårddiskar – den generaliserade hårddisken i operativsystemet och noll eller fler virtuella hårddiskar för datadiskar – till ditt Azure-lagringskonto kan du registrera dem som en virtuell avbildning för användare. Då kan du testa den bilden. Eftersom operativsystemets virtuella hårddisk är generaliserad kan du inte distribuera den virtuella datorn direkt genom att ange VHD-URL:en.

Mer information om vm-bilder finns i följande blogginlägg:

- [Arkivbild.](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM-avbildning PowerShell "Hur"](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Förutsättning: installera nödvändiga verktyg

Om du inte redan har gjort det installerar du Azure PowerShell och Azure CLI med hjälp av följande instruktioner:

- [Installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Distributionssteg

Du kommer att använda följande steg för att skapa och distribuera en användare VM-avbildning:

1. Skapa avbildningen av den virtuella datorn för användare, vilket innebär att bilden hämtas och generaliseras. 
2. Skapa certifikat och lagra dem i ett nytt Azure Key Vault. Ett certifikat krävs för att upprätta en säker WinRM-anslutning till den virtuella datorn.  En Azure Resource Manager-mall och ett Azure PowerShell-skript tillhandahålls. 
3. Distribuera den virtuella datorn från en användare VM-avbildning med hjälp av den medföljande mallen och skriptet.

När den virtuella datorn har distribuerats är du redo att [certifiera avbildningen för den virtuella datorn](./cpp-certify-vm.md).

1. Klicka på **Ny** och sök efter **malldistribution**och välj sedan **Skapa en egen mall i Redigerare**.  <br/>
   ![Skapa VHD-distributionsmall i Azure Portal](./media/publishvm_021.png)

1. Kopiera och klistra in den här [JSON-mallen](./cpp-deploy-json-template.md) i redigeraren och klicka på **Spara**. <br/>
   ![Spara VHD-distributionsmall i Azure Portal](./media/publishvm_022.png)

1. Ange parametervärdena för de **anpassade distributionsegenskapssidorna.**

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parametern**              |   **Beskrivning**                                                            |
   |  -------------              |   ---------------                                                            |
   | Namn på användarkonto för användare   | Namn på lagringskonto där den allmänna virtuella hårddisken finns                    |
   | Behållarens namn för användarlagring | Behållarnamn där den allmänna virtuella hårddisken finns                          |
   | DNS-namn för offentlig IP      | Offentligt IP DNS-namn. DNS-namnet är av den virtuella datorn, kommer du att definiera detta i Azure Portal, när erbjudandet distribueras.  |
   | Administratörsanvändarnamn             | Administratörskontots användarnamn för ny virtuell dator                                  |
   | Adminlösenord              | Administratörskontots lösenord för ny virtuell dator                                  |
   | OS-typ                     | Operativsystemet VM: `Windows` \|`Linux`                                    |
   | Prenumerations-ID:t             | Identifierare för den valda prenumerationen                                      |
   | Location                    | Geografisk plats för distributionen                                        |
   | Storlek på virtuell dator                     | [Azure VM-storlek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), till exempel`Standard_A2` |
   | Namn på offentlig IP-adress      | Namn på din offentliga IP-adress                                               |
   | Namn på virtuell dator                     | Namnet på den nya virtuella datorn                                                           |
   | Namn på virtuellt nätverk        | Namn på det virtuella nätverk som används av den virtuella datorn                                   |
   | Nätverkskortsnamn                    | Namn på nätverkskortet som kör det virtuella nätverket               |
   | VHD URL                     | Fullständig VHD-URL för OS-disk                                                     |
   |  |  |
            
1. När du har anger dessa värden klickar du på **Köp**. 

Azure börjar distributionen: den skapar en ny virtuell dator med den angivna obearmade virtuella hårddisken, i den angivna sökvägen till lagringskontot.  Du kan spåra förloppet i Azure-portalen genom att klicka på **virtuella datorer** till vänster på portalen.  När den virtuella datorn har skapats `Starting` `Running`ändras statusen från till . 


### <a name="deploy-a-vm-from-powershell"></a>Distribuera en virtuell dator från PowerShell

Om du vill distribuera en stor virtuell dator från den generaliserade vm-avbildningen som just skapats använder du följande cmdlets.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Nästa steg

Därefter skapar du [en avbildning](cpp-create-user-image.md) för användare vm för din lösning.

