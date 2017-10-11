---
title: "Konfigurera en replikeringsprincip för Hyper-V-replikering till en sekundär plats med Azure Site Recovery | Microsoft Docs"
description: "Beskriver hur du ställer in en princip för replikering av Hyper-V-dator till en sekundär plats för VMM med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5d9b79cf-89f2-4af9-ac8e-3a32ad8c6c4d
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: a3ecc555e049914d7bffdddce7b0522f09362d24
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="step-8-set-up-a-replication-policy"></a>Steg 8: Skapa en replikeringsprincip

När du har konfigurerat [nätverksmappning](vmm-to-vmm-walkthrough-network-mapping.md), använda den här artikeln för att ställa in en replikeringsprincip för replikering av Hyper-V virtuella datorer (VM) till en sekundär plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).

När du har läst den här artikeln kan du lämna kommentarer längst ned på sidan eller på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Innan du börjar

- När du skapar en replikeringsprincip måste alla värdar med principen ha samma operativsystem. VMM-molnet kan innehålla Hyper-V-värdar som kör olika versioner av Windows Server, men i detta fall måste flera replikeringsprinciper.
- Du kan utföra den första replikeringen offline.

## <a name="configure-replication-settings"></a>Konfigurera replikeringsinställningar

1. Skapa en ny replikeringsprincip genom att klicka på **Förbered infrastruktur** > **Replikeringsinställningar** > **+Skapa och koppla**.

    ![Nätverk](./media/vmm-to-vmm-walkthrough-replication/gs-replication.png)
2. I **Princip för att skapa och koppla** anger du ett principnamn. Typen för källan och målet ska vara **Hyper-V**.
3. I **Hyper-V-värdversion**, Välj vilket operativsystem som körs på värden.
4. I **autentiseringstyp** och **autentiseringsport**, ange hur trafik autentiseras mellan de primära platsen och återställningsplatsen Hyper-V-värdservrarna. Välj **certifikat** om du inte har en fungerande Kerberos-miljö. Azure Site Recovery kommer automatiskt att konfigurera certifikat för HTTPS-autentisering. Du behöver inte göra något manuellt. Som standard öppnas port 8083 och 8084 (för certifikat) i Windows-brandväggen på Hyper-V-värdservrar. Om du väljer **Kerberos**, en Kerberos-biljett används för ömsesidig autentisering av värdservrar. Observera att den här inställningen gäller endast för Hyper-V-värdservrar som körs på Windows Server 2012 R2.
5. I **Kopieringsfrekvens** anger du hur ofta du vill replikera förändringsdata (delta) efter den första replikeringen (med 30 sekunders mellanrum, var femte minut eller varje kvart).
6. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden i antal timmar för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en period.
7. I **frekvens av programkonsekventa ögonblicksbilder**, ange hur ofta (1 – 12 timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder skapas. Hyper-V använder två typer av ögonblicksbilder: en standardögonblicksbild som tillhandahåller en inkrementell ögonblicksbild av hela den virtuella datorn och en programkonsekvent ögonblicksbild som tar en ögonblicksbild vid en viss tidpunkt av programdata på den virtuella datorn. Programkonsekventa ögonblicksbilder använda VSS (Volume Shadow Copy Service) för att säkerställa att programmen är i ett konsekvent tillstånd när ögonblicksbilden tas. Om du aktiverar programkonsekventa ögonblicksbilder så påverkar prestanda för program som körs på virtuella källdatorer. Kontrollera att värdet som du anger är mindre än antalet ytterligare återställningspunkter som du konfigurerar.
8. I **dataöverföring komprimering**, ange om replikerade data som överförs ska komprimeras.
9. Välj **ta bort replikering VM**, för att ange att den replikerade virtuella datorn ska tas bort om du inaktiverar skydd för den Virtuella källdatorn. Om du aktiverar den här inställningen när du inaktiverar skyddet för källan VM det tas bort från Site Recovery-konsolen, Site Recovery-inställningarna för VMM tas bort från VMM-konsolen och repliken tas bort.
10. I **inledande Replikeringsmetod**, om du replikerar över nätverket, ange om du vill starta den inledande replikeringen eller schemalägga. Om du vill spara bandbredd i nätverket, kanske du vill schemalägga den utanför kontorstid. Klicka sedan på **OK**.

     ![Replikeringsprincip](./media/vmm-to-vmm-walkthrough-replication/gs-replication2.png)
11. När du skapar en ny princip associeras den automatiskt med VMM-molnet. I **replikeringsprincipen**, klickar du på **OK**. Du kan associera ytterligare VMM-moln (och de virtuella datorerna i dem) med den här replikeringsprincipen i **replikering** > principnamn > **associera VMM-moln**.

     ![Replikeringsprincip](./media/vmm-to-vmm-walkthrough-replication/policy-associate.png)



## <a name="prepare-for-offline-initial-replication"></a>Förbereda för inledande offlinereplikering

Du kan göra offlinereplikering för första Datakopieringen. Du kan förbereda på följande sätt:

* På källservern, kan du ange en sökväg där dataexporten sker. Tilldela fullständig behörighet för NTFS och dela behörigheter till VMM-tjänsten på exportsökvägen. På målservern, kan du ange en sökväg där dataimporten sker. Tilldela samma behörigheter på den här importsökvägen.
* Om import- eller sökvägen är delad tilldela gruppmedlemskap för administratörer, Privilegierade användare, Skrivaransvariga eller serveransvarig för VMM-tjänstkontot på den fjärrdator där den delade finns.
* Om du använder en Kör som-konton för att lägga till värdar i import- och exportsökvägar tilldela Läs- och skrivbehörighet till Kör som-konton i VMM.
* Importera och exportera resurser bör inte finnas på en dator som används som en Hyper-V-värdserver eftersom loopback konfigurationen inte stöds av Hyper-V.
* I Active Directory på varje Hyper-V begränsad-värdserver som innehåller virtuella datorer som du vill skydda, aktivera och konfigurera delegering ska lita på fjärranslutna datorer där import- och exportsökvägar finns, enligt följande:
  1. Öppna på domänkontrollanten, **Active Directory-användare och datorer**.
  2. I konsolträdet klickar du på **DomainName** > **datorer**.
  3. Högerklicka på servernamnet för Hyper-V-värden > **egenskaper**.
  4. På den **delegering** klickar du på **förtroende för den här datorn för delegering till angivna tjänster**.
  5. Klicka på **Använd valfritt autentiseringsprotokoll**.
  6. Klicka på **lägga till** > **användare och datorer**.
  7. Skriv namnet på den dator som är värd för exportsökvägen > **OK**. Från listan över tillgängliga tjänster, håll ned CTRL och klicka på **cifs** > **OK**. Upprepa för namnet på den dator som är värd för importsökvägen. Upprepa efter behov för ytterligare Hyper-V-värdservrar.



## <a name="next-steps"></a>Nästa steg

Gå till [steg 9: Aktivera replikering](vmm-to-vmm-walkthrough-enable-replication.md).
