---
title: Konfigurera replikeringsprinciper för VMware-haveriberedskap med Azure Site Recovery| Microsoft-dokument
description: Beskriver hur du konfigurerar replikeringsinställningar för VMware-haveriberedskap till Azure med Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257126"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Konfigurera och hantera replikeringsprinciper för VMware-haveriberedskap

I den hÃ¤r artikeln beskrivs hur du konfigurerar en replikeringsprincip nÃ¤r du replikerar virtuella VMware-datorer till Azure med [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Skapa en princip

1. Välj **Hantera** > **infrastruktur för platsåterställning**.
2. I **För VMware och Fysiska datorer**väljer du **Replikeringsprinciper**.
3. Klicka på **+Replikeringsprincip**och ange principnamnet.
4. I **tröskelvärdet för RPO** anger du RPO (mål för återställningspunkt)-gränsen. Aviseringar genereras när kontinuerlig replikering överskrider den här gränsen.
5. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en kvarhållningsperiod. Upp till 24 timmars kvarhållning stöds för datorer som har replikerats till Premium Storage. Upp till 72 timmar stöds för standardlagring.
6. I **Appkonsekvent ögonblicksbildfrekvens**väljer du i listrutan hur ofta (i timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Om du vill inaktivera generering av programkonsekvenspunkter väljer du "Av"-värde i listrutan.
7. Klicka på **OK**. Principen bör skapas inom 30 till 60 sekunder.

När du skapar en replikeringsprincip skapas automatiskt en matchande replikeringsprincip för återställning efter fel, med suffixet "failback". När du har skapat principen kan du redigera den genom att markera den > **Redigera inställningar**.

## <a name="associate-a-configuration-server"></a>Associera en konfigurationsserver

Associera replikeringsprincipen med den lokala konfigurationsservern.

1. Klicka på **Associera**och välj konfigurationsservern.

    ![Associera konfigurationsserver](./media/vmware-azure-set-up-replication/associate1.png)
2. Klicka på **OK**. Konfigurationsservern bör associeras inom en till två minuter.

    ![Associering av konfigurationsserver](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Redigera en princip

Du kan ändra en replikeringsprincip när du har skapat den.

- Ändringar i principen tillämpas på alla datorer som använder principen.
- Om du vill associera replikerade datorer med en annan replikeringsprincip måste du inaktivera och återanvända skydd för relevanta datorer.

Redigera en princip på följande sätt:
1. Välj **Hantera** > principer för replikering av**platsåterställningsinfrastruktur** > **Replication Policies**.
2. Välj den replikeringsprincip som du vill ändra.
3. Klicka på **Redigera inställningar**och uppdatera RPO-tröskelvärdet/återställningspunkten kvarhållningstimmar/appkonsekventa ögonblicksbildfrekvensfält efter behov.
4. Om du vill inaktivera generering av programkonsekvenspunkter väljer du "Av"-värde i listrutan för fältet **Appkonsekvent ögonblicksbildfrekvens**.
5. Klicka på **Spara**. Principen bör uppdateras om 30 till 60 sekunder.



## <a name="disassociate-or-delete-a-replication-policy"></a>Ta bort eller ta bort en replikeringsprincip

1. Välj replikeringsprincipen.
    a. Om du vill ta bort principen från konfigurationsservern kontrollerar du att inga replikerade datorer använder principen. Klicka sedan på **Ta bort.**
    b. Om du vill ta bort principen kontrollerar du att den inte är associerad med en konfigurationsserver. Klicka sedan på **Ta bort**. Det bör ta 30-60 sekunder att ta bort.
2. Klicka på **OK**.
