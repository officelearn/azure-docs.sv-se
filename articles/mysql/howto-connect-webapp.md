---
title: Ansluta till Azure App Service - Azure Database för MySQL
description: Instruktioner för hur du ansluter en befintlig Azure App-tjänst till Azure Database för MySQL på rätt sätt
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ba75daa47ca1f77cd5828d13877238a64deeeb41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062465"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Ansluta en befintlig Azure App-tjänst till Azure Database för MySQL-server
I det här avsnittet beskrivs hur du ansluter en befintlig Azure App-tjänst till din Azure-databas för MySQL-server.

## <a name="before-you-begin"></a>Innan du börjar
Logga in på [Azure-portalen](https://portal.azure.com). Skapa en Azure-databas för MySQL-server. Mer information finns i [Så här skapar du Azure Database för MySQL-server från Portal](quickstart-create-mysql-server-database-using-azure-portal.md) eller Så här skapar du Azure Database för [MySQL-server med CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

För närvarande finns det två lösningar för att aktivera åtkomst från en Azure App Service till en Azure-databas för MySQL. Båda lösningarna innebär att ställa in brandväggsregler på servernivå.

## <a name="solution-1---allow-azure-services"></a>Lösning 1 - Tillåt Azure-tjänster
Azure Database for MySQL ger åtkomstsäkerhet med hjälp av en brandvägg för att skydda dina data. När du ansluter från en Azure App Service till Azure Database för MySQL-server bör du tänka på att de utgående IP-adresser för App Service är dynamiska till sin natur. Om du väljer alternativet "Tillåt åtkomst till Azure-tjänster" kan apptjänsten ansluta till MySQL-servern.

1. Klicka på **Anslutningssäkerhet** under rubriken Inställningar på MySQL-servern för att öppna bladet Anslutningssäkerhet för Azure-databas för MySQL under rubriken Inställningar.

   ![Azure portal - klicka på Anslutningssäkerhet](./media/howto-connect-webapp/1-connection-security.png)

2. Välj **PÅ** i **Tillåt åtkomst till Azure-tjänster** **och**spara sedan .
   ![Azure-portal - Tillåt Azure-åtkomst](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Lösning 2 - Skapa en brandväggsregel för att uttryckligen tillåta utgående IPs
Du kan uttryckligen lägga till alla utgående IPs för din Azure App Service.

1. Visa din **UTGÅENDE IP-ADRESS**på bladet Egenskaper för App-tjänst.

   ![Azure portal - Visa utgående IPs](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. Lägg till utgående IPs en efter en på säkerhetsbladet MySQL-anslutning.

   ![Azure portal - Lägg till explicita IPs](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Kom ihåg att **spara** brandväggsreglerna.

Även om Azure App-tjänsten försöker hålla IP-adresser konstanta över tid, finns det fall där IP-adresserna kan ändras. Detta kan till exempel inträffa när appen återvinns eller en skalningsåtgärd inträffar, eller när nya datorer läggs till i Azures regionala datacenter för att öka kapaciteten. När IP-adresserna ändras kan appen uppleva driftstopp i händelse av att den inte längre kan ansluta till MySQL-servern. Tänk på detta när du väljer en av de tidigare lösningarna.

## <a name="ssl-configuration"></a>SSL-konfiguration
Azure Database för MySQL har SSL aktiverat som standard. Om ditt program inte använder SSL för att ansluta till databasen måste du inaktivera SSL på MySQL-servern. Mer information om hur du konfigurerar SSL finns i [Använda SSL med Azure Database för MySQL](howto-configure-ssl.md).

### <a name="django-pymysql"></a>Django (PyMysql)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
Mer information om anslutningssträngar finns i [Anslutningssträngar](howto-connection-string.md).
