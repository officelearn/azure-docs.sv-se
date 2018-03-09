---
title: "Konfigurera och hantera replikeringsprinciper för VMware-replikering med Azure Site Recovery | Microsoft Docs"
description: "Beskriver hur du konfigurerar replikeringsinställningar för VMware-replikering till Azure med Azure Site Recovery."
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: sutalasi
ms.openlocfilehash: 6a8e6e7c6fbdbcbf58557a0896e976a608164041
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="configure-and-manage-replication-policies-for-vmware-replication"></a>Konfigurera och hantera replikeringsprinciper för VMware-replikering
Den här artikeln beskriver hur du konfigurerar en replikeringsprincip när du vill replikera virtuella VMware-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Skapa en princip

1. Välj **Hantera** > **Site Recovery-infrastruktur**.
2. I **för VMware och fysiska datorer**väljer **replikeringsprinciper**. 
3. Klicka på **+ replikeringsprincip**, och ange namnet på principen.
5. I **tröskelvärdet för RPO** anger du RPO (mål för återställningspunkt)-gränsen. Aviseringar genereras när kontinuerlig replikering överskrider den gränsen.
6. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en kvarhållningsperiod. Upp till 24 timmars kvarhållning stöds för datorer som har replikerats till Premium Storage. Upp till 72 timmar stöds för standardlagring.
7. I **Appkompatibel ögonblicksbildsfrekvens** anger du hur ofta (i minuter) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas.
8. Klicka på **OK**. Principen bör skapas inom 30 till 60 sekunder.

När du skapar en replikeringsprincip skapas en matchande replikeringsprincip automatiskt, med suffixet ”återställning”. När du har skapat principen, kan du redigera den genom att välja den > **redigera inställningar för**.

## <a name="associate-a-configuration-server"></a>Associera en konfigurationsserver 

Koppla replikeringsprincipen till din lokala konfigurationsservern.

1. Klicka på **associera**, och välj konfigurationsservern.

    ![Associera konfigurationsservern](./media/vmware-azure-set-up-replication/associate1.png)

2. Klicka på **OK**. Konfigurationsservern bör associeras inom en till två minuter.

    ![Associering av konfigurationsserver](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Koppla eller ta bort en replikeringsprincip
1. Välj replikeringsprincipen.
    a. Kontrollera att inga replikerade datorer använder principen om du vill koppla bort principen från konfigurationsservern. Klicka på **ta bort kopplingen**.
    b. Kontrollera att den inte är kopplad till en server configuration för att ta bort principen. Klicka på **ta bort**. Det bör ta 30 till 60 sekunder att ta bort.
2. Klicka på **OK**.
