---
title: Distribuera virtuella datorer på din Azure Stack Edge Pro-enhet via mallar
description: 'Beskriver hur du skapar och hanterar virtuella datorer (VM: ar) på en Azure Stack Edge Pro-enhet med hjälp av mallar.'
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: d0d02532f39d676772e5ee5d6414b802faffba7c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505945"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Distribuera virtuella datorer på din Azure Stack Edge Pro GPU-enhet via mallar

I den här självstudien beskrivs hur du skapar och hanterar en virtuell dator på din Azure Stack Edge Pro-enhet med hjälp av mallar. Dessa mallar är JavaScript Object Notation-filer (JSON) som definierar infrastrukturen och konfigurationen för den virtuella datorn. I de här mallarna anger du de resurser som ska distribueras och egenskaperna för dessa resurser.

Mallar är flexibla i olika miljöer eftersom de kan ta parametrar som indata vid körning från en fil. Standard namngivnings strukturen är `TemplateName.json` för mallen och `TemplateName.parameters.json` för parameter filen. Mer information om ARM-mallar finns i [Azure Resource Manager mallar?](../azure-resource-manager/templates/overview.md).

I den här självstudien använder vi fördefinierade exempel mallar för att skapa resurser. Du behöver inte redigera mallfilen och du kan ändra bara `.parameters.json` filerna för att anpassa distributionen till datorn. 

## <a name="vm-deployment-workflow"></a>Arbets flöde för distribution av virtuell dator

Om du vill distribuera virtuella Azure Stack Edge Pro-datorer över många enheter, kan du använda en enda virtuell Sysprep-hårddisk för din fulla flotta, samma mall för distribution och bara göra mindre ändringar i parametrarna för den mallen för varje distributions plats (dessa ändringar kan göras manuellt, eller program mässigt). 

Den övergripande översikten över arbets flödet för distribution med hjälp av mallar är följande:

1. **Konfigurera krav** – det finns tre typer av krav; enhet, klient och för den virtuella datorn.

    1. **Enhets krav**

        1. Anslut till Azure Resource Manager på enheten.
        2. Aktivera beräkning via det lokala användar gränssnittet.

    2. **Klient krav**

        1. Ladda ned mallarna för virtuella datorer och tillhör ande filer på klienten.
        1. Du kan också konfigurera TLS 1,2 på klienten.
        1. [Hämta och installera Microsoft Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) på klienten.

    3. **Krav för virtuell dator**

        1. Skapa en resurs grupp på enhets platsen som innehåller alla VM-resurser.
        1. Skapa ett lagrings konto för att överföra den virtuella hård disk som används för att skapa en VM-avbildning.
        1. Lägg till den lokala lagrings kontots URI i DNS-eller hosts-filen på klienten som har åtkomst till din enhet.
        1. Installera Blob Storage-certifikatet på enheten samt på den lokala klienten som har åtkomst till din enhet. Du kan också installera Blob Storage-certifikatet på Storage Explorer.
        1. Skapa och ladda upp en virtuell hård disk till det lagrings konto som skapades tidigare.

2. **Skapa virtuell dator från mallar**

    1. Skapa en avbildning av en virtuell dator och ett VNet med hjälp av `CreateImageAndVnet.parameters.json` parameter filen och `CreateImageAndVnet.json` distributions mal len.
    1. Skapa en virtuell dator med tidigare skapade resurser med `CreateVM.parameters.json` parameter filen och  `CreateVM.json` distributions mal len.

## <a name="device-prerequisites"></a>Enhets krav

Konfigurera dessa krav på din Azure Stack Edge Pro-enhet.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Klient krav

Konfigurera de här förutsättningarna på klienten som ska användas för att få åtkomst till Azure Stack Edge Pro-enheten.

