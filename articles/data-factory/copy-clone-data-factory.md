---
title: Kopiera eller klona en data fabrik i Azure Data Factory
description: Lär dig hur du kopierar eller klonar en data fabrik i Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 5e44bda8648fbf26487b04cf36a8fd0ec085c411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414109"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopiera eller klona en data fabrik i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här artikeln beskrivs hur du kopierar eller klonar en data fabrik i Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Använda fall för kloning av en data fabrik

Här följer några situationer där det kan vara användbart att kopiera eller klona en data fabrik:

-   **Byta namn på resurser**. Azure har inte stöd för att byta namn på resurser. Om du vill byta namn på en data fabrik kan du klona data fabriken med ett annat namn och sedan ta bort den befintliga.

-   **Fel sökning av ändringar** när fel söknings funktionerna inte räcker. Ibland kan det vara bra att testa dina ändringar i en annan fabrik innan du tillämpar dem på ditt huvud konto. I de flesta fall kan du använda fel sökning. Ändringar i utlösare, men till exempel hur dina ändringar beter sig när en utlösare anropas automatiskt, eller över ett tidsfönster, kanske inte är testable enkelt utan att checka in. I dessa fall är det mycket bra att klona fabriken och tillämpa dina ändringar. Eftersom Azure Data Factory avgifter främst för antalet körningar leder den andra fabriken inte till några ytterligare avgifter.

## <a name="how-to-clone-a-data-factory"></a>Så här klonar du en data fabrik

1. Med Data Factory gränssnittet i Azure Portal kan du exportera hela nytto lasten för din data fabrik till en Resource Manager-mall, tillsammans med en parameter fil som du kan använda för att ändra de värden som du vill ändra när du klonar din fabrik.

1. Som en förutsättning måste du skapa mål data fabriken från Azure Portal.

1. Om du har en SelfHosted-IntegrationRuntime i din käll fabrik måste du förskapa den med samma namn i mål fabriken. Om du vill dela SelfHosted-IRs mellan olika fabriker kan du använda det mönster som publicerats [här](source-control.md#best-practices-for-git-integration).

1. Om du är i GIT-läge, varje gång du publicerar från portalen, sparas fabriks resurs hanterarens Resource Manager-mall i GIT i adf_publish grenen för lagrings platsen.

1. I andra scenarier kan du hämta Resource Manager-mallen genom att klicka på knappen **Exportera Resource Manager-mall** i portalen.

1. När du har hämtat Resource Manager-mallen kan du distribuera den via standard metoder för distribution av Resource Manager-mallar.

1. Av säkerhets skäl innehåller den genererade Resource Manager-mallen ingen hemlig information, till exempel lösen ord för länkade tjänster. Därför måste du ange dessa lösen ord som distributions parametrar. Om det inte är önskvärt att tillhandahålla parametrar måste du hämta anslutnings strängarna och lösen orden för de länkade tjänsterna från Azure Key Vault.

## <a name="next-steps"></a>Nästa steg

Granska vägledningen för att skapa en data fabrik i Azure Portal i [skapa en data fabrik med hjälp av Azure Data Factory gränssnittet](quickstart-create-data-factory-portal.md).
