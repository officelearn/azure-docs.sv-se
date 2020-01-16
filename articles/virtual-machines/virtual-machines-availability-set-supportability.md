---
title: Stöd för att lägga till virtuella Azure-datorer i en befintlig tillgänglighets uppsättning | Microsoft Docs
description: Den här artikeln innehåller en support mat ris om vilken VM-serie du kan blanda i samma tillgänglighets uppsättning
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: cb0034f2b353284e94d6f1508541b31040a5b076
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028418"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Stöd för att lägga till virtuella Azure-datorer i en befintlig tillgänglighets uppsättning

Ibland kan det uppstå begränsningar när du lägger till nya virtuella datorer i en befintlig tillgänglighets uppsättning. Följande diagram innehåller information om vilken VM-serie du kan blanda i samma tillgänglighets uppsättning.

Här är en support mat ris för att blanda olika typer av virtuella datorer:

Serie & tillgänglighets uppsättning|Andra virtuella datorn|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Första virtuella datorn|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Alla andra serier kunde inte ha samma tillgänglighets uppsättning eftersom de kräver en speciell maskin vara.

A8/A9 VM-storlek kan inte blandas på grund av krav på dedikerad RDMA-backend-nätverk.
