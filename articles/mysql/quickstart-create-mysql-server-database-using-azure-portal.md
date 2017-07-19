---
title: "Snabbstart: Skapa Azure-databas för MySQL-server – Azure Portal | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder Azure Portal för att snabbt skapa ett exempel på en Azure-databas för MySQL-server på fem minuter."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: hero-article
ms.date: 06/14/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: dba50b369fb87d5f6d5118038c75392bd719cc10
ms.contentlocale: sv-se
ms.lasthandoff: 06/28/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Skapa en Azure Database för MySQL med Azure Portal
Den här artikeln beskriver hur du använder Azure Portal till att skapa en Azure Database för MySQL-server på fem minuter. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure
Öppna webbläsaren och gå till [Microsoft Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-azure-database-for-mysql-server"></a>Skapa en Azure-databas för MySQL-servern
1. Klicka på knappen **Nytt** i det övre vänstra hörnet i Azure Portal.

2. Välj **Databaser** från sidan **Nytt** och välj **Azure Database för MySQL** från sidan **Databaser**. Du kan också hitta tjänsten genom att skriva **MySQL** i sökrutan för nya sidor.
![Azure Portal – ny – databas – MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Fyll i detaljformuläret för den nya server med följande information, som det visas i föregående bil:

| **Inställning** | **Föreslaget värde** | **Fältbeskrivning** |
|---|---|---|
| *Servernamn* | myserver4demo  | Servernamnet måste vara globalt unikt. |
| *Prenumeration* | mysubscription | Välj din prenumeration från listrutan. |
| *Resursgrupp* | myresourcegroup | Skapa en resursgrupp eller välj en befintlig. |
| *Inloggning för serveradministratör* | myadmin | Ange ett kontonamn som ska vara administratör i MySQL-motorn. |
| *Lösenord* |  | Ange ett starkt administratörslösenord. |
| *Bekräfta lösenord* |  | Bekräfta administratörslösenordet. |
| *Plats* |  | Välj en tillgänglig region. |
| *Version* | 5.7 | Välj den senaste versionen. |
| *Prisnivå* | Basic, 50 Compute-enheter, 50 lagringsutrymme (GB)  | Välj **Prisnivå**, **Compute-enheter**, **Lagring (GB)** och klicka på **OK**. |
| *Fäst vid instrumentpanelen* | Markera | Du bör markera den här kryssrutan så att du enkelt kan hitta servern senare |

   Klicka på **Prisnivå** för att ange pris- och prestandanivå för den nya databasen. I den här snabbstartsguiden väljer du nivån Basic, 50 Compute-enheter och 50 GB lagringsutrymme. Spara sedan prisnivån genom att klicka på **OK**.
   
   ![Azure Portal – skapa MySQL genom att tillhandahålla de formuläruppgifter som krävs](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

   Klicka på **Skapa**. Efter några minuter körs en ny Azure Database för MySQL-server i molnet. Klicka på knappen **Aviseringar** (klockikon) på verktygsfältet om du vill övervaka distribueringsprocessen.

## <a name="configure-the-firewall"></a>Konfigurera brandväggen
Innan du ansluter till Azure Database för MySQL för första gången ska du konfigurera brandväggen och lägga till klientens offentliga nätverks-IP-adress (eller adressintervall) till vitlistan.

1. När distributionen är färdig klickar du på **Alla resurser** i den vänstra menyn och skriver in namnet **myserver4demo** för att söka efter servern du nyss skapade. Klicka på servernamnet som listas i sökresultatet. Sidan Översikt för servern öppnas, och där ser du alternativ för ytterligare konfiguration.

2. I serverbladet, väljer du **anslutningssäkerhet**.

3. Klicka på **Lägg till min IP** för att lägga till den lokala datorns IP-adress eller konfigurera ett intervall med IP-adresser. Kom ihåg att klicka på **Spara** när du har skapat reglerna.
  ![Azure Portal – lägg till brandväggsregel och spara](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Hämta det fullständigt kvalificerade domännamnet för Azure MySQL-servern i Azure Portal. Du använder det fullständigt kvalificerade domännamnet för att ansluta till servern med kommandoradsverktyget **mysql.exe**.

1.  I [Azure Portal](https://portal.azure.com/) klickar du på **Alla resurser** på menyn till vänster och sedan på din Azure-databas för MySQL-servern.

2.  Klicka på **Egenskaper**. Anteckna **SERVERNAMNET** och **INLOGGNING FÖR SERVERADMINISTRATÖR**.
I det här exemplet är servernamnet *myserver4demo.mysql.database.azure.com* och inloggningen för serveradministratören är *myadmin@myserver4demo*.

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>Anslut till servern med kommandoradsverktyget mysqlexe
Använd [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) till att upprätta en anslutning till din Azure Database för MySQL-server. Du kan köra kommandoradsverktyget mysql i webbläsaren med Azure Cloud Shell, eller från din egen dator med mysql-verktyg som installerats lokalt. Om du vill starta Azure Cloud Shell klickar du på knappen `Try It` i ett kodblock i den här artikeln, eller så öppnar du [Azure Portal](https://portal.azure.com) och klickar på ikonen `>_` i det övre högra verktygsfältet. 

1. Skriv anslutningskommandot:
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. Visa serverstatus så att du ser att anslutningen fungerar. Skriv `status` vid prompten mysql> när anslutningen är upprättad.
```sql
status
```

   ![Kommandotolk – exempel på mysql-kommandorad](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

   > [!TIP]
   > Fler kommandon finns i [referenshandboken för MySQL 5.7 – kapitel 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

3. Skapa en tom databas genom att skriva kommandot `CREATE DATABASE` vid prompten mysql>.

   ```sql
   CREATE DATABASE quickstartdb;
   ```

   Du kan ha en eller flera databaser på en Azure Database för MySQL-server. Du kan välja att skapa en databas per server om du vill använda dig av samtliga resurser, eller skapa flera databaser som får dela på resurserna. Det finns ingen gräns för hur många databaser som kan skapas, men flera databaser delar samma serverresurser.  

4. Visa en lista över databaserna genom att skriva `SHOW DATABASES` vid prompten mysql>.

   ```sql
   SHOW DATABASES;
   ```

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Anslut till servern med verktyget MySQL Workbench GUI
1.  Starta programmet MySQL Workbench på klientdatorn. Du kan ladda ned och installera MySQL Workbench [här](https://dev.mysql.com/downloads/workbench/).

2.  I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

   ![konfigurera ny anslutning](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

| **Inställning** | **Föreslaget värde** | **Fältbeskrivning** |
|---|---|---|
|   *Anslutningsnamn* | Demoanslutning| Ange ett namn på anslutningen. |
| *Anslutningsmetod* | Standard (TCP/IP) | Standard (TCP/IP) är tillräckligt. |
| *Värdnamn* | myserver4demo.mysql.database.azure.com | Använd det fullständiga servernamnet. |
| *Port* | 3306 | Använd standardporten 3306. |
| *Användarnamn* | myadmin@myserver4demo  | Använd den administratörsinloggning du antecknade tidigare med ett @-tecken och servernamnet. |
| *Lösenord* | ditt lösenord | Klicka på knappen Spara i valvet... för att spara lösenordet. |

Klicka på **Testanslutning** för att testa om alla parametrar är rätt konfigurerade. Spara anslutningen genom att klicka på OK. 

> [!NOTE]
> SSL tillämpas som standard på din server vilket kräver extra konfiguration av anslutningar. Se [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Konfigurera SSL-anslutning i ditt program för att säkert ansluta till Azure Database för MySQL).  Om du vill inaktivera SSL för den här snabbstartsguiden öppnar du Azure Portal, klickar på sidan Anslutningssäkerhet och inaktiverar alternativet Framtvinga SSL-anslutning.

## <a name="clean-up-resources"></a>Rensa resurser
Rensa upp alla resurser du skapade i snabbstarten genom att ta bort [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter, ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure-portalen.

1.  I den vänstra menyn i Azure-portalen, klickar du på **Resursgrupper** och sedan på **myresourcegroup**.
2.  På sidan med dina resursgrupper, klickar du på **ta bort**, skriver in **myresourcegroup** i textrutan och klickar sedan på ta bort.

Gör så här om du vill ta bort den nyligen skapade servern:
1.  I den vänstra menyn i Azure-portalen, klickar du på PostgreSQL-servrar och söker efter den server som du nyss skapade
2.  Klicka på knappen Ta bort i den övre panelen ![Azure Database för MySQL – Ta bort server](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png) på sidan Översikt
3.  Kontrollera servernamnet som du vill ta bort och visa de databaser under den som påverkas. Skriv in **myserver4demo** i textrutan och klicka på Ta bort.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa din första Azure-databas för MySQL](./tutorial-design-database-using-portal.md)


