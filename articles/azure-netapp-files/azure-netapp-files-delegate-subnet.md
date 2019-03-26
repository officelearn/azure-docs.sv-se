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
ms.date: 11/13/2018
ms.author: b-juche
ms.openlocfilehash: 1cac267be026d0e472db9a7a321f5fff6ab3e917
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434780"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegera ett undernät till Azure NetApp Files 

Du måste delegera ett undernät till Azure NetApp Files.   När du skapar en volym kan behöva du ange det delegerade undernätet.

## <a name="considerations"></a>Överväganden
* Guiden för att skapa ett nytt undernät har som standard en /24-nätverksmask som tillhandahåller 251 tillgängliga IP-adresser. En /28-nätverksmask som tillhandahåller 16 användbara IP-adresser räcker för tjänsten.
* Endast ett undernät kan delegeras till Azure NetApp Files i varje Azure Virtual Network (VNet).
* Du kan inte ange en grupp eller en tjänstslutpunkt i delegerade undernät. Det leder till att delegeringen av undernätet misslyckas.
* Åtkomst till en volym från ett globalt peer-kopplade virtuella nätverk stöds inte för närvarande.
* Skapa [användardefinierade anpassade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) på undernät för virtuella datorer med adressen prefix (mål) till ett undernät som delegerats till Azure NetApp-filer stöds inte och påverkar VM-anslutning.

## <a name="steps"></a>Steg 
1.  Gå till bladet **Virtuella nätverk** i Azure-portalen och välj det virtuella nätverket som du vill använda för Azure NetApp Files.    

1. Välj **Undernät** på bladet Virtuella nätverk och klicka på knappen **+Undernät**. 

1. Skapa ett nytt undernät att använda för Azure NetApp Files genom att fylla i följande obligatoriska fält på sidan Lägg till undernät:
    * **Namn**: Ange namnet på undernätet.
    * **Adressintervall**: Ange IP-adressintervallet.
    * **Delegering av undernät**: Välj **Microsoft.NetApp/volumes**. 

      ![Delegering av undernät](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Du kan också skapa och delegera ett undernät när du [skapar en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Nästa steg  
* [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


