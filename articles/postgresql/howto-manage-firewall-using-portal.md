---
title: "Skapa och hantera brandväggsregler i Azure-databas för PostgreSQL"
description: "Skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure portal"
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: bef927cff49d957728a2a12362786d48d60e61b7
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure portal
Brandväggsregler på servernivå kan administratörer få åtkomst till en Azure-databas för PostgreSQL-Server från en angiven IP-adress eller intervall av IP-adresser. 

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att gå igenom den här instruktioner:
- En server [skapa en Azure-databas för PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal
1. På sidan PostgreSQL server under inställningar rubrik, klickar du på **anslutningssäkerhet** att öppna säkerhetssidan för Azure-databasen för PostgreSQL.

  ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Klicka på **Lägg till Min IP** i verktygsfältet. Detta skapar automatiskt en brandväggsregel med offentliga IP-adressen för datorn, som uppfattas som Azure systemet.

  ![Azure portal – Klicka på Lägg till Min IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Kontrollera din IP-adress innan du sparar du konfigurationen. I vissa situationer kan IP-adressen följas av Azure-portalen som skiljer sig från den IP-adress som används vid åtkomst till internet och Azure-servrar. Därför kan du behöva ändra första IP- och sista IP-om du vill att regeln funktionen som förväntat.
Använd en sökmotor eller andra online för att kontrollera din egen IP-adress. Till exempel söka efter ”vad är Min IP”.

  ![Bing-Sök efter vad som är Min IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Lägg till ytterligare adressintervall. Du kan ange en IP-adress eller ett adressintervall i brandväggsreglerna för Azure-databas för PostgreSQL. Om du vill begränsa regeln till en enda IP-adress skriver du samma adress i fältet för första IP- och slut-IP. Öppna brandväggen kan administratörer, användare och program för att logga in till en databas på PostgreSQL-server som de har giltiga autentiseringsuppgifter.

  ![Azure portal – brandväggsregler ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klicka på **spara** i verktygsfältet för att spara den här brandväggsregeln på servernivå. Vänta på att bekräfta att uppdateringen för brandväggsregler lyckades.

  ![Azure portal – Klicka på Spara](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ansluta från Azure
Azure-anslutningar måste vara aktiverat för att tillåta program från Azure att ansluta till din Azure-databas för PostgreSQL-servern. Till exempel som värd för ett Azure Web Apps-program eller ett program som körs i en Azure VM eller att ansluta från ett Azure Data Factory data management gateway. Resurser behöver inte finnas i samma virtuella nätverk (VNet) eller resursgruppen för brandväggsregeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns ett antal metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Alternativt kan du ställa in den **Tillåt åtkomst till Azure-tjänster** att **ON** på portalen från den **anslutningssäkerhet** rutan och trycker **spara**. Om anslutningen inte är tillåtet i begäran når inte Azure-databasen för PostgreSQL-servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Hantera befintliga brandväggsregler på servernivå via Azure Portal
Upprepa stegen för att hantera brandväggsreglerna.
* Om du vill lägga till den aktuella datorn, klicka på knappen + **Lägg till Min IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill lägga till ytterligare IP-adresser skriver du regelnamnet, start-IP-adressen och slut-IP-adressen. Klicka på **Spara** för att spara ändringarna.
* Om du vill redigera en befintlig regel klickar du på något av fälten i regeln och redigerar det. Klicka på **Spara** för att spara ändringarna.
* Om du vill ta bort en befintlig regel, klicka på knappen [...] och klicka på **ta bort** att ta bort regeln. Klicka på **Spara** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg
- På liknande sätt kan du skapa skript för att [skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure CLI](howto-manage-firewall-using-cli.md).
- Hjälp med att ansluta till en Azure-databas för PostgreSQL-servern finns [anslutningsbibliotek för Azure-databas för PostgreSQL](concepts-connection-libraries.md).
