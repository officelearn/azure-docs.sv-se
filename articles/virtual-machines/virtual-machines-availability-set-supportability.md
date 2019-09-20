---
title: Stöd för att lägga till virtuella Azure-datorer i en befintlig tillgänglighets uppsättning | Microsoft Docs
description: Stöd för att lägga till virtuella Azure-datorer i en befintlig tillgänglighets uppsättning.
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
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155446"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Stöd för att lägga till virtuella Azure-datorer i en befintlig tillgänglighets uppsättning

Ibland kan det uppstå begränsningar när du lägger till nya virtuella datorer i en befintlig tillgänglighets uppsättning. Följande diagram innehåller information om vilken VM-serie du kan blanda i samma tillgänglighets uppsättning.

Här är en support mat ris för att blanda olika typer av virtuella datorer:

Serie & tillgänglighets uppsättning|Andra virtuella datorn|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Första virtuella datorn|||||||
|A||Ok|Ok|Ok|Ok|Ok|
|Av2||Ok|Ok|Ok|Ok|Ok|
|D||Ok|Ok|Ok|Ok|Ok|
|Dv2||Ok|Ok|Ok|Ok|Ok|
|Dv3||Ok|Ok|Ok|Ok|Ok|

Alla andra serier kunde inte ha samma tillgänglighets uppsättning eftersom de kräver en speciell maskin vara.

A8/A9 VM-storlek kan inte blandas på grund av krav på dedikerad RDMA-backend-nätverk.
