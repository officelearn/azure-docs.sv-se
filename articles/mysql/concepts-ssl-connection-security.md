---
title: SSL-anslutning för Azure-databas för MySQL
description: Information för att konfigurera Azure-databas för MySQL och associerade program ska använda SSL-anslutningar
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ee7e0ec8524d66ee89cf7b2c4d44b70efa784f8f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265069"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-anslutning i Azure för MySQL-databas
Azure-databas för MySQL stöder anslutning databasservern till klientprogram som använder Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.

## <a name="default-settings"></a>Standardinställningar
Database-tjänsten ska konfigureras för att kräva SSL-anslutningar när du ansluter till MySQL som standard.  Vi rekommenderar för att undvika att inaktivera SSL-alternativet när det är möjligt. 

Etablera en ny Azure-databas för MySQL-servern via Azure portal och CLI är verkställandet av SSL-anslutningar aktiverad som standard. 

Anslutningssträngar för olika programmeringsspråk som visas i Azure-portalen. Dessa anslutningssträngar inkluderar de SSL-parametrarna som krävs för att ansluta till databasen. Markera din server i Azure-portalen. Under den **inställningar** rubrik, Välj den **anslutningssträngar**. Parametern SSL varierar beroende på anslutningen, till exempel ”ssl = true” eller ”sslmode = kräver” eller ”sslmode = krävs” eller andra ändringar.

Om du vill lära dig mer om att aktivera eller inaktivera SSL-anslutning när du utvecklar program, referera till [hur du konfigurerar SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Nästa steg
[Anslutningsbibliotek för Azure-databas för MySQL](concepts-connection-libraries.md)
