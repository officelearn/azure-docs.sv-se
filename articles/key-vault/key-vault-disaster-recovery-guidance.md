---
title: Vad du gör i händelse av en Azure-tjänsten avbrott som påverkar Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Lär dig vad du gör i händelse av ett avbrott i Azure-tjänsten som påverkar Azure Key Vault.
services: key-vault
author: barclayn
manager: barbkess
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: dd1d2af174f93cceabccd62a78d45a0e4fbed502
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60305449"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault tillgänglighet och redundans

Azure Key Vault har flera lager för redundans för att se till att dina nycklar och hemligheter finnas tillgängliga för ditt program även om enskilda komponenter i tjänsten misslyckas.

Innehållet i ditt nyckelvalv replikeras inom regionen och till en sekundär region minst 150 mil bort men inom samma geografiska område. Detta upprätthåller hög hållbarhet för dina nycklar och hemligheter. Se den [parade Azure-regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) dokumentet för information om specifika regionpar.

Om enskilda komponenter i key vault-tjänsten misslyckas, steg alternativa komponenter för regionen att hantera din förfrågan om att se till att det finns inga försämring av funktioner. Du behöver inte vidta några åtgärder som utlöser. Det sker automatiskt och blir transparent till dig.

Om att en hel Azure-region inte är tillgänglig, som du gör för Azure Key Vault i den regionen begäranden dirigeras automatiskt (*redundansväxlats*) till en sekundär region. När den primära regionen är tillgänglig igen, begäranden dirigeras tillbaka (*växlas tillbaka*) till den primära regionen. Igen, behöver du inte vidta någon åtgärd eftersom detta sker automatiskt.

Det finns några varningar för att vara medveten om:

* Det kan ta några minuter för tjänsten att växla över i händelse av en region redundans. Begäranden som görs under den här tiden kan misslyckas tills redundansväxlingen är klar.
* När en redundansväxling har slutförts, är ditt nyckelvalv i skrivskyddat läge. Begäranden som stöds i det här läget är:
  * Lista nyckelvalv
  * Hämta egenskaper för nyckelvalv
  * En lista över hemligheterna
  * Hämta hemligheter
  * Lista över nycklar
  * Hämta nycklar (Egenskaper)
  * Kryptera
  * Avkryptera
  * Omsluta
  * Packa upp
  * Verifiera
  * Signera
  * Backup
* Efter en redundansväxling växlas tillbaka, alla typer av begäranden (inklusive Läs *och* skrivförfrågningar) är tillgängliga.

