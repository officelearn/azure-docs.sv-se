---
title: "Skapa och hantera Azure-databas för MySQL brandväggsregler med hjälp av Azure portal | Microsoft Docs"
description: "Skapa och hantera Azure-databas för MySQL brandväggsregler med hjälp av Azure portal"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 0604b29fcd9849545886a783ae5bbb2cbb72f2ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure-databas för MySQL brandväggsregler med hjälp av Azure portal
Brandväggsregler på servernivå kan administratörer få åtkomst till en Azure-databas för MySQL-Server från en angiven IP-adress eller ett intervall med IP-adresser. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

1. I bladet MySQL-servern under inställningar rubrik, klickar du på **anslutningssäkerhet** att öppna bladet anslutningssäkerhet för Azure-databas för MySQL.

   ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Klicka på **Lägg till Min IP** i verktygsfältet för att skapa en regel med IP-adressen för datorn, som uppfattas som Azure systemet.

   ![Azure portal – Klicka på Lägg till Min IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Kontrollera din IP-adress innan du sparar du konfigurationen. I vissa situationer kan IP-adressen följas av Azure-portalen som skiljer sig från den IP-adress som används vid åtkomst till internet och Azure-servrar. Därför kan du behöva ändra första IP- och sista IP-om du vill att regeln funktionen som förväntat.

   Använd en sökmotor eller andra online för att kontrollera din egen IP-adress (till exempel Sök ”vad är IP-adress”).

   ![Bing för vad som är Min IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Lägg till ytterligare adressintervall. Du kan ange en IP-adress eller ett adressintervall i regler för Azure-databas för MySQL-brandväggen. Om du vill begränsa regeln till en enda IP-adress skriver du samma adress i fälten första IP- och slut-IP. Öppna brandväggen kan administratörer och användare att komma åt alla databaser på servern MySQL som de har giltiga autentiseringsuppgifter.

   ![Azure portal – brandväggsregler ](./media/howto-manage-firewall-using-portal/5-specify-addresses.png)


5. Klicka på **spara** i verktygsfältet för att spara den här brandväggsregeln på servernivå. Vänta på att bekräfta att uppdateringen för brandväggsregler har lyckats.

   ![Azure portal – Klicka på Spara](./media/howto-manage-firewall-using-portal/4-save-firewall-rule.png)

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Hantera befintliga brandväggsregler för servernivå med hjälp av Azure portal
Upprepa stegen för att hantera brandväggsreglerna.
* Om du vill lägga till den aktuella datorn, klickar du på **+ Lägg till Min IP**.
* Om du vill lägga till ytterligare IP-adresser, ange den **REGELNAMN**, **första IP-**, och **sista IP**.
* Om du vill ändra en befintlig regel klickar du på något av fälten i regeln och sedan ändra.
* Om du vill ta bort en befintlig regel, klicka på knappen [...] och klicka sedan på **ta bort**.
* Klicka på **Spara** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg
- Hjälp med att ansluta till en Azure-databas för MySQL-servern finns [anslutningsbibliotek för Azure-databas för MySQL](./concepts-connection-libraries.md)
