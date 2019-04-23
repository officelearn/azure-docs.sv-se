---
title: Skapa och hantera brandväggsregler i Azure Database för PostgreSQL
description: Skapa och hantera Azure Database för PostgreSQL brandväggsregler med hjälp av Azure-portalen
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: cb142e01009efbeaabd5d4e56dbedfe6384c5fc6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798357"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Skapa och hantera Azure Database för PostgreSQL brandväggsregler med hjälp av Azure-portalen
Brandväggsregler på servernivå kan användas för att hantera åtkomst till en Azure Database for PostgreSQL-Server från en angiven IP-adress eller IP-adressintervall.

Virtuella nätverk (VNet)-regler kan också användas för att skydda åtkomsten till din server. Läs mer om [skapa och hantera Virtual Network service slutpunkter och regler med hjälp av Azure-portalen](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom den här guiden, måste du:
- En server [skapa en Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal
1. På sidan PostgreSQL-server under inställningar klickar du **anslutningssäkerhet** att öppna sidan anslutningssäkerhet för Azure Database för PostgreSQL.

   ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Klicka på **Lägg till Min IP** i verktygsfältet. Detta skapar automatiskt en brandväggsregel med offentliga IP-adressen för datorn, som uppfattas av Azure-systemet.

   ![Azure portal – Klicka på Lägg till Min IP-adress](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Kontrollera din IP-adress innan du sparar konfigurationen. I vissa situationer kan IP-adressen som observerats av Azure-portalen som skiljer sig från den IP-adress som används vid åtkomst till internet och Azure-servrar. Därför kan du behöva ändra den första IP- och slut-IP för att göra regeln fungerar som förväntat.
   Använd en sökmotor eller andra online för att kontrollera din egen IP-adress. Till exempel söka efter ”vad är Min IP-adress”.

   ![Sökning i Bing nyheter Min IP-adress](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Lägga till ytterligare adressintervall. Du kan ange en IP-adress eller ett adressintervall i brandväggsreglerna för Azure Database for PostgreSQL. Om du vill begränsa regeln till en enda IP-adress skriver du samma adress i fältet för första IP- och slut-IP. Om du öppnar brandväggen kan administratörer och användare kan få åtkomst till valfri databas på PostgreSQL-servern som de har giltiga autentiseringsuppgifter.

   ![Azure portal – brandväggsregler](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klicka på **spara** i verktygsfältet för att spara den här brandväggsregeln på servernivå. Vänta på bekräftelse att brandväggsreglerna uppdateringen lyckades.

   ![Azure portal – Klicka på Spara](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure att ansluta till din Azure Database for PostgreSQL-server, vara Azure-anslutningar aktiverade. Till exempel att vara värd för ett Azure Web Apps-program eller ett program som körs i en Azure virtuell dator eller för att ansluta från en Azure Data Factory data management gateway. Resurser behöver inte finnas i samma virtuella nätverk (VNet) eller resursgruppen för brandväggsregeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns flera olika metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Du kan också ange den **Tillåt åtkomst till Azure-tjänster** alternativet att **på** på portalen från den **anslutningssäkerhet** rutan och trycker på **spara**. Om anslutningsförsöket inte tillåts kommer begäran inte att nå Azure Database for PostgreSQL-server.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Hantera befintliga brandväggsregler på servernivå via Azure Portal
Upprepa stegen för att hantera brandväggsreglerna.
* Om du vill lägga till den aktuella datorn, klicka på knappen till + **Lägg till Min IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill lägga till ytterligare IP-adresser skriver du regelnamnet, start-IP-adressen och slut-IP-adressen. Klicka på **Spara** för att spara ändringarna.
* Om du vill redigera en befintlig regel klickar du på något av fälten i regeln och redigerar det. Klicka på **Spara** för att spara ändringarna.
* Om du vill ta bort en befintlig regel, klicka på ellipsen [...] och klicka på **ta bort** att ta bort regeln. Klicka på **Spara** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg
- På samma sätt kan du skapa skript till [skapa och hantera Azure Database för PostgreSQL brandväggsregler med hjälp av Azure CLI](howto-manage-firewall-using-cli.md).
- Ytterligare säker åtkomst till servern genom att [skapa och hantera Virtual Network service slutpunkter och regler med hjälp av Azure-portalen](howto-manage-vnet-using-portal.md).
- Hjälp med att ansluta till en Azure Database for PostgreSQL-server finns i [anslutningsbibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).
