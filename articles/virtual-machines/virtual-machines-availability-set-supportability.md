---
title: Support för att lägga till virtuella Azure-datorer i en befintlig tillgänglighetsuppsättning ange | Microsoft Docs
description: Support för att lägga till virtuella Azure-datorer i en befintlig tillgänglighetsuppsättning.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 13448f4b335d84264d4141cb4fb8c3eadcf0303e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092148"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Support för att lägga till virtuella Azure-datorer i en befintlig tillgänglighetsuppsättning

Du kan ibland uppstå begränsningar när du lägger till nya virtuella datorer (VM) till en befintlig tillgänglighetsuppsättning. Följande diagram innehåller vilka VM-rad som du kan blanda i samma tillgänglighetsuppsättning.

Här är matrisen support att blanda olika typer av virtuella datorer:

Serier och Tillgänglighetsuppsättning|Andra virtuella datorn|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Första virtuella dator|||||||
|A||Ok|Ok|Ok|Ok|Ok|
|Av2||Ok|Ok|Ok|Ok|Ok|
|D||Ok|Ok|Ok|Ok|Ok|
|Dv2||Ok|Ok|Ok|Ok|Ok|
|Dv3||Ok|Ok|Ok|Ok|Ok|

Alla andra serier hittades inte i samma tillgänglighetsuppsättning eftersom de kräver en specifik maskinvara.

A8/A9 VM-storlek kan inte blandas på grund av requirment på dedikerade RDMA backend-nätverket.
