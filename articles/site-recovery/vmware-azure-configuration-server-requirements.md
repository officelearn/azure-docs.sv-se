---
title: Konfigurations Server krav för VMware haveri beredskap till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver support och krav vid distribution av konfigurations servern för VMware haveri beredskap till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: d83b99ea540d6232f4c0786d3a743f97332e1c9f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792323"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Konfigurations Server krav för VMware haveri beredskap till Azure

Du distribuerar en lokal konfigurations server när du använder [Azure Site Recovery](site-recovery-overview.md) för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure.

- Konfigurations servern samordnar kommunikationen mellan lokala VMware och Azure. Den hanterar också datareplikering.
- [Läs mer](vmware-azure-architecture.md) om konfigurations serverns komponenter och processer.

## <a name="configuration-server-deployment"></a>Distribution av konfigurations Server

För haveri beredskap för virtuella VMware-datorer till Azure distribuerar du konfigurations servern som en virtuell VMware-dator.

- Site Recovery innehåller en tjänstmall som du laddar ned från Azure Portal och importera till vCenter Server för att konfigurera den virtuella konfigurations servern.
- När du distribuerar konfigurations servern med hjälp av mallen för ägg, uppfyller den virtuella datorn automatiskt kraven i den här artikeln.
- Vi rekommenderar starkt att du konfigurerar konfigurations servern med hjälp av en ägg-mall. Men om du konfigurerar haveri beredskap för virtuella VMware-datorer och inte kan använda embryo-mallen kan du distribuera konfigurations servern med hjälp av [dessa instruktioner](physical-azure-set-up-source.md).
- Om du distribuerar konfigurations servern för haveri beredskap för lokala fysiska datorer till Azure följer du anvisningarna i [den här artikeln](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Nästa steg
Konfigurera katastrof återställning av [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.
