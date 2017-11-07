---
title: "Utforma din första Azure-databas för MySQL - databas i Azure Portal | Microsoft Docs"
description: "Den här självstudiekursen beskrivs hur du skapar och hanterar Azure-databas för MySQL-server och databas med hjälp av Azure Portal."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.topic: tutorial
ms.date: 11/03/2017
ms.custom: mvc
ms.openlocfilehash: a7f38484e000b05a57cad9bc95abb255414d0162
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Utforma din första Azure-databas för MySQL-databas
Azure Database för MySQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Med Azure-portalen kan du enkelt hantera servern och utforma en databas.

I kursen får du använder Azure-portalen att lära dig hur du:

> [!div class="checklist"]
> * Skapa en Azure-databas för MySQL
> * Konfigurera server-brandväggen
> * Kommandoradsverktyget mysql för att skapa en databas
> * Läs in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna valfri webbläsare och gå den [Microsoft Azure-portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server
En Azure Database för MySQL-server skapas med en definierad uppsättning [compute- och lagringsresurser](./concepts-compute-unit-and-storage.md). Servern skapas inom en [Azure-resursgrupp](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

1. Gå till **databaser** > **Azure MySQL-databas**. Om du inte hittar MySQL-servern under **databaser** kategori, klickar du på **se alla** att visa alla tillgängliga databastjänster. Du kan också skriva **Azure-databas för MySQL** i sökrutan för att snabbt hitta tjänsten.
![2-1 navigerar du till MySQL](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2. Klicka på **Azure-databas för MySQL** panelen och klicka sedan på **skapa**.

I det här exemplet, fyller du i Azure-databasen för MySQL formulär med följande information:

| **Inställning** | **Föreslaget värde** | **Fältbeskrivning** |
|---|---|---|
| *Servernamn* | myserver4demo  | Servernamnet måste vara globalt unikt. |
| *Prenumeration* | mysubscription | Välj din prenumeration från listrutan. |
| *Resursgrupp* | myresourcegroup | Skapa en resursgrupp eller välj en befintlig. |
| *Inloggning för serveradministratör* | myadmin | Konfigurera admin kontonamn. |
| *Lösenord* |  | Ange ett starkt administratörslösenord. |
| *Bekräfta lösenord* |  | Bekräfta administratörslösenordet. |
| *Plats* |  | Välj en tillgänglig region. |
| *Version* | 5.7 | Välj den senaste versionen. |
| *Konfigurera prestanda* | Basic, 50 compute enheter, 50 GB  | Välj **Prisnivå**, **Compute-enheter**, **Lagring (GB)** och klicka på **OK**. |
| *Fäst vid instrumentpanelen* | Markera | Du bör markera den här kryssrutan så att du enkelt kan hitta servern senare |
Klicka på **Skapa**. Efter några minuter körs en ny Azure Database för MySQL-server i molnet. Du kan klicka på **meddelanden** i verktygsfältet för att övervaka processen för distribution.

## <a name="configure-firewall"></a>Konfigurera brandväggen
Azure-databaser för MySQL skyddas av en brandvägg. Som standard avvisas alla anslutningar till servern och databaser på servern. Innan du ansluter till Azure-databas för MySQL för första gången, kan du konfigurera brandväggen för att lägga till den klientdatorn offentliga IP-adress (eller IP-adressintervall).

1. Klicka på din nya server och klicka sedan på **anslutningssäkerhet**.
   ![3-1 anslutningssäkerhet](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)
2. Du kan **Lägg till Min IP**, eller konfigurera brandväggsregler här. Kom ihåg att klicka på **Spara** när du har skapat reglerna.
Nu kan du ansluta till servern med mysql-kommandoradsverktyget eller MySQL arbetsstationen GUI-verktyg.

> [!TIP]
> Azure-databas för MySQL-servern kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 3306 bli nekad av nätverkets brandvägg. I så fall, kan inte du ansluta till Azure MySQL-servern om din IT-avdelning öppnar port 3306.

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Hämta den fullständiga **servernamn** och **serverinloggningsnamnet för admin** för din Azure-databas för MySQL-servern från Azure-portalen. Du kan använda fullständigt kvalificerade servernamnet för att ansluta till servern med hjälp av kommandoradsverktyget för mysql. 

1. I [Azure-portalen](https://portal.azure.com/), klickar du på **alla resurser** skriver du namnet i den vänstra menyn och Sök efter din Azure-databas för MySQL-servern. Välj servernamnet för att visa information.

2. Under inställningarna rubrik, klickar du på **egenskaper**. Notera **servernamn** och **SERVERINLOGGNINGSNAMNET för ADMIN**. Du kan klicka på kopieringsknappen bredvid varje fält att kopiera till Urklipp.
   ![4-2-serveregenskaper](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

I det här exemplet är servernamnet *myserver4demo.mysql.database.azure.com* och inloggningen för serveradministratören är *myadmin@myserver4demo*.

## <a name="connect-to-the-server-using-mysql"></a>Ansluta till servern med mysql
Använd [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) till att upprätta en anslutning till din Azure Database för MySQL-server. Du kan köra kommandoradsverktyget mysql från Azure-molnet Shell i webbläsaren eller från din egen dator med hjälp av mysql-verktygen som installeras lokalt. Om du vill starta Azure Cloud-gränssnittet klickar du på den `Try It` på ett kodblock i den här artikeln eller besök Azure-portalen och klicka sedan på den `>_` ikonen i verktygsfältet övre högra. 

Skriv anslutningskommandot:
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

## <a name="create-a-blank-database"></a>Skapa en tom databas
När du är ansluten till servern kan du skapa en tom databas att arbeta med.
```sql
CREATE DATABASE mysampledb;
```

Kör följande kommando för att växla anslutning till den nya databasen i Kommandotolken:
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen
Nu när du vet hur du ansluter till Azure-databasen för MySQL-databas, kan du utföra vissa grundläggande uppgifter:

Först skapar en tabell och läsa in den med vissa data. Nu ska vi skapa en tabell som innehåller information om maskinvaruinventering.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Läser in data i tabeller
Nu när du har en tabell att infoga vissa data i den. Kör följande fråga för att infoga vissa rader med data i öppna en kommandotolk-fönster.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du två rader med exempeldata i tabellen som du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Fråga efter och uppdatera data i tabeller
Kör följande fråga för att hämta information från databastabellen.
```sql
SELECT * FROM inventory;
```

Du kan också uppdatera data i tabeller.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Raden uppdateras i enlighet med detta när du hämtar data.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt
Anta att du har tagits bort av misstag en viktigt-databastabell och kan enkelt återställa data. Azure MySQL-databas kan du återställa servern till en punkt i tiden, skapar en kopia av databaserna till den nya servern. Du kan använda den här nya servern för att återställa dina data. Följande steg återställa exempelserver till en innan tabellen har lagts till.

1. Hitta din Azure-databas för MySQL på Azure-portalen. På den **översikt** klickar du på **återställa** i verktygsfältet. Restore-sidan öppnas.

   ![10-1 Återställ en databas](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2. Fyll i den **återställa** formulär med informationen som krävs.
   
   ![10-2-formulär för återställning](./media/tutorial-design-database-using-portal/10_2-restore-form.png)
   
   - **Återställningspunkt**: Välj en i tidpunkt som du vill återställa till, under den tidsperiod som visas. Se till att konvertera din lokala tidszon till UTC.
   - **Återställ till en ny server**: Ange ett nytt servernamn som du vill återställa.
   - **Plats**: regionen är samma som källservern och kan inte ändras.
   - **Prisnivån**: prisnivån är samma som källservern och kan inte ändras.
   
3. Klicka på **OK** att återställa servern till [återställa till en tidpunkt](./howto-restore-server-portal.md) innan tabellen har tagits bort. Återställa en server skapar en ny kopia av servern, från och med tidpunkten du anger. 

## <a name="next-steps"></a>Nästa steg
I den här kursen kan du använda Azure portal till inlärda så här:

> [!div class="checklist"]
> * Skapa en Azure-databas för MySQL
> * Konfigurera server-brandväggen
> * Kommandoradsverktyget mysql för att skapa en databas
> * Läs in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

> [!div class="nextstepaction"]
> [Så här ansluter du program till Azure-databas för MySQL](./howto-connection-string.md)
