---
title: Virtuell test dator (VM) distribuerad från VHD – Azure Marketplace
description: Lär dig hur du testar och skickar ett erbjudande för virtuella datorer på den kommersiella marknads platsen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 07/29/2020
ms.openlocfilehash: 36c497a180070358332997649e768999c78935cb
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433102"
---
# <a name="test-virtual-machine-vm-deployed-from-vhd"></a>Virtuell test dator (VM) distribuerad från virtuell hård disk

Den här artikeln beskriver hur du distribuerar och testar en virtuell Azure-dator (VM) från den generaliserade VHD-avbildningen som skapades i föregående avsnitt ([Skapa Azure VM Technical Asset)](create-azure-vm-technical-asset.md) för att säkerställa att VHD-avbildningen uppfyller Azure Marketplace-publicerings kraven.

Slutför de här stegen för att skapa en kompatibilitetsrapport, som certifierar att VHD-avbildningen kan användas på Azure Marketplace.

1. Skapa och distribuera certifikat som krävs för fjärrhantering av virtuell dator för att Azure Key Vault.
2. Distribuera en virtuell Azure-dator från din generaliserade VHD-avbildning som skapats i [skapa teknisk till gång till Azure VM](create-azure-vm-technical-asset.md).
3. Kör tester på den distribuerade virtuella datorn för att se till att VHD-avbildningen är klar att publiceras och används för att distribuera virtuella datorer.

## <a name="running-scripts"></a>Skript körs

Den här artikeln innehåller tre skript som ska köras i PowerShell. Desktop PowerShell fungerar bäst, men Azure Cloud Shell kan också användas med PowerShell-alternativet valt (längst upp till vänster i fönstret).

1. Se till att PowerShell har kon figurer ATS för att köra skript.

    - Öppna alltid PowerShell med alternativet **Kör som administratör** .
    - Se till att du kan köra dessa skript: `Set-ExecutionPolicy` och `RemoteSigned` .

2. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Installera Azure PowerShell AZ-modulen.
    1. **Alternativ A**: inga moduler har installerats än.
        - `Install-Module -Name Az -AllowClobber -Scope AllUsers`

        Mer information finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.2.0).

    2. **Alternativ B**: för närvarande använder AzureRM-modulen.

        - Avinstallera-AzureRM
        - Installera-Modulnamn-Name AZ-AllowClobber-scope AllUsers
        - Enable-AzureRmAlias-scope CurrentUser

        Mer information finns i [migrera Azure PowerShell från AzureRM till AZ](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-4.2.0).

4. Spara parametrar för sessionen.

Skripten i det här avsnittet använder sessionsvariabler/parametrar. Om du stänger sessionen raderas parametrarna. Vi rekommenderar att du använder en session för att köra alla skript för att undvika parameter värdes fel. Om detta inte är möjligt måste du initiera om parametrarna när du öppnar en ny session, särskilt för senare skript.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Skapa och distribuera certifikat för Azure Key Vault

I det här avsnittet beskrivs hur du skapar och distribuerar de självsignerade certifikat som krävs för att konfigurera Windows Remote Management (WinRM)-anslutning till en virtuell Azure-värd dator.

### <a name="create-certificates-for-azure-key-vault"></a>Skapa certifikat för Azure Key Vault

Den här processen består av tre steg:

1. Skapa säkerhetscertifikatet.
2. Skapa Azure Key Vault för att lagra certifikatet.
3. Lagra certifikaten i nyckel valvet.

Du kan använda antingen en ny eller en befintlig Azure-resurs grupp för detta arbete.

#### <a name="create-the-security-certificate"></a>Skapa säkerhetscertifikatet

Kör det här skriptet för att skapa certifikat filen (. pfx) i en lokal mapp. Certifikatet tillhör den planerade virtuella Azure-datorn som kommer att distribueras från VHD-avbildningen. Den virtuella datorn måste ha ett namn, en plats och ett lösen ord som anges av skript parametrarna. Redigera följande skript för att skapa Azure PowerShell- **certifiering** för att ange rätt värden för skript parametrarna som visas i tabellen.

| **Parameter** | **Beskrivning** |
| --- | --- |
| $certroopath | Lokal mapp där PFX-filen ska sparas. |
| $location | En av de geografiska standard platserna för Azure. |
| $vmName | Namnet på den planerade virtuella Azure-datorn. |
| $certname | Certifikatets namn. måste matcha det fullständigt kvalificerade domän namnet för den planerade virtuella datorn. |
| $certpassword | Lösen ordet för certifikaten måste matcha det lösen ord som används för den planerade virtuella datorn. |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
    $location = "westus"

    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"

    # Certification name - should match with FQDN of Windows Azure creating VM
    $certname = "$vmName.$location.cloudapp.azure.com"

    # Certification password - should be match with password of Windows Azure creating VM
    $certpassword = "SecretPassword@123"

    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb

