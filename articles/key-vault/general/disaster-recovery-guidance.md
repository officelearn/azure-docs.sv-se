---
title: Vad du gör om ett avbrott i Azure-tjänsten påverkar Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Lär dig vad som händer i ett Azure-tjänst avbrott påverkar Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 27d8d4de308fe7cf6e6f36dd33f33bb73c495073
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983228"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Tillgänglighet och redundans för Azure Key Vault

Azure Key Vault har flera lager av redundans för att se till att dina nycklar och hemligheter är tillgängliga för ditt program även om enskilda komponenter i tjänsten inte fungerar.

> [!NOTE]
> Den här guiden gäller för valv. Hanterade HSM-pooler använder en annan hög tillgänglighet och katastrof återställnings modell. Mer information finns i [Översikt över hanterad HSM-återställning](../managed-hsm/disaster-recovery-guide.md) .

Innehållet i ditt nyckel valv replikeras i regionen och till en sekundär region minst 150 km, men inom samma geografiska område för att upprätthålla hög hållbarhet för dina nycklar och hemligheter. Mer information om vissa region par finns i dokumentet om [Azure-kopplade regioner](../../best-practices-availability-paired-regions.md) .


Om enskilda komponenter i Key Vault-tjänsten kraschar kan alternativa komponenter i regions steget i hantera din begäran för att se till att det inte finns någon försämring av funktionaliteten. Du behöver inte vidta några åtgärder för att starta den här processen. den sker automatiskt och blir transparent för dig.

I sällsynta fall där en hel Azure-region inte är tillgänglig dirigeras de begär Anden som du skapar Azure Key Vault i den regionen automatiskt till en sekundär*failed over*region. När den primära regionen är tillgänglig igen dirigeras begär Anden tillbaka (*växlas tillbaka*) till den primära regionen. Du behöver inte vidta några åtgärder eftersom detta sker automatiskt.

Med den här designen för hög tillgänglighet behöver Azure Key Vault ingen stillestånds tid för underhålls aktiviteter.

Det finns några varningar som kan vara medvetna om:

* I händelse av en redundansväxling av en region kan det ta några minuter för tjänsten att redundansväxla. Begär Anden som görs under den här tiden innan redundansväxlingen kan Miss lyckas.
* Under redundansväxlingen är ditt nyckel valv i skrivskyddat läge. Begär Anden som stöds i det här läget är:
  * Visa lista över certifikat
  * Hämta certifikat
  * Visa en lista över hemligheter
  * Hämta hemligheter
  * Lista nycklar
  * Hämta (egenskaper för) nycklar
  * Kryptera
  * Avkryptera
  * Flytta
  * Packa upp
  * Verifiera
  * Tecken
  * Backup

* Under redundansväxlingen kan du inte göra ändringar i Key Vault-egenskaperna. Du kan inte ändra åtkomst princip eller brand Väggs konfiguration och inställningar.

* När en redundansväxling har misslyckats igen är alla typer av förfrågningar (inklusive Läs- *och* Skriv förfrågningar) tillgängliga.
