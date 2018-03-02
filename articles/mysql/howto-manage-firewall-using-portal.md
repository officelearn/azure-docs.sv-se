---
title: "Skapa och hantera MySQL brandväggsregler i Azure-databas för MySQL"
description: "Skapa och hantera Azure-databas för MySQL brandväggsregler med hjälp av Azure portal"
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6475c5c3ecb43352a8ef8db8fe1c023a16cd8a3d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure-databas för MySQL brandväggsregler med hjälp av Azure portal
Brandväggsregler på servernivå kan administratörer få åtkomst till en Azure-databas för MySQL-Server från en angiven IP-adress eller ett intervall med IP-adresser. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

1. På sidan MySQL-server under inställningar rubrik, klickar du på **anslutningssäkerhet** att öppna sidan anslutningssäkerhet för Azure-databas för MySQL.

   ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Klicka på **Lägg till Min IP** i verktygsfältet. Detta skapar automatiskt en brandväggsregel med offentliga IP-adressen för datorn, som uppfattas som Azure systemet.

   ![Azure portal – Klicka på Lägg till Min IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Kontrollera din IP-adress innan du sparar du konfigurationen. I vissa situationer kan IP-adressen följas av Azure-portalen som skiljer sig från den IP-adress som används vid åtkomst till internet och Azure-servrar. Därför kan du behöva ändra första IP- och sista IP-om du vill att regeln funktionen som förväntat.

   Använd en sökmotor eller andra online för att kontrollera din egen IP-adress. Sök till exempel ”vad är IP-adress”. 

   ![Bing för vad som är Min IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Lägg till ytterligare adressintervall. Du kan ange en IP-adress eller ett adressintervall i brandväggsreglerna för Azure-databas för MySQL. Om du vill begränsa regeln till en enda IP-adress skriver du samma adress i fälten första IP- och slut-IP. Öppna brandväggen kan administratörer, användare och program för att komma åt alla databaser på servern MySQL som de har giltiga autentiseringsuppgifter.

   ![Azure portal – brandväggsregler ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klicka på **spara** i verktygsfältet för att spara den här brandväggsregeln på servernivå. Vänta på att bekräfta att uppdateringen för brandväggsregler har lyckats.

   ![Azure portal – Klicka på Spara](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ansluta från Azure
Azure-anslutningar så att program från Azure att ansluta till din Azure-databas för MySQL-servern måste vara aktiverat. Till exempel som värd för ett Azure Web Apps-program eller ett program som körs i en Azure VM eller att ansluta från ett Azure Data Factory data management gateway. Resurser behöver inte finnas i samma virtuella nätverk (VNet) eller resursgruppen för brandväggsregeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns ett antal metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Alternativt kan du ställa in den **Tillåt åtkomst till Azure-tjänster** att **ON** på portalen från den **anslutningssäkerhet** rutan och trycker **spara**. Om anslutningen inte är tillåtet i begäran når inte Azure-databasen för MySQL-servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Hantera befintliga brandväggsregler för servernivå med hjälp av Azure portal
Upprepa stegen för att hantera brandväggsreglerna.
* Om du vill lägga till den aktuella datorn, klickar du på **+ Lägg till Min IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill lägga till ytterligare IP-adresser, ange den **REGELNAMN**, **första IP-**, och **sista IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill ändra en befintlig regel klickar du på något av fälten i regeln och sedan ändra. Klicka på **Spara** för att spara ändringarna.
* Om du vill ta bort en befintlig regel, klicka på knappen [...] och klicka sedan på **ta bort**. Klicka på **Spara** för att spara ändringarna.


## <a name="next-steps"></a>Nästa steg
- På liknande sätt kan du skapa skript för att [skapa och hantera Azure-databas för MySQL brandväggsregler med hjälp av Azure CLI](howto-manage-firewall-using-cli.md).
- Hjälp med att ansluta till en Azure-databas för MySQL-servern finns [anslutningsbibliotek för Azure-databas för MySQL](./concepts-connection-libraries.md)
