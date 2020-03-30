---
title: Internet peering - vanliga frågor
titleSuffix: Azure
description: Internet peering - vanliga frågor
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775503"
---
# <a name="internet-peering---faqs"></a>Internet peering - vanliga frågor

Du kan granska informationen nedan för allmänna frågor.

**Vad är skillnaden mellan Internet peering och peering service?**

Peering Service är en tjänst som avser att tillhandahålla offentlig IP-anslutning i företagsklass till Microsoft för sina företagskunder. Internet i företagsklass omfattar anslutning via Internetleverantörer som har hög genomströmningsanslutning till Microsoft och redundans för en HA-anslutning. Dessutom är användartrafiken optimerad för svarstid till närmaste Microsoft Edge. Peering-tjänsten bygger på peering-anslutning med partneroperatören. Peering-anslutningen med partnern måste vara direkt peering i motsats till Exchange-peering. Direkt peering måste ha lokal och geo-redundans.

**Vad är äldre peering?**

Peering-anslutning som konfigureras med Azure PowerShell hanteras som en Azure-resurs. Peering-anslutningar som har konfigurerats tidigare lagras i vårt system som äldre peering som du kan välja att konvertera för att hantera som en Azure-resurs.

**När New-AzPeeringDirectConnectionObject anropas, vilka IP-adresser ges till Microsoft- och Peer-enheter?**

När du anropar New-AzPeeringDirectConnectionObject cmdlet anges en /31-adress (a.b.c.d/31) eller en /30-adress (a.b.c.d/30). Den första IP-adressen (a.b.c.d+0) ges till Peer enhet och andra IP-adress (a.b.c.d+1) ges till Microsoft-enheten.

**Vad är maxprefixerAdvertisedIPv4 och MaxPrefixesAdvertisedIPv6 parametrar i New-AzPeeringDirectConnectionObject cmdlet?**

MaxPrefixesAdvertisedIPv4 och MaxPrefixesAdvertisedIPv6 parametrar representerar det maximala antalet IPv4 och IPv6 prefix en peer vill Microsoft att acceptera. Dessa parametrar kan ändras när som helst.