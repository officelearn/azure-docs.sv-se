---
title: VMware-serverkrav för haveriberedskapskonfiguration i Azure Site Recovery
description: I den här artikeln beskrivs support och krav vid distribution av konfigurationsservern för VMware-haveriberedskap till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257412"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Konfigurationsserverkrav för VMware-haveriberedskap till Azure

Du distribuerar en lokal konfigurationsserver när du använder [Azure Site Recovery](site-recovery-overview.md) för haveriberedskap av virtuella datorer och fysiska servrar till Azure.

- Konfigurationsservern samordnar kommunikationen mellan lokala VMware och Azure. Den hanterar också datareplikering.
- [Läs mer](vmware-azure-architecture.md) om konfigurationsserverns komponenter och processer.

## <a name="configuration-server-deployment"></a>Distribution av konfigurationsserver

För haveriberedskap av virtuella datorer med VMware till Azure distribuerar du konfigurationsservern som en virtuell virtuell VMware-dator.

- Site Recovery innehåller en OVA-mall som du hämtar från Azure-portalen och importerar till vCenter Server för att konfigurera konfigurationsservern VM.
- När du distribuerar konfigurationsservern med OVA-mallen uppfyller den virtuella datorn automatiskt kraven i den här artikeln.
- Vi rekommenderar starkt att du ställer in konfigurationsservern med hjälp av OVA-mallen. Men om du konfigurerar haveriberedskap för virtuella datorer med VMware och inte kan använda OVA-mallen kan du distribuera [konfigurationsservern](physical-azure-set-up-source.md)med hjälp av dessa instruktioner .
- Om du distribuerar konfigurationsservern för haveriberedskap av lokala fysiska datorer till Azure följer du anvisningarna i den [här artikeln](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Nästa steg
Konfigurera haveriberedskap av virtuella datorer med [VMware](vmware-azure-tutorial.md) till Azure.
