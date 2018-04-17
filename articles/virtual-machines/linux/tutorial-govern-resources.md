---
title: Styr virtuella Azure-datorer med Azure CLI | Microsoft Docs
description: Självstudiekurs – hantera virtuella Azure-datorer genom att använda RBAC, principer, lås och taggar med Azure CLI
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: a7d44e421162cf5784dde58f757e235d12b63cba
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="virtual-machine-governance-with-azure-cli"></a>Virtual machine-styrning med Azure CLI

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda CLI lokalt, se [installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="understand-scope"></a>Förstå omfång

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

I den här kursen gäller du alla inställningar till en resursgrupp så du kan enkelt ta bort dessa inställningar när du är klar.

Nu ska vi skapa resursgruppen.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Resursgruppen är för närvarande är tom.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Du vill kontrollera att användare i din organisation har rätt nivå av åtkomst till dessa resurser. Du vill inte bevilja obegränsad åtkomst till användare, men du måste också kontrollera att de kan utföra sitt arbete. [Rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) kan du hantera vilka användare som har behörighet att utföra specifika åtgärder på ett scope.

Om du vill skapa och ta bort rolltilldelningar, måste användarna ha `Microsoft.Authorization/roleAssignments/*` åtkomst. Den här komma åt via rollerna ägare eller administratör för användaråtkomst.

Det finns tre resursspecifika roller som ger vanligtvis behövs åtkomst för att hantera virtuella lösningar:

* [Virtual Machine-deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Nätverksdeltagare](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Storage-konto deltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

I stället för att tilldela roller till enskilda användare, är det ofta lättare att [skapa en Azure Active Directory-grupp](../../active-directory/active-directory-groups-create-azure-portal.md) för användare som behöver vidta liknande åtgärder. Tilldela sedan den gruppen till rätt roll. För att förenkla den här artikeln kan skapa du en Azure Active Directory-grupp utan medlemmar. Du kan fortfarande tilldela den här gruppen till en roll för ett omfång. 

I följande exempel skapas en Azure Active Directory-grupp med namnet *VMDemoContributors* med ett smeknamn för e-post för *vmDemoGroup*. Smeknamn för e-post fungerar som ett alias för gruppen.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

Det tar en stund när Kommandotolken returnerar för gruppen att spridas i Azure Active Directory. Vänta 20 eller 30 sekunder, Använd den [az rolltilldelning skapa](/cli/azure/role/assignment#az_role_assignment_create) kommando för att tilldela den nya Azure Active Directory-gruppen till den virtuella datorn deltagarrollen för resursgruppen.  Om du kör följande kommando innan den har spridits, du får ett felmeddelande om **huvudsakliga <guid> finns inte i katalogen**. Försök köra kommandot igen.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Normalt du upprepa processen för *Network-deltagare* och *Storage-konto deltagare* Kontrollera användare tilldelas Hantera distribuerade resurser. Du kan hoppa över de här stegen i den här artikeln.

## <a name="azure-policies"></a>Principer för Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Tillämpa principer

Din prenumeration redan har flera principdefinitioner av. Tillgängliga principdefinitioner, Använd den [az definition principlistan](/cli/azure/policy/definition#az_policy_definition_list) kommando:

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Du kan visa befintliga principdefinitioner. Typ av princip är antingen **BuiltIn** eller **anpassad**. Titta igenom definitionerna för de som beskriver ett villkor som du vill tilldela. I den här artikeln får tilldela du principer som:

* Begränsa platserna för alla resurser.
* Begränsa SKU: er för virtuella datorer.
* Granska virtuella datorer som inte använder hanterade diskar.

I exemplet nedan kan du hämta tre principdefinitioner baserat på namn. Du använder den [az principtilldelningens skapa](/cli/azure/policy/assignment#az_policy_assignment_create) kommando för att tilldela dessa definitioner till resursgruppen. För vissa principer kan ange du parametervärden för att ange de tillåtna värdena.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

Föregående exempel förutsätter att du redan känner till parametrar för en princip. Om du vill visa parametrar kan använda:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Distribuera den virtuella datorn

Du har tilldelat roller och principer, så att du är redo att distribuera lösningar. Standardstorleken är Standard_DS1_v2 som är en av dina tillåtna SKU: er. Kommandot skapar SSH-nycklar, om de inte finns på standardplatsen.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

När distributionen är klar kan tillämpa du inställningar för flera till lösningen.

## <a name="lock-resources"></a>Lås resurser

[Resurslås](../../azure-resource-manager/resource-group-lock-resources.md) förhindra användare i din organisation av misstag tas bort eller ändra viktiga resurser. Till skillnad från rollbaserad åtkomstkontroll gäller resurslås en begränsning för alla användare och roller. Du kan ange låset för *CanNotDelete* eller *ReadOnly*.

För att skapa eller ta bort management lås, måste du ha åtkomst till `Microsoft.Authorization/locks/*` åtgärder. I de inbyggda rollerna, endast **ägare** och **administratör för användaråtkomst** beviljas dessa åtgärder.

Om du vill låsa den virtuella datorn och en säkerhetsgrupp för nätverk använder den [az Lås skapa](/cli/azure/lock#az_lock_create) kommando:

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Om du vill testa Lås försök att köra följande kommando:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Du ser ett felmeddelande om att ta borttagningsåtgärden inte kan utföras på grund av ett lås. Resursgruppen kan bara tas bort om du uttryckligen ta bort lås. Steget visas i [Rensa resurser](#clean-up-resources).

## <a name="tag-resources"></a>Taggen resurser

Du använder [taggar](../../azure-resource-manager/resource-group-using-tags.md) till Azure-resurser och organisera dem logiskt efter kategorier. Varje tagg består av ett namn och ett värde. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Om du vill lägga till taggar för en virtuell dator, Använd den [az Resurstagg](/cli/azure/resource#az_resource_tag) kommando. Alla eventuella befintliga taggar på resursen behålls inte.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Hitta resurser efter tagg

Hitta resurser med taggnamn och värde i [az resurslistan](/cli/azure/resource#az_resource_list) kommando:

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Du kan använda de returnerade värdena för hanteringsuppgifter som stoppas alla virtuella datorer med ett taggvärde.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Visa kostnader av taggvärden

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Låst nätverkssäkerhetsgruppen kan inte tas bort förrän låset tas bort. Hämta ID: N för lås för att ta bort låset och ge dem till den [az Lås ta bort](/cli/azure/lock#az_lock_delete) kommando:

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

När den inte längre behövs du använda kommandot [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser. Avsluta SSH-sessionen till den virtuella datorn och ta sedan bort resurserna enligt följande:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Nästa steg

I självstudien skapade du en anpassad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Tilldela användare till en roll
> * Tillämpa principer som verkställer standarder
> * Skydda viktiga resurser med lås
> * Tagga resurser för fakturering och hantering

Gå vidare till nästa kurs att lära dig hur högtillgängliga virtuella datorer.

> [!div class="nextstepaction"]
> [Övervaka virtuella datorer](tutorial-monitoring.md)

