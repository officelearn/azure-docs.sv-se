---
title: Säkerhets kopiering och återställning – Azure Portal-Azure Database for MySQL
description: I den här artikeln beskrivs hur du återställer en server i Azure Database for MySQL med hjälp av Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 619dc05d709f41941d16764bf32b49a0d2a11958
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80372997"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Säkerhetskopiera och återställa en server i Azure Database for MySQL med hjälp av Azure Portal

## <a name="backup-happens-automatically"></a>Säkerhets kopiering sker automatiskt
Azure Database for MySQL servrar säkerhets kopie ras regelbundet för att aktivera återställnings funktioner. Med den här funktionen kan du återställa servern och alla dess databaser till en tidigare tidpunkt på en ny server.

## <a name="prerequisites"></a>Krav
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for MySQL-server och-databas](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Ange konfiguration för säkerhets kopiering

Du kan välja mellan att konfigurera servern för antingen lokalt redundanta säkerhets kopieringar eller geografiskt redundanta säkerhets kopieringar när servern skapas, i fönstret **pris nivå** .

> [!NOTE]
> När en server har skapats har den typ av redundans som den har, geografiskt redundant vs lokalt redundant, inte växlats.
>

När du skapar en server via Azure Portal, är **pris nivå** fönstret där du väljer antingen **lokalt redundant** eller **geografiskt redundant** säkerhets kopiering för servern. Det här fönstret är också där du väljer **kvarhållningsperiod för säkerhets kopior** – hur lång tid (i antal dagar) som du vill att serverns säkerhets kopior ska lagras.

   ![Pris nivå – Välj redundans för säkerhets kopiering](./media/howto-restore-server-portal/pricing-tier.png)

Mer information om hur du anger dessa värden under skapa finns i [snabb starten för Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md).

Kvarhållningsperioden för säkerhets kopior kan ändras på en server genom följande steg:
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj din Azure Database for MySQL-server. Den här åtgärden öppnar **översikts** sidan.
3. Välj **pris nivå** på menyn under **Inställningar**. Med skjutreglaget kan du ändra **kvarhållningsperioden för säkerhets kopior** till dina preferenser mellan 7 och 35 dagar.
I skärm bilden nedan har den ökats till 34 dagar.
![Kvarhållning av säkerhets kopior har ökat](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Bekräfta ändringen genom att klicka på **OK** .

Kvarhållningsperioden för säkerhets kopior styr hur långt tillbaka i tiden en tidpunkts återställning kan hämtas, eftersom den baseras på tillgängliga säkerhets kopior. Återställning av tidpunkt för tidpunkt beskrivs ytterligare i följande avsnitt. 

## <a name="point-in-time-restore"></a>Återställning från tidpunkt
Med Azure Database for MySQL kan du återställa servern till en tidpunkt och till en ny kopia av-servern. Du kan använda den här nya servern för att återställa dina data eller låta klient programmen peka på den nya servern.

Om en tabell till exempel råkat släppas efter middag idag, kan du återställa till tiden precis före 12.00 och hämta den saknade tabellen och data från den nya kopian av servern. Återställning av tidpunkt på Server är på server nivå, inte på databas nivå.

Följande steg återställer exempel servern till en tidpunkt:
1. I Azure Portal väljer du Azure Database for MySQL-servern. 

2. I verktygsfältet på sidan **Översikt** för servern väljer du **Återställ**.

   ![Azure Database for MySQL-översikt – knappen Återställ](./media/howto-restore-server-portal/2-server.png)

3. Fyll i formuläret Återställ med den information som behövs:

   ![Azure Database for MySQL-återställnings information](./media/howto-restore-server-portal/3-restore.png)
   - **Återställnings punkt**: Välj den tidpunkt som du vill återställa till.
   - **Mål server**: Ange ett namn för den nya servern.
   - **Plats**: du kan inte välja region. Som standard är det samma som käll servern.
   - **Pris nivå**: du kan inte ändra de här parametrarna när du gör en tidpunkts återställning. Det är samma som källservern. 

4. Klicka på **OK** för att återställa servern för att återställa till en tidpunkt. 

5. När återställningen är klar letar du upp den nya server som har skapats för att verifiera att data har återställts som förväntat.

Den nya servern som skapats av återställning vid olika tidpunkter har samma inloggnings namn och lösen ord för Server administratören som var giltiga för den befintliga servern vid den tidpunkt som väljs. Du kan ändra lösen ordet från den nya serverns **översikts** sida.

Den nya servern som skapades under en återställning saknar de VNet-tjänstens slut punkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den här nya servern. Brand Väggs regler från den ursprungliga servern återställs.

## <a name="geo-restore"></a>Geo-återställning
Om du har konfigurerat servern för geografiskt redundanta säkerhets kopieringar kan en ny server skapas från säkerhets kopian av den befintliga servern. Den nya servern kan skapas i vilken region som helst som Azure Database for MySQL tillgänglig.  

1. Välj knappen **skapa en resurs** (+) i det övre vänstra hörnet i portalen. Välj **databaser** > **Azure Database for MySQL**.

   ![Alternativet Azure Database for MySQL](./media/howto-restore-server-portal/2_navigate-to-mysql.png)

2. Välj **säkerhets kopiering**i list rutan **Välj källa** för formuläret. Den här åtgärden läser in en lista över servrar som har geo-redundanta säkerhets kopieringar aktiverade. Välj en av dessa säkerhets kopior som ska vara källan till den nya servern.
   ![Välj källa: säkerhets kopiering och lista över geo-redundanta säkerhets kopieringar](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > När en server först skapas kanske den inte är omedelbart tillgänglig för geo Restore. Det kan ta några timmar för nödvändiga metadata att fyllas i.
   >

3. Fyll i resten av formuläret med dina inställningar. Du kan välja valfri **plats**. När du har valt platsen kan du välja **pris nivå**. Som standard visas parametrarna för den befintliga servern som du återställer från. Du kan klicka på **OK** utan att göra några ändringar för att ärva dessa inställningar. Du kan också ändra **beräknings genereringen** (om den är tillgänglig i den region du har valt), antal **virtuella kärnor**, **kvarhållning av säkerhets kopior**och **alternativet för redundans**. Att ändra **pris nivå** (Basic, generell användning eller minnesoptimerade) eller **lagrings** storlek under återställningen stöds inte.

Den nya servern som skapades med geo Restore har samma inloggnings namn och lösen ord för Server administratören som var giltiga för den befintliga servern vid den tidpunkt då återställningen initierades. Du kan ändra lösen ordet från den nya serverns **översikts** sida.

Den nya servern som skapades under en återställning saknar de VNet-tjänstens slut punkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den här nya servern. Brand Väggs regler från den ursprungliga servern återställs.

## <a name="next-steps"></a>Nästa steg
- Läs mer om tjänstens [säkerhets kopior](concepts-backup.md)
- Lär dig mer om [repliker](concepts-read-replicas.md)
- Läs mer om alternativ för [affärs kontinuitet](concepts-business-continuity.md)
