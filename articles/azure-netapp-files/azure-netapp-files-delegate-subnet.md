---
title: Delegera ett undernät till Azure NetApp Files | Microsoft Docs
description: Beskriver hur du delegerar ett undernät till Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054471"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegera ett undernät till Azure NetApp Files 

Du måste delegera ett undernät till Azure NetApp Files.   När du skapar en volym kan behöva du ange det delegerade undernätet.

## <a name="considerations"></a>Överväganden
* Guiden för att skapa ett nytt undernät har som standard en /24-nätverksmask som tillhandahåller 251 tillgängliga IP-adresser. En /28-nätverksmask som tillhandahåller 16 användbara IP-adresser räcker för tjänsten.
* Endast ett undernät kan delegeras till Azure NetApp Files i varje Azure Virtual Network (VNet).   
   Med Azure kan du skapa flera delegerade undernät i ett virtuella nätverk.  Alla försök att skapa en ny volym misslyckas dock om du använder mer än ett delegerat undernät.
* Du kan inte ange en grupp eller en tjänstslutpunkt i delegerade undernät. Det leder till att delegeringen av undernätet misslyckas.
* Åtkomst till en volym från ett globalt peer-in-virtuellt nätverk stöds för närvarande inte.
* Det stöds inte att skapa [användardefinierade anpassade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) på VM-undernät med adressprefix (mål) till ett undernät som delegerats till Azure NetApp-filer. Om du gör det påverkar den virtuella datorns anslutning.

## <a name="steps"></a>Steg 
1.  Gå till bladet **Virtuella nätverk** i Azure-portalen och välj det virtuella nätverket som du vill använda för Azure NetApp Files.    

1. Välj **Undernät** på bladet Virtuella nätverk och klicka på knappen **+Undernät**. 

1. Skapa ett nytt undernät att använda för Azure NetApp Files genom att fylla i följande obligatoriska fält på sidan Lägg till undernät:
    * **Namn**: Ange undernätsnamnet.
    * **Adressintervall**: Ange IP-adressintervallet.
    * **Undernätsdelegering**: Välj **Microsoft.NetApp/volymer**. 

      ![Delegering av undernät](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Du kan också skapa och delegera ett undernät när du [skapar en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Nästa steg  
* [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


