---
title: Sluta använda vm-tillägg för hanterad identitet – Azure AD
description: Steg för steg instruktioner för att sluta använda VM-tillägget och börja använda Azure Instance Metadata Service (IMDS) för autentisering.
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
ms.openlocfilehash: 01b8e1dbc290bed86ccfc3c7016e8bd9168e427a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049072"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Så här slutar du använda tillägget för hanterade identiteter för virtuella datorer och börjar använda Azure Instance Metadata Service

## <a name="virtual-machine-extension-for-managed-identities"></a>Tillägg för virtuella datorer för hanterade identiteter

Tillägget för den virtuella datorn för hanterade identiteter används för att begära token för en hanterad identitet på den virtuella datorn. Arbetsflödet består av följande steg:

1. Först anropar arbetsbelastningen inom resursen den lokala slutpunkten `http://localhost/oauth2/token` för att begära en åtkomsttoken.
2. Tillägget för den virtuella datorn använder sedan autentiseringsuppgifterna för den hanterade identiteten för att begära en åtkomsttoken från Azure AD.. 
3. Åtkomsttoken returneras till anroparen och kan användas för att autentisera tjänster som stöder Azure AD-autentisering, till exempel Azure Key Vault eller Azure Storage.

På grund av flera begränsningar som beskrivs i nästa avsnitt har tillägget för hanterad identitet VM inaktuella till förmån för att använda motsvarande slutpunkt i Azure Instance Metadata Service (IMDS)

### <a name="provision-the-extension"></a>Etablera tillägget 

När du konfigurerar en virtuell dator- eller virtuell datorskalauppsättning för att ha en hanterad `-Type` identitet kan du välja att etablera de hanterade identiteterna för Azure Resources VM-tillägg med parametern på cmdleten [Set-AzVMExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) Du kan `ManagedIdentityExtensionForWindows` skicka `ManagedIdentityExtensionForLinux`antingen eller , beroende på vilken typ `-Name` av virtuell dator, och namnge den med parametern. Parametern `-Settings` anger den port som används av OAuth-tokenslutpunkten för tokeninsamling:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Du kan också använda distributionsmallen för Azure Resource Manager för att `resources` etablera vm-tillägget `ManagedIdentityExtensionForLinux` genom att lägga till följande JSON i avsnittet i mallen (använd för namn och typelement för Linux-versionen).

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
    
    
Om du arbetar med skalningsuppsättningar för virtuella datorer kan du också etablera de hanterade identiteterna för Azure-resursers skalningsuppsättningstillägg med tillägg för [tillägg till Tillägg till Tillägg till Tillägg till AzVmssExtension.](/powershell/module/az.compute/add-azvmssextension) Du kan `ManagedIdentityExtensionForWindows` skicka `ManagedIdentityExtensionForLinux`antingen eller , beroende på vilken typ av `-Name` virtuell dator skaluppsättning, och namnge den med parametern. Parametern `-Settings` anger den port som används av OAuth-tokenslutpunkten för tokeninsamling:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Om du vill etablera skalningstillägget för den virtuella datorn med `extensionpProfile` distributionsmallen `ManagedIdentityExtensionForLinux` för Azure Resource Manager lägger du till följande JSON i avsnittet i mallen (använd för namn och typelement för Linux-versionen).

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

Etablering av tillägget för den virtuella datorn kan misslyckas på grund av DNS-sökningsfel. Om detta inträffar startar du om den virtuella datorn och försöker igen. 

