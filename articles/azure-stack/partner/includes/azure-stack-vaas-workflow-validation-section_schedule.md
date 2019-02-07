---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 5cd64b806392162fd3bee14ddaf607385ac05264
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807183"
---
I arbetsflöden verifiering **schemaläggning** ett test använder de gemensamma parametrarna för arbetsflödet på servernivå som du angav under arbetsflödet skapats (se [arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst](../azure-stack-vaas-parameters.md)). Om någon av test parametervärden blir ogiltiga måste du resupply dem som finns beskrivet i [ändra arbetsflödesparametrar](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Schemalägga ett verifieringstest över en befintlig instans skapas en ny instans i stället för den gamla instansen i portalen. Loggar för den gamla instansen kommer att hållas kvar, men är inte tillgängliga från portalen.  
När ett test har slutförts, den **schema** åtgärden inaktiveras.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Välj **schema** från snabbmenyn för att öppna en kommandotolk för att schemalägga test-instans.

1. Granska de test-parametrarna och välj sedan **skicka** att schemalägga testet för körning.