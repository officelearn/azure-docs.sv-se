---
title: Flytta en underhållskonfiguration till en annan Azure-region
description: Lär dig hur du flyttar en vm-underhållskonfiguration till en annan Azure-region
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304464"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Flytta en underhållskontrollkonfiguration till en annan region

Följ den här artikeln om du vill flytta en underhållskontrollkonfiguration till en annan Azure-region. Du kanske vill flytta en konfiguration av flera skäl. Om du till exempel vill dra nytta av en ny region, distribuera funktioner eller tjänster som är tillgängliga i en viss region, för att uppfylla interna princip- och styrningskrav eller som svar på kapacitetsplanering.

Med underhållskontroll, med anpassade underhållskonfigurationer, kan du styra hur plattformsuppdateringar tillämpas på [virtuella Datorer](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) för Windows och [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) och på Azure Dedicated Hosts. Det finns ett par scenarier för att flytta underhållskontroll över regioner:

- Om du vill flytta konfigurationen för underhållskontroll, men inte de resurser som är associerade med konfigurationen, följer du instruktionerna i den här artikeln.
- Om du vill flytta de resurser som är associerade med en [underhållskonfiguration,](move-region-maintenance-configuration-resources.md)men inte själva konfigurationen, följer du dessa instruktioner .
- Om du vill flytta både underhållskonfigurationen och de resurser som är associerade med den följer du först instruktionerna i den här artikeln. Följ sedan [dessa instruktioner](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Krav

Innan du börjar flytta en underhållskontrollkonfiguration:

- Underhållskonfigurationer är associerade med virtuella Azure-datorer eller Azure-dedikerade värdar. Kontrollera att vm/värdresurser finns i den nya regionen innan du börjar.
- Identifiera: 
    - Befintliga konfigurationer för underhållskontroll.
    - De resursgrupper där befintliga konfigurationer för närvarande finns. 
    - De resursgrupper som konfigurationerna ska läggas till efter att ha flyttat till den nya regionen. 
    - De resurser som är associerade med den underhållskonfiguration som du vill flytta.
    - Kontrollera att resurserna i den nya regionen är desamma som de som är associerade med de aktuella underhållskonfigurationerna. Konfigurationerna kan ha samma namn i den nya regionen som de gjorde i den gamla, men detta krävs inte.

## <a name="prepare-and-move"></a>Förbereda och flytta 

1. Hämta alla underhållskonfigurationer i varje prenumeration. Kör kommandot CLI [az underhållskonfigurationslista](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) för att göra detta och ersätter $subId med ditt prenumerations-ID.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Granska den returnerade tabelllistan över konfigurationsposter i prenumerationen. Här är ett exempel. Listan innehåller värden för din specifika miljö.

    **Namn** | **Location** | **Resursgrupp**
    --- | --- | ---
    Hoppa över underhåll | eastus2 (östra) | konfiguration-resurs-grupp
    IgniteDemoConfig | eastus2 (östra) | konfiguration-resurs-grupp
    standardMaintenanceConfiguration-eastus | eastus | testkonfiguration
    

3. Spara listan som referens. När du flyttar konfigurationerna hjälper det dig att kontrollera att allt har flyttats.
4. Som referens mappar du varje konfiguration/resursgrupp till den nya resursgruppen i den nya regionen.
5. Skapa nya underhållskonfigurationer i den nya regionen med [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)eller [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Associera konfigurationerna med resurserna i den nya regionen med [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)eller [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Verifiera flytten

När du har flyttat konfigurationerna jämför du konfigurationer och resurser i den nya regionen med den tabelllista som du har förberett.


## <a name="clean-up-source-resources"></a>Rensa källresurser

Efter flytten bör du överväga att ta bort de flyttade underhållskonfigurationerna i [källregionen, PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)eller [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Nästa steg

Följ [dessa instruktioner](move-region-maintenance-configuration-resources.md) om du behöver flytta resurser som är associerade med underhållskonfigurationer. 
