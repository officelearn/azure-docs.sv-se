---
title: Säkerhetskopiering och återställning - Azure-portal - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du återställer en server i Azure Database för PostgreSQL - Single Server med Azure-portalen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: fb13e4f062976e39c3cec607001e6982db228873
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765638"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Säkerhetskopiering och återställning av en server i Azure Database for PostgreSQL - Single Server med Azure-portalen

## <a name="backup-happens-automatically"></a>Säkerhetskopiering sker automatiskt
Azure Database för PostgreSQL-servrar säkerhetskopieras med jämna mellanrum för att aktivera återställningsfunktioner. Med den här funktionen kan du återställa servern och alla dess databaser till en tidigare tidpunkt, på en ny server.

## <a name="set-backup-configuration"></a>Ange konfiguration för säkerhetskopiering

Du kan välja mellan att konfigurera servern för antingen lokalt redundanta säkerhetskopior eller geografiskt redundanta säkerhetskopior när servern skapas i fönstret **Prisnivå.**

> [!NOTE]
> När en server har skapats kan den typ av redundans som den har, geografiskt redundant kontra lokalt redundant, inte växlas.
>

När du skapar en server via Azure-portalen är fönstret **Prisnivå** där du väljer antingen **Lokalt redundanta** eller **geografiskt redundanta** säkerhetskopior för servern. Det här fönstret är också där du väljer **lagringsperioden för säkerhetskopiering** – hur länge (i antal dagar) du vill att säkerhetskopieringarna av servern ska lagras.

   ![Prisnivå – välj redundans för säkerhetskopiering](./media/howto-restore-server-portal/pricing-tier.png)

Mer information om hur du anger dessa värden under skapa finns i [snabbstarten för Azure Database for PostgreSQL-servern](quickstart-create-server-database-portal.md).

Lagringsperioden för säkerhetskopiering för en server kan ändras genom följande steg:
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj din Azure Database for PostgreSQL-server. Den här åtgärden öppnar sidan **Översikt.**
3. Välj **Prisnivå** på menyn under **INSTÄLLNINGAR**. Med skjutreglaget kan du ändra **lagringsperioden för säkerhetskopiering** till dina önskemål mellan 7 och 35 dagar.
I skärmdumpen nedan har det ökats till 34 dagar.
![Lagringsperioden för säkerhetskopiering har ökat](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Klicka på **OK** för att bekräfta ändringen.

Lagringsperioden för säkerhetskopiering styr hur långt tillbaka i tiden en point-in-time-återställning kan hämtas, eftersom den baseras på tillgängliga säkerhetskopior. Återställning i tid beskrivs ytterligare i följande avsnitt. 

## <a name="point-in-time-restore"></a>Återställning från tidpunkt
Azure Database for PostgreSQL kan du återställa servern tillbaka till en punkt i tid och till en ny kopia av servern. Du kan använda den nya servern för att återställa dina data eller låta klientprogrammen peka på den nya servern.

Om en tabell till exempel av misstag togs bort vid lunchtid i dag kan du återställa till tiden strax före klockan tolv och hämta den saknade tabellen och data från den nya kopian av servern. Point-in-time-återställningen sker på servernivå, inte på databasnivå.

I följande steg återställs exempelservern till en punkt i tid:
1. I Azure-portalen väljer du din Azure-databas för PostgreSQL-server. 

2. Välj **Återställ**i verktygsfältet på serverns **översiktssida** .

   ![Knappen Azure Database för PostgreSQL - Översikt - Återställ](./media/howto-restore-server-portal/2-server.png)

3. Fyll i formuläret Återställ med den information som behövs:

   ![Azure-databas för PostgreSQL - Återställ information](./media/howto-restore-server-portal/3-restore.png)
   - **Återställningspunkt:** Välj den punkt i tid som du vill återställa till.
   - **Målserver:** Ange ett namn för den nya servern.
   - **Plats**: Du kan inte välja regionen. Som standard är det samma som källservern.
   - **Prisnivå**: Du kan inte ändra dessa parametrar när du gör en point-in-time-återställning. Det är samma som källservern. 

4. Klicka på **OK** om du vill återställa servern för att återställa till en punkt i tid. 

5. När återställningen är klar letar du reda på den nya servern som har skapats för att verifiera att data återställdes som förväntat.

Den nya servern som skapas av point-in-time-återställningen har samma inloggningsnamn och lösenord för serveradministratörer som var giltiga för den befintliga servern vid den punkt-i-tid som valts. Du kan ändra lösenordet från den nya serverns **översiktssida.**

Den nya servern som skapades under en återställning har inte brandväggsreglerna eller VNet-tjänstslutpunkterna som fanns på den ursprungliga servern. Dessa regler måste ställas in separat för den nya servern.


## <a name="geo-restore"></a>Geo återställa

Om du har konfigurerat servern för geografiskt redundanta säkerhetskopior kan en ny server skapas från säkerhetskopian av den befintliga servern. Den här nya servern kan skapas i alla regioner som Azure Database for PostgreSQL är tillgänglig.  

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet av portalen. Välj **Databaser** > **Azure Database för PostgreSQL**.

   ![Alternativet ”Azure-databas för PostgreSQL”](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. I listrutan **Välj källa** i formuläret väljer du **Säkerhetskopiering**. Den här åtgärden läser in en lista över servrar som har geo redundanta säkerhetskopior aktiverade. Välj en av dessa säkerhetskopior som ska vara källan till den nya servern.
   ![Välj källa: Säkerhetskopiering och lista över geo redundanta säkerhetskopior](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > När en server först skapas kanske den inte är omedelbart tillgänglig för geoåterställning. Det kan ta några timmar innan de nödvändiga metadata fylls i.
   >

3. Fyll i resten av formuläret med dina önskemål. Du kan välja vilken **plats som**helst . När du har valt plats kan du välja **Prisnivå**. Som standard visas parametrarna för den befintliga servern som du återställer från. Du kan klicka på **OK** utan att göra några ändringar för att ärva dessa inställningar. Du kan också ändra **beräkningsgenerering** (om det finns tillgängligt i den region du har valt), antal **virtuella kärnor,** **lagringsperiod för säkerhetskopiering**och **säkerhetskopieringsredundansalternativ**. Det går inte att ändra **prisnivån** (grundläggande, allmänt syfte eller minnesoptimerad) eller **lagringsstorlek** under återställningen.


Den nya servern som skapas av geo återställning har samma serveradministratörsinloggningsnamn och lösenord som var giltiga för den befintliga servern när återställningen initierades. Lösenordet kan ändras från den nya serverns **översiktssida.**

Den nya servern som skapades under en återställning har inte brandväggsreglerna eller VNet-tjänstslutpunkterna som fanns på den ursprungliga servern. Dessa regler måste ställas in separat för den nya servern.


## <a name="next-steps"></a>Nästa steg
- Läs mer om tjänstens [säkerhetskopior](concepts-backup.md).
- Läs mer om alternativ [för affärskontinuitet.](concepts-business-continuity.md)
