---
title: Översikt över Azure Firewall service-Taggar
description: En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 47377817b62d33e8af79e4a0d2dceb68ba9dbdc5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658655"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall service-Taggar

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa egna tjänsttaggar, och du kan inte heller ange vilka IP-adresser som ska finnas i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

Azure Firewall service-taggar kan användas i nätverks reglernas mål fält. Du kan använda dem i stället för vissa IP-adresser.

## <a name="supported-service-tags"></a>Service märken som stöds

Se [säkerhets grupper](../virtual-network/network-security-groups-overview.md#service-tags) för en lista över service märken som är tillgängliga för användning i Azure Firewall Network rules.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Firewall-regler finns i [regel bearbetnings logik för Azure-brandvägg](rule-processing.md).