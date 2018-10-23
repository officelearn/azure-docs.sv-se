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
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7fcc7d5a1d87fe93d32772dbbb84f1d3c91d5631
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648793"
---
# <a name="validate-software-updates-from-microsoft"></a>Verifiera programuppdateringar från Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft släpper regelbundet uppdateringar av Azure Stack-programvaran. De här uppdateringarna tillhandahålls till Azure Stack delad engineering partners förväg som görs tillgänglig så att de kan verifiera uppdateringarna mot sina lösningar och lämna feedback till Microsoft.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Tillämpa månatliga uppdatering

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Skapa ett arbetsflöde

Uppdatera verifieringar använder samma arbetsflöde som **Paketvalideringen**. Följ anvisningarna på [skapar ett arbetsflöde för Paketvalideringen](azure-stack-vaas-validate-oem-package.md#create-a-package-validation-workflow).

## <a name="run-tests"></a>Kör tester

Uppdatera verifieringar använder samma arbetsflöde som **Paketvalideringen**. Följ anvisningarna på [köra Paketvalideringen testar](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests).

Du behöver inte begära paketet registrerar för uppdatering verifieringar.

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera tester i VaaS-portalen](azure-stack-vaas-monitor-test.md)
