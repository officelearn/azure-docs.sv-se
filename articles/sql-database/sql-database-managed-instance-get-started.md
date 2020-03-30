---
title: 'Azure-portal: Skapa en hanterad instans'
description: Skapa en SQL Database-hanterad instans, nätverksmiljö och klient-VM för åtkomst.
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
ms.openlocfilehash: 25128442cd922f6b9130586e245695b6880f661c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80257622"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Snabbstart: Skapa en hanterad Azure SQL-databas-hanterad instans

Den här snabbstarten går igenom hur du skapar en Hanterad Azure [SQL-databas-hanterad instans](sql-database-managed-instance.md) i Azure-portalen.

> [!IMPORTANT]
> Begränsningar finns i [Regioner som stöds](sql-database-managed-instance-resource-limits.md#supported-regions) och [prenumerationstyper som stöds](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/).

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Skapa en hanterad instans

Följande steg visar hur du skapar en hanterad instans:

1. Välj **Azure SQL** till vänster i Azure-portalen. Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och anger sedan Azure **SQL** i sökrutan.
2. Välj **+Lägg till** om du vill öppna **alternativsidan Välj SQL-distribution.** Du kan visa ytterligare information om en Hanterad Azure SQL-databas-instans genom att välja **Visa information** på panelen **Hanterade instanser.**
3. Välj **Skapa**.

   ![Skapa en hanterad instans](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Använd flikarna i etableringsformuläret **Skapa Azure SQL Database Managed Instance** för att lägga till nödvändig och valfri information. I följande avsnitt beskrivs dessa flikar.

### <a name="basics"></a>Grundläggande inställningar

- Fyll i obligatorisk information som krävs på fliken **Grunderna.** Detta är en minsta uppsättning information som krävs för att etablera en hanterad instans.

   ![Fliken "Grunderna" för att skapa en hanterad instans](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Använd tabellen nedan som referens för information som krävs på den här fliken.

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Prenumeration** | Din prenumeration. | En prenumeration som ger dig behörighet att skapa nya resurser. |
   | **Resursgrupp** | En ny eller befintlig resursgrupp.|Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|
   | **Namn på hanterad instans** | Valfritt giltigt namn.|Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|
   | **Regionen** |Den region där du vill skapa den hanterade instansen.|Information om regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).|
   | **Administratörsinloggning för hanterad instans** | Alla giltiga användarnamn. | Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). Använd inte "serveradmin" eftersom det är en reserverad roll på servernivå.|
   | **Lösenord** | Alla giltiga lösenord.| Lösenordet måste vara minst 16 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Välj **Konfigurera hanterad instans** för att ändra beräknings- och lagringsresurser och granska prisnivåerna. Använd skjutreglagen eller textrutorna för att ange mängden lagringsutrymme och antalet virtuella kärnor. När du är klar väljer du **Använd** för att spara ditt val. 

   ![Formulär för hanterad instans](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Om du vill granska dina val innan du skapar en hanterad instans kan du välja **Granska + skapa**. Du kan också konfigurera nätverksalternativ genom att välja **Nästa: Nätverk**.

### <a name="networking"></a>Nätverk

- Fyll i valfri information på fliken **Nätverk.** Om du utelämnar den här informationen tillämpar portalen standardinställningarna.

   ![Fliken Nätverk för att skapa en hanterad instans](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Använd tabellen nedan som referens för information som krävs på den här fliken.

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Virtuellt nätverk** | Välj antingen **Skapa nytt virtuellt nätverk** eller ett giltigt virtuellt nätverk och undernät.| Om ett nätverk eller undernät inte är tillgängligt måste det [ändras för att uppfylla nätverkskraven](sql-database-managed-instance-configure-vnet-subnet.md) innan du väljer det som ett mål för den nya hanterade instansen. Information om kraven för att konfigurera nätverksmiljön för en hanterad instans finns i [Konfigurera ett virtuellt nätverk för en hanterad instans](sql-database-managed-instance-connectivity-architecture.md). |
   | **Anslutningstyp** | Välj mellan en proxy och en omdirigeringsanslutningstyp.|Mer information om anslutningstyper finns i [Azure SQL Database-anslutningsprincipen](sql-database-connectivity-architecture.md#connection-policy).|
   | **Offentlig slutpunkt**  | Välj **Aktivera**. | För att en hanterad instans ska vara tillgänglig via den offentliga dataslutpunkten måste du aktivera det här alternativet. | 
   | **Tillåt åtkomst från** (om **offentlig slutpunkt** är aktiverad) | Välj ett av alternativen.   |Portalupplevelsen gör det möjligt att konfigurera en säkerhetsgrupp med en offentlig slutpunkt. </br> </br> Baserat på ditt scenario väljer du något av följande alternativ: </br> <ul> <li>**Azure-tjänster**: Vi rekommenderar det här alternativet när du ansluter från Power BI eller en annan tjänst med flera strömförsörjningar. </li> <li> **Internet**: Används i testsyfte när du snabbt vill skapa en hanterad instans. Vi rekommenderar det inte för produktionsmiljöer. </li> <li> **Ingen åtkomst**: Det här alternativet skapar en **neka säkerhetsregel.** Ändra den här regeln om du vill göra en hanterad instans tillgänglig via en offentlig slutpunkt. </li> </ul> </br> Mer information om allmän slutpunktssäkerhet finns i [Använda en Hanterad Azure SQL-databas-hanterad instans på ett säkert sätt med en offentlig slutpunkt](sql-database-managed-instance-public-endpoint-securely.md).|

- Välj **Granska + skapa** om du vill granska dina val innan du skapar en hanterad instans. Du kan också konfigurera fler anpassade inställningar genom att välja **Nästa: Ytterligare inställningar**.

### <a name="additional-settings"></a>Ytterligare inställningar

- Fyll i valfri information på fliken **Ytterligare inställningar.** Om du utelämnar den här informationen tillämpar portalen standardinställningarna.

   ![Fliken "Ytterligare inställningar" för att skapa en hanterad instans](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Använd tabellen nedan som referens för information som krävs på den här fliken.

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Sortering** | Välj den sortering som du vill använda för den hanterade instansen. Om du migrerar databaser från SQL Server kontrollerar `SELECT SERVERPROPERTY(N'Collation')` du källsorteringen med hjälp av och använder det värdet.| Information om sortering finns i [Ange eller ändra serversorteringen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Tidszon** | Välj den tidszon som den hanterade instansen ska observera.|Mer information finns i [Tidszoner](sql-database-managed-instance-timezone.md).|
   | **Använd som redundans sekundär** | Välj **Ja**. | Aktivera det här alternativet om du vill använda den hanterade instansen som sekundär redundanskialgrupp.|
   | **Primär hanterad instans** (om **Användning som sekundär redundans** är inställd på **Ja**) | Välj en befintlig primärhanterad instans som ska anslutas i samma DNS-zon med den hanterade instans som du skapar. | Det här steget aktiverar konfigurationen efter skapandet av redundansgruppen. Mer information finns i [Självstudiekurs: Lägg till en SQL Database-hanterad instans i en redundansk grupp](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Granska + skapa

5. Välj **Fliken Granska + skapa** om du vill granska dina val innan du skapar den hanterade instansen.

   ![Tabb för att granska och skapa en hanterad instans](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Välj **Skapa** om du vill börja etablera den hanterade instansen.

> [!IMPORTANT]
> Att distribuera en hanterad instans är en tidskrävande åtgärd. Distribution av den första instansen i undernätet tar vanligtvis mycket längre tid än att distribuera till ett undernät med befintliga hanterade instanser. För genomsnittliga etableringstider finns i [Hanterade instanshanteringsåtgärder](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Övervaka förloppet för distribution

7. Välj ikonen **Meddelanden** om du vill visa distributionens status.

   ![Distributionsförloppet för en hanterad instansdistribution](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Välj **Pågående distribution** i meddelandet om du vill öppna fönstret hanterad instans och ytterligare övervaka distributionsförloppet. 

> [!TIP]
> Om du har stängt webbläsaren eller flyttat bort från skärmen för förlopp för distributionen följer du dessa steg för att hitta tillbaka skärmen för förloppsstatus:
> 1. Öppna resursgruppen (på fliken **Grunderna)** som du distribuerar en hanterad instans till i Azure-portalen.
> 2. Välj **Distributioner**.
> 3. Välj den pågående åtgärden för hanterad instansdistribution.

> [!IMPORTANT]
> För att kunna få status för att skapa hanterade instanser måste du ha **läsbehörighet** över resursgruppen. Om du inte har den här behörigheten eller återkallar den medan hanterad instans håller på att skapas kan hanterad instans inte visas i listan över resursgruppsdistributioner.
>

## <a name="post-deployment-operations"></a>Åtgärder efter distribution

Så här granskar du resurser som skapats, finjusterar nätverksinställningar och hämtar värdanslutningsinformation (FQDN) följer du stegen som beskrivs i det här avsnittet.

### <a name="view-resources-created"></a>Visa skapade resurser

Vid lyckad distribution av hanterad instans, för att visa resurser som skapats:

1. Öppna resursgruppen för den hanterade instansen. Visa dess resurser som har skapats åt dig i [snabbstarten Skapa en hanterad instans.](#create-a-managed-instance)

   ![Resurser för hanterad instans](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Visa och finjustera nätverksinställningar

Om du vill finjustera nätverksinställningarna bör du kontrollera följande:

1. Välj den vägtabell för att granska den användardefinierade vägen (UDR) som skapades åt dig.

   ![Routningstabell](./media/sql-database-managed-instance-get-started/route-table.png)

2. Granska posterna i flödestabellen för att dirigera trafik från och inom den hanterade instansens virtuella nätverk. Om du skapar eller konfigurerar flödestabellen manuellt kontrollerar du att dessa poster skapas i flödestabellen för hanterade instanser.

   ![Post för ett hanterat instansundernät till lokalt](./media/sql-database-managed-instance-get-started/udr.png)

3. Gå tillbaka till resursgruppen och välj nätverkssäkerhetsgruppen.

   ![Nätverkssäkerhetsgrupp](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Granska inkommande och utgående säkerhetsregler. 

   ![Säkerhetsregler](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Om du har konfigurerat offentlig slutpunkt för din hanterade instans måste du öppna portar för att tillåta nätverkstrafik som tillåter anslutningar till hanterad instans från det offentliga Internet, se [Konfigurera en offentlig slutpunkt för hanterad instans](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) för mer information.
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Hämta anslutningsinformation till hanterad instans

Om du vill ansluta till hanterad instans följer du dessa steg för att hämta värdnamn och fullständigt kvalificerat domännamn (FQDN):

1. Gå tillbaka till resursgruppen och välj din hanterade instans.

   ![Hanterad instans i resursgruppen](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Leta reda på egenskapen **Värd** på fliken **Översikt.** Kopiera värdnamnet för den hanterade instansen för användning i nästa snabbstart.

   ![Värdnamn](./media/sql-database-managed-instance-get-started/host-name.png)

   Det kopierade värdet representerar ett fullständigt kvalificerat domännamn (FQDN) som kan användas för att ansluta till hanterad instans. Det liknar följande adressexempel: *your_host_name.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Nästa steg

Så här lär du dig mer om hur du ansluter till en hanterad instans:
- En översikt över anslutningsalternativen för program finns i [Ansluta dina program till en hanterad instans](sql-database-managed-instance-connect-app.md).
- En snabbstart som visar hur du ansluter till en hanterad instans från en virtuell Azure-dator finns i [Konfigurera en Azure-anslutning till virtuella datorer](sql-database-managed-instance-configure-vm.md).
- En snabbstart som visar hur du ansluter till en hanterad instans från en lokal klientdator med hjälp av en anslutning mellan punkt och plats finns i [Konfigurera en point-to-site-anslutning](sql-database-managed-instance-configure-p2s.md).

Så här återställer du en befintlig SQL Server-databas från lokalt till en hanterad instans: 
- Använd [Azure Database Migration Service för migrering för](../dms/tutorial-sql-server-to-managed-instance.md) att återställa från en databassäkerhetskopieringsfil. 
- Använd [kommandot T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) för att återställa från en säkerhetskopia av databasen.

Avancerad övervakning av prestanda för hanterade instansdatabaser med inbyggd felsökningsinformation finns i [Övervaka Azure SQL Database med hjälp av Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
