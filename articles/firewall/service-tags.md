---
title: Översikt över brandväggen för Azure-tjänsttaggar
description: Den här artikeln är en översikt av de Azure-brandvägg.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 6/27/2019
ms.author: victorh
ms.openlocfilehash: d0ac36e415c056dffc9c75d00968ff74c2156e63
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450173"
---
# <a name="azure-firewall-service-tags"></a>Azure-tjänsttaggar för brandvägg

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa egna tjänsttaggar, och du kan inte heller ange vilka IP-adresser som ska finnas i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

Azure-tjänsttaggar för brandväggen kan användas i fältet mål nätverk. Du kan använda dem i stället för specifika IP-adresser.

## <a name="supported-service-tags"></a>Tjänsttaggar som stöds

Se [säkerhetsgrupper](../virtual-network/security-overview.md#service-tags) en lista över tjänsttaggar som är tillgängliga för användning i Azure network brandväggsregler.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure-brandväggsregler i [Azure brandväggsregel bearbetningslogiken](rule-processing.md).