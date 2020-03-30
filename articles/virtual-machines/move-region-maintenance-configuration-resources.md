---
title: Flytta resurser som är associerade med en underhållskonfiguration till en annan region
description: Lär dig hur du flyttar resurser som är associerade med en vm-underhållskonfiguration till en annan Azure-region
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304451"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Flytta resurser i en underhållskontrollkonfiguration till en annan region

Följ den här artikeln om du vill flytta resurser som är associerade med en underhållskontrollkonfiguration till en annan Azure-region. Du kanske vill flytta en konfiguration av flera skäl. Om du till exempel vill dra nytta av en ny region, distribuera funktioner eller tjänster som är tillgängliga i en viss region, för att uppfylla interna princip- och styrningskrav eller som svar på kapacitetsplanering.

Med underhållskontroll, med anpassade underhållskonfigurationer, kan du styra hur plattformsuppdateringar tillämpas på [virtuella Datorer](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) för Windows och [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) och på Azure Dedicated Hosts. Det finns ett par scenarier för att flytta underhållskontroll över regioner:

- Om du vill flytta de resurser som är associerade med en underhållskonfiguration, men inte själva konfigurationen, följer du den här artikeln.
- Om du vill flytta konfigurationen för underhållskontroll, men inte de resurser som är associerade med konfigurationen, följer du [dessa instruktioner](move-region-maintenance-configuration.md).
- Om du vill flytta både underhållskonfigurationen och de resurser som är associerade med den följer du först [dessa instruktioner](move-region-maintenance-configuration.md). Följ sedan instruktionerna i den här artikeln.

## <a name="prerequisites"></a>Krav

Innan du börjar flytta de resurser som är associerade med en underhållskontrollkonfiguration:

- Kontrollera att de resurser du flyttar finns i den nya regionen innan du börjar.
- Verifiera underhållskontrollkonfigurationerna som är associerade med virtuella Azure-datorer och Azure-dedikerade värdar som du vill flytta. Kontrollera varje resurs individuellt. Det finns för närvarande inget sätt att hämta konfigurationer för flera resurser.
- När du hämtar konfigurationer för en resurs:
    - Se till att du använder prenumerations-ID för kontot, inte ett Azure Dedicated Host ID.
    - CLI: Parametern --output-tabell används endast för läsbarhet och kan tas bort eller ändras.
    - PowerShell: Parametern Format-Table Name används endast för läsbarhet och kan tas bort eller ändras.
    - Om du använder PowerShell får du ett felmeddelande om du försöker lista konfigurationer för en resurs som inte har några associerade konfigurationer. Felet kommer att likna: "Åtgärden misslyckades med status: 'Hittades inte'. Detaljer: 404 Klientfel: Hittades inte för url".

    
## <a name="prepare-to-move"></a>Förbered att flytta

1. Innan du börjar definierar du dessa variabler. Vi har föregått med gott exempel för var och en.

    **Variabel** | **Detaljer** | **Exempel**
    --- | ---
    $subId | ID för prenumeration som innehåller underhållskonfigurationer | "vårt prenumerations-ID"
    $rsrcGroupName | Namn på resursgrupp (Azure VM) | "VMResourceGroup"
    $vmName | VM-resursnamn |  "myVM"
    $adhRsrcGroupName |  Resursgrupp (dedikerade värdar) | "HostResourceGroup"
    $adh | Dedikerat värdnamn | "myHost"
    $adhParentName | Överordnat resursnamn | "Värdgrupp"
    
2. Så här hämtar du underhållskonfigurationerna med kommandot PowerShell [Get-AZConfigurationAssignment:](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)

    - För Azure-dedikerade värdar kör du:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - För virtuella Azure-datorer kör du:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Så här hämtar du underhållskonfigurationerna med kommandot CLI [az maintenance assignment:](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest)

    - För Azure-dedikerade värdar:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - För virtuella Azure-datorer:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Flytta 

1. [Följ dessa instruktioner](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) för att flytta virtuella Azure-datorer till den nya regionen.
2. När resurserna har flyttats, tillämpa underhållskonfigurationer på nytt till resurserna i den nya regionen efter behov, beroende på om du har flyttat underhållskonfigurationerna. Du kan använda en underhållskonfiguration på en resurs med [PowerShell](../virtual-machines/maintenance-control-powershell.md) eller [CLI](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>Verifiera flytten

Verifiera resurser i den nya regionen och verifiera associerade konfigurationer för resurserna i den nya regionen. 

## <a name="clean-up-source-resources"></a>Rensa källresurser

Efter flytten bör du överväga att ta bort de flyttade resurserna i källregionen.


## <a name="next-steps"></a>Nästa steg

Följ [dessa instruktioner](move-region-maintenance-configuration.md) om du behöver flytta underhållskonfigurationer. 
