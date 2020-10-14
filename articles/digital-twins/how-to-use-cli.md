---
title: Använda Azure Digital Twins CLI
titleSuffix: Azure Digital Twins
description: Se hur du kommer igång med och använder Azure Digitals flätade CLI.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 118e137f06a49f2c125b1ca156877514d65af86f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047071"
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

Azures digitala dubbla kommandon är en del av [Azure IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Du kan visa en fullständig lista över kommandon och deras användning som en del av referens dokumentationen för `az iot` kommando uppsättningen: [ *AZ DT* kommando referens](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest).

Du kan kontrol lera att du har den senaste versionen av tillägget med de här stegen. Du kan köra dessa kommandon i [Azure Cloud Shell](../cloud-shell/overview.md) eller i ett [lokalt Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Nästa steg

Utforska CLI och dess fullständiga kommando uppsättningar via referens dokumenten:
* [*AZ DT* -kommando referens](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)