```

> [!TIP]
> Se till att samma Azure PowerShell-konsolsession är öppen och körs under de här stegen för att behålla värdena för de olika parametrarna.

> [!WARNING]
> Om du sparar skriptet ska du bara spara det på en säker plats eftersom det innehåller säkerhets information (ett lösen ord).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Skapa Azure Key Vault för att lagra certifikatet

Kopiera innehållet i mallen nedan till en fil på den lokala datorn. I exempel skriptet nedan är den här resursen `C:\certLocation\keyvault.json` ).

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

Redigera och kör följande Azure PowerShell skript för att skapa en Azure Key Vault och den tillhör ande resurs gruppen. Ersätt värdena för parametrarna som visas i följande tabell

| **Parameter** | **Beskrivning** |
| --- | --- |
| $postfix | Slumpmässig numerisk sträng som är kopplad till distributions identifierare. |
| $rgName | Namn på Azure Resource Group (RG) som ska skapas. |
| $location | En av de geografiska standard platserna för Azure. |
| $kvTemplateJson | Sökvägen till filen (keyvault.jspå) som innehåller Resource Manager-mall för nyckel valv. |
| $kvname | Namnet på det nya nyckel valvet.|
|   |   |

```PowerShell
# Creating Key vault in resource group

    # "Random" number for deployment identifiers
    $postfix = "0101048"

    # Resource group name
    $rgName = "TestRG$postfix"

    # Location of Resource Group
    $location = "westus"

    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"

    # Key vault name
    $kvname = "isvkv$postfix"

    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzContext
        $accountNum = 0
        $accounts.Account | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host

        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }

        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Account

        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id).Id
      }
      catch
      {
      Write-Host $_.Exception.Message
      Break
      }

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].Name)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host

        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].Name)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].Id
        $mysubName=$subslist[$selectedsub-1].Name
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message
        Break
        }

    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     az group create --name $rgName --location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys Decrypt,Encrypt,UnwrapKey,WrapKey,Verify,Sign,Get,List,Update,Create,Import,Delete,Backup,Restore,Recover,Purge -PermissionsToSecrets Get,List,Set,Delete,Backup,Restore,Recover,Purge

```

#### <a name="store-the-certificates-to-the-key-vault"></a>Lagra certifikaten i nyckel valvet

Lagra certifikaten som finns i. pfx-filen till det nya nyckel valvet med det här skriptet:

```PowerShell
 $fileName =$certroopath+"\$certname"+".pfx"

     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-from-your-generalized-vhd-image"></a>Distribuera en virtuell Azure-dator från den generaliserade VHD-avbildningen

I det här avsnittet beskrivs hur du distribuerar en generaliserad VHD-avbildning för att skapa en ny Azure VM-resurs. I den här processen använder vi den tillhandahållna Azure Resource Manager-mallen och Azure PowerShell skriptet.

### <a name="prepare-an-azure-resource-manager-template"></a>Förbereda en Azure Resource Manager mall

Kopiera någon av följande Azure Resource Manager mallar för VHD-distribution (antingen för Windows eller Linux) till en lokal fil med namnet VHDtoImage.jspå. Nästa skript kommer att begära platsen på den lokala datorn att använda denna JSON.

#### <a name="for-windows-based-vms"></a>För Windows-baserade virtuella datorer

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
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
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
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
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
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
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
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
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
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
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
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
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

#### <a name="for-linux-based-vms"></a>För Linux-baserade virtuella datorer

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "linux",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
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
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
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
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
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
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
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
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
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
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
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
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": false,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Kopiera och redigera följande skript för att ange värden för dessa parametrar:

| **Parameter** | **Beskrivning** |
| --- | --- |
| ResourceGroupName | Befintligt namn på Azure-resurs gruppen. Använd vanligt vis samma RG som nyckel valvet. |
| TemplateFile | Fullständig sökväg till filen VHDtoImage.jspå. |
| userStorageAccountName | Namn på lagringskontot. |
| sNameForPublicIP | DNS-namn för den offentliga IP-adressen; måste vara gemener. |
| subscriptionId | Prenumerations-ID för Azure. |
| Position | Standard Azure-geografisk plats för resurs gruppen. |
| vmName | Namn på den virtuella datorn. |
| vaultName | Nyckel valvets namn. |
| vaultResourceGroup | Nyckel valvets resurs grupp. |
| certificateUrl | Webb adress (URL) för certifikatet, inklusive version som lagras i nyckel valvet, till exempel: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` . |
| vhdUrl | Webb adressen för den virtuella hård disken. |
| vmSize | Storlek på den virtuella dator instansen. |
| publicIPAddressName | Namn på den offentliga IP-adressen. |
| virtualNetworkName | Namnet på det virtuella nätverket. |
| nicName | Nätverks gränssnitts kortets namn för det virtuella nätverket. |
| adminUserName | Användar namn för administratörs kontot. |
| adminPassword | Administratörs lösen ord. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Distribuera en virtuell Azure-dator

Kopiera och redigera följande skript för att ange värden för `$storageaccount` `$vhdUrl` variablerna och. Kör den för att skapa en Azure VM-resurs från din befintliga generaliserade virtuella hård disk.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

# Full pathname to the file VHDtoImage.json. inserted these highlighted lines
$templateFile = "$certroopath\VHDtoImage.json"

# Size of the virtual machine instance.
$vmSize = "Standard_D2s_v3"

# Name of the public IP address.
$publicIPAddressName = "myPublicIP1"

# Name of the virtual network
$virtualNetworkName = "myVNET1"

# Name of the network interface card for the virtual network
$nicName = "myNIC1"

# Username of the administrator account
$adminUserName = "isv"

# The OS of the virtual machine
$osType = "windows"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile $templateFile -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "$vmSize" -publicIPAddressName "$publicIPAddressName" -virtualNetworkName "$virtualNetworkName" -nicName "$nicName" -adminUserName "$adminUserName" -adminPassword $pwd -osType "$osType""

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile $templateFile -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "$vmSize" -publicIPAddressName "$publicIPAddressName" -virtualNetworkName "$virtualNetworkName" -nicName "$nicName" -adminUserName "$adminUserName" -adminPassword $pwd -osType "$osType"

```

