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
ms.date: 1/07/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 8ea0f36f3d167109fc0dc0d91707a08cff1c6a17
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243992"
---
# <a name="validate-software-updates-from-microsoft"></a>Verifiera programuppdateringar från Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft släpper regelbundet uppdateringar av Azure Stack-programvaran. De här uppdateringarna tillhandahålls till Azure Stack coengineering partner. Uppdateringarna tillhandahålls i förväg av offentligt tillgängliga. Du kan kontrollera uppdateringarna mot din lösning och lämna feedback till Microsoft.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Tillämpa månatliga uppdatering

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Skapa ett arbetsflöde

Uppdatera verifieringar använder samma arbetsflöde som **lösning verifiering**.

## <a name="run-tests"></a>Kör test

1. Uppdatera verifieringar använder samma arbetsflöde som **lösning verifiering**. 

2. Följ anvisningarna på [kör lösningen verifiering testar](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Välj följande tester i stället:
    - Verifiering av månatlig Azure Stack-uppdatering
    - Simulering Molnteknik

Du behöver inte begära paketet registrerar för uppdatering verifieringar.

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera tester i VaaS-portalen](azure-stack-vaas-monitor-test.md)