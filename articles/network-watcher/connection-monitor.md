---
title: Övervaka nätverksanslutningar med Nätverksbevakaren Azure - Azure-portalen | Microsoft Docs
description: Lär dig att övervaka nätverksanslutningen med Nätverksbevakaren i Azure med Azure-portalen.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: 242da9a3ce52d9c7d801215cde7b72b7f8fe9a91
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Övervaka nätverksanslutningar med Nätverksbevakaren i Azure med Azure-portalen

Lär dig hur du använder Övervakaren anslutning för att övervaka nätverksanslutningen mellan Azure virtuell dator (VM) och en IP-adress. Övervakaren anslutning innehåller övervakning mellan käll- och IP-adress och port. Övervakaren anslutning möjliggör scenarier som övervakning anslutningen från en virtuell dator i ett virtuellt nätverk till en virtuell dator kör SQLServer i samma eller olika virtuella nätverk, via port 1433. Övervakaren anslutning innehåller anslutningens svarstid som ett Azure-Monitor-mått registreras var 60: e sekund. Dessutom ger dig en hopp som nexthop-topologi, och identifierar konfigurationsproblem som påverkar anslutningen.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du slutför stegen i den här artikeln:

* En instans av Nätverksbevakaren i den region som du vill övervaka en anslutning för. Om du inte redan har ett kan du skapa en genom att slutföra stegen i [skapa en instans av Azure Nätverksbevakaren](network-watcher-create.md).
* En virtuell dator och övervaka. Om du vill veta hur du skapar en virtuell dator, se Skapa en [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) VM.
* Har den `AzureNetworkWatcherExtension` installerad på den virtuella datorn som du vill övervaka en anslutning från. Om du vill installera tillägget i en Windows VM [tillägg för virtuell dator i Azure Network Watcher Agent för Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och installera tillägget i en Linux VM finns [tillägg för virtuell dator i Azure Network Watcher Agent för Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Tillägget krävs inte på målslutpunkt som du vill övervaka.

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på [Azure-portalen](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Skapa en anslutning

Följande steg aktivera övervakning av anslutning till ett mål VM via portarna 80 och 1433:

1. På vänster sida av portalen väljer **alla tjänster**.
2. Börja skriva *nätverksbevakaren* i den **Filter** rutan. När **Nätverksbevakaren** visas i sökresultaten väljer den.
3. Under **övervakning**väljer **övervakaren anslutning**.
4. Välj **+ Lägg till**.
5. Ange eller välj information för anslutningen du vill övervaka och välj sedan **Lägg till**. I exemplet som visas i följande bild, är anslutningen övervakas från den *MultiTierApp0* så att den *Database0* VM via port 80:

    ![Lägg till övervakaren anslutning](./media/connection-monitor/add-connection-monitor.png)

    Övervaka börjar. Övervakaren anslutning avsökningar var 60: e sekund.
6. Slutför steg 5 igen, ange samma käll- och virtuella datorer och följande värden:
    
    |Inställning  |Värde          |
    |---------|---------      |
    |Namn     | AppToDB(1433) |
    |Port     | 1433          |

## <a name="view-connection-monitoring"></a>Visa anslutning övervakning

1. Slutför steg 1-3 i [skapa en anslutning Övervakare](#create-a-connection-monitor) att visa anslutning övervakning.
2. Följande bild visar information om den *AppToDB(80)* anslutning. Den **Status** kan nås. Den **kurva visa** visar den **serveranrop genomsnittstiden** och **% avsökningar misslyckades**. Diagrammet innehåller hopp av hopp information och visar att några problem som påverkar mål reachability.

    ![Diagramvy](./media/connection-monitor/view-graph.png)

3. Visa den *AppToDB(1433)* anslutning som visas i följande bild ser att för samma käll- och virtuella datorer, status kan inte nås via port 1433. Den **rutnätsvy** i det här scenariot ger hopp av hopp information och problem som påverkar reachability. I det här fallet blockerar en NSG regel all trafik på port 1433 på ett andra hopp.

    ![Rutnätsvy](./media/connection-monitor/view-grid.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig att automatisera paket insamlingar med VM-aviseringar efter [skapar en avisering utlöses paketinsamling](network-watcher-alert-triggered-packet-capture.md).
- Avgöra om vissa trafik tillåts i eller utanför den virtuella datorn med hjälp av [IP-flöde Kontrollera](diagnose-vm-network-traffic-filtering-problem.md).