1. [Ladda ned Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om du använder den för att ladda upp en virtuell hård disk. Du kan också ladda ned AzCopy för att ladda upp en virtuell hård disk. Du kan behöva konfigurera TLS 1,2 på klient datorn om du kör äldre versioner av AzCopy. 
1. [Ladda ned filerna för VM-mallarna och-parametrarna](https://aka.ms/ase-vm-templates) till klient datorn. Zippa upp den till en katalog som du ska använda som arbets katalog.


## <a name="vm-prerequisites"></a>Krav för virtuell dator

Konfigurera dessa krav för att skapa resurser som behövs för att skapa virtuella datorer. 

    
### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). En resurs grupp är en logisk behållare där Azure-resurser, till exempel lagrings konto, disk, hanterad disk, distribueras och hanteras.

> [!IMPORTANT]
> Alla resurser skapas på samma plats som enheten och platsen är inställd på **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Ett exempel på utdata visas nedan.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett nytt lagrings konto med hjälp av resurs gruppen som skapades i föregående steg. Det här är ett **lokalt lagrings konto** som ska användas för att ladda upp den virtuella disk avbildningen för den virtuella datorn.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Endast lokala lagrings konton som lokalt redundant lagring (Standard_LRS eller Premium_LRS) kan skapas via Azure Resource Manager. Om du vill skapa lagrings konton på nivå, se stegen i [Lägg till, Anslut till lagrings konton på Azure Stack Edge Pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Ett exempel på utdata visas nedan.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Kör kommandot för att hämta lagrings konto nyckeln `Get-AzureRmStorageAccountKey` . Ett exempel på utdata från det här kommandot visas här.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Lägg till BLOB-URI i hosts-filen

Kontrol lera att du redan har lagt till BLOB-URI: n i hosts-filen för den klient som du använder för att ansluta till Blob Storage. **Kör anteckningar som administratör** och Lägg till följande post för BLOB-URI: n i `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

I en typisk miljö skulle du ha konfigurerat din DNS så att alla lagrings konton pekar på den Azure Stack Edge Pro-enheten med en `*.blob.devicename.domainname.com` post.

### <a name="optional-install-certificates"></a>Valfritt Installera certifikat

Hoppa över det här steget om du vill ansluta via Storage Explorer med *http*. Om du använder *https* måste du installera lämpliga certifikat i Storage Explorer. I det här fallet installerar du Blob-slutpunktens certifikat. Mer information finns i så här skapar du och laddar upp certifikat i [Hantera certifikat](azure-stack-edge-j-series-manage-certificates.md). 

### <a name="create-and-upload-a-vhd"></a>Skapa och ladda upp en VHD

Se till att du har en virtuell disk avbildning som du kan använda för att ladda upp i senare steg. Följ stegen i [skapa en avbildning av en virtuell dator](azure-stack-edge-j-series-create-virtual-machine-image.md). 

Kopiera eventuella disk avbildningar som ska användas i sid-blobar i det lokala lagrings kontot som du skapade i föregående steg. Du kan använda ett verktyg som [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) eller [AzCopy för att ladda upp den virtuella hård disken till lagrings kontot](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md#upload-a-vhd) som du skapade i tidigare steg. 

### <a name="use-storage-explorer-for-upload"></a>Använd Storage Explorer för uppladdning

1. Öppna Storage Explorer. Gå till **Redigera** och kontrol lera att programmet är inställt på **mål Azure Stack API: er**.

    ![Ange mål som Azure Stack-API: er](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Installera klient certifikatet i PEM-format. Gå till **redigera > SSL-certifikat > importera certifikat**. Peka på klient certifikatet.

    ![Importera slut punkts certifikat för Blob Storage](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Om du använder enhets genererade certifikat hämtar och konverterar du slut punkt `.cer` certifikatet för Blob Storage till ett `.pem` format. Kör följande kommando. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Om du tar med ditt eget certifikat använder du signerings kedjans rot certifikat i `.pem` formatet.

3. När du har importerat certifikatet startar du om Storage Explorer så att ändringarna börjar gälla.

    ![Starta om Storage Explorer](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. I det vänstra fönstret högerklickar du på **lagrings konton** och väljer **Anslut till Azure Storage**. 

    ![Anslut till Azure Storage 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Välj **Använd ett kontonamn och en nyckel för lagringen**. Välj **Nästa**.

    ![Anslut till Azure Storage 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. I **Anslut med namn och nyckel** anger du **visnings namn** , **lagrings konto namn** Azure Storage **konto nyckel**. Välj en **annan** lagrings domän och ange sedan `<device name>.<DNS domain>` anslutnings strängen. Om du inte har installerat ett certifikat i Storage Explorer, kontrollerar du alternativet **Använd http** . Välj **Nästa**.

    ![Anslut med namn och nyckel](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Granska **anslutnings sammanfattningen** och välj **Anslut**.

8. Lagrings kontot visas i den vänstra rutan. Välj och expandera lagrings kontot. Välj **BLOB-behållare** , högerklicka och välj **skapa BLOB-behållare**. Ange ett namn för din BLOB-behållare.

9. Välj den behållare som du nyss skapade och välj **Ladda upp > Ladda upp filer** i den högra rutan. 

    ![Ladda upp VHD-fil 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Bläddra och peka på den virtuella hård disk som du vill ladda upp i de **valda filerna**. Välj **Blob-typ** som **Page BLOB** och välj **Ladda upp**.

    ![Ladda upp VHD-fil 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. När den virtuella hård disken har lästs in i BLOB-behållaren väljer du den virtuella hård disken, högerklickar och väljer sedan **Egenskaper**. 

    ![Ladda upp VHD-fil 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Kopiera och spara **URI: n** eftersom du kommer att använda det i senare steg.

    ![Kopiera URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)

<!--### Use AzCopy for upload

Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge Pro device.


```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> ![NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->

## <a name="create-image-and-vnet-for-your-vm"></a>Skapa avbildning och VNet för din virtuella dator

Om du vill skapa en avbildning och ett virtuellt nätverk för den virtuella datorn måste du redigera `CreateImageAndVnet.parameters.json` parameter filen och sedan distribuera mallen `CreateImageAndVnet.json` som använder den här parameter filen.


### <a name="edit-parameters-file"></a>Redigera parameter fil

Filen `CreateImageAndVnet.parameters.json` tar följande parametrar: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM iamge>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
        "vnetName": {
            "value": "<Name for the virtual network where you will deploy the VM>"
        },
        "subnetName": {
            "value": "<Name for the subnet for the VNet>"
        },
        "addressPrefix": {
            "value": "<Address prefix for the virtual network>"
        },
        "subnetPrefix": {
            "value": "<Subnet prefix for the subnet for the Vnet>"
        }
    }
```

Redigera filen `CreateImageAndVnet.parameters.json` och Lägg till följande för din Azure Stack Edge Pro-enhet:

1. Ange vilken OS-typ som motsvarar den virtuella hård disk som du vill ladda upp. OS-typen kan vara Windows eller Linux.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Ändra avbildningens URI till URI: n för den avbildning som du laddade upp i det tidigare steget:

    ```json
    "imageUri": {
        "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
    ```
    Om du använder *http* med Storage Explorer ändrar du detta till en *https* -URI.

3. Ändra `addressPrefix` och `subnetPrefix` . I enhetens lokala användar gränssnitt går du till sidan **nätverk** . Hitta den port som du har aktiverat för beräkning. Hämta bas nätverkets IP-adress och Lägg till nät masken för att skapa CIDR-noteringen. Om du har ett standard-255.255.255.0-undernät kan du göra detta genom att ersätta det sista antalet IP-adresser med 0 och lägga till/24 i slutet. Det innebär att 10.126.68.0 med en 255.255.255.0 nät mask blir 10.126.68.0/24. 
    
    ```json
    "addressPrefix": {
                "value": "10.126.68.0/24"
            },
            "subnetPrefix": {
                "value": "10.126.68.0/24"
            }
    ```  

4. Ange det unika avbildnings namnet, VNet-namnet och under nätets namn för parametrarna.

    Här är ett exempel-JSON som används i den här artikeln.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Windows"
        },
        "imageName": {
          "value": "image1"
        },
        "imageUri": {
          "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
        "vnetName": {
          "value": "vnet1"
        },
        "subnetName": {
          "value": "subnet1"
        },
        "addressPrefix": {
          "value": "10.126.68.0/24"
        },
        "subnetPrefix": {
          "value": "10.126.68.0/24"
        }
      }
    }
    ```
5. Spara parameter filen.


### <a name="deploy-template"></a>Distribuera mallen 

Distribuera mallen `CreateImageAndVnet.json` . Den här mallen distribuerar VNet-och avbildnings resurserna som ska användas för att skapa virtuella datorer i senare steg.

> [!NOTE]
> När du distribuerar mallen om du får ett autentiseringsfel kan dina Azure-autentiseringsuppgifter för den här sessionen ha gått ut. Kör `login-AzureRM` kommandot igen för att ansluta till Azure Resource Manager på din Azure Stack Edge Pro-enhet igen.

1. Kör följande kommando: 
    
    ```powershell
    $templateFile = "Path to CreateImageAndVnet.json"
    $templateParameterFile = "Path to CreateImageAndVnet.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```

2. Kontrol lera om avbildningen och VNet-resurserna har kon figurer ATS. Här är ett exempel på utdata från en avbildning och VNet som skapats.
    
    ```powershell
    PS C:\07-30-2020> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1
    
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateImageAndVnet.json"
    PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateImageAndVnet.parameters.json"
    PS C:\07-30-2020> $RGName = "myasegpurgvm"
    PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment1"
    
    DeploymentName          : Deployment1
    ResourceGroupName       : myasegpurgvm
    ProvisioningState       : Succeeded
    Timestamp               : 7/30/2020 5:53:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Windows
                              imageName        String                     image1
                              imageUri         String
                              https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd
                              vnetName         String                     vnet1
                              subnetName       String                     subnet1
                              addressPrefix    String                     10.126.68.0/24
                              subnetPrefix     String                     10.126.68.0/24
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\07-30-2020>
    ```
    
## <a name="create-vm"></a>Skapa en virtuell dator

### <a name="edit-parameters-file-to-create-vm"></a>Redigera parameter filen för att skapa en virtuell dator
 
Skapa en virtuell dator med parameterfilen `CreateVM.parameters.json`. Det tar följande parametrar.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network you created earlier>"
        },
        "subnetName": {
            "value": "<Name for the subnet you created earlier>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Tilldela lämpliga parametrar i `CreateVM.parameters.json` för din Azure Stack Edge Pro-enhet.

1. Ange ett unikt namn, ett namn på ett nätverks gränssnitt och ett namn för ipconfig. 
1. Ange ett användar namn, lösen ord och en VM-storlek som stöds.
1. Ge samma namn för **VnetName** , **subnetName** och **ImageName** som anges i parametrarna för `CreateImageAndVnet.parameters.json` . Om du till exempel har angett VnetName, subnetName och ImageName som **vnet1** , **subnet1** och **image1** behåller du samma värden för parametrarna i den här mallen också.
1. Nu behöver du en statisk IP-adress för att tilldela den virtuella datorn som finns i det undernäts nätverk som anges ovan. Ersätt **PrivateIPAddress** med den här adressen i parameter filen. Lämna värdet tomt om du vill att den virtuella datorn ska få en IP-adress från din lokala DCHP-server `privateIPAddress` .  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Spara parameter filen.

    Här är ett exempel-JSON som används i den här artikeln.
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vmName": {
                "value": "mywindowsvm"
            },
            "adminUsername": {
                "value": "Administrator"
            },
            "Password": {
                "value": "Password1"
            },
            "imageName": {
                "value": "image1"
            },
            "vmSize": {
                "value": "Standard_D1_v2"
            },
            "vnetName": {
                "value": "vnet1"
            },
            "subnetName": {
                "value": "subnet1"
            },
            "nicName": {
                "value": "nic1"
            },
            "privateIPAddress": {
                "value": "10.126.68.186"
            },
            "IPConfigName": {
                "value": "ipconfig1"
            }
        }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Distribuera mall för att skapa en virtuell dator

Distribuera mallen för att skapa virtuella datorer `CreateVM.json` . Den här mallen skapar ett nätverks gränssnitt från det befintliga virtuella nätverket och skapar en virtuell dator från den distribuerade avbildningen.

1. Kör följande kommando: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    Det tar 15-20 minuter att skapa den virtuella datorn. Här är ett exempel på utdata från en korrekt skapad virtuell dator.
    
    ```powershell
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateWindowsVM.json"
        PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateWindowsVM.parameters.json"
        PS C:\07-30-2020> $RGName = "myasegpurgvm"
        PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
        >>     -ResourceGroupName $RGName `
        >>     -TemplateFile $templateFile `
        >>     -TemplateParameterFile $templateParameterFile `
        >>     -Name "Deployment2"    
        
        DeploymentName          : Deployment2
        ResourceGroupName       : myasegpurgvm
        ProvisioningState       : Succeeded
        Timestamp               : 7/30/2020 6:21:09 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                  Name             Type                       Value
                                  ===============  =========================  ==========
                                  vmName           String                     MyWindowsVM
                                  adminUsername    String                     Administrator
                                  password         String                     Password1
                                  imageName        String                     image1
                                  vmSize           String                     Standard_D1_v2
                                  vnetName         String                     vnet1
                                  subnetName       String                     subnet1
                                  nicName          String                     Nic1
                                  ipConfigName     String                     ipconfig1
                                  privateIPAddress  String                    10.126.68.186
        
        Outputs                 :
        DeploymentDebugLogLevel :    
        
        PS C:\07-30-2020>
    ```   

    Du kan också köra `New-AzureRmResourceGroupDeployment` kommandot asynkront med `–AsJob` parametern. Här är ett exempel på utdata när cmdleten körs i bakgrunden. Du kan sedan fråga statusen för jobbet som skapas med hjälp av `Get-Job` cmdleten.

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. Kontrol lera om den virtuella datorn har kon figurer ATS. Kör följande kommando:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Ansluta till en virtuell dator

Stegen för att ansluta kan vara olika beroende på om du har skapat ett Windows eller en virtuell Linux-dator.

### <a name="connect-to-windows-vm"></a>Anslut till virtuell Windows-dator

Följ dessa steg för att ansluta till en virtuell Windows-dator.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Anslut till virtuell Linux-dator

Följ dessa steg för att ansluta till en virtuell Linux-dator.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

<!--## Manage VM

The following section describes some of the common operations around the VM that you will create on your Azure Stack Edge Pro device.

[!INCLUDE [azure-stack-edge-gateway-manage-vm](../../includes/azure-stack-edge-gateway-manage-vm.md)]-->


## <a name="supported-vm-sizes"></a>VM-storlekar som stöds

[!INCLUDE [azure-stack-edge-gateway-supported-vm-sizes](../../includes/azure-stack-edge-gateway-supported-vm-sizes.md)]

## <a name="unsupported-vm-operations-and-cmdlets"></a>VM-åtgärder och cmdlets som inte stöds

Tillägg, skalnings uppsättningar, tillgänglighets uppsättningar, ögonblicks bilder stöds inte.

<!--## Configure AzCopy

When you install the latest version of AzCopy, you will need to configure AzCopy to ensure that it matches the blob storage REST API version of your Azure Stack Edge Pro device.

On the client used to access your Azure Stack Edge Pro device, set up a global variable to match the blob storage REST API version.

### On Windows client 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### On Linux client

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

To verify if the environment variable for AzCopy was set correctly, take the following steps:

1. Run "azcopy env".
2. Find `AZCOPY_DEFAULT_SERVICE_API_VERSION` parameter. This should have the value you set in the preceding steps.-->


## <a name="next-steps"></a>Nästa steg

[Azure Resource Manager-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
