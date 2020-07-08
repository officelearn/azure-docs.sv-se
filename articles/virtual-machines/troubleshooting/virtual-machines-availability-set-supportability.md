---
title: Stöd för att lägga till virtuella Azure-datorer i en befintlig tillgänglighets uppsättning | Microsoft Docs
description: Den här artikeln innehåller en support mat ris om vilken VM-serie du kan blanda i samma tillgänglighets uppsättning
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77122927"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Stöd för att lägga till virtuella Azure-datorer i en befintlig tillgänglighets uppsättning

Ibland kan det uppstå begränsningar när du lägger till nya virtuella datorer i en befintlig tillgänglighets uppsättning. Följande diagram innehåller information om vilken VM-serie du kan blanda i samma tillgänglighets uppsättning.

Här är en support mat ris för att blanda olika typer av virtuella datorer:

Serie & tillgänglighets uppsättning|Andra virtuella datorn|A|AV2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Första virtuella datorn|||||||
|A||OK|OK|OK|OK|OK|
|AV2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Alla andra serier kunde inte ha samma tillgänglighets uppsättning eftersom de kräver en speciell maskin vara.

A8/A9 VM-storlek kan inte blandas på grund av krav på dedikerad RDMA-backend-nätverk.
