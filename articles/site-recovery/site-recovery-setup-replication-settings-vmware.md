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
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/05/2017
ms.author: sutalasi
ms.openlocfilehash: 73a1f19177f23441f5f7165cf2bc92ba85e62aa5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Hantera en replikeringsprincip för VMware till Azure


## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Välj **Hantera** > **Site Recovery-infrastruktur**.
2. Välj **Replikeringsprinciper** under **For VMware and Physical machines** (För VMware-datorer och fysiska datorer).
3. Välj **+Replikeringsprincip**.

    ![Skapa replikeringsprincip](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Ange principens namn.

5. I **tröskelvärdet för RPO** anger du RPO (mål för återställningspunkt)-gränsen. Aviseringar genereras när den kontinuerliga replikeringen överskrider den här gränsen.
6. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en kvarhållningsperiod.

    > [!NOTE]
    > Upp till 24 timmars kvarhållning stöds för datorer som har replikerats till Premium Storage. Upp till 72 timmars kvarhållning stöds för datorer som har replikerats till Standard Storage.

    > [!NOTE]
    > En replikeringsprincip för redundans skapas automatiskt.

7. I **Appkompatibel ögonblicksbildsfrekvens** anger du hur ofta (i minuter) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas.

8. Klicka på **OK**. Principen bör skapas inom 30 till 60 sekunder.

![Replikeringsprincipsgeneration](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-a-configuration-server-with-a-replication-policy"></a>Associera en konfigurationsserver med en replikeringsprincip
1. Välj den replikeringsprincip som du vill associera med konfigurationsservern.
2. Klicka på **Associera**.
![Associera konfigurationsserver](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Välj konfigurationsservern från listan med servrar.
4. Klicka på **OK**. Konfigurationsservern bör associeras inom en till två minuter.

![Associering av konfigurationsserver](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-a-replication-policy"></a>Redigera en replikeringsprincip
1. Välj replikeringsprincipen vars replikeringsinställningar du vill ändra.
![Redigera replikeringsprincip](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Klicka på **Redigera inställningar**.
![Redigera inställningar för replikeringsprinciper](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Ändra inställningarna efter behov.
4. Klicka på **Save** (Spara). Principen bör sparas inom två till fem minuter beroende på hur många virtuella datorer som använder replikeringsprincipen.

![Spara replikeringsprincip](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-a-configuration-server-from-a-replication-policy"></a>Koppla bort en konfigurationsserver från replikeringsprincipen
1. Välj den replikeringsprincip som du vill associera med konfigurationsservern.
2. Klicka på **Koppla bort**.
3. Välj konfigurationsservern från listan med servrar.
4. Klicka på **OK**. Konfigurationsservern bör kopplas bort inom en till två minuter.

    > [!NOTE]
    > Du kan inte koppla bort en konfigurationsserver om ett eller flera replikerade objekt använder principen. Kontrollera att det inte finns några replikerade objekt som använder principen innan du kopplar bort konfigurationsservern.

## <a name="delete-a-replication-policy"></a>Skapa en replikeringsprincip

1. Välj den replikeringsprincip som du vill ta bort.
2. Klicka på **Ta bort**. Principen bör tas bort inom 30 till 60 sekunder.

    > [!NOTE]
    > Du kan inte ta bort en replikeringsprincip om en eller flera konfigurationsservrar är associerade med den. Kontrollera att det inte finns några replikerade objekt som använder principen och ta bort alla associerade konfigurationsservrar innan du tar bort principen.
