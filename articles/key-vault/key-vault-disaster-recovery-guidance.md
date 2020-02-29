---
title: Vad du gör om ett avbrott i Azure-tjänsten påverkar Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Lär dig hur du gör i händelse av ett avbrott i Azure-tjänsten som påverkar Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 530ae2b795b4d94802e9f0d3420f7b3af86936ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184646"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault tillgänglighet och redundans

Azure Key Vault har flera lager av redundans för att se till att dina nycklar och hemligheter är tillgängliga för ditt program även om enskilda komponenter i tjänsten inte fungerar.

Innehållet i ditt nyckel valv replikeras i regionen och till en sekundär region minst 150 km, men inom samma geografi. Detta behåller hög tålighet för dina nycklar och hemligheter. Mer information om vissa region par finns i dokumentet om [Azure-kopplade regioner](../best-practices-availability-paired-regions.md) .

Om enskilda komponenter i Key Vault-tjänsten kraschar kan alternativa komponenter i regions steget i hantera din begäran för att se till att det inte finns någon försämring av funktionaliteten. Du behöver inte vidta några åtgärder för att utlösa detta. Det sker automatiskt och är transparent för dig.

I sällsynta fall där en hel Azure-region inte är tillgänglig dirigeras de begär Anden som du skapar Azure Key Vault i den regionen automatiskt till en sekundärregion. När den primära regionen är tillgänglig igen dirigeras begär Anden tillbaka (*växlas tillbaka*) till den primära regionen. Du behöver inte vidta några åtgärder eftersom detta sker automatiskt.

Med den här designen för hög tillgänglighet behöver Azure Key Vault ingen stillestånds tid för underhålls aktiviteter.

Det finns några varningar som kan vara medvetna om:

* I händelse av en redundansväxling av en region kan det ta några minuter för tjänsten att redundansväxla. Begär Anden som görs under den här tiden kan Miss lyckas tills redundansväxlingen har slutförts.
* När redundansväxlingen är klar är ditt nyckel valv i skrivskyddat läge. Begär Anden som stöds i det här läget är:
  * Visa en lista över nyckel valv
  * Hämta egenskaper för nyckel valv
  * Lista hemligheter
  * Hämta hemligheter
  * Lista nycklar
  * Hämta (egenskaper för) nycklar
  * Kryptera
  * Avkryptera
  * Flytta
  * Packa upp
  * Verifiera
  * Logga in
  * Backup
* När en redundansväxling har misslyckats igen är alla typer av förfrågningar (inklusive Läs- *och* Skriv förfrågningar) tillgängliga.

