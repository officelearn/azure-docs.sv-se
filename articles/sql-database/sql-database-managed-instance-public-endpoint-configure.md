---
title: Konfigurera offentlig slut punkt – Azure SQL Database Hanterad instans
description: Lär dig hur du konfigurerar en offentlig slut punkt för en hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 6f953e4c549619a30564bdb061e98761474174c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687963"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Konfigurera en offentlig slut punkt i Azure SQL Database Hanterad instans

En offentlig slut punkt för en [hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) ger data åtkomst till din hanterade instans utanför det [virtuella nätverket](../virtual-network/virtual-networks-overview.md). Du kan komma åt din hanterade instans från Azure-tjänster med flera innehavare, t. ex. Power BI, Azure App Service eller ett lokalt nätverk. Genom att använda den offentliga slut punkten på en hanterad instans behöver du inte använda ett VPN, vilket kan hjälpa till att undvika problem med VPN-dataflöde.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> - Aktivera offentlig slut punkt för din hanterade instans i Azure Portal
> - Aktivera offentlig slut punkt för din hanterade instans med hjälp av PowerShell
> - Konfigurera din hanterade instans nätverks säkerhets grupp för att tillåta trafik till den offentliga slut punkten för hanterade instanser
> - Hämta den offentliga slut punkts anslutnings strängen för hanterad instans

## <a name="permissions"></a>Behörigheter

På grund av känsligheten hos data som finns i en hanterad instans kräver konfigurationen för att aktivera offentlig slut punkt för hanterade instanser en två stegs process. Detta säkerhets mått följer separering av uppgifter (SoD):

- Att aktivera en offentlig slut punkt på en hanterad instans måste utföras av administratören för den hanterade instansen. Den hanterade instans administratören finns på **översikts** sidan för din SQL-hanterade instans resurs.
- Tillåta trafik med hjälp av en nätverks säkerhets grupp som måste utföras av en nätverks administratör. Mer information finns i [behörigheter för nätverks säkerhets grupper](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Aktiverar offentlig slut punkt för en hanterad instans i Azure Portal

1. Starta Azure Portal på <https://portal.azure.com/.>
1. Öppna resurs gruppen med den hanterade instansen och välj den **SQL-hanterade instans** som du vill konfigurera offentlig slut punkt på.
1. Välj fliken **virtuellt nätverk** på **säkerhets** inställningarna.
1. På sidan konfiguration av virtuellt nätverk väljer du **Aktivera** och sedan **Spara** -ikonen för att uppdatera konfigurationen.

![mi-VNet-config. png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Aktivera offentlig slut punkt för en hanterad instans med hjälp av PowerShell

### <a name="enable-public-endpoint"></a>Aktivera offentlig slut punkt

Kör följande PowerShell-kommandon. Ersätt **prenumerations-ID** med ditt PRENUMERATIONS-ID. Ersätt också **RG-Name** med resurs gruppen för din hanterade instans och Ersätt **mi-Name** med namnet på din hanterade instans.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Inaktivera offentlig slut punkt

Om du vill inaktivera den offentliga slut punkten med hjälp av PowerShell kör du följande kommando (och glöm inte heller att stänga NSG för den inkommande porten 3342 om du har den konfigurerad):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Tillåt offentlig slut punkts trafik för nätverks säkerhets gruppen

1. Om sidan konfiguration av den hanterade instansen fortfarande är öppen går du till fliken **Översikt** . gå tillbaka till din SQL- **hanterade instans** resurs. Välj länken **virtuellt nätverk/undernät** , som kommer att gå till sidan konfiguration av virtuellt nätverk.

    ![mi-overview. png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Välj fliken **undernät** i det vänstra konfigurations fönstret i det virtuella nätverket och anteckna **säkerhets gruppen** för din hanterade instans.

    ![mi-VNet-Subnet. png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Gå tillbaka till din resurs grupp som innehåller din hanterade instans. Du bör se namnet på den **nätverks säkerhets grupp** som anges ovan. Välj namnet för att gå till konfigurations sidan för nätverks säkerhets gruppen.

1. Välj fliken **inkommande säkerhets regler** och **Lägg till** en regel som har högre prioritet än **deny_all_inbound** -regeln med följande inställningar: </br> </br>

    |Inställning  |Föreslaget värde  |Beskrivning  |
    |---------|---------|---------|
    |**Källa**     |Valfri IP-adress eller service tag         |<ul><li>För Azure-tjänster som Power BI väljer du Azure Cloud Service-taggen</li> <li>För datorn eller den virtuella Azure-datorn använder du NAT IP-adress</li></ul> |
    |**Käll port intervall**     |*         |Lämna det till * (valfritt) eftersom käll portarna vanligt vis är dynamiskt allokerade och som sådana, oförutsägbara |
    |**Mål**     |Alla         |Lämna destination som valfri för att tillåta trafik till under nätet för hanterade instanser |
    |**Mål ports intervall**     |3342         |Scope-målport till 3342, som är den offentliga TDS-slutpunkten för hanterad instans |
    |**Protokoll**     |TCP         |Den hanterade instansen använder TCP-protokollet för TDS |
    |**Åtgärd**     |Tillåt         |Tillåt inkommande trafik till hanterad instans via den offentliga slut punkten |
    |**Prioritet**     |1300         |Kontrol lera att den här regeln är högre prioritet än **deny_all_inbound** -regeln |

    ![mi-NSG-rules. png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 används för offentliga slut punkts anslutningar till hanterade instanser och kan inte ändras i det här läget.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Hämtning av den offentliga slut punkts anslutnings strängen för hanterad instans

1. Gå till konfigurations sidan för SQL-hanterad instans som har Aktiver ATS för den offentliga slut punkten. Välj fliken **anslutnings strängar** under **inställnings** konfigurationen.
1. Observera att värd namnet för den offentliga slut punkten anges i formatet < mi_name >. **Public**. < dns_zone >. Database. Windows. net och att porten som används för anslutningen är 3342.

    ![mi-Public-Endpoint-Conn-String. png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att [använda Azure SQL Database hanterade instanser på ett säkert sätt med offentlig slut punkt](sql-database-managed-instance-public-endpoint-securely.md).