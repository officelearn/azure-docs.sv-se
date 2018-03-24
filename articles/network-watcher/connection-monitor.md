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
ms.openlocfilehash: c7d98350dc8f66ebd4097f22b44dcbbe2653b25d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Övervaka nätverksanslutningar med Nätverksbevakaren i Azure med Azure-portalen

Lär dig hur du använder Övervakaren anslutning för att övervaka nätverksanslutningen mellan en virtuell Azure-dator och en IP-adress. Övervakaren anslutning innehåller övervakning på en nivå för anslutningen. En anslutning har definierats som en kombination av källa och mål-IP-adress och port. Övervakaren anslutning möjliggör scenarier som övervakning anslutningen från en virtuell dator i ett virtuellt nätverk till en virtuell dator som kör SQLServer i samma eller olika virtuella nätverk, via port 1433. Övervakaren anslutning innehåller anslutningens svarstid som ett Azure-Monitor-mått registreras var 60: e sekund. Dessutom ger dig en hopp som nexthop-topologi, och identifierar konfigurationsproblem som påverkar anslutningen.


## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du slutför stegen i den här artikeln:

* En instans av Nätverksbevakaren i den region som du vill övervaka en anslutning för. Om du inte redan har ett kan du skapa en genom att slutföra stegen i [skapa en instans av Azure Nätverksbevakaren](network-watcher-create.md).
* En virtuell dator ska övervaka.
* Har den `AzureNetworkWatcherExtension` installerad på den virtuella datorn som du vill övervaka en anslutning från. Om du vill installera tillägget på en Windows-dator, [tillägg för virtuell dator i Azure Network Watcher Agent för Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och installera tillägget i en virtuell Linux-finns [Azure Network Watcher Agent tillägg för virtuell dator för Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på [Azure-portalen](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Skapa en anslutning

Följande steg aktivera anslutning övervakning till en virtuell dator för målet via portarna 80 och 1433:

1. På vänster sida av portalen väljer **fler tjänster**.
2. Börja skriva *nätverksbevakaren*. När **Nätverksbevakaren** visas i sökresultaten väljer den.
3. Under **övervakning**väljer **övervakaren anslutning (förhandsgranskning)**. Funktioner i förhandsversionen har inte samma nivå av tillförlitlighet eller regional tillgänglighet som funktioner i allmänhet versionen.
4. Välj **+ Lägg till**.
5. Ange eller välj information för anslutningen du vill övervaka och välj sedan **Lägg till**. I exemplet som visas i följande bild, är anslutningen övervakas mellan den *MultiTierApp0* och *Database0* virtuella datorer:

    ![Lägg till övervakaren anslutning](./media/connection-monitor/add-connection-monitor.png)

    Övervaka börjar. Övervakaren anslutning avsökningar var 60: e sekund.

## <a name="view-connection-monitoring"></a>Visa anslutning övervakning

1. Slutför steg 1-3 i [skapa en anslutning Övervakare](#create-a-connection-monitor) att visa anslutning övervakning.
2. Följande bild visar information för AppToDB(80) anslutningen. Den **Status** kan nås. Den **kurva visa** visar den **serveranrop genomsnittstiden** och **% avsökningar misslyckades**. Diagrammet innehåller hopp av hopp information och visar att några problem som påverkar mål reachability.

    ![Övervakaren anslutning för vyn](./media/connection-monitor/view-connection-monitor.png)

3. Visa den *AppToDB(1433)* Övervakare, visas i följande bild ser att för samma källa och mål virtuella datorer, status kan inte nås via port 1433. Den **rutnätsvy** i det här scenariot ger hopp av hopp information och problem som påverkar reachability. I det här fallet blockerar en NSG regel all trafik på port 1433 på ett andra hopp.

    ![Övervakaren anslutning för vyn](./media/connection-monitor/view-connection-monitor-2.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig att automatisera insamlingar paket med virtuella aviseringar efter [skapar en avisering utlöses paketinsamling](network-watcher-alert-triggered-packet-capture.md).
- Avgöra om vissa trafik tillåts i eller utanför den virtuella datorn med hjälp av [IP-flöde Kontrollera](network-watcher-check-ip-flow-verify-portal.md).