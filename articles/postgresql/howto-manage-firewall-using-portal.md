---
title: Hantera brandväggsregler - Azure portal - Azure Database för PostgreSQL - Single Server
description: Skapa och hantera brandväggsregler för Azure Database for PostgreSQL - Single Server med Azure-portalen
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: aeef22bf96221061a444f40e16e33343fafe511c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770313"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Skapa och hantera brandväggsregler för Azure Database for PostgreSQL - Single Server med Azure-portalen
Brandväggsregler på servernivå kan användas för att hantera åtkomst till en Azure-databas för PostgreSQL-server från en angiven IP-adress eller ett visst intervall med IP-adresser.

Virtuella nätverksregler (Virtual Network) kan också användas för att skydda åtkomsten till servern. Läs mer om [hur du skapar och hanterar slutpunkter och regler för tjänsten För virtuella nätverk med hjälp av Azure-portalen](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- En server [Skapa en Azure-databas för PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal
1. Klicka på **Anslutningssäkerhet** under Inställningsrubrik på postgreSQL-serversidan för att öppna säkerhetssidan Anslutning för Azure-databasen för PostgreSQL under rubriken Inställningar.

   ![Azure portal - klicka på Anslutningssäkerhet](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Klicka på **Lägg till min IP i** verktygsfältet. Detta skapar automatiskt en brandväggsregel med den offentliga IP-adressen för din dator, vilket uppfattas av Azure-systemet.

   ![Azure portal - klicka på Lägg till min IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifiera din IP-adress innan du sparar konfigurationen. I vissa situationer skiljer sig IP-adressen som observeras av Azure-portalen från IP-adressen som används vid åtkomst till Internet- och Azure-servrar. Därför kan du behöva ändra Start IP och End IP för att få regeln att fungera som förväntat.
   Använd en sökmotor eller annat online-verktyg för att kontrollera din egen IP-adress. Sök till exempel efter "vad är min IP".

   ![Bing söka efter Vad är min IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Lägg till ytterligare adressintervall. I brandväggsreglerna för Azure-databasen för PostgreSQL kan du ange en enda IP-adress eller ett adressintervall. Om du vill begränsa regeln till en enda IP-adress skriver du samma adress i fältet för Start IP och End IP. Om du öppnar brandväggen kan administratörer, användare och program komma åt alla databaser på PostgreSQL-servern som de har giltiga autentiseringsuppgifter till.

   ![Azure portal - brandväggsregler](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klicka på **Spara** i verktygsfältet om du vill spara brandväggsregeln på servernivå. Vänta på bekräftelsen att uppdateringen av brandväggsreglerna lyckades.

   ![Azure-portalen - klicka på Spara](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure ansluter till din Azure-databas för PostgreSQL-server måste Azure-anslutningar vara aktiverade. Om du till exempel vill vara värd för ett Azure Web Apps-program eller ett program som körs i en Virtuell Azure-dator eller för att ansluta från en Azure Data Factory-datahanteringsgateway. Resurserna behöver inte finnas i samma virtuella nätverk (VNet) eller resursgrupp för brandväggsregeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns ett par metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Du kan också ange alternativet **Tillåt åtkomst till Azure-tjänster** till **ON** i portalen från **säkerhetsfönstret Anslutning** och trycka på **Spara**. Om anslutningsförsöket inte är tillåtet når begäran inte Azure-databasen för PostgreSQL-servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Hantera befintliga brandväggsregler på servernivå via Azure Portal
Upprepa stegen för att hantera brandväggsreglerna.
* Om du vill lägga till den aktuella datorn klickar du på knappen till + **Lägg till min IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill lägga till ytterligare IP-adresser skriver du regelnamnet, start-IP-adressen och slut-IP-adressen. Klicka på **Spara** för att spara ändringarna.
* Om du vill redigera en befintlig regel klickar du på något av fälten i regeln och redigerar det. Klicka på **Spara** för att spara ändringarna.
* Om du vill ta bort en befintlig regel klickar du på ellipsen [...] och klickar på **Ta bort** för att ta bort regeln. Klicka på **Spara** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg
- På samma sätt kan du skript för att [skapa och hantera Azure Database för PostgreSQL-brandväggsregler med Azure CLI](howto-manage-firewall-using-cli.md).
- Ytterligare säker åtkomst till servern genom [att skapa och hantera slutpunkter och regler för tjänsten För virtuella nätverk med hjälp av Azure-portalen](howto-manage-vnet-using-portal.md).
- Mer information om hur du ansluter till en Azure-databas för PostgreSQL-server finns i [Anslutningsbibliotek för Azure Database för PostgreSQL](concepts-connection-libraries.md).
