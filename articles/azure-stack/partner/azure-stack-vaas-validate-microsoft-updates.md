---
title: Verifiera programuppdateringar från Microsoft i Azure Stack-verifiering som en tjänst | Microsoft Docs
description: Lär dig hur du validerar programuppdateringar från Microsoft med verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/14/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b7fa03cdf52fc3218e9556c9664daafdc60243f3
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593225"
---
# <a name="validate-software-updates-from-microsoft"></a>Verifiera programuppdateringar från Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft släpper regelbundet uppdateringar av Azure Stack-programvaran. De här uppdateringarna tillhandahålls till Azure Stack coengineering partner. Uppdateringarna tillhandahålls i förväg av offentligt tillgängliga. Du kan kontrollera uppdateringarna mot din lösning och lämna feedback till Microsoft.

Microsoft-programuppdateringar till Azure Stack betecknas med en namngivningskonvention, till exempel 1803 som anger uppdateringen för mars 2018. Information om Azure Stack-uppdateringsprincip takt och viktig information som är tillgängliga, se [Azure Stack hanteringsprincip](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

## <a name="prerequisites"></a>Förutsättningar

Innan du arbeta med månatliga uppdateringen i VaaS, bör du känna till följande objekt:

- [Verifiering som en tjänst viktiga begrepp](azure-stack-vaas-key-concepts.md)
- [Interaktiva funktionen verifiering testning](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Tester som krävs

Följande tester ska köras i följande ordning för månatliga programvara-verifiering:

1. Verifiering av månatlig Azure Stack-uppdatering
2. Cloud Simulation Engine

## <a name="validating-software-updates"></a>Verifiering av programuppdateringar

1. Skapa en ny **Paketvalideringen** arbetsflöde.
1. För de nödvändiga testerna ovan följer du anvisningarna från [kör Paketvalideringen testar](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Finns i avsnittet nedan för ytterligare information om den **månatliga Azure Stack Update verifiering** testa.

### <a name="apply-the-monthly-update"></a>Tillämpa månadsuppdateringen

1. Välj en agent för att köra tester mot.
1. Schema **verifiering av månatlig Azure Stack-uppdatering**.
1. Ange platsen för paketets OEM-tillägget för närvarande har distribuerats på stämpeln och platsen som OEM-tillägg-paket som ska användas under uppdateringen. För att konfigurera URL: er för dessa paket, se [hantera paket för verifiering](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Följ stegen i Användargränssnittet från den markerade agenten.

Om du har frågor eller funderingar kan du kontakta [VaaS hjälp](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera tester i VaaS-portalen](azure-stack-vaas-monitor-test.md)