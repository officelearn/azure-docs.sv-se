---
title: 'Snabb start: skapa en hanterad instans av SQL-hanterad instans (portal)'
description: Skapa en hanterad instans, nätverks miljö och virtuell klient dator för åtkomst med hjälp av Azure Portal i den här snabb starten.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: 1899baa927bffdaedd1d7ee00f442c449480fc04
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986854"
---
# <a name="quickstart-create-a-managed-instance-of-sql-managed-instance"></a>Snabb start: skapa en hanterad instans av SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här snabb starten lär dig att skapa en hanterad instans av [Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md) i Azure Portal.

> [!IMPORTANT]
> För begränsningar, se [regioner som stöds](resource-limits.md#supported-regions) och [prenumerations typer som stöds](resource-limits.md#supported-subscription-types).

## <a name="create-a-managed-instance"></a>Skapa en hanterad instans

Följ dessa steg om du vill skapa en hanterad instans: 

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/).

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **Azure SQL** på den vänstra menyn i Azure Portal. Om **Azure SQL** inte finns i listan väljer du **alla tjänster**och anger sedan **Azure SQL** i sökrutan.
1. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa mer information om den hanterade Azure SQL-instansen genom att välja **Visa information** på panelen **SQL-hanterade instanser** .
1. Välj **Skapa**.

   ![Skapa en hanterad instans](./media/instance-create-quickstart/create-managed-instance.png)

4. Använd flikarna i etablerings formuläret **Skapa Azure SQL-hanterad instans** för att lägga till obligatorisk och valfri information. I följande avsnitt beskrivs de här flikarna.

### <a name="basics-tab"></a>Fliken Grundläggande

