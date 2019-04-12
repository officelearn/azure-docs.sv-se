---
title: 'Azure-portalen: Skapa en hanterad SQL-instans | Microsoft Docs'
description: Skapa en hanterad SQL-instans, en nätverksmiljö och en virtuell klientdator för åtkomst.
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
ms.date: 04/10/2019
ms.openlocfilehash: d94e00c8a475e29ddd671004b8137ba4e6efd107
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495045"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Snabbstart: Skapa en hanterad Azure SQL Database-instans

I den här snabbstarten går vi igenom hur du skapar en [hanterad instans](sql-database-managed-instance.md) av Azure SQL Database i Azure-portalen.

> [!IMPORTANT]
> Begränsningar, finns i [regioner som stöds](sql-database-managed-instance-resource-limits.md#supported-regions) och [stöds prenumerationstyper](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Skapa en hanterad instans

I följande steg visas hur du skapar en hanterad instans.

1. Välj **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Leta upp **hanterad instans** och välj sedan **Azure SQL Managed Instance**.
3. Välj **Skapa**.

   ![Skapa en hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Fyll i formuläret **Hanterad SQL-instans** med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Prenumeration** | Din prenumeration | En prenumeration där du har behörighet att skapa nya resurser |
   |**Namn på hanterad instans**|Valfritt giltigt namn|Giltiga namn finns i [namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Administratörsinloggning för hanterad instans**|Giltigt användarnamn|Giltiga namn finns i [namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Använd inte ”serveradmin” eftersom det är en reserverad servernivåroll.|
   |**Lösenord**|Valfritt giltigt lösenord|Lösenordet måste vara minst 16 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Tidszon**|Tidszonen för din hanterade instans|Mer information finns i [tidszoner](sql-database-managed-instance-timezone.md)|
   |**Sortering**|Den sortering som du vill använda för din hanterade instans|Om du migrerar databaser från SQL Server kontrollerar du källans sortering med hjälp av `SELECT SERVERPROPERTY(N'Collation')` och det värdet. Mer information om sorteringar finns i [sorteringar på servernivå](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Plats**|Den plats där du vill skapa den hanterade instansen|För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).|
   |**Virtuellt nätverk**|Välj antingen **Skapa nytt virtuellt nätverk** eller ett giltigt virtuellt nätverk och undernät.| Om en nätverk/undernät är inte tillgänglig är det måste vara [ändras för att uppfylla nätverkskraven](sql-database-managed-instance-configure-vnet-subnet.md) innan du väljer den som mål för den nya hantera instansen. Information om kraven för att konfigurera nätverksmiljön för en hanterad instans finns i avsnittet om att [konfigurera ett virtuellt nätverk för en hanterad instans](sql-database-managed-instance-connectivity-architecture.md). |
   |**Anslutningstyp**|Välj mellan Proxy och omdirigerings-anslutningstyp|Mer information om anslutningstyper finns i [Azure SQL-anslutningsprincip](sql-database-connectivity-architecture.md#connection-policy).|
   |**Resursgrupp**|En ny eller befintlig resursgrupp|Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|

   ![formulär för hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Om du vill använda den hanterade instansen som en sekundär redundansgrupp för instansen väljer du utcheckningen och anger den hanterade DnsAzurePartner-instansen. Den här funktionen är en förhandsversion och visas inte i den tillhörande skärmbilden.
6. Välj **Prisnivå** för att beräkna storlek på instanser och lagringsresurser samt granska alternativen för prisnivå. Standardvärdet är prisnivån Generell användning med 32 GB minne och 16 virtuella kärnor.
7. Använd skjutreglagen eller textrutorna för att ange mängden lagringsutrymme och antalet virtuella kärnor.
8. När det är klart väljer du **Verkställ** för att spara ditt val.  
9. Välj **Skapa** för att distribuera den hanterade instansen.
10. Välj ikonen **Meddelanden** för att visa status för distributionen.

    ![distributionsförlopp för hanterad instans](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Välj **Distribution pågår** för att öppna fönstret för den hanterade instansen om du vill fortsätta övervaka förloppet för distributionen.

> [!IMPORTANT]
> För den första instansen i ett undernät tar distributionen vanligtvis mycket längre tid än för efterföljande instanser. Avbryt inte distributionsåtgärden om den tar längre tid än förväntat. Det tar bara några minuter att skapa den andra hanterade instansen i undernätet.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Granska resurser och hämta det fullständigt kvalificerade servernamnet

När distributionen är färdig kan du granska resurserna som har skapats och hämta det fullständigt kvalificerade servernamnet för användning i senare snabbstarter.

1. Öppna resursgruppen för din hanterade instans och visa dess resurser som skapades åt dig i snabbstarten [skapa en hanterad instans](#create-a-managed-instance).

   ![Resurser för hanterad instans](./media/sql-database-managed-instance-get-started/resources.png)

2. Välj routningstabellen för att granska den tabell för användardefinierad väg (UDR) som har skapats.

   ![Routningstabell](./media/sql-database-managed-instance-get-started/route-table.png)

3. I routningstabellen granskar du posterna för routningstrafik från och i den hanterade instansens virtuella nätverk. Om du skapar eller konfigurerar routningstabellen manuellt måste du att se till att skapa de här posterna i routningstabellen.

   ![Posten för MI-undernätet till lokalt](./media/sql-database-managed-instance-get-started/udr.png)

4. Gå tillbaka till resursgruppen och välj nätverkssäkerhetsgruppen för att granska säkerhetsreglerna.

   ![Nätverkssäkerhetsgrupp](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Granska inkommande och utgående säkerhetsregler.

   ![Säkerhetsregler](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Gå tillbaka till resursgruppen och välj din hanterade instans.

   ![Hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. På fliken **Översikt** letar du upp egenskapen **Värd** och kopierar den hanterade instansens fullständigt kvalificerade värdadress för användning i nästa snabbstart.

   ![Värdnamn](./media/sql-database-managed-instance-get-started/host-name.png)

   Namnet ser ut ungefär så här: **ditt_datornamn.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Nästa steg

- Läs om hur du ansluter till en hanterad instans i:
  - En översikt över anslutningsalternativen för olika program finns i [ansluta dina program till en hanterad instans](sql-database-managed-instance-connect-app.md).
  - En snabbstart som visar hur du ansluter till en hanterad instans från en virtuell Azure-dator finns i [Konfigurera en anslutning för en virtuell Azure-dator](sql-database-managed-instance-configure-vm.md).
  - En snabbstart som visar hur du ansluter till en hanterad instans från en lokal klientdator via punkt-till-plats-anslutning finns i [Konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md).
- Om du vill återställa en lokal befintlig SQL Server-databas till en hanterad instans kan du använda [Azure Database Migration Service (DMS) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) till att återställa från en databassäkerhetskopia eller kommandot [T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) till att återställa från en databassäkerhetskopia.
- Information om avancerad övervakning av databasprestanda för hanterad instans med inbyggd felsökningsintelligens finns i artikeln om att [övervaka Azure SQL Database med hjälp av Azure SQL-analys](../azure-monitor/insights/azure-sql.md)
