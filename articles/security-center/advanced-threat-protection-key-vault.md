---
title: Hotskydd för Azure Key Vault
description: I den här artikeln beskrivs hur du konfigurerar avancerat hotskydd för Azure Key Vault i Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914814"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Hotskydd för Azure Key Vault (förhandsversion)

Avancerat hotskydd för Azure Key Vault ger ytterligare ett lager av säkerhetsinformation. Det här verktyget identifierar potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. Med hjälp av det inbyggda avancerade hotskyddet i Azure Security Center kan du hantera hot utan att vara säkerhetsexpert och utan att lära dig ytterligare säkerhetsövervakningssystem.

När Security Center upptäcker avvikande aktivitet visas aviseringar. Det skickar också e-post till prenumerationsadministratören med information om den misstänkta aktiviteten och rekommendationer för hur du undersöker och åtgärdar de identifierade hoten.

## <a name="configuring-threat-protection-from-security-center"></a>Konfigurera skydd mot hot från Security Center

Som standard är avancerat hotskydd aktiverat för alla Key Vault-konton när du prenumererar på Security Centers standardprisnivå. Mer information finns i [Prissättning](security-center-pricing.md).

Så här aktiverar eller inaktiverar du skyddet för en viss prenumeration:

1. Välj **Priser & inställningar**i den vänstra rutan i Security Center .

1. Välj den prenumeration med de lagringskonton som du vill aktivera eller inaktivera skydd mot hot.

1. Välj **prisnivå**.

1. Leta reda på raden **Nyckelvalv** på gruppen **Välj prisnivå efter resurstyp** och välj **Aktiverad** eller **Inaktiverad**.

    [![Aktivera eller inaktivera avancerat hotskydd för Key Vault i Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Välj **Spara**.


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och inaktiverar avancerat hotskydd för Azure Key Vault. 

För relaterat material, se följande artiklar:

- [Hotskydd i Azure Security Center](threat-protection.md)– I den här artikeln beskrivs källorna till säkerhetsaviseringar i Azure Security Center.
- [Säkerhetsaviseringar för Key Vault](alerts-reference.md#alerts-azurekv)– avsnittet Nyckelvalv i referenstabellen för alla Azure Security Center-aviseringar