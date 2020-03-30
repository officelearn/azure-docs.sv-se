---
title: Utfasning av haveriberedskap mellan kundhanterade platser (med VMM) med Azure Site Recovery | Microsoft-dokument
description: Information om kommande utfasning av DR mellan kundägda platser med Hyper-V och mellan platser som hanteras av SCVMM till Azure och alternativa alternativ
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661678"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Infasning av haveriberedskap mellan kundhanterade platser (med VMM) med Azure Site Recovery

I den här artikeln beskrivs den kommande utfasningsplanen, motsvarande konsekvenser och de alternativa alternativ som är tillgängliga för kunderna i följande scenario:

DR mellan kundägda platser som hanteras av System Center Virtual Machine Manager (SCVMM) med hjälp av Site Recovery

> [!IMPORTANT]
> Kunderna uppmanas att vidta åtgärder tidigast för att undvika störningar i sin miljö. 

## <a name="what-changes-should-you-expect"></a>Vilka förändringar bör du förvänta dig?

- Från och med mars 2020 får du Azure-portalmeddelanden & e-postkommunikation med den kommande utfasningen av webbplats-till-plats-replikering av virtuella hyper-virtuella datorer. Utfasningen är planerad till mars 2023.

- Om du har en befintlig konfiguration påverkas inte konfigurationen.

- När scenarierna är inaktuella om inte kunden följer de alternativa metoderna kan de befintliga replikeringarna störas. Kunder kan inte visa, hantera eller utföra dr-relaterade åtgärder via Azure Site Recovery-upplevelsen i Azure-portalen.
 
## <a name="alternatives"></a>Alternativ 

Nedan finns de alternativ som kunden kan välja mellan för att säkerställa att deras DR-strategi inte påverkas när scenariot är inaktuellt. 

- Alternativ 1 (Rekommenderas): Välj att [börja använda Azure som DR-mål](hyper-v-vmm-azure-tutorial.md).


- Alternativ 2: Välj att fortsätta med plats-till-plats-replikering med den underliggande [Hyper-V-repliklösningen](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), men du kan inte hantera DR-konfigurationer med Azure Site Recovery i Azure-portalen. 


## <a name="remediation-steps"></a>Åtgärder för reparation

Om du väljer att gå med alternativ 1, utför följande steg:

1. [Inaktivera skydd för alla virtuella datorer som är associerade med vmm-datorerna](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Använd alternativet **Inaktivera replikering och ta bort** eller kör de skript som nämns för att säkerställa att replikeringsinställningarna lokalt rensas. 

2. [Avregistrera alla VMM-servrar](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) från konfigurationen för plats-till-plats-replikering.

3. [Förbered Azure-resurser](tutorial-prepare-azure-for-hyperv.md) för att aktivera replikering av dina virtuella datorer.
4. [Förbereda lokala Hyper-V-servrar](hyper-v-prepare-on-premises-tutorial.md)
5. [Konfigurera replikering för de virtuella datorerna i VMM-molnet](hyper-v-vmm-azure-tutorial.md)
6. Tillval men rekommenderas: [Kör en DR-borr](tutorial-dr-drill-azure.md)

Om du väljer att gå med alternativ 2 för att använda Hyper-V-replik kör du följande steg:

1. Högerklicka på datorn > **Inaktivera replikering**i **Skyddade objekt** > **replikerade objekt**.
2. Välj **Ta bort**i **Inaktivera replikering**.

    Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Replikeringskonfigurationen på den lokala virtuella datorn rensas **inte.** 

## <a name="next-steps"></a>Nästa steg
Planera för utfasningen och välj ett alternativt alternativ som passar bäst för din infrastruktur och ditt företag. Om du har några frågor om detta, kontakta Microsoft Support