- Fyll i obligatorisk information som krävs på fliken **grundläggande** . Detta är en minsta uppsättning information som krävs för att etablera en hanterad instans.

   ![Fliken "grunder" för att skapa en hanterad instans](./media/instance-create-quickstart/mi-create-tab-basics.png)

   Använd tabellen nedan som referens för den information som krävs på den här fliken.

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Prenumeration** | Din prenumeration. | En prenumeration som ger dig behörighet att skapa nya resurser. |
   | **Resursgrupp** | En ny eller befintlig resursgrupp.|Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|
   | **Namn på hanterad instans** | Ett giltigt namn.|Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|
   | **Region** |Den region där du vill skapa den hanterade instansen.|Information om regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).|
   | **Administratörsinloggning för hanterad instans** | Ett giltigt användar namn. | Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). Använd inte "ServerAdmin" eftersom det är en reserverad server nivå roll.|
   | **Lösenord** | Ett giltigt lösen ord.| Lösenordet måste vara minst 16 tecken långt och uppfylla [de definierade kraven på komplexitet](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Välj **Konfigurera hanterad instans** för att ändra storlek på beräknings-och lagrings resurser och granska pris nivåerna. Använd skjutreglagen eller textrutorna för att ange mängden lagringsutrymme och antalet virtuella kärnor. När du är klar väljer du **tillämpa** för att spara ditt val. 

   ![Formulär för hanterad instans](./media/instance-create-quickstart/mi-create-tab-configure-performance.png)

- Om du vill granska dina val innan du skapar en SQL-hanterad instans kan du välja **Granska + skapa**. Du kan också konfigurera nätverks alternativ genom att välja **Nästa: nätverk**.

### <a name="networking-tab"></a>Fliken nätverk

- Fyll i valfri information på fliken **nätverk** . Om du utelämnar den här informationen kommer portalen att tillämpa standardinställningar.

   ![Fliken nätverk för att skapa en hanterad instans](./media/instance-create-quickstart/mi-create-tab-networking.png)

   Använd tabellen nedan som referens för den information som krävs på den här fliken.

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Virtuellt nätverk** | Välj antingen **Skapa nytt virtuellt nätverk** eller ett giltigt virtuellt nätverk och undernät.| Om ett nätverk eller undernät inte är tillgängligt, måste det [ändras för att uppfylla nätverks kraven](vnet-existing-add-subnet.md) innan du väljer det som mål för den nya hanterade instansen. Information om kraven för att konfigurera nätverks miljön för SQL-hanterad instans finns i [Konfigurera ett virtuellt nätverk för SQL-hanterad instans](connectivity-architecture-overview.md). |
   | **Anslutningstyp** | Välj mellan en proxy och en Anslutnings typ för omdirigering.|Mer information om anslutnings typer finns i [Anslutnings typ för Azure SQL-hanterad instans](../database/connectivity-architecture.md#connection-policy).|
   | **Offentlig slutpunkt**  | Välj **Aktivera**. | För att en hanterad instans ska kunna nås via den offentliga data slut punkten måste du aktivera det här alternativet. | 
   | **Tillåt åtkomst från** (om den **offentliga slut punkten** är aktive rad) | Välj ett av alternativen.   |Med Portal upplevelsen kan du konfigurera en säkerhets grupp med en offentlig slut punkt. </br> </br> Välj något av följande alternativ baserat på ditt scenario: </br> <ul> <li>**Azure-tjänster**: Vi rekommenderar det här alternativet när du ansluter från Power BI eller en annan tjänst för flera innehavare. </li> <li> **Internet**: används i test syfte när du snabbt vill skapa en hanterad instans. Vi rekommenderar det inte för produktions miljöer. </li> <li> **Ingen åtkomst**: det här alternativet skapar en säkerhets regel för **neka** . Ändra den här regeln för att göra en hanterad instans tillgänglig via en offentlig slut punkt. </li> </ul> </br> Mer information om säkerhet för offentliga slut punkter finns i [använda Azure SQL-hanterad instans på ett säkert sätt med en offentlig slut punkt](public-endpoint-overview.md).|

- Välj **Granska + skapa** för att granska dina val innan du skapar en hanterad instans. Du kan också konfigurera fler anpassade inställningar genom att välja **Nästa: ytterligare inställningar**.

### <a name="additional-settings"></a>Ytterligare inställningar

- Fyll i valfri information på fliken **ytterligare inställningar** . Om du utelämnar den här informationen kommer portalen att tillämpa standardinställningar.

   ![Fliken Ytterligare inställningar för att skapa en hanterad instans](./media/instance-create-quickstart/mi-create-tab-additional-settings.png)

   Använd tabellen nedan som referens för den information som krävs på den här fliken.

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Sortering** | Välj den sortering som du vill använda för din hanterade instans. Om du migrerar databaser från SQL Server kontrollerar du käll sorteringen genom `SELECT SERVERPROPERTY(N'Collation')` att använda och använda det värdet.| Information om sorteringar finns i [Ange eller ändra Server sorteringen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Tidszon** | Välj den tidszon som den hanterade instansen ska observera.|Mer information finns i [tids zoner](timezones-overview.md).|
   | **Använd som sekundär redundans** | Välj **Ja**. | Aktivera det här alternativet om du vill använda den hanterade instansen som en sekundär grupp för redundans.|
   | **Primär SQL-hanterad instans** (om **Använd som sekundär redundans** är inställt på **Ja**) | Välj en befintlig primär hanterad instans som ska anslutas till samma DNS-zon med den hanterade instans som du skapar. | I det här steget aktive ras konfigurationen efter skapandet av gruppen redundans. Mer information finns i [Självstudier: Lägg till en hanterad instans i en failover-grupp](failover-group-add-instance-tutorial.md).|

## <a name="review--create"></a>Granska + skapa

1. Välj **Granska + fliken Skapa** för att granska dina val innan du skapar en hanterad instans.

   ![Flik för att granska och skapa en hanterad instans](./media/instance-create-quickstart/mi-create-tab-review-create.png)

1. Välj **skapa** för att starta etableringen av den hanterade instansen.

> [!IMPORTANT]
> Att distribuera en hanterad instans är en tids krävande åtgärd. Distribution av den första instansen i under nätet tar vanligt vis mycket längre tid än att distribuera till ett undernät med befintliga hanterade instanser. För genomsnittlig etablerings tid, se [hanterings åtgärder för SQL-hanterad instans](sql-managed-instance-paas-overview.md#management-operations).

## <a name="monitor-deployment-progress"></a>Övervaka distributions förlopp

1. Välj ikonen **meddelanden** om du vill visa status för distributionen.

   ![Distributions förlopp för en SQL-hanterad instans distribution](./media/instance-create-quickstart/mi-create-deployment-in-progress.png)

1. Välj **distribution pågår** i meddelandet för att öppna fönstret SQL-hanterad instans och övervaka distributions förloppet ytterligare. 

> [!TIP]
> Om du stängde webbläsaren eller flyttat bort från distributions förloppet följer du stegen nedan för att leta upp skärmen distributions förlopp:
> 1. I Azure Portal öppnar du resurs gruppen (på fliken **grundläggande** ) där du distribuerar SQL-hanterad instans.
> 2. Välj **distributioner**.
> 3. Välj den SQL-hanterade instansen distributions åtgärd pågår.

> [!IMPORTANT]
> - Skapandet av SQL-hanterad instans är en tids krävande åtgärd som kan ta några timmar i taget, beroende på särskilda omständigheter. Se [varaktighet för hanterings åtgärder](management-operations-overview.md#duration) för vanliga skapande tider.
> - Det gick inte att skapa en SQL-hanterad instans i fall när det finns andra effekter, t. ex. tids krävande återställnings-eller skalnings åtgärder på andra hanterade instanser i samma undernät. Mer information finns i [hanterings åtgärder mellan påverkan](management-operations-overview.md#management-operations-cross-impact).
> - För att kunna hämta statusen för skapandet av hanterade instanser måste du ha **Läs behörighet** över resurs gruppen. Om du inte har den här behörigheten eller återkalla den medan den hanterade instansen håller på att skapas, kan detta orsaka att SQL-hanterad instans inte visas i listan över distributioner av resurs grupper.
>

## <a name="view-resources-created"></a>Visa resurser som skapats

Vid lyckad distribution av en hanterad instans för att visa resurser som skapats:

1. Öppna resurs gruppen för din hanterade instans. 

   ![SQL-hanterade instans resurser](./media/instance-create-quickstart/resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Visa och finjustera nätverks inställningar

Om du vill finjustera nätverks inställningarna bör du kontrol lera följande:

1. Välj routningstabellen för att granska den användardefinierade väg (UDR) som skapades åt dig.

   ![Routningstabell](./media/instance-create-quickstart/route-table.png)

2. I routningstabellen granskar du posterna för att dirigera trafik från och inom SQL Managed instance Virtual Network. Om du skapar eller konfigurerar din routningstabell manuellt, se till att skapa dessa poster i routningstabellen för SQL-hanterad instans.

   ![Post för ett undernät för SQL-hanterad instans till lokalt](./media/instance-create-quickstart/udr.png)

3. Gå tillbaka till resurs gruppen och välj nätverks säkerhets gruppen.

   ![Nätverkssäkerhetsgrupp](./media/instance-create-quickstart/network-security-group.png)

4. Granska inkommande och utgående säkerhetsregler. 

   ![Säkerhetsregler](./media/instance-create-quickstart/security-rules.png)

> [!IMPORTANT]
> Om du har konfigurerat en offentlig slut punkt för SQL-hanterad instans måste du öppna portar för att tillåta nätverks trafik att tillåta anslutningar till SQL-hanterad instans från det offentliga Internet. Mer information finns i [Konfigurera en offentlig slut punkt för SQL-hanterad instans](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) .
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Hämta anslutningsinformation till SQL Managed Instance

Följ dessa steg för att hämta värd namnet och det fullständiga domän namnet (FQDN) för att ansluta till SQL-hanterad instans:

1. Gå tillbaka till resursgruppen och välj din hanterade instans.

   ![Hanterad instans i resurs gruppen](./media/instance-create-quickstart/managed-instance.png)

2. Leta upp **värd** egenskapen på fliken **Översikt** . Kopiera värd namnet för den hanterade instansen för användning i nästa snabb start.

   ![Värdnamn](./media/instance-create-quickstart/host-name.png)

   Värdet som kopieras representerar ett fullständigt kvalificerat domän namn (FQDN) som kan användas för att ansluta till SQL-hanterad instans. Det liknar följande adress exempel: *your_host_name. a1b2c3d4e5f6. Database. Windows. net*.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du ansluter till SQL-hanterad instans:
- En översikt över anslutnings alternativen för program finns i [ansluta dina program till SQL-hanterad instans](connect-application-instance.md).
- En snabb start som visar hur du ansluter till SQL-hanterad instans från en virtuell Azure-dator finns i [Konfigurera en anslutning till en virtuell Azure-dator](connect-vm-instance-configure.md).
- En snabb start som visar hur du ansluter till SQL-hanterad instans från en lokal klient dator med hjälp av en punkt-till-plats-anslutning finns i [Konfigurera en punkt-till-plats](point-to-site-p2s-configure.md)-anslutning.

Så här återställer du en befintlig SQL Server databas från lokal till SQL-hanterad instans: 
- Använd [Azure Database migration service för migrering](../../dms/tutorial-sql-server-to-managed-instance.md) för att återställa från en databas säkerhets kopia. 
- Använd [kommandot T-SQL REstore](restore-sample-database-quickstart.md) för att återställa från en databas säkerhets kopia.

Avancerad övervakning av SQL-hanterad instans databas prestanda med inbyggd fel söknings information finns i [övervaka Azure SQL-hanterad instans med hjälp av Azure SQL-analys](../../azure-monitor/insights/azure-sql.md).
