---
title: 'Snabb start: skapa en Azure SQL-hanterad instans (portal)'
description: Skapa en hanterad Azure SQL-instans, nätverks miljö och virtuell klient dator för åtkomst med hjälp av Azure Portal i den här snabb starten.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: cc3a25992297dd8deb02deb2c561cad4b53e318b
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84113749"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Snabb start: skapa en Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här snabb starten lär dig att skapa en [hanterad Azure SQL-instans](sql-managed-instance-paas-overview.md) i Azure Portal.

> [!IMPORTANT]
> För begränsningar, se [regioner som stöds](resource-limits.md#supported-regions) och [prenumerations typer som stöds](resource-limits.md#supported-subscription-types).

## <a name="create-sql-managed-instance"></a>Skapa hanterad SQL-instans

Följ dessa steg om du vill skapa en Azure SQL hanterade-instans: 

### <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/).

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Välj **Azure SQL** på den vänstra menyn i Azure Portal. Om **Azure SQL** inte finns i listan väljer du **alla tjänster**och anger sedan **Azure SQL** i sökrutan.
1. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa ytterligare information om en Azure SQL-hanterad instans genom att välja **Visa information** på panelen **SQL-hanterad instans** .
1. Välj **Skapa**.

   ![Skapa en SQL-hanterad instans](./media/instance-create-quickstart/create-managed-instance.png)

4. Använd flikarna i etablerings formuläret **Skapa Azure SQL-hanterad instans** för att lägga till obligatorisk och valfri information. I följande avsnitt beskrivs de här flikarna.

### <a name="basics-tab"></a>Fliken Grundläggande

- Fyll i obligatorisk information som krävs på fliken **grundläggande** . Detta är en minsta uppsättning information som krävs för att etablera en SQL-hanterad instans.

   ![Fliken "grunder" för att skapa en SQL-hanterad instans](./media/instance-create-quickstart/mi-create-tab-basics.png)

   Använd tabellen nedan som referens för den information som krävs på den här fliken.

   | Inställningen| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Prenumeration** | Din prenumeration. | En prenumeration som ger dig behörighet att skapa nya resurser. |
   | **Resursgrupp** | En ny eller befintlig resursgrupp.|Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|
   | **Namn på hanterad instans** | Ett giltigt namn.|Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|
   | **Region** |Den region där du vill skapa den SQL-hanterade instansen.|Information om regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).|
   | **Administratörsinloggning för hanterad instans** | Ett giltigt användar namn. | Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). Använd inte "ServerAdmin" eftersom det är en reserverad server nivå roll.|
   | **Lösenord** | Ett giltigt lösen ord.| Lösenordet måste vara minst 16 tecken långt och uppfylla [de definierade kraven på komplexitet](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Välj **Konfigurera hanterad instans** för att ändra storlek på beräknings-och lagrings resurser och granska pris nivåerna. Använd skjutreglagen eller textrutorna för att ange mängden lagringsutrymme och antalet virtuella kärnor. När du är klar väljer du **tillämpa** för att spara ditt val. 

   ![Formulär för hanterad instans](./media/instance-create-quickstart/mi-create-tab-configure-performance.png)

- Om du vill granska dina val innan du skapar en SQL-hanterad instans kan du välja **Granska + skapa**. Du kan också konfigurera nätverks alternativ genom att välja **Nästa: nätverk**.

### <a name="networking-tab"></a>Fliken nätverk

- Fyll i valfri information på fliken **nätverk** . Om du utelämnar den här informationen kommer portalen att tillämpa standardinställningar.

   ![Fliken nätverk för att skapa en SQL-hanterad instans](./media/instance-create-quickstart/mi-create-tab-networking.png)

   Använd tabellen nedan som referens för den information som krävs på den här fliken.

   | Inställningen| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Virtuellt nätverk** | Välj antingen **Skapa nytt virtuellt nätverk** eller ett giltigt virtuellt nätverk och undernät.| Om ett nätverk eller undernät inte är tillgängligt, måste det [ändras för att uppfylla nätverks kraven](vnet-existing-add-subnet.md) innan du väljer det som mål för den nya SQL-hanterade instansen. Information om kraven för att konfigurera nätverks miljön för en SQL-hanterad instans finns i [Konfigurera ett virtuellt nätverk för en SQL-hanterad instans](connectivity-architecture-overview.md). |
   | **Anslutnings typ** | Välj mellan en proxy och en Anslutnings typ för omdirigering.|Mer information om anslutnings typer finns i [Anslutnings typ för Azure SQL-hanterad instans](../database/connectivity-architecture.md#connection-policy).|
   | **Offentlig slutpunkt**  | Välj **Aktivera**. | För att en SQL-hanterad instans ska kunna nås via den offentliga data slut punkten måste du aktivera det här alternativet. | 
   | **Tillåt åtkomst från** (om den **offentliga slut punkten** är aktive rad) | Välj ett av alternativen.   |Med Portal upplevelsen kan du konfigurera en säkerhets grupp med en offentlig slut punkt. </br> </br> Välj något av följande alternativ baserat på ditt scenario: </br> <ul> <li>**Azure-tjänster**: Vi rekommenderar det här alternativet när du ansluter från Power BI eller en annan tjänst för flera innehavare. </li> <li> **Internet**: används i test syfte när du snabbt vill skapa en SQL-hanterad instans. Vi rekommenderar det inte för produktions miljöer. </li> <li> **Ingen åtkomst**: det här alternativet skapar en säkerhets regel för **neka** . Ändra den här regeln om du vill göra en SQL-hanterad instans tillgänglig via en offentlig slut punkt. </li> </ul> </br> Mer information om säkerhet för offentliga slut punkter finns i [använda Azure SQL-hanterad instans på ett säkert sätt med en offentlig slut punkt](public-endpoint-overview.md).|

- Välj **Granska + skapa** för att granska dina val innan du skapar en SQL-hanterad instans. Du kan också konfigurera fler anpassade inställningar genom att välja **Nästa: ytterligare inställningar**.

### <a name="additional-settings"></a>Ytterligare inställningar

- Fyll i valfri information på fliken **ytterligare inställningar** . Om du utelämnar den här informationen kommer portalen att tillämpa standardinställningar.

   ![Fliken Ytterligare inställningar för att skapa en SQL-hanterad instans](./media/instance-create-quickstart/mi-create-tab-additional-settings.png)

   Använd tabellen nedan som referens för den information som krävs på den här fliken.

   | Inställningen| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Sortering** | Välj den sortering som du vill använda för din SQL-hanterade instans. Om du migrerar databaser från SQL Server kontrollerar du käll sorteringen genom `SELECT SERVERPROPERTY(N'Collation')` att använda och använda det värdet.| Information om sorteringar finns i [Ange eller ändra Server sorteringen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Tidszon** | Välj den tidszon som din SQL-hanterade instans ska observera.|Mer information finns i [tids zoner](timezones-overview.md).|
   | **Använd som sekundär redundans** | Välj **Ja**. | Aktivera det här alternativet om du vill använda den SQL-hanterade instansen som en sekundär grupp för redundans.|
   | **Primär SQL-hanterad instans** (om **Använd som sekundär redundans** är inställt på **Ja**) | Välj en befintlig primär SQL-hanterad instans som kommer att kopplas till samma DNS-zon med den SQL-hanterade instans som du skapar. | I det här steget aktive ras konfigurationen efter skapandet av gruppen redundans. Mer information finns i [Självstudier: Lägg till en SQL Database SQL-hanterad instans i en failover-grupp](failover-group-add-instance-tutorial.md).|

## <a name="review--create"></a>Granska + skapa

1. Välj **Granska + fliken Skapa** för att granska dina val innan du skapar SQL-hanterad instans.

   ![Flik för att granska och skapa en SQL-hanterad instans](./media/instance-create-quickstart/mi-create-tab-review-create.png)

1. Välj **skapa** för att börja ETABLERINGEN av SQL-hanterad instans.

> [!IMPORTANT]
> Distribution av en SQL-hanterad instans är en tids krävande åtgärd. Distribution av den första instansen i under nätet tar vanligt vis mycket längre tid än att distribuera till ett undernät med befintliga SQL-hanterade instanser. För genomsnittlig etablerings tid, se [hanterings åtgärder för SQL-hanterad instans](sql-managed-instance-paas-overview.md#management-operations).

## <a name="monitor-deployment-progress"></a>Övervaka distributions förlopp

1. Välj ikonen **meddelanden** om du vill visa status för distributionen.

   ![Distributions förlopp för en SQL-hanterad instans distribution](./media/instance-create-quickstart/mi-create-deployment-in-progress.png)

1. Välj **distribution pågår** i meddelandet för att öppna fönstret SQL-hanterad instans och övervaka distributions förloppet ytterligare. 

> [!TIP]
> Om du stängde webbläsaren eller flyttat bort från distributions förloppet följer du stegen nedan för att leta upp skärmen distributions förlopp:
> 1. I Azure Portal öppnar du resurs gruppen (på fliken **grundläggande** ) där du distribuerar en SQL-hanterad instans.
> 2. Välj **distributioner**.
> 3. Välj den SQL-hanterade instansen distributions åtgärd pågår.

> [!IMPORTANT]
> För att kunna hämta statusen för skapandet av SQL-hanterad instans måste du ha **Läs behörighet** över resurs gruppen. Om du inte har den här behörigheten eller återkalla den medan SQL-hanterad instans skapas, kan detta orsaka att SQL-hanterad instans inte visas i listan över distributioner av resurs grupper.
>

## <a name="view-resources-created"></a>Visa resurser som skapats

Vid lyckad distribution av SQL-hanterad instans för att visa resurser som skapats:

1. Öppna resurs gruppen för din SQL-hanterade instans. 

   ![SQL-hanterade instans resurser](./media/instance-create-quickstart/resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Visa och finjustera nätverks inställningar

Om du vill finjustera nätverks inställningarna bör du kontrol lera följande:

1. Välj routningstabellen för att granska den användardefinierade väg (UDR) som skapades åt dig.

   ![Routningstabell](./media/instance-create-quickstart/route-table.png)

2. I routningstabellen granskar du posterna för att dirigera trafik från och inom SQL Managed instances virtuella nätverk. Om du skapar eller konfigurerar din routningstabell manuellt, se till att skapa dessa poster i routningstabellen för SQL-hanterad instans.

   ![Post för ett undernät för SQL-hanterad instans till lokalt](./media/instance-create-quickstart/udr.png)

3. Gå tillbaka till resurs gruppen och välj nätverks säkerhets gruppen.

   ![Nätverkssäkerhetsgrupp](./media/instance-create-quickstart/network-security-group.png)

4. Granska inkommande och utgående säkerhetsregler. 

   ![Säkerhetsregler](./media/instance-create-quickstart/security-rules.png)

> [!IMPORTANT]
> Om du har konfigurerat en offentlig slut punkt för din SQL-hanterade instans måste du öppna portar för att tillåta nätverks trafik att tillåta anslutningar till SQL-hanterad instans från det offentliga Internet. mer information finns i [Konfigurera en offentlig slut punkt för SQL-hanterad instans](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) .
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Hämta anslutnings information till SQL-hanterad instans

Följ dessa steg för att hämta värdnamn och fullständigt kvalificerat domän namn (FQDN) för att ansluta till SQL-hanterad instans:

1. Gå tillbaka till resurs gruppen och välj din SQL-hanterade instans.

   ![SQL-hanterad instans i resurs gruppen](./media/instance-create-quickstart/managed-instance.png)

2. Leta upp **värd** egenskapen på fliken **Översikt** . Kopiera värd namnet för SQL-hanterad instans för användning i nästa snabb start.

   ![Värdnamn](./media/instance-create-quickstart/host-name.png)

   Värdet som kopieras representerar ett fullständigt kvalificerat domän namn (FQDN) som kan användas för att ansluta till SQL-hanterad instans. Det liknar följande adress exempel: *your_host_name. a1b2c3d4e5f6. Database. Windows. net*.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du ansluter till en SQL-hanterad instans:
- En översikt över anslutnings alternativen för program finns i [ansluta dina program till en SQL-hanterad instans](connect-application-instance.md).
- En snabb start som visar hur du ansluter till en SQL-hanterad instans från en virtuell Azure-dator finns i [Konfigurera en anslutning till en virtuell Azure-dator](connect-vm-instance-configure.md).
- En snabb start som visar hur du ansluter till en SQL-hanterad instans från en lokal klient dator med hjälp av en punkt-till-plats-anslutning finns i [Konfigurera en punkt-till-plats](point-to-site-p2s-configure.md)-anslutning.

Återställa en befintlig SQL Server databas från en lokal plats till en SQL-hanterad instans: 
- Använd [Azure Database migration service för migrering](../../dms/tutorial-sql-server-to-managed-instance.md) för att återställa från en databas säkerhets kopia. 
- Använd [kommandot T-SQL REstore](restore-sample-database-quickstart.md) för att återställa från en databas säkerhets kopia.

Avancerad övervakning av SQL-hanterad instans databas prestanda med inbyggd fel söknings information finns i [övervaka Azure SQL-hanterad instans med hjälp av Azure SQL-analys](../../azure-monitor/insights/azure-sql.md).
