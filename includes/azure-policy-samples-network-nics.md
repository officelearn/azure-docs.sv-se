---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
origin.date: 05/17/2018
ms.date: 11/12/2018
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: f189843e865863d9b4088363e691ebc42ad9f2a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60866031"
---
### <a name="network-interfaces"></a>Nätverksgränssnitt

|  |  |
|---------|---------|
| [NSG X på varje NIC](../articles/governance/policy/samples/nsg-on-nic.md) | Kräver att en viss nätverkssäkerhetsgrupp används med varje gränssnitt för virtuellt nätverk. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [Använda godkända undernät för VM-nätverksgränssnitt](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt undernät. Du anger ID för det godkända undernätet. |
| [Använda godkända vNet för VM-nätverksgränssnitt](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt virtuellt nätverk. Du anger ID för det godkända virtuella nätverket. |