---
title: Så här konfigurerar du Avancerat skydd för Azure Key Vault | Microsoft Docs
description: I den här artikeln förklaras hur du konfigurerar Avancerat skydd för Azure Key Vault i Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521883"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Konfigurera Avancerat skydd för Azure Key Vault (för hands version)

Avancerat skydd för Azure Key Vault ger ytterligare ett lager med säkerhets information. Det här verktyget identifierar potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. Genom att använda Security Centers inbyggda avancerade hot skydd kan du åtgärda hot utan att vara säkerhets expert, och utan att lära dig fler säkerhets övervaknings system.

När Security Center identifierar avvikande aktivitet visas aviseringar. Den skickar också prenumerations administratören med information om den misstänkta aktiviteten och rekommendationerna för att undersöka och åtgärda de identifierade hoten. 

> [!NOTE]
> Avancerat skydd för Azure Key Vault är för närvarande endast tillgängligt i Nordamerika regioner.

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>Konfigurera Avancerat skydd från Azure Security Center

Som standard aktive ras Avancerat skydd för alla Key Vault-konton när du prenumererar på Security Center standard nivån (se [prissättning](security-center-pricing.md)). 

Så här aktiverar eller inaktiverar du skyddet för en speciell prenumeration:

1. Klicka på **prissättning & inställningar**från Security Center marginal List.
1. Välj prenumerationen med de lagrings konton som du vill aktivera eller inaktivera hot skydd för.
1. Klicka på **pris nivå**.
1. Från gruppen **Välj pris nivå efter resurs typ** letar du upp raden med nyckel valv och klickar på **aktive** rad eller **inaktive**rad.
    [![aktivera eller inaktivera det avancerade skydds skyddet för Key Vault i Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Klicka på **Spara**.


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och inaktiverar Avancerat skydd mot Azure Key Vault. 

Information om annat relaterat material finns i följande artikel:

- [Hot identifiering för Azure-tjänster-skikt i Security Center – i](security-center-alerts-service-layer.md) den här artikeln beskrivs aviseringar relaterade till avancerat skydd för Azure Key Vault