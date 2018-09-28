---
title: Ansluta befintliga Azure App Service till Azure Database for MySQL
description: Instruktioner för hur du ansluter en befintlig Azure App Service korrekt till Azure Database for MySQL
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 09/26/2018
ms.openlocfilehash: 4aecc4941f2181216ea537c0019152ce822ac4b0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408951"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Ansluta en befintlig Azure App Service till Azure Database for MySQL-server
Det här avsnittet beskrivs hur du ansluter en befintlig Azure App Service till din Azure Database for MySQL-server.

## <a name="before-you-begin"></a>Innan du börjar
Logga in på [Azure-portalen](https://portal.azure.com). Skapa en Azure Database for MySQL-server. Mer information finns att [hur du skapar Azure Database for MySQL-server från portalen](quickstart-create-mysql-server-database-using-azure-portal.md) eller [hur du skapar Azure Database for MySQL-server med hjälp av CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Det finns för närvarande två lösningar att aktivera åtkomst från en Azure App Service till en Azure Database för MySQL. Båda lösningarna handla om hur du konfigurerar brandväggsregler på servernivå.

## <a name="solution-1---allow-azure-services"></a>Lösning 1 – ge Azure-tjänster
Azure Database för MySQL ger åtkomstsäkerhet med hjälp av en brandvägg för att skydda dina data. När du ansluter från en Azure App Service till Azure Database for MySQL-server, Kom ihåg att den utgående IP-adresser för App Service är dynamiska. Välja alternativet ”Tillåt åtkomst till Azure-tjänster” kan den app service för att ansluta till MySQL-server.

1. På bladet MySQL server under inställningar klickar du **anslutningssäkerhet** att öppna bladet anslutningssäkerhet för Azure Database för MySQL.

   ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-connect-webapp/1-connection-security.png)

2. Välj **på** i **Tillåt åtkomst till Azure-tjänster**, sedan **spara**.
   ![Azure portal – Tillåt Azure-åtkomst](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Lösning 2 – Skapa en brandväggsregel för att uttryckligen tillåta utgående IP-adresser
Du kan uttryckligen lägga till alla utgående IP-adresser i Azure App Service.

1. På bladet egenskaper för appen att visa dina **utgående IP-adress**.

   ![Azure portal – Visa utgående IP-adresser](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. På bladet MySQL-anslutningen, lägger du till utgående IP-adresser i taget.

   ![Azure portal – Lägg till explicita IP-adresser](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Kom ihåg att **spara** brandväggsreglerna.

Även om Azure App-tjänsten försöker hålla IP-adresser konstant över tid, finns det fall där IP-adresser kan ändras. Exempel: Detta kan inträffa när appen återvinns eller en skalningsåtgärd inträffar eller när nya datorer har lagts till i Azures regionala Datacenter för att öka kapaciteten. När IP-adresserna ändras, gick appen ut för avbrott i den händelse att den inte längre kan ansluta till MySQL-server. Ha detta i åtanke när du väljer något av ovanstående lösningar.

## <a name="ssl-configuration"></a>SSL-konfiguration
Azure Database för MySQL har SSL aktiverat som standard. Om ditt program inte är med SSL för att ansluta till databasen, måste du inaktivera SSL på MySQL-server. Mer information om hur du konfigurerar SSL finns i [med hjälp av SSL med Azure Database for MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Nästa steg
Mer information om anslutningssträngar finns [anslutningssträngar](howto-connection-string.md).
