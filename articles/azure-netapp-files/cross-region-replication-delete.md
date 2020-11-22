---
title: Ta bort volymer eller volymer för replikering Azure NetApp Files över flera regioner | Microsoft Docs
description: Beskriver hur du tar bort en replikeringsanslutning som inte längre behövs mellan käll-och mål volymerna.
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
ms.topic: how-to
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249902"
---
# <a name="delete-volume-replications-or-volumes"></a>Ta bort volymer eller volymer

Den här artikeln beskriver hur du tar bort volym replikeringar. Det beskriver också hur du tar bort käll-eller mål volymen.

## <a name="delete-volume-replications"></a>Ta bort volym replikeringar

Du kan avsluta replikeringen mellan käll-och mål volymerna genom att ta bort volym replikering. Du måste ta bort replikeringen från mål volymen. Borttagnings åtgärden tar bara bort auktorisering för replikering. käll-eller mål volymen tas inte bort. 

1. Kontrol lera att peering-replikeringen har brutits innan du tar bort en volym replikering. Så här avbryter du replikeringens peering: 

    1. Välj *mål* volym. Klicka på **replikering** under lagrings tjänst.  

    2.  Kontrol lera följande fält innan du fortsätter:  
        * Se till att speglings status visar ***speglade** _.   
            Försök inte att avbryta replikerings-peering om speglings status visar _Uninitialized *.
        * Se till att Relations status visar ***inaktiv** _.   
            Försök inte att avbryta replikerings-peering om Relations status visar _Transferring *.   

        Se [Visa hälso status för replikeringsrelation](cross-region-replication-display-health-status.md). 

    3.  Klicka på **Bryt peering**.  

    4.  Skriv **Ja** när du uppmanas till det och klicka på **Avbryt**. 

        ![Bryt peering-replikering](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. Om du vill ta bort en volym replikering väljer du **replikering** från käll-eller mål volymen.  

2. Klicka på **Ta bort**.    

3. Bekräfta borttagningen genom att skriva **Ja** och klicka på **ta bort**.   

    ![Ta bort replikering](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>Ta bort käll-eller mål volymer

Om du vill ta bort käll-eller mål volymen måste du utföra följande steg i den beskrivna ordningen. Annars `Volume with replication cannot be deleted` uppstår felet.  

1. [Ta bort volym replikeringen](#delete-volume-replications)från mål volymen.   

2. Ta bort mål-eller käll volymen vid behov genom att högerklicka på volym namnet och välja **ta bort**.   

    ![Skärm bild som visar snabb menyer på en volym.](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>Nästa steg  

* [Replikering mellan regioner](cross-region-replication-introduction.md)
* [Krav och överväganden för att använda replikering över flera regioner](cross-region-replication-requirements-considerations.md)
* [Visa hälsostatus för replikeringsrelation](cross-region-replication-display-health-status.md)
* [Felsöka replikering mellan regioner](troubleshoot-cross-region-replication.md)

