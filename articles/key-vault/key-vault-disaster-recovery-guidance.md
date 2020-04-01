---
title: Vad du ska göra i händelse av ett avbrott i Azure-tjänsten som påverkar Azure Key Vault - Azure Key Vault | Microsoft-dokument
description: Lär dig vad du kan göra i händelse av ett Avbrott i Azure-tjänsten som påverkar Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 530ae2b795b4d94802e9f0d3420f7b3af86936ad
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78184646"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Tillgänglighet och redundans för Azure Key Vault

Azure Key Vault har flera lager av redundans för att se till att dina nycklar och hemligheter förblir tillgängliga för ditt program även om enskilda komponenter i tjänsten misslyckas.

Innehållet i nyckelvalvet replikeras inom regionen och till en sekundär region minst 150 miles away men inom samma geografi. Detta upprätthåller hög hållbarhet av dina nycklar och hemligheter. Mer information om specifika regionpar finns i dokumentet med kopplade regioner i [Azure.](../best-practices-availability-paired-regions.md)

Om enskilda komponenter i nyckelvalvstjänsten misslyckas går alternativa komponenter inom regionen in för att visa din begäran för att se till att funktionaliteten inte försämras. Du behöver inte vidta några åtgärder för att utlösa detta. Det sker automatiskt och kommer att vara transparent för dig.

I den sällsynta händelsen att en hel Azure-region inte är tillgänglig dirigeras de begäranden som du gör av Azure Key Vault i den regionen automatiskt *(misslyckades över)* till en sekundär region. När den primära regionen är tillgänglig igen dirigeras begäranden tillbaka (*misslyckades tillbaka)* till den primära regionen. Återigen behöver du inte vidta några åtgärder eftersom detta sker automatiskt.

Genom den här designen med hög tillgänglighet kräver Azure Key Vault inga driftstopp för underhållsaktiviteter.

Det finns några förbehåll att vara medveten om:

* I händelse av en region redundans kan det ta några minuter för tjänsten att växla över. Begäranden som görs under den här tiden kan misslyckas tills redundansen har slutförts.
* När en redundans har slutförts är nyckelvalvet skrivskyddat. Begäranden som stöds i det här läget är:
  * Lista nyckelvalv
  * Hämta egenskaper för nyckelvalv
  * Lista hemligheter
  * Få hemligheter
  * Lista nycklar
  * Hämta (egenskaper för) nycklar
  * Kryptera
  * Avkryptera
  * Wrap
  * Packa upp
  * Verifiera
  * Tecken
  * Backup
* När en redundans har misslyckats tillbaka är alla begärandetyper (inklusive *läs- och* skrivbegäranden) tillgängliga.

