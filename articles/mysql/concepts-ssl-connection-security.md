---
title: SSL-anslutning för Azure Database for MySQL
description: Information för att konfigurera Azure Database för MySQL och associerade program att använda normalt SSL-anslutningar
author: JasonMAnderson
ms.author: janders
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 129f90d495627edb25dfafdeb1b274aa2c4c71cb
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "60525820"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-anslutning i Azure Database for MySQL
Azure Database för MySQL stöder anslutning av din databasserver för klientprogram som använder Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.

## <a name="default-settings"></a>Standardinställningar
Som standard ska databastjänsten konfigureras för att kräva SSL-anslutningar när du ansluter till MySQL.  Vi rekommenderar att undvika att inaktivera SSL-alternativet när det är möjligt. 

När du etablerar en ny Azure Database for MySQL-server via Azure portal och CLI är tillämpning av SSL-anslutningar aktiverat som standard. 

Anslutningssträngar för olika programmeringsspråk visas i Azure-portalen. Dessa anslutningssträngar omfattar de obligatoriska SSL-parametrarna för att ansluta till databasen. Välj din server i Azure-portalen. Under den **inställningar** väljer du den **anslutningssträngar**. SSL-parametern varierar beroende på anslutningen, till exempel ”ssl = true” eller ”sslmode = kräver” eller ”sslmode = krävs” eller andra ändringar.

Om du vill lära dig mer om att aktivera eller inaktivera SSL-anslutning när du utvecklar program, som avser [hur du konfigurerar SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Nästa steg
[Anslutningsbibliotek för Azure Database for MySQL](concepts-connection-libraries.md)
