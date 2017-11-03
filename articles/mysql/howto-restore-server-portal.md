---
title: "Återställa en Server i Azure-databas för MySQL | Microsoft Docs"
description: "Den här artikeln beskriver hur du återställer en server i Azure-databas för MySQL med Azure-portalen."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 6c1c0f8a0c0e59661b70b787b551b8cfdb024cda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Säkerhetskopiera och återställa en server i Azure-databas för MySQL med hjälp av Azure portal

## <a name="backup-happens-automatically"></a>Säkerhetskopieringen sker automatiskt
När du använder Azure-databas för MySQL görs databastjänsten en säkerhetskopia av tjänsten var femte minut. 

Säkerhetskopiorna är tillgängliga för 7 dagar vid användning av grundläggande nivån och 35 dagar när du använder standardnivån. Mer information finns i [Azure-databas för MySQL-servicenivåer](concepts-service-tiers.md)

Med hjälp av funktionen för automatisk säkerhetskopiering kan du återställa servern och alla databaser i en ny server till en tidigare punkt i tidpunkt.

## <a name="restore-in-the-azure-portal"></a>Återställa i Azure-portalen
Azure MySQL-databas kan du återställa servern till en viss tid och till att en ny kopia av servern. Du kan använda den här nya servern när du återställer data. 

En tabell släpptes av misstag kl. tolv idag, kan du återställa till en tidpunkt innan på dagen och hämta saknas tabellen och data från den nya kopian av servern.

Följande steg återställa exempelserver till en specifik tidpunkt:

1. Logga in på den [Azure-portalen](https://portal.azure.com/)

2. Hitta din Azure-databas för MySQL-servern. I den vänstra rutan, Välj **alla resurser**, och välj sedan din server i listan.

3.  Överst översikt serverblad klickar du på **återställa** i verktygsfältet. Återställ blad öppnas.
![Klicka på återställningsknappen](./media/howto-restore-server-portal/click-restore-button.png)

4. Fyll i formuläret återställning med informationen som krävs:

- **Återställningspunkt (UTC)**: med hjälp av Väljaren för datum och tid Väljaren, markera en punkt i tid att återställa till. Den angivna tiden är i UTC-format, så du behöver förmodligen konvertera lokal tid till UTC.
- **Återställ till en ny server**: Ange ett nytt servernamn att återställa den befintliga servern till.
- **Plats**: region valet fylls automatiskt med källan server region och kan inte ändras.
- **Prisnivån**: prisnivå nivå valet fylls automatiskt med samma prisnivån som källservern och kan inte ändras här. 
![PITR-återställning](./media/howto-restore-server-portal/pitr-restore.png)

5. Klicka på **OK** återställa servern till angiven tid. 

6. Leta upp den nya servern som har skapats och sedan kontrollera att databaserna har återställts korrekt när återställningen är klar.

## <a name="next-steps"></a>Nästa steg
- [Anslutningsbibliotek för Azure-databas för MySQL](concepts-connection-libraries.md).