---
title: Återställa en Server i Azure-databas för PostgreSQL
description: Den här artikeln beskriver hur du återställer en server i Azure-databas för PostgreSQL med Azure-portalen.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: 0d67bf5625ee9037c5ec152c8ce8564235018e8e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Säkerhetskopiera och återställa en server i Azure-databas för PostgreSQL med Azure-portalen

## <a name="backup-happens-automatically"></a>Säkerhetskopieringen sker automatiskt
Azure-databas för PostgreSQL servrar säkerhetskopieras regelbundet för att aktivera funktioner för återställning. Med den här funktionen kan du återställa servern och alla dess databaser till en tidigare i tidpunkt, på en ny server.

## <a name="set-backup-configuration"></a>Ange konfigurationen för säkerhetskopiering

Du gör att välja mellan att konfigurera servern för antingen lokalt redundant säkerhetskopiering eller geografiskt redundant säkerhetskopiering när servern skapas i den **prisnivån** fönster.

> [!NOTE]
> När en server har skapats kan typ av redundans som finns, kan inte växlas geografiskt redundant jämfört med lokalt redundant.
>

När du skapar en server via Azure-portalen på **prisnivån** fönstret är där du väljer antingen **lokalt Redundant** eller **geografiskt Redundant** säkerhetskopieringar för din server. Det här fönstret är också där du väljer den **säkerhetskopiering kvarhållningsperiod** – hur lång tid (i antal dagar) du lagrade för serversäkerhetskopior.

   ![Prisnivån - Välj säkerhetskopiering redundans](./media/howto-restore-server-portal/pricing-tier.png)

Mer information om hur du anger dessa värden under skapa finns i [Azure-databas för PostgreSQL server quickstart](quickstart-create-server-database-portal.md).

Säkerhetskopiering kvarhållningsperiod på en server kan ändras genom följande steg:
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj din Azure-databas för PostgreSQL-servern. Den här åtgärden öppnar den **översikt** sidan.
3. Välj **prisnivån** på menyn under **inställningar**. Med skjutreglaget kan du ändra den **säkerhetskopiering kvarhållningsperiod** enligt önskemål mellan 7 och 35 dagar.
I skärmbilden här nedan har den ökat till 34 dagar.
![Ökat period för lagring av säkerhetskopior.](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Klicka på **OK** att bekräfta ändringen.

Säkerhetskopiering loggperioden styr hur långt tillbaka i tiden en point-in-time-återställning kan hämtas, eftersom den är baserad på säkerhetskopior som är tillgängliga. Point-in-time-återställning är ytterligare beskrivs i följande avsnitt. 

## <a name="point-in-time-restore"></a>Återställning från tidpunkt
Azure PostgreSQL-databas kan du återställa servern till en i tidpunkt och till att en ny kopia av servern. Du kan använda den här nya servern för att återställa dina data eller har ditt klientprogram som pekar på den här nya servern.

Till exempel om en tabell av misstag kan släppas kl. tolv idag, du återställa tiden innan på dagen och hämta saknas tabellen och data från den nya kopian av servern. Point-in-time-återställning är på servern nivån, inte på databasnivå.

Följande steg återställa exempel servern till en i tidpunkt:
1. Välj din Azure-databas för PostgreSQL-server i Azure-portalen. 

2. I verktygsfältet på serverns **översikt** väljer **återställa**.

   ![Azure-databas för knappen PostgreSQL - översikt – Återställ](./media/howto-restore-server-portal/2-server.png)

3. Fyll i formuläret återställning med informationen som krävs:

   ![Azure-databas för PostgreSQL - information för återställningspunkter ](./media/howto-restore-server-portal/3-restore.png)
  - **Återställningspunkt**: Markera den i tidpunkt du vill återställa.
  - **Målservern**: Ange ett namn för den nya servern.
  - **Plats**: du kan inte välja regionen. Som standard är det samma som källservern.
  - **Prisnivån**: du kan inte ändra dessa parametrar när du gör en point-in-time-återställning. Det är samma som källservern. 

4. Klicka på **OK** att återställa servern att återställa till en i tidpunkt. 

5. Leta upp den nya servern som har skapats för att verifiera data har återställts korrekt när återställningen är klar.

>[!Note]
>Den nya servern som skapats av point-in-time-återställning har samma server admin inloggningsnamnet och lösenordet som användes för den befintliga servern på point-in-time valde. Du kan ändra lösenordet i den nya servern **översikt** sidan.

## <a name="geo-restore"></a>GEO-återställning
Om du har konfigurerat din server för geografiskt redundant säkerhetskopiering till kan en ny server skapas från en säkerhetskopia av den befintliga servern. Den här nya servern kan skapas i en region som Azure-databasen för PostgreSQL är tillgänglig.  

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen. Välj **Databaser** > **Azure-databas för PostgreSQL**.

   ![Alternativet ”Azure-databas för PostgreSQL”](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. I formulärets **Välj källa** listrutan, Välj **säkerhetskopiering**. Den här åtgärden läser in en lista över servrar som har geo-redundant säkerhetskopieringar aktiverad. Välj en av dessa säkerhetskopior som källa för den nya servern.
   ![Välj källa: Säkerhetskopiering och listan över geo-redundant säkerhetskopieringar](./media/howto-restore-server-portal/2-georestore.png)

3. Fyll i resten av formuläret med dina inställningar. Du kan välja en annan **plats**. Markera platsen och du kan välja **prisnivån**. Parametrarna för den befintliga servern som du återställer från visas som standard. Du kan klicka på **OK** utan ändringar ärver inställningarna. Eller så kan du ändra **Compute Generation** (om tillgänglig i regionen du har valt), antal **vCores**, **säkerhetskopiering kvarhållningsperiod**, och **säkerhetskopiering Redundans alternativet**. Ändra **prisnivån** (Basic, generella eller Minnesoptimerade) eller **lagring** storlek under återställning stöds inte.

>[!Note]
>Den nya servern som skapats av geo återställning har samma server admin inloggningsnamn och lösenord som är giltig för den befintliga servern när återställningen har startats. Lösenordet kan ändras från den nya servern **översikt** sidan.


## <a name="next-steps"></a>Nästa steg
- Mer information om tjänstens [säkerhetskopieringar](concepts-backup.md).
- Lär dig mer om [företagskontinuitet](concepts-business-continuity.md) alternativ.
