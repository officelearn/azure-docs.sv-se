---
title: Hur du återställer en Server i Azure Database for MariaDB
description: Den här artikeln beskriver hur du återställer en server i Azure Database for MariaDB med Azure portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: cf6d0399c790a42eaf74b3ec339bf8cf67495fb3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548773"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-portal"></a>Säkerhetskopiera och återställa en server i Azure Database for MariaDB med Azure portal

## <a name="backup-happens-automatically"></a>Säkerhetskopieringen sker automatiskt
Azure Database for MariaDB servrar säkerhetskopieras regelbundet för att aktivera funktioner för återställning. Med den här funktionen kan du återställa servern och dess databaser till en tidigare point-in-time, på en ny server.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här guiden:
- En [Azure Database for MariaDB-server och databas](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Konfiguration av säkerhetskopiering

Du gör valet mellan att konfigurera servern för lokalt redundant säkerhetskopieringar eller geografiskt redundanta säkerhetskopieringar på servern har skapats, i den **prisnivå** fönster.

> [!NOTE]
> När en server har skapats kan typ av redundans som den har kan geografiskt redundant eller lokalt redundant inte växlas.
>

När du skapar en server via Azure-portalen i **prisnivå** fönstret är här du väljer antingen **lokalt Redundant** eller **geografiskt Redundant** säkerhetskopieringar för din server. Det här fönstret är också där du väljer den **kvarhållningsperiod** – hur lång tid (i antal dagar) som du vill att server-säkerhetskopior som lagras för.

   ![Prisnivån - Välj Säkerhetskopieringsredundans](./media/howto-restore-server-portal/pricing-tier.png)

Mer information om hur du anger dessa värden under skapa finns i den [Azure Database for MariaDB server Snabbstart](quickstart-create-mariadb-server-database-using-azure-portal.md).

Kvarhållningsperiod för säkerhetskopiering kan ändras på en server via följande steg:
1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj din Azure Database for MariaDB-server. Den här åtgärden öppnar den **översikt** sidan.

3. Välj **prisnivå** från menyn, under **inställningar**. Med hjälp av skjutreglaget som du kan ändra den **kvarhållningsperiod** enligt önskemål mellan 7 och 35 dagar.
På skärmbilden nedan har den ökat till 35 dagar.
![Kvarhållningsperiod för säkerhetskopiering ökar](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Klicka på **OK** att bekräfta ändringen.

Kvarhållningsperioden för säkerhetskopior styr hur långt tillbaka i tiden som en point-in-time-återställning kan hämtas, eftersom den är baserad på säkerhetskopior som är tillgängliga. Point-in-time-återställning är beskrivs mer i följande avsnitt. 

## <a name="point-in-time-restore"></a>Återställning från tidpunkt
Azure Database for MariaDB kan du återställa servern tillbaka till point-in-time och till att en ny kopia av servern. Du kan använda den nya servern för att återställa dina data eller ha dina klientprogram som pekar på den här nya servern.

Till exempel om en tabell var av misstag kan bort kl. tolv idag, du återställa till tid innan middagstid och hämta saknas tabellen och data från den nya kopian av servern. Point-in-time-återställning har servern skyddsnivå, inte på databasnivå.

Följande steg återställer exempelservern till point-in-time:
1. Välj din Azure Database for MariaDB-server i Azure-portalen. 

2. I verktygsfältet på serverns **översikt** väljer **återställa**.

   ![Azure Database for MariaDB - översikt – återställningsknappen](./media/howto-restore-server-portal/2-server.png)

3. Fyll i formuläret återställning med informationen som krävs:

   ![Azure Database for MariaDB - information för återställningspunkter ](./media/howto-restore-server-portal/3-restore.png)
  - **Återställningspunkt**: Välj du vill återställa till point-in-time.
  - **Målservern**: Ange ett namn för den nya servern.
  - **Plats**: Du kan inte välja region. Som standard är det samma som källservern.
  - **Prisnivå**: Du kan inte ändra dessa parametrar när du gör en point-in-time-återställning. Det är samma som källservern. 

4. Klicka på **OK** att återställa servern att återställa till point-in-time. 

5. Leta upp den nya servern som har skapats för att kontrollera dina data har återställts som förväntat när återställningen är klar.

>[!Note]
>Obs den nya servern som skapats av point-in-time-återställning har samma serveradministratör och lösenordet som användes för att befintliga server på point-in-time valde. Du kan ändra lösenordet från den nya servern **översikt** sidan.

## <a name="geo-restore"></a>GEO-återställning
Om du har konfigurerat din server som geografiskt redundanta säkerhetskopieringar, kan en ny server skapas från en säkerhetskopia av den befintliga servern. Den här nya servern kan skapas i valfri region som Azure Database for MariaDB är tillgängligt.  

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen. Skriv in **Azure Database for MariaDB** i sökrutan för att hitta tjänsten.

   ![Alternativet ”Azure-databas för MariaDB”](./media/howto-restore-server-portal/2_navigate-to-mariadb.png)

2. I formulärets **Välj källa för** listrutan Välj **Backup**. Den här åtgärden läser in en lista med servrar som har geo redundant säkerhetskopiering aktiverad. Välj en av dessa säkerhetskopior som källa för den nya servern.
   ![Välj källa: Säkerhetskopiering och listan över geo-redundanta säkerhetskopieringar](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > När en server skapas kanske det inte omedelbart tillgängliga för geo-återställning. Det kan ta några timmar innan den nödvändiga metadata fyllas i.
   >

3. Fyll i resten av formuläret med dina inställningar. Du kan välja en annan **plats**. När du har valt platsen, kan du välja **prisnivå**. Som standard visas parametrarna för den befintliga servern som du återställer från. Du kan klicka på **OK** utan några ändringar att ärva inställningarna. Eller du kan ändra **Compute-generering** (om tillgängligt i regionen du har valt), antal **vCores**, **kvarhållningsperiod**, och **säkerhetskopiering Alternativ för dataredundans**. Ändra **prisnivå** (Basic, generell användning eller Minnesoptimerade) eller **Storage** storleken under återställning stöds inte.

>[!Note]
>Den nya servern som skapats av geo-återställning har samma inloggningsnamn för serveradministratören och lösenord som var giltig för den befintliga servern vid den tidpunkt som återställningen startades. Lösenordet kan ändras från den nya servern **översikt** sidan.

## <a name="next-steps"></a>Nästa steg
- Mer information om tjänstens [säkerhetskopior](concepts-backup.md).
- Läs mer om [affärskontinuitet](concepts-business-continuity.md) alternativ.
