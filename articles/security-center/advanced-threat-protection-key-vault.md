---
title: Konfigurera Avancerat skydd för Azure Key Vault | Microsoft Docs
description: I den här artikeln förklaras hur du konfigurerar Avancerat skydd för Azure Key Vault i Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 2375d8ee92d9c04c287b7fca793fcdc236e1e8f4
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720046"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Konfigurera Avancerat skydd för Azure Key Vault (för hands version)

Avancerat skydd för Azure Key Vault ger ytterligare ett lager med säkerhets information. Det här verktyget identifierar potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. Med det inbyggda avancerade skydds skyddet i Azure Security Center kan du åtgärda hot utan att vara säkerhets expert, och utan att lära dig fler säkerhets övervaknings system.

När Security Center identifierar avvikande aktivitet visas aviseringar. Den skickar också prenumerations administratören med information om den misstänkta aktiviteten och rekommendationerna för att undersöka och åtgärda de identifierade hoten.

> [!NOTE]
> Avancerat skydd för Azure Key Vault är för närvarande endast tillgängligt i Nordamerika regioner.

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Konfigurera Avancerat skydd från Azure Security Center

Som standard är Avancerat skydd aktiverat för alla Key Vault-konton när du prenumererar på Security Center standard nivån. Mer information finns i [prissättning](security-center-pricing.md).

Följ dessa steg om du vill aktivera eller inaktivera skyddet för en speciell prenumeration.

1. I det vänstra fönstret i Security Center väljer du **pris & inställningar**.
1. Välj prenumerationen med de lagrings konton som du vill aktivera eller inaktivera hot skydd för.
1. Välj **Prisnivå**.
1. Från gruppen **Välj pris nivå efter resurs typ** letar du upp raden med **nyckel valv** och väljer **aktive** rad eller **inaktive**rad.

    [![aktivera eller inaktivera Avancerat skydd för Key Vault i Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Välj **Spara**.


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och inaktiverar Avancerat skydd mot Azure Key Vault. 

Information om annat relaterat material finns i följande artikel:

- [Hot identifiering för Azure-tjänster-skikt i Security Center](security-center-alerts-service-layer.md): den här artikeln beskriver de aviseringar som rör det avancerade skydds skyddet för Azure Key Vault.
