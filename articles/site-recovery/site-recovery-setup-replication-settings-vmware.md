---
title: "Ange replikeringsinställningar för Azure Site Recovery| Microsoft Docs"
description: "Beskriver hur du distribuerar Site Recovery för att dirigera replikering, redundans och återställning av virtuella Hyper-V-datorer i VMM-moln till Azure."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Hantera en replikeringsprincip för VMware till Azure


## <a name="create-a-new-replication-policy"></a>Skapa en ny replikeringsprincip

1. Klicka på ”Hantera -> Site Recovery-infrastruktur” på menyn till vänster. 
2. Välj ”Replikeringsprinciper” under avsnittet ”For VMware and Physical machines" (För VMware-datorer och fysiska datorer).
3. Klicka på ”+Replikeringsprincip” längst upp.

    ![Skapa replikeringsprincip](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Ange principens namn.

5. I Tröskelvärde för replikeringspunktmål anger du RPO-gränsen. Aviseringar genereras när den kontinuerliga replikeringen överskrider den här gränsen.
6. I Återställningspunkt för kvarhållning anger du kvarhållningsperioden i antal timmar för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en period. 

    > [!NOTE] 
    > Upp till 24 timmars kvarhållning stöds för datorer som har replikerats till premiumlagring och 72 timmars kvarhållning stöds för datorer som har replikerats till standardlagring.
    
    > [!NOTE] 
    > En replikeringsprincip för redundans skapas automatiskt.

7. I Appkompatibel ögonblicksbildsfrekvens anger du hur ofta (i minuter) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas.

8. Klicka på ”OK”. Principen bör skapas inom 30 sekunder till 1 minut.

![Skapa replikeringsprincip](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>Associera konfigurationsservern med replikeringsprincipen
1. Klicka på den replikeringsprincip som du vill associera konfigurationsservern med.
2. Klicka på ”Associera” längst upp.
![Skapa replikeringsprincip](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Välj ”Konfigurationsserver” från listan med servrar.
4. Klicka på OK. Konfigurationsservern bör associeras inom 1 till 2 minuter.

![Skapa replikeringsprincip](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>Redigera replikeringsprincip
1. Klicka på replikeringsprincipen vars replikeringsinställningar du vill ändra.
![Skapa replikeringsprincip](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Klicka på ”Redigera inställningar” längst upp.
![Skapa replikeringsprincip](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Ändra inställningarna efter behov.
4. Klicka på ”Spara” längst upp. Principen bör sparas inom 2–5 minuter beroende på hur många virtuella datorer som använder replikeringsprincipen.

![Skapa replikeringsprincip](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>Koppla bort konfigurationsservern från replikeringsprincipen
1. Klicka på den replikeringsprincip som du vill associera konfigurationsservern med.
2. Klicka på ”Ta bort koppling” längst upp.
3. Välj ”Konfigurationsserver” från listan med servrar.
4. Klicka på OK. Konfigurationsservern bör kopplas bort inom 1 till 2 minuter.
    
    > [!NOTE] 
    > Du kan inte koppla bort en konfigurationsserver om ett eller flera replikerade objekt använder principen. Kontrollera att det inte finns några replikerade objekt som använder principen innan du kopplar bort konfigurationsservern.

## <a name="delete-replication-policy"></a>Ta bort replikeringsprincip 

1. Klicka på den replikeringsprincip som du vill ta bort.
2. Klicka på Ta bort. Principen bör tas bort inom 30 sekunder till 1 minut.

    > [!NOTE] 
    > Du kan inte ta bort en replikeringsprincip om en eller flera konfigurationsservrar är associerade med den. Kontrollera att det inte finns några replikerade objekt som använder principen och ta bort alla associerade konfigurationsservrar innan du tar bort principen.


<!--HONumber=Jan17_HO4-->


