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
ms.date: 11/02/2017
ms.openlocfilehash: 37e2bbbe1ed4b6a9cca0e0b001e5e632b9b73be2
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-anslutning i Azure för MySQL-databas
Azure-databas för MySQL stöder anslutning databasservern till klientprogram som använder Secure Sockets Layer (SSL). Framtvinga SSL-anslutningar mellan databasservern och ditt klientprogram hjälper dig att skydda mot attacker som ”man i mitten” genom att kryptera dataströmmen mellan servern och ditt program.

## <a name="default-settings"></a>Standardinställningar
Database-tjänsten ska konfigureras för att kräva SSL-anslutningar när du ansluter till MySQL som standard.  Vi rekommenderar för att undvika att inaktivera SSL-alternativet när det är möjligt. 

Etablera en ny Azure-databas för MySQL-servern via Azure portal och CLI är verkställandet av SSL-anslutningar aktiverad som standard. 

Anslutningssträngar för olika programmeringsspråk som visas i Azure-portalen. Dessa anslutningssträngar inkluderar de SSL-parametrarna som krävs för att ansluta till databasen. Markera din server i Azure-portalen. Under den **inställningar** rubrik, Välj den **anslutningssträngar**. Parametern SSL varierar beroende på anslutningen, till exempel ”ssl = true” eller ”sslmode = kräver” eller ”sslmode = krävs” eller andra ändringar.

Om du vill lära dig mer om att aktivera eller inaktivera SSL-anslutning när du utvecklar program, referera till [hur du konfigurerar SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Nästa steg
[Anslutningsbibliotek för Azure-databas för MySQL](concepts-connection-libraries.md)
