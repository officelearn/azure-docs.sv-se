---
title: Hantera ögonblicks bilder med Azure NetApp Files | Microsoft Docs
description: Beskriver hur du skapar och hanterar ögonblicks bilder med hjälp av Azure NetApp Files.
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 7d583172fe4021a2709a4d58b5488e9bc3898919
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497604"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Hantera ögonblicksbilder med hjälp av Azure NetApp Files

Azure NetApp Files har stöd för att skapa ögonblicks bilder och använda ögonblicks bilder för att schemalägga automatisk skapande av ögonblicks bilder.  Du kan också återställa en ögonblicks bild till en ny volym.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Skapa en ögonblicks bild på begäran för en volym

Du kan skapa ögonblicks bilder av volymen på begäran. 

1.  Gå till den volym som du vill skapa en ögonblicks bild för. Klicka på **ögonblicks bilder**.

    ![Navigera till ögonblicks bilder](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klicka på **+ Lägg till ögonblicks bild** för att skapa en ögonblicks bild på begäran för en volym.

    ![Lägg till ögonblicks bild](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  I fönstret ny ögonblicks bild anger du ett namn för den nya ögonblicks bilden som du skapar.   

    ![Ny ögonblicks bild](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klicka på **OK**. 

## <a name="manage-snapshot-policies"></a>Hantera ögonblicks bilds principer

Du kan schemalägga att ögonblicks bilder av volymen ska tas automatiskt med hjälp av ögonblicks bilds principer. Du kan också ändra en ögonblicks bild princip efter behov eller ta bort en ögonblicks bild princip som du inte längre behöver.  

### <a name="register-the-feature"></a>Registrera funktionen

1. Funktionen för **ögonblicks bilds principen** är för närvarande en för hands version. Om det här är första gången du använder den här funktionen ska du registrera funktionen innan du använder den: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Kontrol lera status för funktions registreringen: 

    > [!NOTE]
    > **RegistrationState** kan vara i ett `Registering` tillstånd i flera minuter innan du ändrar till `Registered` . Vänta tills statusen har **registrerats** innan du fortsätter.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

### <a name="create-a-snapshot-policy"></a>Skapa en ögonblicks bild princip 

Med en ögonblicks bild princip kan du ange frekvensen för ögonblicks bild skapande i varje timme, varje dag, varje vecka eller månads vis. Du måste också ange det maximala antal ögonblicks bilder som ska behållas för volymen.  

1.  I vyn NetApp konto klickar du på **ögonblicks bild princip**.

    ![Navigering för ögonblicks bild princip](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  I fönstret ögonblicks bild princip anger du princip tillstånd till **aktive rad**. 

3.  Klicka på fliken varje **timme**, **varje dag**, **varje vecka**eller varje **månad** för att skapa dagliga, dagliga, vecko Visa eller månatliga ögonblicks bilds principer. Ange det **antal ögonblicks bilder som ska behållas**.  

    Se [resurs gränser för Azure NetApp Files](azure-netapp-files-resource-limits.md) om det maximala antalet ögonblicks bilder som tillåts för en volym. 

    I följande exempel visas konfiguration av Tim ögonblicks bild princip. 

    ![Ögonblicks bild princip varje timme](../media/azure-netapp-files/snapshot-policy-hourly.png)

    I följande exempel visas daglig princip konfiguration för ögonblicks bilder.

    ![Ögonblicks bild princip varje dag](../media/azure-netapp-files/snapshot-policy-daily.png)

    I följande exempel visas konfigurationen för vecko ögonblicks bilds principer.

    ![Ögonblicks bild princip varje vecka](../media/azure-netapp-files/snapshot-policy-weekly.png)

    I följande exempel visas månatlig konfiguration av ögonblicks bilds principen.

    ![Ögonblicks bild princip varje månad](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Klicka på **Spara**.  

Om du behöver skapa ytterligare ögonblicks bilds principer upprepar du steg 3.
De principer som du har skapat visas på sidan princip för ögonblicks bild.

Om du vill att en volym ska använda ögonblicks bild principen måste du [tillämpa principen på volymen](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Tillämpa en ögonblicks bild princip på en volym

Om du vill att en volym ska använda en ögonblicks bilds princip som du har skapat måste du tillämpa principen på volymen. 

1.  Gå till sidan **volymer** , högerklicka på den volym som du vill tillämpa en ögonblicks bild princip på och välj **Redigera**.

    ![Volymer på snabb menyn för volymer](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  I redigerings fönstret, under **ögonblicks bild princip**, väljer du en princip som ska användas för volymen.  Klicka på **OK** för att tillämpa principen.  

    ![Redigera ögonblicks redigerings princip](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Ändra en ögonblicks bild princip 

Du kan ändra en befintlig ögonblicks bild princip om du vill ändra principens tillstånd, frekvens för ögonblicks bilder (varje timme, varje dag, varje vecka eller månad) eller antal ögonblicks bilder som ska behållas.  
 
1.  I vyn NetApp konto klickar du på **ögonblicks bild princip**.

2.  Högerklicka på den ögonblicks bilds princip som du vill ändra och välj sedan **Redigera**.

    ![Ögonblicks bild princip, snabb meny](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Gör ändringarna i fönstret princip för ögonblicks bild som visas och klicka sedan på **Spara**. 

### <a name="delete-a-snapshot-policy"></a>Ta bort en ögonblicks bild princip 

Du kan ta bort en ögonblicks bilds princip som du inte längre vill behålla.   

1.  I vyn NetApp konto klickar du på **ögonblicks bild princip**.

2.  Högerklicka på den ögonblicks bilds princip som du vill ändra och välj sedan **ta bort**.

    ![Ögonblicks bild princip, snabb meny](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Klicka på **Ja** för att bekräfta att du vill ta bort ögonblicks bild principen.   

    ![Bekräftelse av borttagning av ögonblicks princip](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Återställa en ögonblicks bild till en ny volym

För närvarande kan du bara återställa en ögonblicks bild till en ny volym. 
1. Välj **ögonblicks bilder** från volym bladet för att Visa ögonblicks bild listan. 
2. Högerklicka på den ögonblicks bild som du vill återställa och välj **Återställ till ny volym** från meny alternativet.  

    ![Återställ ögonblicks bild till ny volym](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Ange information för den nya volymen i fönstret Skapa en volym:  
    * **Namn**   
        Ange namnet på den volym du skapar.  
        
        Namnet måste vara unikt inom sin resursgrupp. Det måste innehålla minst tre tecken.  Du kan använda alla alfanumeriska tecken.

    * **Kvot**  
        Ange mängden logisk lagring som du vill tilldela till volymen.  

    ![Återställ till ny volym](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Klicka på **Granska + skapa**.  Klicka på **Skapa**.   
    Den nya volymen använder samma protokoll som ögonblicks bilden använder.   
    Den nya volymen som ögonblicks bilden återställs till visas på bladet volymer.

## <a name="next-steps"></a>Nästa steg

* [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
