---
title: 'Snabb start: skapa en Azure DB för MySQL-flexibel Server – Azure Portal'
description: Den här artikeln beskriver hur du använder Azure Portal för att snabbt skapa en Azure Database for MySQL flexibel server på flera minuter.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 0c082c797c75ba912bafead15d24ea3941cfc25e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534200"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Snabb start: Använd Azure Portal för att skapa en Azure Database for MySQL flexibel Server

Azure Database for MySQL flexibel Server är en hanterad tjänst som du använder för att köra, hantera och skala hög tillgängliga MySQL-servrar i molnet. Den här snabb starten visar hur du skapar en flexibel server i flera med hjälp av Azure Portal.

> [!IMPORTANT] 
> Azure Database for MySQL flexibel Server är för närvarande en offentlig för hands version

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna webbläsaren och gå sedan till [Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Skapa en Azure Database for MySQL flexibel Server

Du skapar en flexibel server med en definierad uppsättning [beräknings-och lagrings resurser](./concepts-compute-storage.md). Du skapar servern i en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md).

Följ de här stegen för att skapa en flexibel Server:

1. Sök efter "Azure Database for MySQL" i portalen med hjälp av sökrutan för att hitta tjänsten. 
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Sök efter Azure Database for MySQL":::

2. Välj **Lägg till** . 

3. På sidan Välj distributions alternativ väljer du **flexibel Server** som distributions alternativ.
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Sök efter Azure Database for MySQL":::    

4. Fyll i formuläret **grundläggande** med följande information: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Sök efter Azure Database for MySQL"::: 
                                    
    |**Inställning**|**Föreslaget värde**|**Beskrivning**|
    |---|---|---|
    Prenumeration|Ditt prenumerationsnamn|Den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som du vill fakturera för resursen.|
    Resursgrupp|*myresourcegroup*| Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.|
    Servernamn |*mydemoserver*|Ett unikt namn som identifierar din flexibla Server. Domän namnet *MySQL.Database.Azure.com* läggs till i det Server namn du anger. Servernamnet får bara innehålla gemener, siffror och bindestreck (-). Det måste innehålla minst 3 och upp till 63 tecken.|
    Användarnamn för administratör |*mydemouser*| Ett eget inloggningskonto att använda när du ansluter till servern. Inloggnings namnet för administratören får inte vara **azure_superuser** , **administratör** , **administratör** , **rot** , **gäst** eller **offentlig** .|
    Lösenord |Ditt lösenord| Ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till och med 9) och icke-alfanumeriska tecken (!, $, #, % osv.).|
    Region|Den region som är närmast dina användare| Den plats som är närmast dina användare.|
    Version|5.7| MySQL huvud version.|
    Beräkning och lagring | **Burstable** , **Standard_B1ms** , **10 GIB** , **7 dagar** | Konfigurationerna för beräkning, lagring och säkerhetskopiering för den nya servern. Välj **Konfigurera Server** . *Burstable* , *Standard_B1ms* , *10 GIB* och *7 dagar* är standardvärden för     **beräknings nivå** , **beräknings storlek** , **lagring** och **bevarande period för säkerhets kopior** . Du kan lämna skjutreglagen som de är eller justera dem. Om du vill spara den här beräknings-och lagrings markeringen väljer du **Spara** för att fortsätta med konfigurationerna. Skärm bilden nedan visar beräknings-och lagrings alternativen.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Sök efter Azure Database for MySQL":::

5. Konfigurera nätverks alternativ

    På fliken nätverk kan du välja hur servern kan kontaktas. Azure Database for MySQL flexibel Server erbjuder två alternativ för att ansluta till servern via *offentlig åtkomst (tillåtna IP-adresser)* och *privat åtkomst (VNet-integrering)* . Med *offentlig åtkomst (tillåtna IP-adresser)* begränsas åtkomsten till servern till tillåtna IP-adresser som läggs till i en brand Väggs regel. Den förhindrar att externa program och verktyg ansluter till servern och alla databaser på servern, om du inte skapar en regel för att öppna brand väggen för en speciell IP-adress eller ett intervall. Med *privat åtkomst (VNet-integrering)* är åtkomst till din server begränsad till ditt virtuella nätverk. I den här snabb starten visar vi hur du aktiverar offentlig åtkomst för att ansluta till servern. Lär dig mer om anslutnings metoder i [artikeln begrepp](./concepts-networking.md).

    > [!NOTE]
    > Anslutnings metoden kan inte ändras när servern har skapats. Om du till exempel har valt *offentlig åtkomst (tillåtna IP-adresser)* under skapandet kan du inte ändra till *privat åtkomst (VNet-integrering)* när du har skapat. Vi rekommenderar starkt att du skapar en server med privat åtkomst för säker åtkomst till servern med VNet-integrering. Läs mer om privat åtkomst i [artikeln begrepp](./concepts-networking.md).

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Sök efter Azure Database for MySQL":::  

6. Välj **Granska + skapa** för att granska din flexibla Server konfiguration.

7. Välj **Skapa** för att etablera servern. Etableringen kan ta några minuter.

8. Välj **Aviseringar** (klockikonen) i verktygsfältet för att övervaka distributionsprocessen. När distributionen är färdig kan du välja **Fäst vid instrument panelen** , vilket skapar en panel för den här flexibla servern på Azure Portal instrument panelen som en genväg till serverns **översikts** sida. Om du väljer **Gå till resurs** öppnas serverns **översiktssida** .

Som standard skapas följande databaser i din server: **information_schema** , **mysql** , **performance_schema** och **sys** .

> [!NOTE]
> Kontrol lera om nätverket tillåter utgående trafik över Port 3306 som används av Azure Database for MySQL flexibel Server för att undvika anslutnings problem.  

## <a name="connect-to-using-mysql-command-line-client"></a>Ansluta till med MySQL kommando rads klient

Om du har skapat din flexibla server med *privat åtkomst (VNet-integrering)* måste du ansluta till servern från en resurs i samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i VNet som skapats med din flexibla Server.

Om du har skapat din flexibla server med *offentlig åtkomst (tillåtna IP-adresser)* kan du lägga till din lokala IP-adress i listan över brand Väggs regler på servern.

Du kan välja antingen [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) eller [MySQL Workbench](./connect-workbench.md) för att ansluta till servern från den lokala miljön. 

Med mysql.exe ansluter du med kommandot nedan. Ersätt värden med det faktiska Server namnet och lösen ordet. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Rensa resurser
Du har skapat en Azure Database for MySQL flexibel server i en resurs grupp.  Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort dem genom att ta bort resurs gruppen eller bara ta bort MySQL-servern. Följ dessa steg om du vill ta bort resurs gruppen:

1. I Azure Portal söker du efter och väljer **resurs grupper** .
1. I listan resurs grupp väljer du namnet på din resurs grupp.
1. På sidan Översikt i resurs gruppen väljer du **ta bort resurs grupp** .
1. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort** .

Om du vill ta bort servern kan du klicka på knappen **ta bort** på sidan **Översikt** på servern enligt nedan:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Sök efter Azure Database for MySQL":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bygg en PHP-webbapp (Laravel) med MySQL](tutorial-php-database-app.md)