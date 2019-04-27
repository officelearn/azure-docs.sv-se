---
title: Skapa och hantera MariaDB brandväggsregler i Azure Database for MariaDB
description: Skapa och hantera Azure-databas för MariaDB brandväggsregler med hjälp av Azure-portalen
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: e9ab243692f5a4a1ec7de25774f5bad867698fc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746412"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure-databas för MariaDB brandväggsregler med hjälp av Azure portal
Brandväggsregler på servernivå kan användas för att hantera åtkomst till en Azure Database for MariaDB-Server från en angiven IP-adress eller ett intervall med IP-adresser.

Virtuella nätverk (VNet)-regler kan också användas för att skydda åtkomsten till din server. Läs mer om [skapa och hantera Virtual Network service slutpunkter och regler med hjälp av Azure-portalen](howto-manage-vnet-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

1. På sidan MariaDB, under inställningar klickar du **anslutningssäkerhet** att öppna sidan anslutningssäkerhet för Azure Database for MariaDB.

   ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Klicka på **Lägg till Min IP** i verktygsfältet. Detta skapar automatiskt en brandväggsregel med offentliga IP-adressen för datorn, som uppfattas av Azure-systemet.

   ![Azure portal – Klicka på Lägg till Min IP-adress](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Kontrollera din IP-adress innan du sparar konfigurationen. I vissa situationer kan IP-adressen som observerats av Azure-portalen som skiljer sig från den IP-adress som används vid åtkomst till internet och Azure-servrar. Därför kan du behöva ändra den första IP- och slut-IP för att göra regeln fungerar som förväntat.

   Använd en sökmotor eller andra online för att kontrollera din egen IP-adress. Sök till exempel ”vad är Min IP-adress”.

4. Lägga till ytterligare adressintervall. Du kan ange en IP-adress eller ett adressintervall i brandväggsreglerna för Azure Database for MariaDB. Om du vill begränsa regeln till en enda IP-adress skriver du samma adress i fälten första IP- och slut-IP. Om du öppnar brandväggen kan administratörer, användare och program till valfri databas på servern MariaDB som de har giltiga autentiseringsuppgifter.

   ![Azure portal – brandväggsregler](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Klicka på **spara** i verktygsfältet för att spara den här brandväggsregeln på servernivå. Vänta för att bekräfta att uppdateringen till brandväggsreglerna har lyckats.

   ![Azure portal – Klicka på Spara](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure att ansluta till din Azure Database for MariaDB-server, vara Azure-anslutningar aktiverade. Till exempel att vara värd för ett Azure Web Apps-program eller ett program som körs i en Azure virtuell dator eller för att ansluta från en Azure Data Factory data management gateway. Resurser behöver inte finnas i samma virtuella nätverk (VNet) eller resursgruppen för brandväggsregeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns flera olika metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Du kan också ange den **Tillåt åtkomst till Azure-tjänster** alternativet att **på** på portalen från den **anslutningssäkerhet** och klicka på **spara**. Om anslutningsförsöket inte tillåts kommer begäran inte att nå Azure Database for MariaDB-server.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Hantera befintliga brandväggsregler i Azure portal
Upprepa stegen för att hantera brandväggsreglerna.
* Lägg till den aktuella datorn, klicka på **+ Lägg till Min IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill lägga till ytterligare IP-adresser, ange den **REGELNAMN**, **första IP-**, och **slut-IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill ändra en befintlig regel klickar du på något av fälten i regeln och sedan ändra. Klicka på **Spara** för att spara ändringarna.
* Om du vill ta bort en befintlig regel, klicka på ellipsen [...] och klicka sedan på **ta bort**. Klicka på **Spara** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg
 - På samma sätt kan du skapa skript till [skapa och hantera Azure-databas för MariaDB brandväggsregler med hjälp av Azure CLI](howto-manage-firewall-cli.md).
 - Ytterligare säker åtkomst till servern genom att [skapa och hantera Virtual Network service slutpunkter och regler med hjälp av Azure-portalen](howto-manage-vnet-portal.md).