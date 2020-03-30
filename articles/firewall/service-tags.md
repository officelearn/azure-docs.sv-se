---
title: Översikt över tjänsttaggar för Azure-brandväggen
description: En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168690"
---
# <a name="azure-firewall-service-tags"></a>Tjänsttaggar för Azure-brandväggen

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa egna tjänsttaggar, och du kan inte heller ange vilka IP-adresser som ska finnas i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

Azure Firewall service-taggar kan användas i målfältet för nätverksregler. Du kan använda dem i stället för specifika IP-adresser.

## <a name="supported-service-tags"></a>Tjänsttaggar som stöds

Se [Säkerhetsgrupper](../virtual-network/security-overview.md#service-tags) för en lista över tjänsttaggar som är tillgängliga för användning i Azure-brandväggsnätverksregler.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure-brandväggsregler finns i [Azure Firewall rule processing logic](rule-processing.md).