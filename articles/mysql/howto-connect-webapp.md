---
title: Ansluta befintliga Azure App Service till Azure-databas för MySQL
description: Instruktioner för hur du ansluter en befintlig Azure App Service korrekt till Azure-databas för MySQL
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ff4a28e2f9a0149016d0e47c24e4665ab2e0500d
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265511"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Ansluta en befintlig Azure App Service till Azure-databas för MySQL-server
Det här avsnittet beskriver hur du ansluter en befintlig Azure App Service till din Azure-databas för MySQL-servern.

## <a name="before-you-begin"></a>Innan du börjar
Logga in på [Azure-portalen](https://portal.azure.com). Skapa en Azure-databas för MySQL-servern. Mer information finns i [skapa Azure-databas för MySQL-servern från portalen](quickstart-create-mysql-server-database-using-azure-portal.md) eller [skapa Azure-databas för MySQL-servern med hjälp av CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Det finns för närvarande två lösningar för att aktivera åtkomst från en Azure App Service till en Azure-databas för MySQL. Båda lösningarna omfattar att konfigurera brandväggsregler på servernivå.

## <a name="solution-1---create-a-firewall-rule-to-allow-all-ips"></a>Lösning 1 - skapa en brandväggsregel för att tillåta alla IP-adresser
Azure-databas för MySQL skyddar åtkomst med hjälp av en brandvägg för att skydda dina data. När du ansluter från en Azure App Service till Azure-databas för MySQL-server, Kom ihåg att utgående IP-adresser för App Service är dynamiska. 

För att säkerställa tillgängligheten för Azure App Service, bör du använda den här lösningen så att alla IP-adresser.

> [!NOTE]
> Microsoft arbetar på en långsiktig lösning för att undvika att tillåta att alla IP-adresser för Azure-tjänster att ansluta till Azure-databas för MySQL.

1. I bladet MySQL-servern under inställningarna rubrik, klickar du på **anslutningssäkerhet** att öppna bladet anslutningssäkerhet för Azure-databas för MySQL.

   ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Ange **REGELNAMN**, **första IP-**, och **sista IP**, och klicka sedan på **spara**.
   - Regelnamnet: Tillåt-alla-IP-adresser
   - Start-IP: 0.0.0.0
   - Avsluta IP: 255.255.255.255

   ![Azure portal – Lägg till alla IP-adresser](./media/howto-connect-webapp/1_2-add-all-ips.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Lösning 2 – Skapa en brandväggsregel som uttryckligen tillåta utgående IP-adresser
Du kan uttryckligen lägga till alla utgående IP-adresser i Azure App Service.

1. På bladet App tjänstegenskaper visa din **utgående IP-adress**.

   ![Azure portal – Visa utgående IP-adresser](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. Lägg till utgående IP-adresser i taget på bladet MySQL-anslutning säkerhet.

   ![Azure portal – Lägg till explicita IP-adresser](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Kom ihåg att **spara** brandväggsreglerna.

Även om Azure App service försöker hålla IP-adresser konstant över tid, finns det fall där IP-adresser kan ändras. T.ex, detta kan inträffa när appen återanvänder eller en skalningsåtgärden inträffar eller när nya datorer läggs till i Azure regionala data centers för att öka kapaciteten. När IP-adresser ändras, uppleva driftstopp appen om den inte längre kan ansluta till MySQL-servern. Kom ihåg detta när du väljer ett av ovanstående lösningar.

## <a name="ssl-configuration"></a>SSL-konfiguration
Azure MySQL-databas har SSL aktiverat som standard. Om ditt program inte är använder SSL för att ansluta till databasen, måste du inaktivera SSL på MySQL-servern. Mer information om hur du konfigurerar SSL finns [med hjälp av SSL med Azure-databas för MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Nästa steg
Mer information om anslutningssträngar finns i [anslutningssträngar](howto-connection-string.md).
