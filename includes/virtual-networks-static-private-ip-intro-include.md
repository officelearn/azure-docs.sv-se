---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 5c1caf87f32ddd827b85263ee634d3e15d821124
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52272376"
---
Dina IaaS-datorer (VM) och PaaS-rollinstanser i ett virtuellt nätverk får automatiskt en privat IP-adress från ett område som du anger, baserat på det undernät som de är anslutna till. Den här adressen sparas av virtuella datorer och rollinstanser, tills de tas ur drift. Du inaktiverar en instans av virtuell dator eller roll genom att stoppa den från PowerShell, Azure CLI eller Azure-portalen. I sådana fall när den virtuella dator eller rollinstansen instansen startas igen, får den en tillgänglig IP-adress från Azure-infrastrukturen, vilket inte kanske är samma som den hade tidigare. Om du stänger av den virtuella dator eller rollinstansen instansen från gästoperativsystemet behåller den IP-adressen som den hade.  

I vissa fall kan du en virtuell dator eller roll-instans som har en statisk IP-adress, till exempel om den virtuella datorn ska köras DNS eller kommer att vara en domänkontrollant. Du kan göra det genom att ange en statisk privat IP-adress.

