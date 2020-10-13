---
title: Använda Azure Digital Twins CLI
titleSuffix: Azure Digital Twins
description: Se hur du kommer igång med och använder Azure Digitals flätade CLI.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 65462937db4711066bdb8b31cc22de508321d701
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950915"
---
# <a name="use-the-azure-digital-twins-cli"></a>Använda Azure Digital Twins CLI

Förutom att hantera Azure Digitals-instansen i Azure Portal, har Azure Digitals-dubbla, ett **kommando rads gränssnitt (CLI)** som du kan använda för att utföra de flesta större åtgärder med tjänsten, inklusive:
* Hantera en digital Azure-instans
* Hantera modeller
* Hantera digitala dubbla
* Hantera dubbla relationer
* Konfigurera slut punkter
* Hantera [vägar](concepts-route-events.md)
* Konfigurera [säkerhet](concepts-security.md) via rollbaserad åtkomst kontroll (RBAC)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>Använder (distribuera och validera)

Förutom att hantera instansen generellt är CLI också ett användbart verktyg för distribution och validering.
* Kontroll Plans kommandona kan användas för att göra distributionen av en ny instans repeterbar eller automatiserad.
* Du kan använda data Plans kommandon för att snabbt kontrol lera värden i din instans och kontrol lera att åtgärderna har slutförts som förväntat.

## <a name="get-the-extension"></a>Hämta tillägget

Azures digitala dubbla kommandon är en del av [Azure IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Du kan visa en fullständig lista över kommandon och deras användning som en del av referens dokumentationen för `az iot` kommando uppsättningen: [ *AZ DT* kommando referens](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true).

Du kan kontrol lera att du har den senaste versionen av tillägget med de här stegen. Du kan köra dessa kommandon i [Azure Cloud Shell](../cloud-shell/overview.md) eller i ett [lokalt Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Nästa steg

Utforska CLI och dess fullständiga kommando uppsättningar via referens dokumenten:
* [*AZ DT* -kommando referens](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
