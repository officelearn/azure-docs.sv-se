---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0a4a66543b323b1dbe56158851dafcb1dfe695f0
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47185349"
---
| Resurs | Standardgräns |
| --- | --- |
| Bearbetade data |1000 TB/brandvägg/månad <sup>1</sup> |
|Regler|regler för 10 k program, 10 k Nätverksregler|
|VNET-peering|Max av 50 virtuella ekernätverk för implementeringar av NAV och ekrar.|
|Global peering|Stöds ej. Du bör ha minst en distribution av brandvägg per region.|
|Högsta antal portar i en enda regel|15<br>Ett portintervall (exempel: 2 – 10) räknas som två.


<sup>1</sup> kontakta Azure-supporten om du måste öka gränserna.
