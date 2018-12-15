---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53430052"
---
| Resurs | Standardgräns |
| --- | --- |
| Bearbetade data |1000 TB/brandvägg/månad <sup>1</sup> |
|Regler|10k - regel alla typer kombineras|
|Global peering|Stöds ej. Du bör ha minst en distribution av brandvägg per region.|
|Högsta antal portar i en enda regel|15<br>En serie portar (till exempel: 2 – 10) räknas som två.
|Minimistorlek för AzureFirewallSubnet |/26|
|Portintervall i nätverk och regler|0-64 000. Arbete pågår till slappna av den här begränsningen.|
|


<sup>1</sup> kontakta Azure-supporten om du måste öka gränserna.
