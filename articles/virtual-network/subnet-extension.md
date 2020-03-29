---
title: Nättillägg i Azure | Microsoft-dokument
description: Läs mer om nättillägg i Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587516"
---
# <a name="subnet-extension"></a>Undernätstillägg
Arbetsbelastningsmigrering till det offentliga molnet kräver noggrann planering och samordning. En av de viktigaste övervägandena kan vara möjligheten att behålla dina IP-adresser. Vilket kan vara viktigt, särskilt om dina program har IP-adressberoende eller om du har efterlevnadskrav för att använda specifika IP-adresser. Azure Virtual Network löser det här problemet åt dig genom att du kan skapa virtuella nätverk och undernät med hjälp av ett IP-adressintervall som du väljer.

Migreringar kan bli lite utmanande när ovanstående krav kombineras med ett extra krav för att hålla vissa program lokalt. I till exempel en situation måste du dela upp programmen mellan Azure och lokala, utan att numrera om IP-adresserna på båda sidor. Dessutom måste du tillåta att programmen kommuniceras som om de finns i samma nätverk.

En lösning på ovanstående problem är nättillägg. Om du utökar ett nätverk kan program prata över samma broadcast-domän när de finns på olika fysiska platser, vilket tar bort behovet av att bakåtkuppa till nätverkstopologin. 

Att utöka nätverket är inte en bra idé i allmänhet, men under användningsfall kan det bli nödvändigt.

- **Stegvis migrering:** Det vanligaste scenariot är att du vill fasa in migreringen. Du vill ta med några program först och med tiden migrera resten av programmen till Azure.
- **Svarstid:** Krav på låg latens kan vara en annan anledning för dig att hålla vissa program lokalt för att säkerställa att de är så nära ditt datacenter som möjligt.
- **Efterlevnad**: Ett annat användningsfall är att du kan ha efterlevnadskrav för att hålla vissa av dina program lokala.
 
> [!NOTE] 
> Du bör inte förlänga dina undernät om det inte är nödvändigt. I de fall där du utökar dina undernät bör du försöka göra det till ett mellansteg. Med tiden bör du prova att numrera om program i ditt lokala nätverk och migrera dem till Azure.

I nästa avsnitt diskuterar vi hur du kan utöka dina undernät till Azure.


## <a name="extend-your-subnet-to-azure"></a>Utöka ditt undernät till Azure
 Du kan utöka dina lokala undernät till Azure med hjälp av en layer-3 overlay nätverksbaserad lösning. De flesta lösningar använder en överlagringsteknik som VXLAN för att utöka layer-2-nätverket med hjälp av ett layer-3 overlay-nätverk. Diagrammet nedan visar en generaliserad lösning. I den här lösningen finns samma undernät på båda sidor som är Azure och lokalt. 

![Exempel på tillägg i undernät](./media/subnet-extension/subnet-extension.png)

IP-adresserna från undernätet tilldelas virtuella datorer på Azure och lokalt. Både Azure och lokala har en NVA infogad i sina nätverk. När en virtuell dator i Azure försöker prata med en virtuell dator i lokalt nätverk samlar Azure NVA paketet, kapslar in det och skickar det via VPN/Express Route till det lokala nätverket. Den lokala NVA tar emot paketet, avkapslar det och vidarebefordrar det till den avsedda mottagaren i sitt nätverk. Returtrafiken använder en liknande sökväg och logik.

I exemplet ovan kommunicerar och lär sig Azure NVA och den lokala NVA och lär dig mer om IP-adresser bakom varandra. Mer komplexa nätverk kan också ha en mappningstjänst som upprätthåller mappningen mellan NVA:erna och IP-adresserna bakom dem. När en NVA tar emot ett paket frågar den mappningstjänsten för att ta reda på adressen till NVA som har mål-IP-adressen bakom sig.

I nästa avsnitt hittar du information om lösningar för tillägg i undernät som vi har testat på Azure.

## <a name="next-steps"></a>Nästa steg 
[Utöka ditt undernät till Azure med hjälp av leverantörslösningar.](https://github.com/microsoft/Azure-LISP)