---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/26/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8b236bc1f0089b89aca90e7c69e9b445b01a374b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440460"
---
| Resurs | Standardgräns |
| --- | --- |
| Bearbetade data |1000 TB/brandvägg/månad <sup>1</sup> |
|Regler|10k - regel alla typer kombineras|
|Global peering|Stöds ej. Du bör ha minst en distribution av brandvägg per region.|
|Högsta antal portar i en enda regel|15<br>Ett portintervall (exempel: 2 – 10) räknas som två.
|Minimistorlek för AzureFirewallSubnet |/26


<sup>1</sup> kontakta Azure-supporten om du måste öka gränserna.