### <a name="remove-the-extension"></a>Ta bort tillägget 
Om du vill `-n ManagedIdentityExtensionForWindows` ta `-n ManagedIdentityExtensionForLinux` bort tillägget använder eller växlar du (beroende på vilken typ av virtuell dator) som används `Remove-AzVMExtension` för [az vm-tillägg](https://docs.microsoft.com/cli/azure/vm/)eller az [vmss-tilläggstillägget borttagning](https://docs.microsoft.com/cli/azure/vmss) för skalningsuppsättningar för virtuella datorer med Azure CLI eller för Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Hämta en token med tillägget för den virtuella datorn

Följande är en exempelbegäran med hjälp av hanterade identiteter för Azure Resources VM Extension Endpoint:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet, som anger att du vill hämta data från slutpunkten. I det här fallet en OAuth-åtkomsttoken. | 
| `http://localhost:50342/oauth2/token` | Den hanterade identitetspunkten för Azure-resurser slutpunkt, där 50342 är standardporten och kan konfigureras. |
| `resource` | En frågesträngparameter som anger målresursens app-ID-IURI. Det visas också `aud` i (publiken) anspråk på den utfärdade token. I det här exemplet begärs en token för att komma `https://management.azure.com/`åt Azure Resource Manager, som har en App ID URI av . |
| `Metadata` | Ett SIDFÄLT för HTTP-begäran, som krävs av hanterade identiteter för Azure-resurser som en begränsning mot SSRF-attack (Server Side Request Forgery). Det här värdet måste anges till "true", i alla gemener.|
| `object_id` | (Valfritt) En frågesträngparameter som anger object_id för den hanterade identitet som du vill ha token för. Obligatoriskt om den virtuella datorn har flera användartilldelade hanterade identiteter.|
| `client_id` | (Valfritt) En frågesträngparameter som anger client_id för den hanterade identitet som du vill ha token för. Obligatoriskt om den virtuella datorn har flera användartilldelade hanterade identiteter.|


Exempelsvar:

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
| `access_token` | Den begärda åtkomsttoken. När du anropar ett skyddat REST `Authorization` API bäddas token in i fältet för begäranhuvud som en "bärare"-token, vilket gör att API:et kan autentisera anroparen. | 
| `refresh_token` | Används inte av hanterade identiteter för Azure-resurser. |
| `expires_in` | Antalet sekunder som åtkomsttoken fortsätter att vara giltigt, innan den upphör att gälla, från tidpunkten för utfärdandet. Tidpunkten för utfärdandet finns i tokens `iat` anspråk. |
| `expires_on` | Tidsintervallet när åtkomsttoken upphör att gälla. Datumet representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" (motsvarar tokens `exp` anspråk). |
| `not_before` | Tidsintervallet när åtkomsttoken börjar gälla och kan accepteras. Datumet representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" (motsvarar tokens `nbf` anspråk). |
| `resource` | Resursen som åtkomsttoken begärdes `resource` för, vilket matchar frågesträngparametern för begäran. |
| `token_type` | Typen av token, som är en "Bärare" åtkomsttoken, vilket innebär att resursen kan ge åtkomst till innehavaren av den här token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Felsöka tillägget för den virtuella datorn 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Starta om tillägget för den virtuella datorn efter ett fel

På Windows och vissa versioner av Linux, om tillägget stoppas, kan följande cmdlet användas för att manuellt starta om den:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Där: 
- Tilläggsnamn och typ för Windows är:`ManagedIdentityExtensionForWindows`
- Tilläggsnamn och typ för Linux är:`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Automation script" misslyckas vid försök till schemaexport för hanterade identiteter för Azure-resurstillägg

När hanterade identiteter för Azure-resurser är aktiverade på en virtuell dator visas följande fel när du försöker använda funktionen "Automation script" för den virtuella datorn eller dess resursgrupp:

![Exportera fel i det hanterade identiteterna för Azure-resursautomatiseringsskript](./media/howto-migrate-vm-extension/automation-script-export-error.png)

De hanterade identiteterna för Virtuell azure-resurser-tillägg stöder för närvarande inte möjligheten att exportera schemat till en resursgruppmall. Därför visar den genererade mallen inte konfigurationsparametrar för att aktivera hanterade identiteter för Azure-resurser på resursen. Dessa avsnitt kan läggas till manuellt genom att följa exemplen i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av en mall .](qs-configure-template-windows-vm.md)

När schemaexportfunktionen blir tillgänglig för hanterade identiteter för virtuellt azure-resurstillägg (planeras för utfasning i januari 2019) visas den i [Exportera resursgrupper som innehåller VM-tillägg](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Begränsningar för tillägget för virtuella datorer 

Det finns flera stora begränsningar för att använda tillägget för virtuella datorer. 

 * Den allvarligaste begränsningen är det faktum att autentiseringsuppgifterna som används för att begära token lagras på den virtuella datorn. En angripare som framgångsrikt bryter mot den virtuella datorn kan exfiltrate autentiseringsuppgifterna. 
 * Dessutom är den virtuella datorn förlängning fortfarande stöds inte av flera Linux-distributioner, med en enorm utvecklingskostnad för att ändra, bygga och testa förlängningen på var och en av dessa distributioner. För närvarande stöds endast följande Linux-distributioner: 
    * CoreOS Stabil
    * CentOS 7,1 
    * Röd hatt 7,2 
    * Ubuntu 15,04 
    * Ubuntu 16.04
 * Det finns en prestandapåverkan för att distribuera virtuella datorer med hanterade identiteter, eftersom tillägget för virtuella datorer också måste etableras. 
 * Slutligen kan tillägget för virtuella datorer bara stödja att ha 32 användartilldelade hanterade identiteter per virtuell dator. 

## <a name="azure-instance-metadata-service"></a>Azure-instansmetadatatjänst

[Azure Instance Metadata Service (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) är en REST-slutpunkt som innehåller information om att köra instanser av virtuella datorer som kan användas för att hantera och konfigurera dina virtuella datorer. Slutpunkten är tillgänglig på en välkänd icke-dirigerbar IP-adress (`169.254.169.254`) som endast kan nås inifrån den virtuella datorn.

Det finns flera fördelar med att använda Azure IMDS för att begära token. 

1. Tjänsten är extern till den virtuella datorn, därför finns inte längre de autentiseringsuppgifter som används av hanterade identiteter på den virtuella datorn. I stället är de värd och säkras på värddatorn för den virtuella Azure-datorn.   
2. Alla Windows- och Linux-operativsystem som stöds på Azure IaaS kan använda hanterade identiteter.
3. Distributionen är snabbare och enklare, eftersom vm-tillägget inte längre behöver etableras.
4. Med IMDS-slutpunkten kan upp till 1 000 användartilldelade hanterade identiteter tilldelas en enda virtuell dator.
5. Det finns ingen betydande ändring av begäranden som använder IMDS i motsats till dem som använder tillägget för virtuella datorer, därför är det ganska enkelt att portera över befintliga distributioner som för närvarande använder tillägget för virtuella datorer.

Av dessa skäl kommer Azure IMDS-tjänsten att vara det faktiska sättet att begära token, när tillägget för den virtuella datorn är inaktuell. 


## <a name="next-steps"></a>Efterföljande moment

* [Så här använder du hanterade identiteter för Azure-resurser på en virtuell Azure-dator för att hämta en åtkomsttoken](how-to-use-vm-token.md)
* [Azure-instansmetadatatjänst](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
