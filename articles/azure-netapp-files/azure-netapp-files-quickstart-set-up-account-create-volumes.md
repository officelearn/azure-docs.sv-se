---
title: Konfigurera Azure NetApp filer och skapa en volym | Microsoft Docs
description: Beskriver hur du snabbt ställa in Azure NetApp filer och skapa en volym.
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
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: 634f23cf3161fff09f21c79fd8300cb269dcc5b7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546591"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>Konfigurera Azure NetApp Files och skapa en volym 

Den här artikeln visar hur du snabbt ställa in Azure NetApp filer och skapa en volym. 

## <a name="before-you-begin"></a>Innan du börjar 

Du behöver delta i programmet för allmänt tillgängliga förhandsversioner och vara med i listan över tillåtna för att få åtkomst till Azure-resursprovidern för Microsoft.NetApp. Mer information om att gå med i programmet för allmänt tillgängliga förhandsversioner finns på [registreringssidan för allmänt tillgängliga förhandsversioner för Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registrera dig för Azure NetApp filer och NetApp-Resursprovidern

1. Klicka på ikonen Azure Cloud Shell i det övre högra hörnet i Azure Portal.

      ![Azure Cloud Shell-ikonen](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Ange den prenumeration som har varit godkänd för Azure NetApp-filer:
    
        az account set --subscription <subscriptionId>

3. Registrera Azure-Resursprovidern: 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Registreringsprocessen kan ta lite tid att slutföra.

## <a name="create-a-netapp-account"></a>Skapa ett NetApp-konto

1. I sökrutan i Azure portal anger **Azure NetApp Files** och välj sedan **Azure NetApp-filer (förhandsversion)** från listan som visas.

      ![Välj Azure NetApp-filer](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Klicka på **+ Lägg till** för att skapa ett nytt NetApp-konto.

     ![Skapa nytt NetApp-konto](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Ange följande information i fönstret Nytt NetApp-konto: 
   1. Ange **myaccount1** för namnet på kontot. 
   2. Välj din prenumeration.
   3. Välj **Skapa nytt** att skapa en ny resursgrupp. Ange **myRG1** för resursgruppens namn. Klicka på **OK**. 
   4. Välj din kontoplats.  

      ![Nytt konto NetApp-fönster](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Fönstret för resurs-grupp](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Klicka på **skapa** att skapa ditt nya NetApp-konto.

## <a name="set-up-a-capacity-pool"></a>Konfigurera en kapacitetspool

1. Välj ditt NetApp-konto från bladet för hantering av Azure NetApp-filer (**myaccount1**).

    ![Välj NetApp-konto](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Från bladet för Azure Files för NetApp-hantering för NetApp-kontot klickar du på **kapacitet pooler**.

    ![Klicka på kapacitet pooler](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Klicka på **+ Lägg till pooler**. 

    ![Klicka på Lägg till pooler](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Ange information för poolen kapacitet: 
    1. Ange **mypool1** som poolnamn på.
    2. Välj **Premium** för servicenivån. 
    3. Ange **4 (TiB)** som poolstorleken. 

5. Klicka på **OK**.

## <a name="create-a-volume-for-azure-netapp-files"></a>Skapa en volym för Azure NetApp Files

1. Från bladet för Azure Files för NetApp-hantering för NetApp-kontot klickar du på **volymer**.

    ![Klicka på volymer](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Klicka på **+ Lägg till volymen**.

    ![Klicka på Lägg till volymer](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Ange information för volymen i avsnittet Skapa en volym-fönstret: 
   1. Ange **myvol1** som volymens namn. 
   2. Ange **myfilepath1** som sökvägen till filen som ska användas för att skapa exportsökvägen för volymen.
   3. Välj din kapacitet pool (**mypool1**).
   4. Använd standardvärdet för kvoten. 
   5. I det virtuella nätverket, klickar du på **Skapa nytt** att skapa ett nytt Azure virtuellt nätverk (Vnet).  Fyll sedan i följande information:
       * Ange **myvnet1** som virtuella nätverkets namn.
       * Ange ett adressutrymme för dina inställningar, till exempel 10.7.0.0/16
       * Ange **myANFsubnet** som namnet på undernätet.
       * Ange adressintervallet i undernätet, till exempel 10.7.0.0/24. Observera att du inte kan dela det dedikerade undernätet som med andra resurser.
       * Välj **Microsoft.NetApp/volumes** för undernätet delegering.
       * Klicka på **OK** att skapa det virtuella nätverket.
   6. Välj det nyligen skapade virtuella nätverket i undernätet, (**myvnet1**) som ombud undernätet.

      ![Skapa en volym-fönster](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Skapa virtuella nätverk](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Klicka på **Granska + skapa**.

    ![Granska och skapa fönster](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Granska information för volymen och klicka sedan på **skapa**.  
    Skapade volymen visas i bladet volymer.

    ![Volym som har skapats](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>Nästa steg  

* [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Hantera volymer med hjälp av Azure NetApp filer](azure-netapp-files-manage-volumes.md) 