## <a name="run-tests-on-the-deployed-vm"></a>Köra tester på den distribuerade virtuella datorn

### <a name="download-and-run-the-certification-test-tool"></a>Hämta och kör verktyget för certifierings test

Certifierings test verktyget för Azure Certified är ett självtests verktyg som körs på en lokal Windows-dator, men testar en Azure-baserad virtuell Windows-eller Linux-dator. Det certifierar att din användar avbildning av virtuella datorer kan användas med Microsoft Azure och att rikt linjerna och kraven kring att förbereda din virtuella hård disk är uppfyllda. Det här verktyget ser till att den virtuella datorn är redo att publicera per Azure Marketplace-krav. "

1. Hämta och installera det senaste [certifierings test verktyget för Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Öppna certifierings verktyget och välj sedan **Starta nytt test**.
3. På skärmen **testa information** anger du ett **testnamn** för test körningen.
4. Välj **plattform** för din virtuella dator, antingen Windows Server eller Linux. Valet av plattform påverkar de återstående alternativen.
5. Om den virtuella datorn använder den här databas tjänsten markerar du kryss rutan **test för Azure SQL Database** .

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Ansluta certifierings verktyget till en VM-avbildning

Verktyget ansluter till Windows-baserade virtuella datorer med [Azure PowerShell](https://docs.microsoft.com/powershell/) och ansluter till virtuella Linux-datorer via [SSH.net](https://www.ssh.com/ssh/protocol/). Välj något av följande två alternativ, antingen Linux eller Windows.

#### <a name="option-1-connect-the-certification-tool-to-a-linux-vm-image"></a>Alternativ 1: Anslut certifierings verktyget till en virtuell Linux-avbildning

1. Välj **SSH-autentiseringsläge** : autentisering av lösen ord eller nyckel fil-autentisering.
2. Om du använder lösenordsbaserad autentisering anger du värden för den **virtuella datorns DNS-namn**, **användar namn**och **lösen ord**. Du kan också ändra standard- **SSH-portnumret** .

    ![Azure Certified Test Tool, lösenordsautentisering för VM-avbildningen i Linux](media/avm-cert2.png)

3. Om du använder nyckelbaserad autentisering anger du värden för den **virtuella datorns DNS-namn**, **användar namn**och plats för den **privata nyckeln** . Du kan också inkludera en **lösen fras** eller ändra standard- **SSH-portnumret** .

#### <a name="option-2-connect-the-certification-tool-to-a-windows-based-vm-image"></a>Alternativ 2: Anslut certifierings verktyget till en Windows-baserad VM-avbildning

1. Ange det fullständigt kvalificerade **DNS-namnet för virtuell dator** (till exempel MyVMName.cloudapp.net).
2. Ange värden för **användar namn** och **lösen ord**.

    ![Azure Certified Test Tool, lösenordsautentisering för Windows-baserad VM-avbildning](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Kör ett certifierings test

När du har fått parameter värden för din VM-avbildning i certifierings verktyget väljer du **Testa anslutning** för att skapa en giltig anslutning till din virtuella dator. När en anslutning har verifierats väljer du **Nästa** för att starta testet. När testet är klart visas test resultatet i en tabell. I kolumnen Status visas (pass/misslyckande/varning) för varje test. Om något av testerna inte fungerar är din avbildning _inte_ godkänd. I det här fallet granskar du kraven och felen, gör de föreslagna ändringarna och kör testet igen.

När det automatiserade testet har slutförts, anger du ytterligare information om din VM-avbildning på de två flikarna i **enkät** skärmen, **allmän utvärdering** och automatisk distribution av **kernel**. Välj sedan **Nästa**.

På den sista skärmen kan du ange mer information, till exempel SSH Access-information för en virtuell Linux-avbildning och en förklaring till eventuella misslyckade utvärderingar om du söker efter undantag.

Välj slutligen **Skapa rapport** för att ladda ned test resultaten och loggfilerna för de test ärenden som körts, tillsammans med dina svar till enkäten. Spara resultatet i samma behållare som dina virtuella hård diskar.

## <a name="next-step"></a>Nästa steg

- [Vanliga SAS URI-problem och korrigeringar](common-sas-uri-issues.md)
