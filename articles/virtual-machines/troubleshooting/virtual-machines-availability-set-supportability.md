---
title: Support för att lägga till virtuella Azure-datorer i en befintlig tillgänglighetsuppsättning | Microsoft-dokument
description: Den här artikeln innehåller en supportmatris om vilken VM-serie du kan blanda i samma tillgänglighetsuppsättning
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122927"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Support för att lägga till virtuella Azure-datorer i en befintlig tillgänglighetsuppsättning

Ibland kan du stöta på begränsningar när du lägger till nya virtuella datorer i en befintlig tillgänglighetsuppsättning. Följande diagraminformation vilka VM-serier du kan blanda i samma tillgänglighetsuppsättning.

Här är supportmatrisen för att blanda olika typer av virtuella datorer:

Serie & tillgänglighetsuppsättning|Andra virtuella datorer|A|Av2 (av2)|D|Dv2 (olika)|Dv3 (olika)|
|---|---|---|---|---|---|---|
|Första virtuella datorn|||||||
|A||OK|OK|OK|OK|OK|
|Av2 (av2)||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2 (olika)||OK|OK|OK|OK|OK|
|Dv3 (olika)||OK|OK|OK|OK|OK|

Alla andra serier kunde inte vara i samma tillgänglighetsuppsättning eftersom de kräver en viss maskinvara.

A8/A9 VM-storlek kan inte blandas på grund av kravet på dedikerade RDMA-backend-nätverk.
