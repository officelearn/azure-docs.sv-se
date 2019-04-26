---
title: Sluta använda VM-tillägget hanterad identitet och börja använda Azure Instance Metadata Service-slutpunkt
description: Steg för steg-anvisningar för att sluta använda VM-tillägget och börja använda Azure Instance Metadata Service (IMDS) för autentisering.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 5b3c6c99b05320ee53c3ff49f5c299650c32e939
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440839"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Hanterad identiteter tillägget och börja använda Azure Instance Metadata Service att sluta använda den virtuella datorn

## <a name="virtual-machine-extension-for-managed-identities"></a>Tillägg för virtuell dator för hanterade identiteter

Tillägget för virtuell dator för hanterade identiteter för att begära token för en hanterad identitet i den virtuella datorn. Arbetsflödet består av följande steg:

1. Först arbetsbelastningen inom resursen anropar den lokala slutpunkten `http://localhost/oauth2/token` begär en åtkomsttoken.
2. Tillägget för virtuell dator använder sedan autentiseringsuppgifterna för den hanterade identitet för att begära en åtkomsttoken från Azure AD... 
3. Åtkomst-token returneras till anroparen och kan användas för att autentisera till tjänster som stöder Azure AD-autentisering, t.ex. Azure Key Vault eller Azure Storage.

På grund av flera begränsningar som beskrivs i nästa avsnitt, har hanterad identitet VM-tillägget ersatts av med hjälp av motsvarande slutpunkt i Azure Instance Metadata Service (IMDS)

### <a name="provision-the-extension"></a>Etablera tillägget 

