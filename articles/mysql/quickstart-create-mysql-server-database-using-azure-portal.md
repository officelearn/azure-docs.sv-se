---
title: 'Snabb start: skapa en server-Azure Portal-Azure Database for MySQL'
description: Den här artikeln beskriver hur du använder Azure Portal för att snabbt skapa ett exempel på en Azure-databas för MySQL-server på fem minuter.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: eabc077080e16578857f9ba06e6874441dad07ee
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425864"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Snabb start: skapa en Azure Database for MySQL-server i Azure Portal

Azure Database för MySQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Den här snabb starten visar hur du använder Azure Portal för att skapa en Azure Database for MySQL enskild server och ansluta till servern.

## <a name="prerequisites"></a>Förutsättningar
En Azure-prenumeration krävs. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Skapa en Azure Database for MySQL enskild server
Gå till [Azure Portal](https://portal.azure.com/) för att skapa en MySQL-databas för enskild server. Sök efter och välj *Azure Database for MySQL*.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Hitta Azure Database for MySQL":::

1. Välj **Lägg till**.

2. På sidan Skapa en Azure Database for MySQL väljer du  **enskild server**.
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Välj en enskild server":::

3. Ange nu de grundläggande inställningarna för en ny enskild server.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Formulär för att skapa server":::

   **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
   ---|---|---
   Prenumeration | din prenumeration | Välj önskad Azure-prenumeration.
   Resursgrupp | *myresourcegroup* | En ny eller en befintlig resurs grupp från din prenumeration.
   Servernamn | *mydemoserver* | Ange ett unikt namn. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
   Datakälla |*Inga* | Välj Ingen om du vill skapa en ny server från grunden. Välj endast säkerhets kopiering om du har återställt från en geo-säkerhetskopia av en befintlig server.
   Användarnamn för administratör | *mydemoadmin* | Ange ditt användar namn för Server administratör. Du kan inte använda **azure_superuser** , **administratör** , **administratör** , **rot** , **gäst** eller **offentlig** som administratörs användar namn.
   Lösenord | ditt lösenord | Ett nytt lösen ord för Server administratörs användaren. Lösen ordet måste vara mellan 8 och 128 tecken långt med en kombination av versaler eller gemener, siffror och icke-alfanumeriska tecken (!, $, #,% osv.).
   Plats |önskad plats | Välj en plats i list rutan.
   Version | senaste huvud version| Använd den senaste huvud versionen. Se [alla versioner som stöds](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)
   Compute + Storage | Använd standardvärden| Standard pris nivån är Generell användning med **4 virtuella kärnor** och **100 GB** lagring. Kvarhållning av säkerhets kopior har angetts till **7 dagar** med geografiskt redundant säkerhets kopierings alternativ.<br/>Läs om [prissättningen](https://azure.microsoft.com/pricing/details/mysql/) och uppdatera standardinställningarna om det behövs.

   > [!NOTE]
   > Överväg att använda prisnivån Basic om lätt beräkning och I/O är lämpligt för din arbetsbelastning. Observera att servrar som skapas på prisnivån Basic inte senare kan skalas till Generell användning eller Minnesoptimerad.

4. Välj **Granska + skapa** för att etablera servern.

5. Vänta tills Portal sidan visar att **distributionen är klar**. Välj **gå till resurs** för att gå till den nyligen skapade Server sidan.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="lyckad distribution":::

[Har du problem? Berätta för oss](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Som standard skyddas servern som skapats med en brand vägg. För att ansluta måste du ge åtkomst till din IP-adress genom att följa dessa steg:

1. Gå till **anslutnings säkerhet** från den vänstra menyn för Server resursen. Vet inte hur du hittar din resurs, se [så här öppnar du en resurs](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Anslutningssäkerhet – Brandväggsregler":::

2. Välj **Lägg till aktuell klient-IP-adress** och välj sedan **Spara**.

   > [!NOTE]
   > Kontrol lera om nätverket tillåter utgående trafik över Port 3306 som används av Azure Database for MySQL för att undvika anslutnings problem.

Du kan lägga till ytterligare IP-adresser eller ange ett IP-intervall för att ansluta till servern från de IP-adresserna. Mer information finns i [Hantera brand Väggs regler på Azure Database for MySQL server](./concepts-firewall-rules.md)


[Har du problem? Berätta för oss](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-with-mysql-command-line-client"></a>Ansluta till servern med kommando rads klienten mysql
Du kan välja antingen [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) eller [MySQL Workbench](./connect-workbench.md) för att ansluta till servern från den lokala miljön. I den här snabb starten kommer vi att köra **mysql.exe** i [Azure Cloud Shell](../cloud-shell/overview.md) för att ansluta till servern.


1. Starta Azure Cloud Shell i portalen genom att klicka på den markerade ikonen på den översta vänstra sidan. Anteckna Server namnet, inloggnings namnet för Server administratören, lösen ordet och prenumerationen för den nyligen skapade servern från **översikts** avsnittet, som visas på bilden nedan.

    > [!NOTE]
    > Om du startar Cloud Shell för första gången visas en uppstarts fråga om att skapa en resurs grupp, ett lagrings konto. Det här är ett engångs steg och kommer automatiskt att bifogas för alla sessioner.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Portal fullständig vy Cloud Shell":::
2. Kör det här kommandot på Azure Cloud Shell terminal. Ersätt värdena med det faktiska Server namnet och inloggnings namnet för administratörs användaren. Administratörens användar namn kräver @ \<servername> som visas nedan för Azure Database for mysql

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Så här ser upplevelsen ut som i Cloud Shell terminalen

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. I samma Azure Cloud Shell terminal skapar du en databas **gäst**
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Ändra till databas **gäst**
      ```
      mysql> USE guest;
      Database changed
      ```
5. Skriv ```quit``` och välj sedan nyckeln RETUR för att avsluta MySQL.

[Har du problem? Berätta för oss](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Rensa resurser
Du har skapat en Azure Database for MySQL-server i en resurs grupp.  Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort dem genom att ta bort resurs gruppen eller bara ta bort MySQL-servern. Följ dessa steg om du vill ta bort resurs gruppen:
1. I Azure Portal söker du efter och väljer **resurs grupper**.
2. I listan resurs grupp väljer du namnet på din resurs grupp.
3. På sidan Översikt i resurs gruppen väljer du **ta bort resurs grupp**.
4. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort**.

Om du vill ta bort servern kan du klicka på knappen **ta bort** på sidan **Översikt** på servern enligt nedan:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Ta bort dina resurser":::

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
>[Bygg en PHP-app i Windows med MySQL](../app-service/app-service-web-tutorial-php-mysql.md) <br/>

> [!div class="nextstepaction"]
>[Bygga PHP-app på Linux med MySQL](../app-service/containers/tutorial-php-mysql-app.md)<br/><br/>

[Hittar du inte det du letar efter? Berätta för oss.](https://aka.ms/mysql-doc-feedback)
