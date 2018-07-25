---
title: Konfigurera en kapacitetspool för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du ställer in en kapacitetspool så att du kan skapa volymer inom den.
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 0e9203f5b4e2a9043e242b804c82017cf6fc3ee1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010811"
---
# <a name="set-up-a-capacity-pool"></a>Konfigurera en kapacitetspool
När du konfigurerar en kapacitetspool kan du skapa volymer inom den.  

## <a name="before-you-begin"></a>Innan du börjar 
Du måste redan ha skapat ett NetApp-konto.   

[Skapa ett NetApp-konto](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Steg 

1. Öppna hanteringsbladet för NetApp-kontot. I navigeringsfönstret väljer du **Kapacitetspooler**.

2. Klicka på **+ Lägg till pooler** när du vill skapa en ny kapacitetspool.   
    Fönstret Ny kapacitetspool visas.

3. Ange följande information för den nya kapacitetspoolen:  
  * **Namn**  
    Ange ett namn för kapacitetspoolen.  
    Namnet på kapacitetspoolen måste vara unikt för varje NetApp-konto.

  * **Servicenivå**   
    Det här fältet visar målprestanda för kapacitetspoolen.  
    För närvarande är endast servicenivån Premium tillgänglig. 

  *  **Storlek**     
      Ange storleken på den kapacitetspool som du köper.        
      Den minsta kapacitetspoolstorleken är 4 TiB. Du kan skapa poolstorlekar som är multiplar av 4 TiB.   
      
      ![Ny kapacitetspool](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klicka på **OK**.

## <a name="next-steps"></a>Nästa steg 

1. [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
2. [Konfigurera exportprincipen för en volym (valfritt)](azure-netapp-files-configure-export-policy.md)

