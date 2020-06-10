---
title: Använd Azure Digitals flätat CLI
titleSuffix: Azure Digital Twins
description: Se hur du kommer igång med och använder Azure Digitals flätade CLI.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 53b20ded8e4b4a003beff1ef8489ecd9ff3451ac
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612805"
---
# <a name="use-the-azure-digital-twins-cli"></a>Använd Azure Digitals flätat CLI

Förutom att hantera Azure Digitals-instansen i Azure Portal, har Azure Digitals-dubbla, ett **kommando rads gränssnitt (CLI)** som du kan använda för att utföra de flesta större åtgärder med tjänsten, inklusive:
* Hantera en digital Azure-instans
* Hantera modeller
* Hantera digitala dubbla
* Hantera dubbla relationer
* Konfigurera slut punkter
* Hantera [vägar](concepts-route-events.md)
* Konfigurera [säkerhet](concepts-security.md) via rollbaserad åtkomst kontroll (RBAC)

Azures digitala dubbla kommandon är en del av [Azure IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Du kan visa referens dokumentationen för de här kommandona som en del av `az iot` kommando uppsättningen: [AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

## <a name="deploy-and-validate"></a>Distribuera och validera

Förutom att hantera instansen generellt är CLI också ett användbart verktyg för distribution och validering.
* Kontroll Plans kommandona kan användas för att göra distributionen av en ny instans repeterbar eller automatiserad.
* Du kan använda data Plans kommandon för att snabbt kontrol lera värden i din instans och kontrol lera att åtgärderna har slutförts som förväntat.

## <a name="next-steps"></a>Nästa steg

Ett alternativ till CLI-kommandon finns i hantera en digital Azure-instans med API: er och SDK: er:
* [Anvisningar: använda Azures digitala dubbla API: er och SDK: er](how-to-use-apis-sdks.md)
