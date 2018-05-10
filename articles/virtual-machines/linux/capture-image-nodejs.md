---
title: Avbilda en virtuell Azure Linux-dator ska användas som en mall | Microsoft Docs
description: Lär dig mer om att fånga och generalisera en avbildning av en Linux-baserade Azure virtuell dator (VM) skapats med Azure Resource Manager-distributionsmodellen.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 3b45f46197467dd7b83bd986604338e14daa8107
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Avbilda en Linux-dator som körs på Azure
Följ stegen i den här artikeln för att generalisera och avbilda dina Azure Linux-dator (VM) i Resource Manager-distributionsmodellen. När du generaliserar den virtuella datorn, ta bort personlig information och förbereda den virtuella datorn som ska användas som en bild. Du sedan avbilda en generaliserad virtuell hårddisk (VHD) avbildas för Operativsystemet, virtuella hårddiskar för bifogade datadiskar och en [Resource Manager-mall](../../azure-resource-manager/resource-group-overview.md) för nya VM-distributioner. Den här artikeln beskriver hur du hämta en datoravbildning av virtuell med Azure CLI 1.0 för en virtuell dator med hjälp av ohanterade diskar. Du kan också [avbilda en virtuell dator i Azure hanterade diskar med Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Hanterade diskar hanteras av Azure-plattformen och behöver inte alla förberedelse eller plats att lagra dem. Mer information finns i [Översikt över Azure Managed Disks](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Ställ in nätverksresurser för varje ny virtuell dator för att skapa virtuella datorer med hjälp av avbildningen, och Använd mall (en JavaScript Object Notation eller JSON,-fil) för att distribuera från de VHD-avbildningarna. På så sätt kan replikera du en virtuell dator med dess aktuella programvarukonfiguration, ungefär samma sätt som du använder avbildningar i Azure Marketplace.

> [!TIP]
> Om du vill skapa en kopia av din befintliga Linux VM med det särskilda tillståndet för säkerhetskopiering eller felsökning, se [skapar en kopia av en Linux-dator som körs på Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Och om du vill överföra en Linux VHD från en lokal virtuell dator finns [överför och skapa en Linux VM från anpassade diskavbildning](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#before-you-begin) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – vår nästa generations CLI för distributionsmodellen resurshantering

## <a name="before-you-begin"></a>Innan du börjar
Se till att du uppfyller följande krav:

* **Azure VM som skapats i Resource Manager-distributionsmodellen** -om du inte har skapat en Linux VM, kan du använda den [portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), eller [Resource Manager-mallar ](create-ssh-secured-vm-from-template.md). 
  
    Konfigurera den virtuella datorn. Till exempel [lägga till datadiskar](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), tillämpa uppdateringar och installera program. 
* **Azure CLI** -installera den [Azure CLI](../../cli-install-nodejs.md) på en lokal dator.

## <a name="step-1-remove-the-azure-linux-agent"></a>Steg 1: Ta bort Azure Linux-agent
Kör först den **waagent** kommandot med de **avetablering** parameter på Linux-VM. Det här kommandot tar bort filer och data så att den virtuella datorn redo för att generalisera. Mer information finns i [Azure Linux-agenten användarhandboken](../extensions/agent-windows.md).

1. Ansluta till din Linux VM som använder en SSH-klient.
2. I fönstret SSH skriver du följande kommando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Endast köra kommandot på en virtuell dator som du vill samla in som en bild. Är det inte säkert att avbildningen är avmarkerad av all känslig information eller lämpar sig för omfördelning.
 
3. Typen **y** att fortsätta. Du kan lägga till den **-tvinga** parametern för att undvika det här steget bekräftelse.
4. När kommandot har slutförts genom att skriva **avsluta**. Det här steget stänger SSH-klienten.

## <a name="step-2-capture-the-vm"></a>Steg 2: Skapa den virtuella datorn
Använda Azure CLI för att generalisera och avbilda den virtuella datorn. Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar **myResourceGroup**, **myVnet**, och **myVM**.

1. Öppna Azure CLI från den lokala datorn och [logga in på Azure-prenumerationen](/cli/azure/authenticate-azure-cli). 
2. Kontrollera att du är i läget Resource Manager.
   
    ```azurecli
    azure config mode arm
    ```
3. Stänga av den virtuella datorn som du redan avetableras med hjälp av följande kommando:
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. Generalisera den virtuella datorn med följande kommando:
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. Nu köra den **azure vm-avbildning** kommandot, som samlar in den virtuella datorn. I följande exempel avbildningen virtuella hårddiskar till med namn som börjar med **MyVHDNamePrefix**, och **-t** alternativet anger en sökväg till mallen **MyTemplate.json**. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > VHD-avbildningsfiler skapas som standard i samma lagringskonto som används för den ursprungliga virtuella datorn. Använd den *samma lagringskonto* att lagra de virtuella hårddiskarna för alla nya virtuella datorer som du skapar från avbildningen. 

6. Öppna JSON-mall för att hitta platsen för en avbildning i en textredigerare. I den **storageProfile**, hitta den **uri** av den **bild** finns i den **system** behållare. Till exempel liknar URI för diskavbildning OS `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Steg 3: Skapa en virtuell dator från avbildningen
Nu använda avbildningen med en mall för att skapa en Linux VM. Dessa steg visar hur du använder Azure CLI och JSON-fil-mall som du hämtat för att skapa den virtuella datorn i ett nytt virtuellt nätverk.

### <a name="create-network-resources"></a>Skapa nätverksresurser
Om du vill använda en mall måste du först konfigurera ett virtuellt nätverk och nätverkskort för den nya virtuella datorn. Vi rekommenderar att du skapar en resursgrupp för dessa resurser i den plats där VM-avbildning lagrad. Kör kommandon som liknar följande, ersätta namnen för dina resurser och en lämplig Azure plats (”centralus” i de här kommandona):

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>Hämta Id för nätverkskortet
Om du vill distribuera en virtuell dator från avbildningen med hjälp av JSON som du sparade under hämtning måste Id för nätverkskortet. Skaffa det genom att köra följande kommando:

```azurecli
azure network nic show myResourceGroup1 myNIC
```

Den **Id** i utdata liknar `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`

### <a name="create-a-vm"></a>Skapa en virtuell dator
Nu ska du köra följande kommando för att skapa den virtuella datorn från den VM-avbildningen. Använd den **-f** parametern för att ange sökvägen till mallen JSON-fil som du sparade.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

Kommandots utdata uppmanas du att ange ett nytt VM-namn, admin-användarnamn och lösenord och Id för det nätverkskort som du skapade tidigare.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

I följande exempel visar vad som visas för en lyckad distribution:

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>Kontrollera distributionen
Nu SSH till den virtuella datorn som du skapade för att kontrollera distributionen och börja använda den nya virtuella datorn. Hitta IP-adressen för den virtuella datorn som du skapade genom att köra följande kommando för att ansluta via SSH:

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

Den offentliga IP-adressen visas i kommandot. Som standard ansluter till Linux-VM av SSH på port 22.

## <a name="create-additional-vms"></a>Skapa ytterligare virtuella datorer
Använd den fångade avbildningen och mallen för att distribuera ytterligare virtuella datorer med hjälp av stegen i föregående avsnitt. Andra alternativ för att skapa virtuella datorer från avbildningen är en mall i Snabbstart eller körs den **azure vm skapa** kommando.

### <a name="use-the-captured-template"></a>Använda mallen avbildade
Så här (beskrivs i föregående avsnitt) för att använda den fångade avbildningen och mallen:

* Se till att VM-avbildning finns i samma lagringskonto som är värd för den Virtuella datorns virtuella Hårddisken.
* Kopiera mallen JSON-fil och ange ett unikt namn för OS-disken på den nya VM VHD (eller virtuella hårddiskar). Till exempel i den **storageProfile**under **vhd**i **uri**, ange ett unikt namn för den **osDisk** VHD som liknar `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Skapa ett nätverkskort i samma eller ett annat virtuellt nätverk.
* Skapa en distribution i resursgruppen som du ställer in det virtuella nätverket med ändrade mallen JSON-fil.

### <a name="use-a-quickstart-template"></a>Använd en mall för Snabbstart
Om du vill att nätverket ställa in automatiskt när du skapar en virtuell dator från avbildningen kan du ange resurserna i en mall. Se exempelvis den [101-vm-från--användaravbildning mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) från GitHub. Den här mallen skapar en virtuell dator från den anpassade avbildningen och nödvändiga virtuella nätverk, offentlig IP-adress och NIC-resurser. En genomgång av mallen i Azure portal finns [hur du skapar en virtuell dator från en anpassad avbildning med hjälp av en Resource Manager-mall](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Använd azure vm skapa kommando
Vanligtvis är det enklast att använda en Resource Manager-mall för att skapa en virtuell dator från avbildningen. Du kan dock skapa den virtuella datorn *imperatively* med hjälp av den **azure vm skapa** kommandot med den **-Q** (**--bild urn**) parametern. Om du använder den här metoden kan du också ange den **-d** (**--os-disk-vhd**) parameter för att ange platsen för OS VHD-filen för den nya virtuella datorn. Den här filen måste vara i behållaren virtuella hårddiskar för det lagringskonto där VHD-avbildningsfilen lagras. Kommandot kopierar VHD för den nya virtuella datorn automatiskt till den **virtuella hårddiskar** behållare.

Innan du kör **azure vm skapa** med avbildningen, gör du följande:

1. Skapa en resursgrupp eller identifiera en befintlig resursgrupp för distributionen.
2. Skapa en offentlig IP-adressresurs och en resurs för nätverkskort för den nya virtuella datorn. Steg för att skapa ett virtuellt nätverk, offentlig IP-adress och NIC med hjälp av CLI, finns i tidigare i den här artikeln. (**azure vm skapa** kan också skapa ett nätverkskort, men du måste lägga till extraparametrar för ett virtuellt nätverk och undernät.)

Kör ett kommando som klarar URI: er för både den nya OS VHD-filen och den befintliga avbildningen. I det här exemplet en storlek Standard_A1 VM har skapats i östra USA.

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

Ytterligare alternativ för, kör `azure help vm create`.

## <a name="next-steps"></a>Nästa steg
För att hantera dina virtuella datorer med CLI, se uppgifterna i [distribuera och hantera virtuella datorer med hjälp av Azure Resource Manager-mallar och Azure CLI](create-ssh-secured-vm-from-template.md).

