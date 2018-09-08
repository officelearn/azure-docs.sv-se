---
title: En översikt över verifiering som en tjänst för Azure Stack | Microsoft Docs
description: En översikt över Azure Stack-verifiering som en tjänst som är kända problem.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 56251245a23df031f3bc8fe3d36de43e194fbcc7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159681"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Vad är verifiering som en tjänst för Azure Stack?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Verifiering som en tjänst (VaaS) är en intern Azure-tjänst som utformats för lösningspartners som gemensam tekniska erbjudanden för Azure Stack med Microsoft. Samarbetspartner kan använda tjänsten för att kontrollera att sina lösningar som uppfyller Microsofts krav och fungerar som förväntat med Azure Stack.

Primär användning för VaaS är:

- Verifiera nya lösningar i Azure Stack
- Validera ändringar av Azure Stack-programvaran
- Hämta digitalt signerat lösningspartner paket som används under distributionen
- Förhandsversion av Azure Stack-verifiering säkerheter

## <a name="validate-new-azure-stack-solution"></a>Verifiera ny Azure Stack-lösning

Partner använda valideringsarbetsflödet lösning för att kontrollera nya Azure Stack-lösningar. Lösningen måste klara Komponenttester som krävs Hardware Lab Kit (HKL) Azure Stack tester. Du behöver bara att köra arbetsflödet två gånger för varje ny lösning: en gång för lägsta och högsta konfigurationen.

Mer information finns i [verifiera en ny Azure Stack-lösning](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Validera ändringar av Azure Stack-programvaran

Partner använda valideringsarbetsflödet paketet för att kontrollera att lösningen fungerar med den senaste uppdateringen av Azure Stack. Minst måste valideringsarbetsflödet paketet köras på maskinvarumiljö som rekommenderas av Microsoft och på en lösning där korrigerings- och uppdateringsinformation (P & U) användes för att installera uppdateringen. Du bör köra verifiera paketet baslinje-version.

Mer information finns i [Validera programuppdateringar från Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Hämta digitalt signerat lösningen partner-paket

Förutom validerat Azure Stack-uppdateringar kan använda du valideringsarbetsflödet paketet för att kontrollera uppdateringar av OEM-anpassning paket, bland annat Azure Stack-partner-specifika drivrutiner, inbyggd programvara och annan programvara som används vid distribution av Azure Stack programvara. Distribuera paketet som du markerar på den aktuella versionen av Azure Stack-programvaran som med en minsta den minsta storlek lösning som stöds. Det uppdaterade paketet måste överföras till tjänsten innan du startar testet. Om testerna lyckas meddelar vaashelp@microsoft.com. Berätta för Azure Stack-partner att paketet har slutfört testningen och vara digitalt signerade med den digitala signaturen för Azure Stack. Microsoft registrerar paketet och meddelar Azure Stack-partner att paketet är tillgänglig för hämtning i portalen.

Mer information finns i [Validera OEM-paket](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-azure-stack-validation-collateral"></a>Förhandsversion av Azure Stack-verifiering säkerheter

Microsoft gör regelbundet nya funktioner tillgängliga i Azure Stack. Som en del av utvecklingsprocessen för att leverera dessa funktioner på marknaden, görs nya test säkerheter tillgänglig i arbetsflödet test-pass. Testet arbetsflödet innehåller test säkerheter från andra arbetsflöden för att tillåta inofficiella testkörning av. Använd inte arbetsflödet test-pass för att skicka resultaten för godkännande. Använda arbetsflödet för lösningen validering och paketet verifieringar för att få officiella godkännande för din lösning.

## <a name="next-steps"></a>Nästa steg

- Kom igång och [ställa in din validering som ett tjänstkonto](azure-stack-vaas-validate-solution-new.md)
