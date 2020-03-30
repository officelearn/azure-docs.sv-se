---
title: Hantera brandväggsregler - Azure portal - Azure Database för MySQL
description: Skapa och hantera Azure Database for MySQL-brandväggsregler med Hjälp av Azure-portalen
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: edd6403ed3d7607eb96bc7c6a603c3fef8a4f99e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063554"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure Database for MySQL-brandväggsregler med hjälp av Azure-portalen
Brandväggsregler på servernivå kan användas för att hantera åtkomst till en Azure-databas för MySQL-server från en angiven IP-adress eller ett intervall med IP-adresser. 

Virtuella nätverksregler (Virtual Network) kan också användas för att skydda åtkomsten till servern. Läs mer om [hur du skapar och hanterar slutpunkter och regler för tjänsten För virtuella nätverk med hjälp av Azure-portalen](howto-manage-vnet-using-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

1. Klicka på **Anslutningssäkerhet** under Rubriken Inställningar på sidan MySQL-server för att öppna sidan Anslutningssäkerhet för Azure-databasen för MySQL.

   ![Azure portal - klicka på Anslutningssäkerhet](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Klicka på **Lägg till min IP i** verktygsfältet. Detta skapar automatiskt en brandväggsregel med den offentliga IP-adressen för din dator, vilket uppfattas av Azure-systemet.

   ![Azure portal - klicka på Lägg till min IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifiera din IP-adress innan du sparar konfigurationen. I vissa situationer skiljer sig IP-adressen som observeras av Azure-portalen från IP-adressen som används vid åtkomst till Internet- och Azure-servrar. Därför kan du behöva ändra Start IP och End IP för att få regeln att fungera som förväntat.

   Använd en sökmotor eller annat online-verktyg för att kontrollera din egen IP-adress. Sök till exempel på "vad är min IP-adress".

4. Lägg till ytterligare adressintervall. I brandväggsreglerna för Azure Database for MySQL kan du ange en enda IP-adress eller ett adressintervall. Om du vill begränsa regeln till en enda IP-adress skriver du samma adress i fälten Start IP och End IP. Om du öppnar brandväggen kan administratörer, användare och program komma åt alla databaser på MySQL-servern som de har giltiga autentiseringsuppgifter till.

   ![Azure portal - brandväggsregler](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klicka på **Spara** i verktygsfältet om du vill spara brandväggsregeln på servernivå. Vänta tills bekräftelsen på att uppdateringen av brandväggsreglerna har framgångsrikt.

   ![Azure-portalen - klicka på Spara](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure ansluter till din Azure-databas för MySQL-server måste Azure-anslutningar vara aktiverade. Om du till exempel vill vara värd för ett Azure Web Apps-program eller ett program som körs i en Virtuell Azure-dator eller för att ansluta från en Azure Data Factory-datahanteringsgateway. Resurserna behöver inte finnas i samma virtuella nätverk (VNet) eller resursgrupp för brandväggsregeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns ett par metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Du kan också ange alternativet **Tillåt åtkomst till Azure-tjänster** till **ON** i portalen från **säkerhetsfönstret Anslutning** och trycka på **Spara**. Om anslutningsförsöket inte är tillåtet når begäran inte Azure-databasen för MySQL-servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Hantera befintliga brandväggsregler på servernivå med hjälp av Azure-portalen
Upprepa stegen för att hantera brandväggsreglerna.
* Om du vill lägga till den aktuella datorn klickar du på **+ Lägg till min IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill lägga till ytterligare IP-adresser skriver du in **RULE NAME**, **START IP**och END **IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill ändra en befintlig regel klickar du på något av fälten i regeln och ändrar sedan. Klicka på **Spara** för att spara ändringarna.
* Om du vill ta bort en befintlig regel klickar du på ellipsen [...]och sedan **på Ta bort**. Klicka på **Spara** för att spara ändringarna.


## <a name="next-steps"></a>Nästa steg
     Similarly, you can script to [Create and manage Azure Database for MySQL firewall rules using Azure CLI](howto-manage-firewall-using-cli.md).
     Further secure access to your server by [creating and managing Virtual Network service endpoints and rules using the Azure portal](howto-manage-vnet-using-portal.md).
        For help in connecting to an Azure     atabase for MySQL server, see [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        