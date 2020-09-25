---
title: Validering av avbildning av virtuella Azure-datorer – Azure Marketplace
description: Lär dig hur du testar och skickar ett erbjudande för virtuella datorer på den kommersiella marknads platsen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: 751fbbb83f1ccb75cb84453f8c03296f6d1a786c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275787"
---
# <a name="azure-virtual-machine-image-validation"></a>Validering av avbildning av virtuella Azure-datorer

Den här artikeln beskriver hur du testar och skickar en virtuell dator avbildning (VM) på den kommersiella Marketplace för att säkerställa att den uppfyller de senaste publicerings kraven för Azure Marketplace.

Slutför de här stegen innan du skickar ditt virtuella dator erbjudande:

- Distribuera en virtuell Azure-dator med hjälp av din generaliserade avbildning. Mer information om [generaliserade avbildningar](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-vm-technical-asset#generalize-the-image)finns här.
- Köra valideringar.

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Distribuera en virtuell Azure-dator med hjälp av din generaliserade avbildning

I det här avsnittet beskrivs hur du distribuerar en generaliserad virtuell hård disk avbildning (VHD) för att skapa en ny Azure VM-resurs. I den här processen använder vi den tillhandahållna Azure Resource Manager-mallen och Azure PowerShell skriptet.

### <a name="prepare-an-azure-resource-manager-template"></a>Förbereda en Azure Resource Manager mall

I det här avsnittet beskrivs hur du skapar och distribuerar en användardefinierad avbildning av en virtuell dator (VM). Du kan göra detta genom att tillhandahålla VHD-avbildningar för operativ system och data diskar från en Azure-distribuerad virtuell hård disk. De här stegen distribuerar den virtuella datorn med generaliserad virtuell hård disk.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Överför dina generaliserade operativ system diskar och datadisk-VHD: ar till ditt Azure Storage-konto.
3. På sidan start väljer du **skapa en resurs**, söker efter "mall distribution" och väljer **skapa**.
4. Välj **Bygg en egen mall i redigeraren**.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Visar valet av mall.":::

5. Klistra in följande JSON-mall i redigeraren och välj **Spara**.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
    "variables": {
        "addressPrefix": "10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix": "10.0.0.0/24",
        "subnet2Prefix": "10.0.1.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnet1Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet1Prefix')]"
                        }
                    },
                    {
                        "name": "[variables('subnet2Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet2Prefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnet1Ref')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "name": "[concat(parameters('vmName'),'-osDisk')]",
                        "osType": "[parameters('osType')]",
                        "caching": "ReadWrite",
                        "image": {
                            "uri": "[parameters('vhdUrl')]"
                        },
                        "vhd": {
                            "uri": "[variables('osDiskVhdName')]"
                        },
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2015-06-15",
            "name": "[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "fileUris": [
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd"
                    ],
                    "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. Ange parameter värden för de visade egenskaps sidorna för anpassad distribution.

| ResourceGroupName | Befintligt namn på Azure-resurs gruppen. Använd vanligt vis samma RG som nyckel valvet. |
| --- | --- |
| TemplateFile | Fullständig sökväg till filen VHDtoImage.jspå. |
| userStorageAccountName | Namn på lagringskontot. |
| dnsNameForPublicIP | DNS-namn för den offentliga IP-adressen; måste vara gemener. |
| subscriptionId | Prenumerations-ID för Azure. |
| Plats | Standard Azure-geografisk plats för resurs gruppen. |
| vmName | Namn på den virtuella datorn. |
| vhdUrl | Webb adressen för den virtuella hård disken. |
| vmSize | Storlek på den virtuella dator instansen. |
| publicIPAddressName | Namn på den offentliga IP-adressen. |
| virtualNetworkName | Namnet på det virtuella nätverket. |
| nicName | Nätverks gränssnitts kortets namn för det virtuella nätverket. |
| adminUserName | Användar namn för administratörs kontot. |
| adminPassword | Administratörs lösen ord. |
|

7. När du har angett dessa värden väljer du **köp**.

Azure kommer att börja distribuera. Den skapar en ny virtuell dator med den angivna ohanterade virtuella hård disken i den angivna sökvägen för lagrings kontot. Du kan följa förloppet i Azure Portal genom att välja **Virtual Machines** till vänster i portalen. När den virtuella datorn skapas kommer statusen att ändras från att börja köras.

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>Använd den här mallen för distribution av virtuella datorer i generation 2:

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>Distribuera en virtuell Azure-dator med PowerShell

Kopiera och redigera följande skript för att ange värden för `$storageaccount` `$vhdUrl` variablerna och. Kör den för att skapa en Azure VM-resurs från din befintliga generaliserade virtuella hård disk.