När du konfigurerar en virtuell dator eller en VM-skalningsuppsättning ha en hanterad identitet, du kan valfritt välja att du kan välja att etablera hanterade identiteter för Azure-resurser VM en tillägget med hjälp av den `-Type` parametern på [ Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) cmdlet. Du kan skicka antingen `ManagedIdentityExtensionForWindows` eller `ManagedIdentityExtensionForLinux`, beroende på vilken typ av virtuell dator och namnge den med hjälp av den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Du kan också använda mallen för distribution av Azure Resource Manager för att etablera VM-tillägget genom att lägga till följande JSON till det `resources` avsnitt i mallen (Använd `ManagedIdentityExtensionForLinux` för elementen namn och typ för Linux-versionen).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Om du arbetar med VM-skalningsuppsättningar kan du också etablera hanterade identiteter för Azure-resurser VM-skalningsuppsättningen tillägget med hjälp av den [Lägg till AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet. Du kan skicka antingen `ManagedIdentityExtensionForWindows` eller `ManagedIdentityExtensionForLinux`beroende på vilken typ av VM-skalningsuppsättning ställa in och använda den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Ange tillägg med distributionsmallen Azure Resource Manager-att etablera VM-skalningsuppsättningen lägger du till följande JSON till det `extensionpProfile` avsnitt i mallen (Använd `ManagedIdentityExtensionForLinux` för elementen namn och typ för Linux-versionen).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

Etablering av tillägget för virtuell dator kan misslyckas på grund av DNS-sökning fel. Om detta inträffar kan du starta om den virtuella datorn och försök igen. 

### <a name="remove-the-extension"></a>Ta bort tillägget 
Ta bort tillägget med `-n ManagedIdentityExtensionForWindows` eller `-n ManagedIdentityExtensionForLinux` växla (beroende på typ av virtuell dator) med [az vm-tillägget delete](https://docs.microsoft.com/cli/azure/vm/), eller [az vmss tillägget delete](https://docs.microsoft.com/cli/azure/vmss) för VM-skalningsuppsättning Anger med hjälp av Azure CLI eller `Remove-AzVMExtension` för Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Hämta en token med hjälp av tillägget för virtuell dator

Följande är ett exempel på begäran med hjälp av hanterade identiteter för Azure-resurser slutpunkten för VM-tillägget:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet, vilket innebär att du vill hämta data från slutpunkten. I det här fallet en OAuth åtkomsttoken. | 
| `http://localhost:50342/oauth2/token` | Hanterade identiteter för Azure-resurser slutpunkten, där 50342 är standardporten och kan konfigureras. |
| `resource` | En frågesträngsparameter som anger App-ID-URI för målresursen. Den visas också i de `aud` (målgrupp) anspråk i utfärdade token. Det här exemplet begär en token för att få åtkomst till Azure Resource Manager, som har en App-ID-URI för https://management.azure.com/. |
| `Metadata` | En HTTP-begäran med huvud anger krävs av hanterade identiteter för Azure-resurser som en minskning mot angrepp Server sida begäran förfalskning (SSRF). Det här värdet måste anges till ”true”, i gemener.|
| `object_id` | (Valfritt) En frågesträngsparameter som anger object_id för den hanterade identitet som token för. Krävs om den virtuella datorn har flera användartilldelade hanterade identiteter.|
| `client_id` | (Valfritt) En frågesträngsparameter som anger client_id för den hanterade identitet som token för. Krävs om den virtuella datorn har flera användartilldelade hanterade identiteter.|


Exempelsvaret:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Beskrivning |
| ------- | ----------- |
| `access_token` | Den begärda åtkomst-token. När du anropar en REST API för säker token är inbäddad i den `Authorization` begäran rubrik fältet som en ”ägartoken”, vilket gör att API för att autentisera anroparen. | 
| `refresh_token` | Används inte av hanterade identiteter för Azure-resurser. |
| `expires_in` | Antal sekunder som den åtkomst-token fortsätter att vara giltig, innan du går ut från tidpunkten för utfärdande. Tid för utfärdande finns i token `iat` anspråk. |
| `expires_on` | Timespan när åtkomsttoken upphör att gälla. Det datum då representeras som hur många sekunder från ”1970-01-01T0:0:0Z UTC” (motsvarar token `exp` anspråk). |
| `not_before` | Timespan när åtkomsttoken träder i kraft och kan godkännas. Det datum då representeras som hur många sekunder från ”1970-01-01T0:0:0Z UTC” (motsvarar token `nbf` anspråk). |
| `resource` | Resursen åtkomsttoken har begärts för som matchar den `resource` frågesträngparametern för begäran. |
| `token_type` | Typ av token, som är en ”ägar” åtkomsttoken, vilket innebär att resursen kan ge åtkomst till innehavare av denna token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Felsök tillägg för virtuell dator 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Starta om tillägget för virtuell dator efter ett fel

På Windows och vissa versioner av Linux om tillägget slutar kan följande cmdlet användas för att starta om den manuellt:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Där: 
- Namn på tillägg och typ för Windows är: `ManagedIdentityExtensionForWindows`
- Tilläggsnamn och typ för Linux är: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>”Automationsskript” misslyckas vid försök schemat exporten av hanterade identiteter för tillägget för Azure-resurser

När hanterade identiteter för Azure-resurser är aktiverat på en virtuell dator, visas följande felmeddelande när du försöker använda funktionen ”automationsskript” för den virtuella datorn eller en resursgrupp:

![Hanterade identiteter för Azure-resurser automationsskript exportfel](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Hanterade identiteter för tillägg för virtuell dator i Azure-resurser stöder för närvarande inte möjligheten att exportera dess schema till en resursgruppmall. Därför visas inte den genererade mallen konfigurationsparametrar för att aktivera hanterade identiteter för Azure-resurser på resursen. Dessa avsnitt kan läggas till manuellt genom att följa exemplen i [konfigurera hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av en mallar](qs-configure-template-windows-vm.md).

När schemat exportfunktionen blir tillgängliga för hanterade identiteter för tillägg för virtuell dator för Azure-resurser (planerad för utfasning i januari 2019), kommer den att listas i [exportera resursgrupper som innehåller VM-tillägg ](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Begränsningar av tillägget för virtuell dator 

Det finns flera viktiga begränsningar med att använda tillägget för virtuell dator. 

 * Den mest allvarliga begränsningen är det faktum att de autentiseringsuppgifter som används för att begära token lagras på den virtuella datorn. En angripare som har dataintrång på den virtuella datorn kan stjäla autentiseringsuppgifter. 
 * Dessutom stöds fortfarande tillägget för virtuell dator med flera Linux-distributioner, med en enorm utveckling kostnad för att ändra, skapa och testa tillägget på var och en av dessa distributioner. För närvarande stöds endast följande Linux-distributioner: 
    * CoreOS stabila
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Det finns en prestandapåverkan att distribuera virtuella datorer med hanterade identiteter som tillägget för virtuell dator också måste tillhandahållas. 
 * Slutligen har tillägget för virtuell dator endast stöd för att ha 32 användartilldelade hanterade identiteter per virtuell dator. 

## <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Den [Azure Instance Metadata Service (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) är en REST-slutpunkt som ger information om hur du kör instanser av virtuella datorer som kan användas för att hantera och konfigurera dina virtuella datorer. Slutpunkten är tillgänglig på en välkänd icke-dirigerbara IP-adress (`169.254.169.254`) som kan nås från den virtuella datorn.

Det finns flera fördelar med att använda Azure IMDS att begära token. 

1. Tjänsten är extern till den virtuella datorn, de autentiseringsuppgifter som används av hanterade identiteter är därför inte längre finns på den virtuella datorn. I stället de finns och skyddas på värddatorn på Azure-datorn.   
2. Alla Windows och Linux-operativsystem som stöds på Azure IaaS kan använda hanterade identiteter.
3. Distributionen är snabbare och enklare, eftersom VM-tillägget inte längre behöver etableras.
4. Med IMDS kan slutpunkt, upp till 1000 användartilldelade hanterade identiteter tilldelas till en enskild virtuell dator.
5. Det finns några betydande förändringar av på begäran med hjälp av IMDS till skillnad från de som använder tillägget för virtuell dator, därför är det ganska enkelt att port över befintliga distributioner som för närvarande använder tillägget för virtuell dator.

Därmed behöver är IMDS för Azure-tjänsten det självklara sättet att begära token när tillägget för virtuell dator är inaktuell. 


## <a name="next-steps"></a>Nästa steg

* [Hur du använder hanterade identiteter för Azure-resurser på virtuella Azure-datorer för att hämta en åtkomsttoken](how-to-use-vm-token.md)
* [Azure Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
