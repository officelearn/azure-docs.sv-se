---
title: Konfigurera och hantera replikeringsprinciper för VMware-haveriberedskap till Azure med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du konfigurerar replikeringsinställningar för VMware-haveriberedskap till Azure med Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 4f558d8d1e5e4e743e6d44cca1a804b3e5898b30
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443420"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>Konfigurera och hantera replikeringsprinciper för VMware-haveriberedskap till Azure
Den här artikeln beskriver hur du konfigurerar en replikeringsprincip när du är replikerar virtuella VMware-datorer till Azure, med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Skapa en princip

1. Välj **Hantera** > **Site Recovery-infrastruktur**.
2. I **för VMware och fysiska datorer**väljer **replikeringsprinciper**. 
3. Klicka på **+ replikeringsprincip**, och ange namnet på principen.
4. I **tröskelvärdet för RPO** anger du RPO (mål för återställningspunkt)-gränsen. Aviseringar genereras när kontinuerliga replikeringen överskrider den här gränsen.
5. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en kvarhållningsperiod. Upp till 24 timmars kvarhållning stöds för datorer som har replikerats till Premium Storage. Upp till 72 timmar stöds för standardlagring.
6. I **appkonsekvent ögonblicksbildsfrekvens**, Välj i listrutan hur ofta (i timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Om du vill stänga av generering av programmet konsekvens aspekterna av väljer du ”Off” värdet i listrutan.
7. Klicka på **OK**. Principen bör skapas inom 30 till 60 sekunder.

När du skapar en replikeringsprincip, skapas en motsvarande replikeringsprincip för återställning efter fel automatiskt, med suffixet ”återställning”. När du har skapat principen kan du redigera den genom att välja den > **redigera inställningar för**.

## <a name="associate-a-configuration-server"></a>Associera en konfigurationsserver 

Koppla replikeringsprincipen till den lokala konfigurationsservern.

1. Klicka på **associera**, och välj configuration server.

    ![Associera konfigurationsserver](./media/vmware-azure-set-up-replication/associate1.png)
2. Klicka på **OK**. Konfigurationsservern bör associeras inom en till två minuter.

    ![Associering av konfigurationsserver](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Redigera en princip

1. Välj **hantera** > **Site Recovery-infrastruktur** > **replikeringsprinciper**.
2. Välj den replikeringsprincip som du vill ändra.
3. Klicka på **redigera inställningar för**, och uppdatera fält som behöver RPO/återställning av tröskelvärde punkt kvarhållning timmar/appkompatibel ögonblicksbild frekvens.
4. Om du vill stänga av generering av programmet konsekvens aspekterna av väljer du ”Off” värdet i listrutan för fältet **appkonsekvent ögonblicksbildsfrekvens**.
5. Klicka på **Spara**. Principen bör uppdateras inom 30 till 60 sekunder.

## <a name="disassociate-or-delete-a-replication-policy"></a>Ta bort koppling till eller ta bort en replikeringsprincip

1. Välj replikeringsprincipen.
    a. Om du vill ta bort princip från konfigurationsservern, se till att inga replikerade datorer använder principen. Klicka sedan på **koppla bort**.
    b. Kontrollera att det inte associeras med en konfigurationsserver för att ta bort principen. Klicka sedan på **ta bort**. Det bör ta mellan 30 och 60 sekunder att ta bort.
2. Klicka på **OK**.
