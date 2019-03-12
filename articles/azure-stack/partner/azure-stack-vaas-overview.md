---
title: En översikt över verifiering som en tjänst för Azure Stack | Microsoft Docs
description: En översikt över Azure Stack-verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ca22f29597cb452d6d33338b8ba0367c9377f6fb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772525"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Vad är verifiering som en tjänst för Azure Stack?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Verifiering som en tjänst (VaaS) är en intern Azure-tjänst som utformats för lösningspartners som gemensam tekniska erbjudanden för Azure Stack med Microsoft. Samarbetspartner kan använda tjänsten för att kontrollera att sina lösningar som uppfyller Microsofts krav och fungerar som förväntat med Azure Stack.

De primära användningsområden för VaaS är:

- Verifiera nya lösningar i Azure Stack
- Verifiera ändringar av Azure Stack-programvaran
- Signera lösningspaket partner som används under distributionen
- Förhandsgranskar VaaS testa säkerheter

## <a name="validate-a-new-azure-stack-solution"></a>Verifiera en ny Azure Stack-lösning

Partner använda den **lösning verifiering** arbetsflöde för att verifiera nya Azure Stack-lösningar. Lösningen måste klara Hardware Lab Kit (HLK) Azure Stack-Komponenttester som krävs. Certifiera ett antal maskinvarukonfigurationer genom arbetsflödet måste köras två gånger för varje ny lösning: en gång var och en för lägsta och högsta konfigurationer.

Mer information finns i [verifiera en ny Azure Stack-lösning](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Validera ändringar av Azure Stack-programvaran

Partner använda den **Paketvalideringen** arbetsflöde för att kontrollera att lösningen fungerar med de senaste Azure Stack-programuppdateringarna. Paketvalideringen arbetsflödet måste köras på en Microsoft-rekommenderad maskinvarumiljö där korrigeringar och uppdateringar (P & U) användes för att installera uppdateringen. Vi rekommenderar att även köra arbetsflödet på baslinje-version.

Mer information finns i [Validera programuppdateringar från Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Hämta digitalt signerat lösningen partner-paket

Förutom validerar Azure Stack-uppdateringar, partner använda den **Paketvalideringen** arbetsflöde för att verifiera uppdateringar av OEM-anpassning paket, bland annat Azure Stack-partner-specifika drivrutiner, inbyggd programvara och annan programvara används vid distribution av Azure Stack-programvaran. Distribuera paketet du validerar på den aktuella versionen av Azure Stack-programvaran som använder minst lösningen minsta storlek som stöds. Paketet skickas till VaaS innan du kör testerna. Om testerna lyckas meddelar [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) att paketet har slutfört testningen och bör vara digitalt signerade med den digitala signaturen för Azure Stack. Microsoft registrerar paketet och meddelar Azure Stack-partner att paketet är tillgänglig för hämtning i VaaS-portalen.

Mer information finns i [Validera OEM-paket](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Förhandsgranska VaaS testa säkerheter

Microsoft gör regelbundet nya funktioner tillgängliga i Azure Stack. Som en del av utvecklingsprocessen för att leverera dessa funktioner på marknaden, nya test säkerheter är tillgänglig i den **testet** arbetsflöde. Testet arbetsflödet innehåller test säkerheter från andra arbetsflöden för att tillåta inofficiella testkörning av. Använd inte testet arbetsflödet för att skicka resultaten för godkännande. Använda arbetsflöden för lösningen validering och verifiera paketet officiella godkännande för din lösning.

Mer information finns i [ Snabbstart: Använda verifieringen som en tjänst-portal för att schemalägga första testet](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Valideringsarbetsflödet testar sammanfattning

| Valideringsarbetsflödet | Tester som krävs |
|----|------------|
| [Ny solution-validering](azure-stack-vaas-validate-solution-new.md) | Cloud Simulation Engine<br>Compute SDK operativa Suite<br>Disk identifiering Test<br>KeyVault Extension SDK Operational Suite<br>KeyVault SDK Operational Suite<br>Network SDK Operational Suite<br>Storage-konto SDK operativa Suite<br> |
| [Verifiera för OEM-paketet](azure-stack-vaas-validate-oem-package.md) | Verifiering av OEM-tillägg-paketet<br>Cloud Simulation Engine |
| [Månatliga verifiering av update](azure-stack-vaas-validate-microsoft-updates.md) | Verifiering av månatlig Azure Stack-uppdatering<br>Cloud Simulation Engine<br> |

## <a name="next-steps"></a>Nästa steg

- [Ställ in din validering som en tjänstresurser](azure-stack-vaas-set-up-resources.md)
- Lär dig mer om [verifiering som en tjänst viktiga begrepp](azure-stack-vaas-key-concepts.md)
