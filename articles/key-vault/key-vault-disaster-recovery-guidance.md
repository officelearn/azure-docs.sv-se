---
title: "Vad du gör vid en Azure-tjänst avbrott som påverkar Azure Key Vault | Microsoft Docs"
description: "Lär dig vad du ska göra om ett avbrott i Azure-tjänsten som påverkar Azure Key Vault."
services: key-vault
documentationcenter: 
author: adamglick
manager: mbaldwin
editor: 
ms.assetid: 19a9af63-3032-447b-9d1a-b0125f384edb
ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: sumedhb;aglick
ms.openlocfilehash: 6419d54c54e7d19103419262b79e7a5268b2268c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault tillgänglighet och redundans
Azure Key Vault har flera lager för redundans för att se till att dina nycklar och hemligheter förblir tillgängliga för ditt program även om enskilda komponenter i tjänsten misslyckas.

Innehållet i nyckelvalvet replikeras för regionen och en sekundär region minst 150 mil bort men inom samma geografiska. Detta upprätthåller en hög hållbarhet för dina nycklar och hemligheter. Finns det [Azure länkas regioner](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions) dokumentet för mer information om specifika region par.

Om enskilda komponenter i tjänsten nyckelvalv misslyckas, steg alternativa komponenter för regionen att hantera din förfrågan om att se till att det finns ingen försämring av funktioner. Du behöver inte göra något för att utlösa det här. Det sker automatiskt och blir transparent.

Om att en hela Azure-region är tillgängligt, begäranden som du gör av Azure Key Vault i regionen dirigeras automatiskt (*redundansväxlats*) till en sekundär region. När den primära regionen är tillgänglig igen begäranden dirigeras tillbaka (*misslyckades tillbaka*) till den primära regionen. Igen, behöver du inte vidta någon åtgärd eftersom detta sker automatiskt.

Det finns några varningar att vara medveten om:

* Det kan ta några minuter för tjänsten att växla över vid en region redundans. Begäranden som görs under den här tiden kan misslyckas tills redundansväxlingen är klar.
* Efter en växling vid fel är nyckelvalvet i skrivskyddat läge. Begäranden som stöds i det här läget är:
  * Lista nyckelvalv
  * Hämta egenskaper för nyckelvalv
  * Lista hemligheter
  * Hämta hemligheter
  * Lista nycklar
  * Hämta nycklar (egenskaperna för)
  * Kryptera
  * Dekryptera
  * Omsluta
  * Packa upp
  * Verifiera
  * Logga
  * Säkerhetskopiering
* Efter en redundansväxling har misslyckats tillbaka, alla typer av begäranden (inklusive Läs *och* skrivåtgärder) är tillgängliga.

