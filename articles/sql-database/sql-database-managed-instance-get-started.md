---
title: 'Azure-portalen: Skapa en hanterad SQL Database-instans | Microsoft Docs'
description: Skapa en SQL-hanterad databasinstans, nätverksmiljö och Virtuella klientdatorer för åtkomst.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 09ab4cd44515d90df0e36e2775f50ac33955fe75
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466267"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Snabbstart: Skapa en hanterad Azure SQL Database-instans

Den här snabbstarten får du veta hur du skapar en Azure SQL Database [hanterad instans](sql-database-managed-instance.md) i Azure-portalen.

> [!IMPORTANT]
> Begränsningar, finns i [regioner som stöds](sql-database-managed-instance-resource-limits.md#supported-regions) och [stöds prenumerationstyper](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/). 

## <a name="create-a-managed-instance"></a>Skapa en hanterad instans

I följande steg visas hur du skapar en hanterad instans.

1. Välj **skapa en resurs** i det övre vänstra hörnet i Azure Portal.
2. Leta upp **hanterad instans**, och välj sedan **Azure SQL Managed Instance**.
3. Välj **Skapa**.

   ![Skapa en hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Fyll i **SQL-hanterad instans** formuläret med den begärda informationen med hjälp av informationen i följande tabell.

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Prenumeration** | Din prenumeration. | En prenumeration som ger dig behörighet att skapa nya resurser. |
   |**Namn på hanterad instans**|Valfritt giltigt namn.|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Administratörsinloggning för hanterad instans**|Valfritt giltigt användarnamn.|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Använd inte ”serveradmin” eftersom det är en reserverad på servernivå roll.|
   |**Lösenord**|Valfritt giltigt lösenord.|Lösenordet måste vara minst 16 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Tidszon**|Tidszon för din hanterade instans.|Mer information finns i [tidszoner](sql-database-managed-instance-timezone.md).|
   |**Sortering**|Den sortering som du vill använda för din hanterade instans.|Om du migrerar databaser från SQL Server, kontrollera sorteringen källan med hjälp av `SELECT SERVERPROPERTY(N'Collation')` och det värdet. Information om sorteringar finns i [ange eller ändra serversorteringen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Plats**|Den plats där du vill skapa den hanterade instansen.|För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).|
   |**Virtuellt nätverk**|Välj antingen **Skapa nytt virtuellt nätverk** eller ett giltigt virtuellt nätverk och undernät.| Om ett nätverk eller undernät är inte tillgänglig, måste den vara [ändras för att uppfylla nätverkskraven](sql-database-managed-instance-configure-vnet-subnet.md) innan du väljer den som mål för den nya hantera instansen. Information om kraven för att konfigurera nätverksmiljön för en hanterad instans finns i [konfigurera ett virtuellt nätverk för den hanterade instansen](sql-database-managed-instance-connectivity-architecture.md). |
   |**Aktivera offentlig slutpunkt**   |Markera det här alternativet att aktivera offentlig slutpunkt   |För den hanterade instansen ska nås via den offentliga data-slutpunkten, **aktivera offentlig slutpunkt** måste kontrolleras.| 
   |**Tillåta åtkomst från**   |Välj något av alternativen: <ul> <li>**Azure-tjänster**</li> <li>**Internet**</li> <li>**Ingen åtkomst**</li></ul>   |Portalen kan konfigurera säkerhetsgrupp med offentlig slutpunkt. </br> </br> Beroende på ditt scenario kan välja något av följande alternativ: </br> <ul> <li>Azure-tjänster – rekommenderas när du ansluter från Power BI eller annan tjänst för flera innehavare. </li> <li> Internet - användning för testning när du vill skapa snabbt en hanterad instans. Det rekommenderas inte för användning i produktionsmiljöer. </li> <li> Ingen åtkomst – det här alternativet skapar en neka-säkerhetsregel. Du behöver ändra den här regeln för att kunna göra hanterad instans åtkomliga via offentlig slutpunkt. </li> </ul> </br> Mer information om offentlig slutpunktssäkerhet finns i [med Azure SQL Database-hanterad instans på ett säkert sätt med offentliga slutpunkten](sql-database-managed-instance-public-endpoint-securely.md).|
   |**Anslutningstyp**|Välj mellan en Proxy och en omdirigerings-anslutningstyp.|Mer information om anslutningstyper finns i [princip för Azure SQL Database](sql-database-connectivity-architecture.md#connection-policy).|
   |**Resursgrupp**|En ny eller befintlig resursgrupp.|Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|

   ![hanterad instans-formulär](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Om du vill använda den hanterade instansen som en sekundär redundansgrupp för instansen väljer du utcheckningen och anger den hanterade DnsAzurePartner-instansen. Den här funktionen är en förhandsversion och är inte visas i följande skärmbild.
6. Välj **prisnivå** till resurser för beräkning och lagring av storlek och granska alternativen för prisnivå. Standardvärdet är prisnivån Generell användning med 32 GB minne och 16 virtuella kärnor.
7. Använd skjutreglagen eller textrutorna för att ange mängden lagringsutrymme och antalet virtuella kärnor.
8. När du är klar väljer du **tillämpa** att spara ditt val. 
9. Välj **Skapa** för att distribuera den hanterade instansen.
10. Välj den **meddelanden** ikon för att visa status för distributionen.

    ![hanterad instans förloppsindikator](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Välj **Distribution pågår** för att öppna fönstret för den hanterade instansen om du vill fortsätta övervaka förloppet för distributionen. 

> [!IMPORTANT]
> För den första instansen i ett undernät tar distributionen vanligtvis mycket längre tid än för efterföljande instanser. Avbryt inte distributionsåtgärden om den tar längre tid än förväntat. Det tar bara ett par minuter att skapa den andra hantera instansen i undernätet.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Granska resurser och hämta det fullständigt kvalificerade servernamnet

När distributionen har slutförts granskar de resurser som har skapats och hämta det fullständigt kvalificerade servernamnet för användning i senare snabbstarter.

1. Öppna resursgruppen för din hanterade instans. Visa dess resurser som har skapats för dig i den [skapa en hanterad instans](#create-a-managed-instance) Snabbstart.

   ![Resurser för hanterad instans](./media/sql-database-managed-instance-get-started/resources.png)

2. Välj routningstabellen med en användardefinierad väg (UDR) tabellen som har skapats för dig.

   ![Routningstabell](./media/sql-database-managed-instance-get-started/route-table.png)

3. I routningstabellen granskar du posterna för routningstrafik från och i den hanterade instansens virtuella nätverk. Om du skapar eller konfigurerar din routningstabellen manuellt, måste du att se till att skapa de här posterna i routningstabellen.

   ![Post för ett undernät för hanterad instans till lokal](./media/sql-database-managed-instance-get-started/udr.png)

4. Gå tillbaka till resursgruppen och välj nätverkssäkerhetsgrupp att granska säkerhetsreglerna.

   ![Nätverkssäkerhetsgrupp](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Granska inkommande och utgående säkerhetsregler. Om du har konfigurerat offentliga slutpunkter för din hanterade instans kan du läsa artikeln [konfigurera offentliga slutpunkten](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) för mer information.

   ![Säkerhetsregler](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Gå tillbaka till resursgruppen och välj din hanterade instans.

   ![Hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. På den **översikt** fliken, leta upp den **värden** egenskapen. Kopiera den fullständiga värd-adressen för den hanterade instansen för användning i nästa Snabbstart.

   ![Värdnamn](./media/sql-database-managed-instance-get-started/host-name.png)

   Namnet ser ut ungefär så här: **ditt_datornamn.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du ansluter till en hanterad instans:
  - En översikt över anslutningsalternativ för program, se [ansluta dina program till en hanterad instans](sql-database-managed-instance-connect-app.md).
  - En Snabbstart som visar hur du ansluter till en hanterad instans från Azure-datorer, se [konfigurera en virtuell dator i Azure anslutning](sql-database-managed-instance-configure-vm.md).
  - En Snabbstart som visar hur du ansluter till en hanterad instans från en lokal klientdator med hjälp av en punkt-till-plats-anslutning, se [konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md).
- Att återställa en befintlig SQL Server-databas från en lokal plats till en hanterad instans: 
    - Använd den [Azure Database Migration Service (DMS) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) att återställa från en databassäkerhetskopia. 
    - Använd den [T-SQL RESTORE-kommandot](sql-database-managed-instance-get-started-restore.md) att återställa från en databassäkerhetskopia.
- Avancerade övervakning av databasprestanda för hanterad instans med inbyggd intelligens som felsökning finns i [övervaka Azure SQL-databasen med hjälp av Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