```PowerShell
# storage account of existing generalized VHD

$storageaccount = "testwinrm11815" # generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" - userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" - vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id -vhdUrl"$vhdUrl" - vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" - adminPassword$pwd
```

## <a name="run-validations"></a>Kör valideringar

Det finns två sätt att köra valideringar på den distribuerade avbildningen.

### <a name="use-certification-test-tool-for-azure-certified"></a>Använd certifierings test verktyget för Azure Certified

#### <a name="download-and-run-the-certification-test-tool"></a>Hämta och kör verktyget för certifierings test

Certifierings test verktyget för Azure Certified körs på en lokal Windows-dator men testar en Azure-baserad virtuell Windows-eller Linux-dator. Det certifierar att din användar avbildning av virtuella datorer kan användas med Microsoft Azure och att rikt linjerna och kraven kring att förbereda din virtuella hård disk är uppfyllda.

1. Hämta och installera det senaste [certifierings test verktyget för Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Öppna certifierings verktyget och välj sedan **Starta nytt test**.
3. På skärmen testa information anger du ett **testnamn** för test körningen.
4. Välj plattform för din virtuella dator, antingen **Windows Server** eller **Linux**. Valet av plattform påverkar de återstående alternativen.
5. Om den virtuella datorn använder den här databas tjänsten markerar du kryss rutan **test för Azure SQL Database** .

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Ansluta certifierings verktyget till en VM-avbildning

1. Välj SSH-autentiseringsläge: autentisering av lösen ord eller nyckel fil-autentisering.
2. Om du använder lösenordsbaserad autentisering anger du värden för den **virtuella datorns DNS-namn**, **användar namn**och **lösen ord**. Du kan också ändra standard-SSH-portnumret.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Visar valet av test information för virtuella datorer.":::

3. Om du använder nyckelbaserad autentisering anger du värden för den virtuella datorns DNS-namn, användar namn och plats för den privata nyckeln. Du kan också inkludera en lösen fras eller ändra standard-SSH-portnumret.
4. Ange det fullständigt kvalificerade DNS-namnet för virtuell dator (till exempel MyVMName.Cloudapp.net).
5. Ange **användar namn** och **lösen ord**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Visar valet av användar namn och lösen ord för virtuella datorer.":::

6. Välj **Nästa**.

#### <a name="run-a-certification-test"></a>Kör ett certifierings test

När du har fått parameter värden för din VM-avbildning i certifierings verktyget väljer du testa anslutning för att skapa en giltig anslutning till din virtuella dator. När en anslutning har verifierats väljer du **Nästa** för att starta testet. När testet är klart visas resultatet i en tabell. I kolumnen Status visas (pass/misslyckande/varning) för varje test. Om något av testerna inte fungerar är din avbildning inte godkänd. I det här fallet granskar du kraven och felen, gör de föreslagna ändringarna och kör testet igen.

När det automatiserade testet har slutförts, anger du ytterligare information om din VM-avbildning på de två flikarna i enkät skärmen, allmän utvärdering och automatisk distribution av kernel. Välj sedan Nästa.

På den sista skärmen kan du ange mer information, till exempel SSH Access-information för en virtuell Linux-avbildning och en förklaring till eventuella misslyckade utvärderingar om du söker efter undantag.

Välj slutligen skapa rapport för att ladda ned test resultaten och loggfilerna för de test ärenden som körts, tillsammans med dina svar till enkäten. 
> [!Note]
> Några utgivare har scenarier där virtuella datorer måste låsas eftersom de har program vara, till exempel brand väggar som är installerade på den virtuella datorn. I det här fallet kan utgivare Ladda ned det [certifierade testverktyget](https://aka.ms/AzureCertificationTestTool) här och tillhandahålla rapporten på [Marketplace Publisher-support](https://aka.ms/marketplacepublishersupport)

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Hur du använder PowerShell för att använda API: et för självtest

### <a name="on-linux-os"></a>På Linux OS

Anropa API: t i PowerShell:

1. Använd kommandot Invoke-WebRequest för att anropa API: et.
2. Metoden är post och Content-Type är JSON, som du ser i följande kod exempel och skärmdump.
3. Ange Body-parametrarna i JSON-format.

I följande exempel visas ett PowerShell-anrop till API: et:

```POWERSHELL
$accesstoken = “token”
$headers = New-Object “System.Collections.Generic.Dictionary[[String],[String]]”
$headers.Add(“Authorization”, “Bearer $accesstoken”)
$DNSName = “\&lt;\&lt;Machine DNS Name\&gt;\&gt;”
$UserName = “\&lt;\&lt;User ID\&gt;\&gt;”
$Password = “\&lt;\&lt;Password\&gt;\&gt;”
$OS = “Linux”
$PortNo = “22”
$CompanyName = “ABCD”
$AppID = “\&lt;\&lt;Application ID\&gt;\&gt;”
$TenantId = “\&lt;\&lt;Tenant ID\&gt;\&gt;”

$body =
@{
DNSName = $DNSName
UserName = $UserName
Password = $Password
OS = $OS
PortNo = $PortNo
CompanyName = $CompanyName
AppID = $AppID
TenantId = $TenantId
}| ConvertTo-Json

$body

$uri = “URL”

$res = (Invoke-WebRequest -Method “Post” -Uri $uri -Body $body -ContentType “application/json” -Headers $headers).Content
```

<br>Här är ett exempel på hur du anropar API: et i PowerShell:

[![Skärm exempel för att anropa API: et i PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>I föregående exempel kan du hämta JSON och parsa den för att få följande information:

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>Den här exempel skärmen, som visar `$res.Content` , visar information om dina test resultat i JSON-format:

[![Skärm exempel för att anropa API i PowerShell med information om test resultat.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Här är ett exempel på JSON-test resultat som visas i en online-JSON-visningsprogrammet (till exempel [Code beautify](https://codebeautify.org/jsonviewer) eller [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Fler test resultat i ett JSON-visningsprogram online.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>På Windows OS

Anropa API: t i PowerShell:

1. Använd kommandot Invoke-WebRequest för att anropa API: et.
2. Metoden är post och Content-Type är JSON, som du ser i följande kod exempel och exempel skärm.
3. Skapa Body-parametrarna i JSON-format.

I det här kod exemplet visas ett PowerShell-anrop till API: et:

```PowerShell
$accesstoken = “Get token for your Client AAD App”$headers = New-Object”System.Collections.Generic.Dictionary[[String],[String]]”$headers.Add(“Authorization”, “Bearer $accesstoken”)$Body = @{ “DNSName” = “XXXX.westus.cloudapp.azure.com”“UserName” = “XXX”“Password” = “XXX@123456”“OS” = “Windows”“PortNo” = “5986”“CompanyName” = “ABCD” “AppID” = “XXXX-XXXX-XXXX” “TenantId” = “XXXX-XXXX-XXXX” } | ConvertTo-Json$res = Invoke-WebRequest -Method”Post” -Uri$uri -Body$Body -ContentType”application/json” –Headers $headers;$Content = $res | ConvertFrom-Json
```

Följande exempel skärmar visar exempel för att anropa API: et i PowerShell:

**Med SSH-nyckel**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Anropar API: et i PowerShell med en SSH-nyckel.":::

**Med lösen ord**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Anropar API: et i PowerShell med ett lösen ord.":::

<br>I föregående exempel kan du hämta JSON och parsa den för att få följande information:

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>Den här skärmen, som visar `$res.Content` , visar information om dina test resultat i JSON-format:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Information om test resultat i JSON-format.":::

<br>Här är ett exempel på test resultat som visas i en online-JSON-visningsprogrammet (till exempel [Code beautify](https://codebeautify.org/jsonviewer) eller [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Test resultat i ett online-JSON-visningsprogram.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Så här använder du en sväng för att använda API: et för självtest på Linux OS

Anropa API: t i sväng:

1. Använd kommandot vänd för att anropa API: et.
2. Metoden är post och Content-Type är JSON, vilket visas i följande kodfragment.

```JSON
CURL POST -H “Content-Type:application/json”

-H “Authorization: Bearer XXXXXX-Token-XXXXXXXX”

[https://isvapp.azure-api.net/selftest-vm](https://isvapp.azure-api.net/selftest-vm)

-d ‘{ “DNSName”:”XXXX.westus.cloudapp.azure.com”, “UserName”:”XXX”, “Password”:”XXXX@123456”, “OS”:”Linux”, “PortNo”:”22”, “CompanyName”:”ABCD”, “AppId”:”XXXX-XXXX-XXXX”, “TenantId “XXXX-XXXX-XXXX”}’
```

<br>Här är ett exempel på hur du använder en sväng för att anropa API: et:

![Exempel på hur du använder sväng för att anropa API: et.](media/vm/use-curl-call-api.png)

<br>Här är JSON-resultatet från det TYPOGRAFISKa anropet:

![JSON-resultat från ett spiral samtal.](media/vm/test-results-json-viewer-3.png)

## <a name="next-step"></a>Nästa steg

- Läs [vanliga problem och korrigeringar för SAS-URI](common-sas-uri-issues.md).
