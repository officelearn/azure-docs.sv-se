---
title: Hantera brandväggsregler – Hyperskala (Citus) - Azure-databas för PostgreSQL
description: Skapa och hantera brandväggsregler för Azure Database for PostgreSQL - Hyperscale (Citus) med Hjälp av Azure-portalen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977547"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Hantera brandväggsregler för Azure Database för PostgreSQL - Hyperskala (Citus)
Brandväggsregler på servernivå kan användas för att hantera åtkomst till en citus-koordinatornod (Hyperscale) från en angiven IP-adress eller ett visst intervall med IP-adresser.

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- En servergrupp [Skapa en Azure-databas för PostgreSQL – Citus-servergrupp (Hyperscale).](quickstart-create-hyperscale-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

> [!NOTE]
> Dessa inställningar är också tillgängliga när du skapar en Azure-databas för PostgreSQL - Citus-servergrupp (Hyperscale). Klicka på **Offentlig slutpunkt**under fliken **Nätverk** .
> ![Fliken Azure-portal – fliken Nätverk](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Öppna brandväggsreglerna under rubriken Säkerhet **på** servergruppssidan För PostgreSQL.

   ![Azure portal - klicka på Nätverk](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Klicka på **Lägg till klient-IP**, antingen i verktygsfältet (alternativ A nedan) eller i länken (alternativ B). Hursomhelst automatiskt skapar en brandväggsregel med den offentliga IP-adressen för din dator, som uppfattas av Azure-systemet.

   ![Azure portal - klicka på Lägg till klient-IP](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Alternativt klickar du på **+Lägg till 0.0.0.0 - 255.255.255.255** (till höger om alternativ B) tillåter inte bara din IP, men hela Internet för att komma åt samordnaren nodens port 5432. I det här fallet måste klienter fortfarande logga in med rätt användarnamn och lösenord för att använda klustret. Vi rekommenderar dock att du tillåter global åtkomst under korta tidsperioder och endast för databaser som inte är produktionsdatabaser.

3. Verifiera din IP-adress innan du sparar konfigurationen. I vissa situationer skiljer sig IP-adressen som observeras av Azure-portalen från IP-adressen som används vid åtkomst till Internet- och Azure-servrar. Därför kan du behöva ändra Start IP och End IP för att få regeln att fungera som förväntat.
   Använd en sökmotor eller annat online-verktyg för att kontrollera din egen IP-adress. Sök till exempel efter "vad är min IP".

   ![Bing söka efter Vad är min IP](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Lägg till ytterligare adressintervall. I brandväggsreglerna kan du ange en enda IP-adress eller ett adressintervall. Om du vill begränsa regeln till en enda IP-adress skriver du samma adress i fältet för Start IP och End IP. Om du öppnar brandväggen kan administratörer, användare och program komma åt koordinatornsnoden i port 5432.

5. Klicka på **Spara** i verktygsfältet om du vill spara brandväggsregeln på servernivå. Vänta på bekräftelsen att uppdateringen av brandväggsreglerna lyckades.

## <a name="connecting-from-azure"></a>Ansluta från Azure

Det finns ett enkelt sätt att bevilja hyperskala databasåtkomst till program som finns på Azure (till exempel ett Azure Web Apps-program eller de som körs i en Virtuell Azure-dator). Ange helt enkelt **alternativet Tillåt Azure-tjänster och resurser för att komma åt det här servergruppsalternativet** till **Ja** i portalen från **nätverksfönstret** och tryck på **Spara**.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Hantera befintliga brandväggsregler på servernivå via Azure Portal
Upprepa stegen för att hantera brandväggsreglerna.
* Om du vill lägga till den aktuella datorn klickar du på knappen till + **Lägg till klient-IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill lägga till ytterligare IP-adresser skriver du regelnamnet, start-IP-adressen och slut-IP-adressen. Klicka på **Spara** för att spara ändringarna.
* Om du vill redigera en befintlig regel klickar du på något av fälten i regeln och redigerar det. Klicka på **Spara** för att spara ändringarna.
* Om du vill ta bort en befintlig regel klickar du på ellipsen [...] och klickar på **Ta bort** för att ta bort regeln. Klicka på **Spara** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Koncept för brandväggsregler](concepts-hyperscale-firewall-rules.md), inklusive hur du felsöker anslutningsproblem.
