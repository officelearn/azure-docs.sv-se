---
title: "Snabbstart: Skapa Azure-databas för MySQL-server – Azure Portal | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder Azure Portal för att snabbt skapa ett exempel på en Azure-databas för MySQL-server på fem minuter."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 25bfd2c6c25ddb8747dec58fdc68f904f81127fa
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Skapa en Azure Database för MySQL med Azure Portal

Den här artikeln beskriver hur du använder Azure Portal för att skapa ett exempel på en Azure-databas för MySQL-server på fem minuter. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure
Öppna webbläsaren och gå till [Microsoft Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter för att logga in på portalen. Standardvyn är instrumentpanelen.

![Azure Portal – inloggning och instrumentpanel](./media/quickstart-create-mysql-server-database-using-azure-portal/1_portal-login.png)

## <a name="create-azure-database-for-mysql-server"></a>Skapa en Azure-databas för MySQL-servern

1. Navigera till **Databaser** > **MySQL**. Om du inte hittar Azure Database för MySQL Server under kategorin **Databaser** klickar du på **Visa alla** för att visa alla tillgängliga databastjänster. Du kan också skriva **MySQL** i sökrutan för att snabbt hitta tjänsten.
![Azure Portal – ny – databas – MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

2. Klicka på ikonen **MySQL** och sedan på **Skapa**.
I vårt exempel ska du fylla i Azure Database för MySQL-sidan med följande information:

| **Formulärfält** | **Fältbeskrivning** |
|----------------|-----------------------|
| *Servernamn* | mysqlserver4demo (servernamnet är globalt unikt) |
| *Prenumeration* | MySQLaaS (välj från listrutan) |
| *Resursgrupp* | myresource (skapa en resursgrupp eller välj en befintlig) |
| *Inloggning för serveradministratör* | myadmin (konfigurera namn på administratörskonto) |
| *Lösenord* | konfigurera lösenord för administratörskonto |
| *Bekräfta lösenord* | bekräfta lösenord för administratörskonto |
| *Plats* | Europa, norra (välj mellan **Europa, norra** och **USA, västra**) |
| *Version* | 5.6 (välj MySQL-serverversion) |
| *Konfigurera prestanda* | Basic (välj **Prestandanivå**, **Compute-enheter**, **Storage** (Minne) och sedan på **OK**) |

![Azure Portal – skapa MySQL genom att tillhandahålla de formuläruppgifter som krävs](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

Efter några minuter är Azure Database för MySQL-servern etablerad och körs. Du kan klicka på knappen **Aviseringar** (klockikon) på verktygsfältet för att övervaka distribueringsprocessen.

> [!TIP]
> Vi rekommenderar att du lägger Azure-tjänster i samma region och väljer den plats som är närmast dig. Du kan också markera alternativet **Fäst på instrumentpanelen** för att enkelt spåra dina distributioner.

## <a name="configure-the-firewall"></a>Konfigurera brandväggen
Innan du ansluter till Azure-databasen för MySQL från klientdatorer för första gången måste du konfigurera brandväggen och lägga till klientens offentliga nätverks-IP-adress (eller IP-adressintervall) till vitlistan.

1. Klicka på din nyligen skapade server och sedan på **Inställningar**.
  ![Azure Portal – MySQL – knappen Inställningar](./media/quickstart-create-mysql-server-database-using-azure-portal/4_server-settings.png)

2. I avsnittet **ALLMÄNT** klickar du på **Brandväggsinställningar**. Du kan klicka på **Lägg till min IP** för att lägga till den lokala datorns IP-adress eller konfigurera ett intervall med IP-adresser. Kom ihåg att klicka på **Spara** när du har skapat reglerna.
  ![Azure Portal – lägg till brandväggsregel och spara](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Hämta det fullständigt kvalificerade domännamnet för Azure MySQL-servern i Azure Portal. Du använder det fullständigt kvalificerade domännamnet för att ansluta till servern med kommandoradsverktyget **mysql.exe**.

1.    I [Azure Portal](https://portal.azure.com/) klickar du på **Alla resurser** på menyn till vänster och sedan på din Azure-databas för MySQL-servern.

2.    Klicka på **Egenskaper**. Anteckna **SERVERNAMNET** och **INLOGGNING FÖR SERVERADMINISTRATÖR**.
I det här exemplet är servernamnet *mysql4doc.database.windows.net*, och inloggningen för serveradministratören är *mysqladmin@mysql4doc*.

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>Anslut till servern med kommandoradsverktyget mysqlexe
Du kan skapa flera databaser i en MySQL-server. Det finns ingen gräns för hur många databaser som kan skapas, men flera databaser delar samma serverresurser.  Du ansluter till din server med kommandoradsverktyget **mysql.exe**, öppnar **Azure Cloud Shell** i portalen och anger följande:

1. Anslut till servern med kommandoradsverktyget **mysql**:
```dos
 mysql -h mysqlserver4demo.database.windows.net -u myadmin@mysqlserver4demo -p
```

2. Visa status för servern:
```dos
 mysql> status
```
  ![Kommandotolk – exempel på mysql-kommandorad](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

> [!TIP]
> Fler kommandon finns i [referenshandboken för MySQL 5.6 – kapitel 4.5.1](https://dev.mysql.com/doc/refman/5.6/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Anslut till servern med verktyget MySQL Workbench GUI
1.    Starta programmet MySQL Workbench på klientdatorn. Du kan ladda ned och installera MySQL Workbench [här](https://dev.mysql.com/downloads/workbench/).

2.    I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

| **Parametrar** | **Beskrivning** |
|----------------|-----------------|
|    *Anslutningsnamn* | ange ett namn på anslutningen (det kan vara vad som helst) |
| *Anslutningsmetod* | välj Standard (TCP/IP) |
| *Värdnamn* | mycliserver.database.windows.net (SERVERNAMNET du antecknade tidigare) |
| *Port* | 3306 |
| *Användarnamn* | myadmin@mycliserver (INLOGGNING FÖR SERVERADMINISTRATÖR som du antecknade tidigare) |
| *Lösenord* | du kan lagra lösenordet för administratörskontot i valvet |

![konfigurera ny anslutning](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

3.    Klicka på **Testanslutning** för att testa om alla parametrar är rätt konfigurerade.

4.    Nu kan du klicka på anslutningen du just skapade för att ansluta till servern.

> SSL tillämpas som standard på din server vilket kräver extra konfiguration för att ansluta ordentligt. Se [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Konfigurera SSL-anslutning i ditt program för att säkert ansluta till Azure Database för MySQL).  Om du vill avaktivera SSL för den här snabbstarten kan du gå till ”Anslutningssäkerhet” i portalen för att avaktivera tvingande SSL.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de här resurserna för en annan snabbstart/självstudie kan du ta bort dem genom att göra följande:

1. På menyn till vänster i Azure Portal klickar du på **Resursgrupper** och sedan på **myresource**. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver **myresource** i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa din första Azure-databas för MySQL](./tutorial-design-database-using-portal.md)


