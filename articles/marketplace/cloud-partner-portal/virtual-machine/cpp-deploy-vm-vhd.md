---
title: 'Distribuera en virtuell dator från dina VHD: er för Azure Marketplace | Microsoft Docs'
description: Beskriver hur du registrerar en virtuell dator från en Azure-distribuerade virtuella Hårddisken.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2771549af29b3e717d117afb42de6db03fbee226
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639943"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Distribuera en virtuell dator från dina VHD: er

Den här artikeln beskrivs hur du registrerar en virtuell dator (VM) från en Azure-distribuerade virtuella hårddisk (VHD).  Visas en lista med de verktyg som krävs och hur du använder dem för att skapa en VM-avbildning för användaren och sedan distribuera den till Azure med hjälp av antingen den [Microsoft Azure-portalen](https://ms.portal.azure.com/) eller PowerShell-skript. 

När du har laddat upp dina virtuella hårddiskar (VHD) – generaliserad operativsystemet VHD och noll eller fler disk virtuella hårddiskar – till Azure storage-kontot, kan du registrera dem som en avbildning för användaren. Du kan sedan testa avbildningen. Du kan inte direkt distribuera den virtuella datorn genom att tillhandahålla VHD-URL eftersom operativsystemet VHD är generaliserad.

Mer information om VM-avbildningar finns i följande blogginlägg:

- [VM-avbildning](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Bild-dator med PowerShell, hur du'](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="set-up-the-necessary-tools"></a>Konfigurera nödvändiga verktyg

Om du inte redan har gjort det installerar du Azure PowerShell och Azure CLI, med hjälp av följande anvisningar:

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [Installera Azure PowerShell på Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="create-a-user-vm-image"></a>Skapa en virtuell datoravbildning för användare

Därefter skapar du en ohanterad avbildning från en generaliserad virtuell Hårddisk.

#### <a name="capture-the-vm-image"></a>Skapa VM-avbildning

Följ instruktionerna i följande artikel om hur du fångar den virtuella datorn som motsvarar ditt åtkomst-metoden:

-  PowerShell: [hur du skapar en ohanterad VM-avbildning från en Azure-dator](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [så här skapar du en avbildning av en virtuell dator eller virtuell Hårddisk](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines – samla in](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

### <a name="generalize-the-vm-image"></a>Generalisera avbildningen

Eftersom du har genererat användaravbildningen från en tidigare generaliserad virtuell Hårddisk, måste det också vara generaliserat.  Välj igen, i följande artikel som motsvarar ditt åtkomstmekanism.  (Du kanske har redan generaliserats hårddisken när du har hämtat.)

-  PowerShell: [generalisera den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [steg 2: skapa VM-avbildning](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [generalisera virtuella datorer -](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="deploy-a-vm-from-a-user-vm-image"></a>Distribuera en virtuell dator från en användaravbildning för virtuell dator

Sedan distribuerar du en virtuell dator från en användaravbildning av virtuell dator med Azure portal eller PowerShell.

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>Distribuera en virtuell dator från Azure-portalen

Du kan använda följande process för att distribuera ditt användar-VM i Azure Portal.

1.  Logga in på [Azure-portalen](https://portal.azure.com).

2.  Klicka på **New** och Sök efter **Malldistributionen**och välj sedan **skapa din egen mall i redigeraren**.  <br/>
  ![Skapa mall för distribution av virtuell Hårddisk i Azure-portalen](./media/publishvm_021.png)

3. Kopiera och klistra in det här [JSON-mall](./cpp-deploy-json-template.md) i redigeraren och klicka på **spara**. <br/>
  ![Spara mall för distribution av virtuell Hårddisk i Azure-portalen](./media/publishvm_022.png)

4. Ange parametervärden för den visade **anpassad distribution** egenskapssidor.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parametern**              |   **Beskrivning**                                                            |
   |  -------------              |   ---------------                                                            |
   | Användaren Lagringskontonamn   | Lagringskontonamn där den generaliserade virtuella Hårddisken finns                    |
   | Namnet på användaren lagringsbehållaren | Behållarens namn där den generaliserade virtuella Hårddisken finns                          |
   | DNS-namn för offentlig IP-adress      | Offentliga IP-DNS-namn                                                           |
   | Systemadministratörsanvändarens namn             | Användarnamn för administratörskontot för nya virtuella datorn                                  |
   | Administratörslösenord              | Lösenordet för administratörskontot för nya virtuella datorn                                  |
   | OS-typ                     | VM-operativsystem: `Windows` \| `Linux`                                    |
   | Prenumerations-ID:t             | Identifierare för den valda prenumerationen                                      |
   | Plats                    | Geografisk plats för distributionen                                        |
   | Storlek på virtuell dator                     | [Azure VM-storlek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), till exempel `Standard_A2` |
   | Offentliga IP-adressnamn      | Namnet på din offentliga IP-adress                                               |
   | Namn på virtuell dator                     | Namnet på den nya virtuella datorn                                                           |
   | Namn på virtuellt nätverk        | Namnet på det virtuella nätverket som används av den virtuella datorn                                   |
   | Namn på nätverkskort                    | Namnet på nätverkskortet som kör det virtuella nätverket               |
   | VHD-URL                     | Fullständig VHD URL-adress för OS-Disk                                                     |
   |  |  |
            
5. När du anger dessa värden klickar du på **köp**. 

Azure börjar distribution: den skapar en ny virtuell dator med den angivna ohanterade VHD i sökvägen för angivna lagringskontot.  Du kan följa förloppet i Azure portal genom att klicka på **virtuella datorer** till vänster i portalen.  När den virtuella datorn har skapats ändras status från `Starting` till `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Distribuera en virtuell dator från PowerShell

Använd följande cmdlets för att distribuera en stor virtuell dator från den generaliserade avbildningen som nyss skapade.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

<!-- TD: The following is a marketplace-publishing article and may be out-of-date.  TD: update and move topic.
For help with issues, see [Troubleshooting common issues encountered during VHD creation](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting) for additional assistance.
-->

## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har distribuerats kan du är redo att [konfigurera den virtuella datorn](./cpp-configure-vm.md).
