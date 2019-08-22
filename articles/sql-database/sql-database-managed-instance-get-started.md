---
title: 'Azure-portalen: Skapa en SQL Database Hanterad instans | Microsoft Docs'
description: Skapa en SQL Database Hanterad instans, nätverks miljö och virtuell klient dator för åtkomst.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: cacd67e26b13df8ef456ac8f1391e4396f5bdd96
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873883"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Snabbstart: Skapa en hanterad Azure SQL Database-instans

Den här snabb starten vägleder dig genom hur du skapar en Azure SQL Database [hanterad instans](sql-database-managed-instance.md) i Azure Portal.

> [!IMPORTANT]
> För begränsningar, se [regioner som stöds](sql-database-managed-instance-resource-limits.md#supported-regions) och [prenumerations typer som stöds](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Skapa en hanterad instans

I följande steg visas hur du skapar en hanterad instans.

1. Välj **Azure SQL** i den vänstra menyn i Azure Portal. Om Azure SQL inte finns i listan väljer du **alla tjänster**och skriver sedan *Azure SQL* i sökrutan.
2. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa mer information om Azure SQL Database Hanterad instans genom att välja **Visa information** på panelen **hanterade instanser** .
3. Välj **skapa**:

   ![Skapa en hanterad instans](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Fyll i formuläret **SQL-hanterad instans** med den begärda informationen med hjälp av informationen i följande tabell.

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Prenumeration** | Din prenumeration. | En prenumeration som ger dig behörighet att skapa nya resurser. |
   |**Namn på hanterad instans**|Ett giltigt namn.|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Administratörsinloggning för hanterad instans**|Ett giltigt användar namn.|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Använd inte "ServerAdmin" eftersom det är en reserverad server nivå roll.|
   |**Lösenord**|Ett giltigt lösen ord.|Lösenordet måste vara minst 16 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Tidszon**|Tids zonen som ska observeras av din hanterade instans.|Mer information finns i [tids zoner](sql-database-managed-instance-timezone.md).|
   |**Sortering**|Den sortering som du vill använda för din hanterade instans.|Om du migrerar databaser från SQL Server kontrollerar du käll sorteringen genom att `SELECT SERVERPROPERTY(N'Collation')` använda och använda det värdet. Information om sorteringar finns i [Ange eller ändra Server sorteringen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Location**|Den plats där du vill skapa den hanterade instansen.|För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).|
   |**Virtuellt nätverk**|Välj antingen **Skapa nytt virtuellt nätverk** eller ett giltigt virtuellt nätverk och undernät.| Om ett nätverk eller undernät inte är tillgängligt, måste det [ändras för att uppfylla nätverks kraven](sql-database-managed-instance-configure-vnet-subnet.md) innan du väljer det som mål för den nya hanterade instansen. Information om kraven för att konfigurera nätverks miljön för en hanterad instans finns i [Konfigurera ett virtuellt nätverk för en hanterad instans](sql-database-managed-instance-connectivity-architecture.md). |
   |**Aktivera offentlig slut punkt**   |Markera det här alternativet om du vill aktivera offentlig slut punkt   |För att hanterad instans ska vara tillgänglig via den offentliga data slut punkten måste du kontrol lera att den **offentliga slut punkten** är markerad.| 
   |**Tillåt åtkomst från**   |Välj ett av alternativen: <ul> <li>**Azure-tjänster**</li> <li>**E**</li> <li>**Ingen åtkomst**</li></ul>   |Med Portal upplevelsen kan du konfigurera säkerhets grupper med offentlig slut punkt. </br> </br> Välj något av följande alternativ baserat på ditt scenario: </br> <ul> <li>Azure-tjänster – rekommenderas när du ansluter från Power BI eller någon annan tjänst för flera innehavare. </li> <li> Internet-Använd i test syfte när du snabbt vill skapa en hanterad instans. Det rekommenderas inte för användning i produktions miljöer. </li> <li> Ingen åtkomst – det här alternativet skapar en neka-säkerhetsregel. Du måste ändra den här regeln för att kunna göra hanterad instans tillgänglig via en offentlig slut punkt. </li> </ul> </br> Mer information om säkerhet för offentliga slut punkter finns i [använda Azure SQL Database Hanterad instans på ett säkert sätt med en offentlig slut punkt](sql-database-managed-instance-public-endpoint-securely.md).|
   |**Anslutnings typ**|Välj mellan en proxy och en Anslutnings typ för omdirigering.|Mer information om anslutnings typer finns i [Azure SQL Database anslutnings princip](sql-database-connectivity-architecture.md#connection-policy).|
   |**Resursgrupp**|En ny eller befintlig resursgrupp.|Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|

   ![Formulär för hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Om du vill använda den hanterade instansen som en sekundär redundansgrupp för instansen väljer du utcheckningen och anger den hanterade DnsAzurePartner-instansen. Den här funktionen är i för hands version och visas inte i följande skärm bild.
6. Välj **pris nivå** för att ändra storlek på beräknings-och lagrings resurser och granska alternativ för pris nivå. Standardvärdet är prisnivån Generell användning med 32 GB minne och 16 virtuella kärnor.
7. Använd skjutreglagen eller textrutorna för att ange mängden lagringsutrymme och antalet virtuella kärnor.
8. När du är klar väljer du **tillämpa** för att spara ditt val. 
9. Välj **Skapa** för att distribuera den hanterade instansen.
10. Välj ikonen **meddelanden** om du vill visa status för distributionen.

    ![Distributions förlopp för hanterad instans](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Välj **Distribution pågår** för att öppna fönstret för den hanterade instansen om du vill fortsätta övervaka förloppet för distributionen. 

> [!IMPORTANT]
> För den första instansen i ett undernät tar distributionen vanligtvis mycket längre tid än för efterföljande instanser. Avbryt inte distributionsåtgärden om den tar längre tid än förväntat.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Granska resurser och hämta det fullständigt kvalificerade servernamnet

När distributionen har slutförts granskar du de resurser som har skapats och hämtat det fullständigt kvalificerade Server namnet för användning i senare snabb starter.

1. Öppna resurs gruppen för din hanterade instans. Visa dess resurser som har skapats för dig i snabb starten för att [skapa en hanterad instans](#create-a-managed-instance) .

   ![Hanterade instansresurser](./media/sql-database-managed-instance-get-started/resources.png)

2. Välj routningstabellen för att granska den användardefinierade Route-tabellen (UDR) som skapades åt dig.

   ![Routningstabell](./media/sql-database-managed-instance-get-started/route-table.png)

3. I routningstabellen granskar du posterna för routningstrafik från och i den hanterade instansens virtuella nätverk. Om du skapar eller konfigurerar routningstabellen manuellt måste du vara säker på att du skapar dessa poster i routningstabellen.

   ![Post för ett undernät för hanterad instans till lokalt](./media/sql-database-managed-instance-get-started/udr.png)

4. Gå tillbaka till resurs gruppen och välj nätverks säkerhets gruppen för att granska säkerhets reglerna.

   ![Nätverkssäkerhetsgrupp](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Granska inkommande och utgående säkerhetsregler. Om du har konfigurerat offentliga slut punkter för din hanterade instans kan du läsa artikeln [Konfigurera en offentlig slut punkt](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) för mer information.

   ![Säkerhetsregler](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Gå tillbaka till resurs gruppen och välj din hanterade instans.

   ![Hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Leta upp **värd** egenskapen på fliken **Översikt** . Kopiera den fullständigt kvalificerade värd adressen för den hanterade instansen för användning i nästa snabb start.

   ![Värddatornamn](./media/sql-database-managed-instance-get-started/host-name.png)

   Namnet ser ut ungefär så här: **ditt_datornamn.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du ansluter till en hanterad instans:
  - En översikt över anslutnings alternativen för program finns i [ansluta dina program till en hanterad instans](sql-database-managed-instance-connect-app.md).
  - En snabb start som visar hur du ansluter till en hanterad instans från en virtuell Azure-dator finns i [Konfigurera en anslutning till en virtuell Azure-dator](sql-database-managed-instance-configure-vm.md).
  - En snabb start som visar hur du ansluter till en hanterad instans från en lokal klient dator med hjälp av en punkt-till-plats-anslutning finns i [Konfigurera en punkt-till-plats](sql-database-managed-instance-configure-p2s.md)-anslutning.
- Så här återställer du en befintlig SQL Server databas från en lokal plats till en hanterad instans: 
    - Använd [Azure Database migration service (DMS) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) för att återställa från en databas säkerhets kopia. 
    - Använd [kommandot T-SQL](sql-database-managed-instance-get-started-restore.md) Restore för att återställa från en databas säkerhets kopia.
- Avancerad övervakning av hanterade instanser av databas prestanda med inbyggd fel söknings information finns i [övervaka Azure SQL Database med hjälp av Azure SQL-analys](../azure-monitor/insights/azure-sql.md).
