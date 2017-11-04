---
title: "Support för att lägga till virtuella Azure-datorer i en befintlig tillgänglighetsuppsättning ange | Microsoft Docs"
description: "När du kan lägga till virtuella Azure-datorer i en befintlig tillgänglighetsuppsättning."
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 7/15/2017
ms.author: delhan
ms.openlocfilehash: eb1a6f93670317203c4de53b7b1307a2034c26ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>När du kan lägga till virtuella Azure-datorer i en befintlig tillgänglighetsuppsättning

Du kan ibland uppstå begränsningar när du lägger till nya virtuella datorer (VM) till en befintlig tillgänglighetsuppsättning. Följande diagram visar detaljerad information som VM-serien som du kan blanda i samma tillgänglighetsuppsättning.

Här är matrisen support att blanda olika typer av virtuella datorer:

Serie & Tillgänglighetsuppsättning|Andra VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Första virtuella dator|||||||
|A||OKEJ|OKEJ|OKEJ|OKEJ|OKEJ|
|Av2||OKEJ|OKEJ|OKEJ|OKEJ|OKEJ|
|D||OKEJ|OKEJ|OKEJ|OKEJ|OKEJ|
|Dv2||OKEJ|OKEJ|OKEJ|OKEJ|OKEJ|
|Dv3||OKEJ|OKEJ|OKEJ|OKEJ|OKEJ|

Alla serier hittades inte i samma tillgänglighetsuppsättning eftersom de kräver att en specifik maskinvara.

A8/A9 VM-storlek kan inte blandas på grund av requirment på dedikerade RDMA backend-nätverket.
