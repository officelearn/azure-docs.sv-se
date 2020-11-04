---
title: 'Snabb start: skapa en server-Azure Portal-Azure Database for MySQL'
description: Den här snabb starten visar hur du använder Azure Portal för att snabbt skapa ett exempel på en Azure Database for MySQL server på ungefär fem minuter.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: 2cae0187643eb596bd98bcd99a588a4d214e6f6e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341220"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Snabb start: skapa en Azure Database for MySQL-server i Azure Portal

Azure Database för MySQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure Database for MySQL-server på ungefär fem minuter med Azure Portal.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna webbläsaren och gå sedan till [Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server
Du skapar en Azure Database för MySQL-server med en definierad uppsättning [Compute- och Storage-resurser](./concepts-pricing-tiers.md). Du skapar servern i en [Azure-resursgrupp](../azure-resource-manager/management/overview.md).

Följ de här stegen för att skapa en Azure Database för MySQL-server:

1. Välj **skapa en resurs** (+) i det övre vänstra hörnet i portalen.

2. Välj **databaser**  >  **Azure Database for MySQL**. Du kan också ange **MySQL** i sökrutan för att hitta tjänsten.

  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Alternativet Azure-databas för MySQL":::

3. Fyll i formuläret om den nya servern och uppge följande information:
    
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Formulär för att skapa server":::

**Inställning** | **Föreslaget värde** | **Fältbeskrivning** 
---|---|---
Prenumeration | Din prenumeration | Välj den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
Resursgrupp | *myresourcegroup* | Ange ett nytt eller ett befintligt resursgruppnamn. Resurs gruppen kan användas för att ordna beroenden som tillhör ett enskilt projekt.
Servernamn | Unikt servernamn | Ange ett unikt namn som identifierar din Azure Database for MySQL-server. Till exempel "mysqldbserver". Server namnet får bara innehålla gemena bokstäver, siffror och bindestreck (-). Det måste innehålla mellan 3 och 63 tecken.
Datakälla |*Inga* | Välj *Ingen* om du vill skapa en ny server från grunden. (Du väljer *säkerhets kopia* om du skapar en server från en geo-säkerhetskopia av en befintlig Azure Database for MySQL-server).
Inloggning för serveradministratör | myadmin | Ange ett användar namn för Server administratören. Du kan inte använda **azure_superuser** , **administratör** , **administratör** , **rot** , **gäst** eller **offentlig** som administratörs användar namn.
Lösenord | *Ditt val* | Ange ett nytt lösenord för serverns administratörskonto. Lösen ordet måste vara mellan 8 och 128 tecken långt med en kombination av versaler eller gemener, siffror och icke-alfanumeriska tecken (!, $, #,% osv.).
Bekräfta lösenordet | *Ditt val*| Bekräfta administratörslösenordet.
Plats | *Den region som är närmast dina användare*| Välj den plats som är närmast dina användare eller dina andra Azure-program.
Version | *Senaste huvudversion*| Välj den senaste huvudversionen (om du inte har särskilda behov som gör att du måste ha en annan version).
Compute + Storage | **Generell användning** , **Gen 5** , **2 virtuella kärnor** , **5 GB** , **7 dagar** , **Geografiskt redundant** |Konfigurationerna för beräkning, lagring och säkerhetskopiering för den nya servern. Välj **Konfigurera Server**. Sedan väljer du lämplig pris nivå. mer information finns på [sidan med priser](https://azure.microsoft.com/pricing/details/mysql/). Om du vill aktivera server säkerhets kopieringar i Geo-redundant lagring väljer du **geografiskt redundant** från **alternativen för redundans för säkerhets kopiering**. Välj **OK**.

   > [!NOTE]
   > Överväg att använda prisnivån Basic om lätt beräkning och I/O är lämpligt för din arbetsbelastning. Observera att servrar som skapas på prisnivån Basic inte senare kan skalas till Generell användning eller Minnesoptimerad. 

4. Välj **Granska + skapa** för att etablera servern. Etableringen kan ta upp till 20 minuter.
   
5. Välj **Aviseringar** (klockikonen) i verktygsfältet för att övervaka distributionsprocessen.
   
Som standard skapas följande databaser i din server: **information_schema** , **mysql** , **performance_schema** och **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå
Som standard skyddas servern som skapats med en brand vägg och är inte tillgänglig offentligt. Om du vill ge åtkomst till din IP-adress går du till Server resursen i Azure Portal och väljer **anslutnings säkerhet** på den vänstra menyn för Server resursen. Vet inte hur du hittar din resurs, se [så här öppnar du en resurs](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Anslutningssäkerhet – Brandväggsregler":::
   
Välj nu **Lägg till aktuell klient-IP-adress** och välj sedan **Spara**. Du kan lägga till ytterligare IP-adresser eller ange ett IP-intervall för att ansluta till servern från de IP-adresserna. Mer information finns i [Hantera brand Väggs regler på Azure Database for MySQL server](./concepts-firewall-rules.md)

> [!NOTE]
> Kontrol lera om nätverket tillåter utgående trafik över Port 3306 som används av Azure Database for MySQL för att undvika anslutnings problem.  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Ansluta till Azure Database for MySQL server med MySQL kommando rads klient
Du kan välja antingen [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) eller [MySQL Workbench](./connect-workbench.md) för att ansluta till servern från den lokala miljön. I den här snabb starten kommer vi att köra **mysql.exe** i [Azure Cloud Shell](../cloud-shell/overview.md) för att ansluta till servern.

1. Starta Azure Cloud Shell i portalen genom att klicka på den markerade ikonen på den översta vänstra sidan. Anteckna Server namnet, inloggnings namnet för Server administratören, lösen ordet och prenumerationen för den nyligen skapade servern från **översikts** avsnittet, som visas på bilden nedan.

    >[!NOTE]
    >Om du startar Cloud Shell för första gången visas en uppstarts fråga om att skapa en resurs grupp, ett lagrings konto. Det här är ett engångs steg och kommer automatiskt att bifogas för alla sessioner. 

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
>[Bygg en PHP-app i Windows med MySQL](../app-service/tutorial-php-mysql-app.md)
