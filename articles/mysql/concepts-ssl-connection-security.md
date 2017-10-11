---
title: "SSL-anslutning för Azure-databas för MySQL | Microsoft Docs"
description: "Information för att konfigurera Azure-databas för MySQL och associerade program ska använda SSL-anslutningar"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 05/10/2017
ms.openlocfilehash: 4b03b3a2dbfad92cc0cfa84777b38ddff90452cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-anslutning i Azure för MySQL-databas
Azure-databas för MySQL stöder anslutning databasservern till klientprogram som använder Secure Sockets Layer (SSL). Framtvinga SSL-anslutningar mellan databasservern och ditt klientprogram hjälper dig att skydda mot attacker som ”man i mitten” genom att kryptera dataströmmen mellan servern och ditt program.

## <a name="default-settings"></a>Standardinställningar
Database-tjänsten ska konfigureras för att kräva SSL-anslutningar när du ansluter till MySQL som standard.  Det rekommenderas att undvika att inaktivera SSL-alternativet när det är möjligt. 

Etablera en ny Azure-databas för MySQL-servern via Azure portal och CLI är verkställandet av SSL-anslutningar aktiverad som standard. 

På samma sätt innehåller anslutningssträngar som har fördefinierats i inställningarna för ”anslutningssträngar” under din server i Azure portal de obligatoriska parametrarna för vanliga språk att ansluta till databasservern med SSL. Parametern SSL varierar beroende på anslutningen, till exempel ”ssl = true” eller ”sslmode = kräver” eller ”sslmode = krävs” eller andra ändringar.

Om du vill lära dig mer om att aktivera eller inaktivera SSL-anslutning när du utvecklar program, se [hur du konfigurerar SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Nästa steg
[Anslutningsbibliotek för Azure-databas för MySQL](concepts-connection-libraries.md)
