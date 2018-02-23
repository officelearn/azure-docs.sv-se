---
title: "Övervaka nätverksanslutningar med Nätverksbevakaren Azure - Azure-portalen | Microsoft Docs"
description: "Lär dig att övervaka nätverksanslutningen med Nätverksbevakaren i Azure med Azure-portalen."
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: beaa458d727a05eccf496933deb3c998828868cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Övervaka nätverksanslutningar med Nätverksbevakaren i Azure med Azure-portalen

Lär dig hur du använder Övervakaren anslutning för att övervaka nätverksanslutningen mellan en virtuell Azure-dator och en IP-adress. IP-adressen kan tilldelas en annan Azure-resurs eller en Internet- eller lokal resurs.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du slutför stegen i den här artikeln:

* En instans av Nätverksbevakaren i den region som du vill övervaka en anslutning för. Om du inte redan har ett kan du skapa en genom att slutföra stegen i [skapa en instans av Azure Nätverksbevakaren](network-watcher-create.md).
* En virtuell dator ska övervaka.
* Har den `AzureNetworkWatcherExtension` installerad på den virtuella datorn som du vill övervaka en anslutning från. Om du vill installera tillägget på en Windows-dator, [tillägg för virtuell dator i Azure Network Watcher Agent för Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och installera tillägget i en virtuell Linux-finns [Azure Network Watcher Agent tillägg för virtuell dator för Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på [Azure-portalen](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Skapa en anslutning

1. På vänster sida av portalen väljer **fler tjänster**.
2. Börja skriva *nätverksbevakaren*. När **Nätverksbevakaren** visas i sökresultaten väljer den.
3. Under **övervakning**väljer **övervakaren anslutning (förhandsgranskning)**. Funktioner i förhandsversionen har inte samma nivå av tillförlitlighet eller regional tillgänglighet som funktioner i allmänhet versionen.
4. Välj **+ Lägg till**.
5. Ange eller välj lämplig information för anslutningen du vill övervaka och välj sedan **Lägg till**. I det här exemplet är en anslutning mellan den *myVmSource* och *myVmDestination* virtuella datorer som övervakas via port 80.
    
    |  Inställning                                 |  Värde               |
    |  -------------------------------------   |  ------------------- |
    |  Namn                                    |  myConnectionMonitor |
    |  Virtuella källdatorn                  |  myVmSource          |
    |  Källport                             |                      |
    |  Mål, Välj en virtuell dator   |  myVmDestination     |
    |  Målport                        |  80                  |

6. Övervaka börjar. Övervakaren anslutning avsökningar var 60: e sekund.
7. Övervakaren anslutning visar genomsnittlig förfluten tid och procent avsökningar som misslyckas. Du kan visa Övervakare data i ett rutnät eller ett diagram.

## <a name="next-steps"></a>Nästa steg

- Lär dig att automatisera insamlingar paket med virtuella aviseringar efter [skapar en avisering utlöses paketinsamling](network-watcher-alert-triggered-packet-capture.md).

- Avgöra om vissa trafik tillåts i eller utanför den virtuella datorn med hjälp av [IP-flöde Kontrollera](network-watcher-check-ip-flow-verify-portal.md).