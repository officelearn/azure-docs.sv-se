---
title: Konfigurera offentlig slutpunkt – Azure SQL Database managed instance | Microsoft Docs
description: Lär dig hur du konfigurerar en offentlig slutpunkt för den hanterade instansen
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: ea16efbb846f21ec7c3fa39b2efeac741d8f8ce0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928369"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Konfigurera offentlig slutpunkt i Azure SQL Database-hanterad instans

Offentlig slutpunkt för en [hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) möjliggör åtkomst till din hanterade instans från utanför den [virtuellt nätverk](../virtual-network/virtual-networks-overview.md). Du kan få åtkomst till din hanterade instans från flera innehavare Azure-tjänster som Power BI, Azure App Service eller ett lokalt nätverk. Med den offentliga slutpunkten på en hanterad instans, behöver du inte använder en VPN-anslutning, vilket hjälper dig att undvika problem med VPN-dataflöde.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> - Aktivera offentlig slutpunkt för din hanterade instans i Azure portal
> - Aktivera offentlig slutpunkt för din hanterade instans med hjälp av PowerShell
> - Konfigurera din hanterade instans någon nätverkssäkerhetsgrupp för att tillåta trafik på den offentliga slutpunkten för hanterad instans
> - Hämta anslutningssträngen för offentlig slutpunkt för hanterad instans

## <a name="permissions"></a>Behörigheter

På grund av hur känsliga data i en hanterad instans, kräver konfigurationen att aktivera hanterad instans offentlig slutpunkt en tvåstegsprocess. Den här säkerhetsåtgärd följer uppdelning av uppgifter (matjordsutläggning):

- Aktivera offentlig slutpunkt på en hanterad instans behöver utföras av administratören för hanterad instans. Administratör för hanterad instans finns på **översikt** för din SQL-hanterad instans resurs.
- Tillåter trafik med hjälp av en nätverkssäkerhetsgrupp som behöver göras av en nätverksadministratör. Mer information finns i [network säkerhetsgruppsbehörigheter](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Aktivera offentlig slutpunkt för en hanterad instans i Azure portal

1. Starta Azure-portalen på <https://portal.azure.com/.>
1. Öppna resursgruppen med den hanterade instansen och välj den **SQL-hanterad instans** att du vill konfigurera en offentlig slutpunkt på.
1. På den **Security** inställningar, Välj den **virtuellt nätverk** fliken.
1. Konfigurationssida för virtuella nätverk, Välj **aktivera** och sedan den **spara** ikon för att uppdatera konfigurationen.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Aktivera offentlig slutpunkt för en hanterad instans med hjälp av PowerShell

### <a name="enable-public-endpoint"></a>Aktivera offentlig slutpunkt

Kör följande PowerShell-kommandon. Ersätt **prenumerations-id** med ditt prenumerations-ID. Byt även ut **rg-name** med resursgruppen för din hanterade instans och Ersätt **mi-name** med namnet på din hanterade instans.

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

### <a name="disable-public-endpoint"></a>Inaktivera offentlig slutpunkt

Om du vill inaktivera den offentliga slutpunkten med hjälp av PowerShell, du kan köra följande kommando (och även Glöm inte att Stäng NSG för den inkommande porten 3342 om du har konfigurerat):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Tillåta trafik för offentlig slutpunkt på den nya nätverkssäkerhetsgruppen

1. Om du har konfigurationssidan för den hanterade instansen som är öppna, navigerar du till den **översikt** fliken. Annars kan gå tillbaka till din **SQL-hanterad instans** resurs. Välj den **virtuella nätverk/undernät** länk som leder dig till konfigurationssidan för virtuella nätverk.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Välj den **undernät** fliken på fönstret vänstra konfiguration av virtuellt nätverk och anteckna den **SÄKERHETSGRUPP** för din hanterade instans.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Gå tillbaka till din resursgrupp som innehåller din hanterade instans. Du bör se den **nätverkssäkerhetsgrupp** namn som anges ovan. Välj namnet för att gå till konfigurationssidan för network security group.

1. Välj den **ingående säkerhetsregler** fliken och **Lägg till** en regel med högre prioritet än den **deny_all_inbound** regel med följande inställningar: </br> </br>

    |Inställning  |Föreslaget värde  |Beskrivning  |
    |---------|---------|---------|
    |**Källa**     |Alla IP-adress eller en tjänsttagg         |<ul><li>Välj Azure Cloud Service Tag för Azure-tjänster som Power BI</li> <li>Använd NAT IP-adressen för din dator eller virtuell dator i Azure</li></ul> |
    |**Källportsintervall**     |*         |Lämna det här alternativet om du till * (alla) som källportar är vanligtvis dynamiskt allokerade och som oförutsägbar |
    |**mål**     |Alla         |Lämna mål som helst att tillåta trafik till hanterad instans-undernät |
    |**Målportsintervall**     |3342         |Omfång målport till 3342, vilket är den offentliga TDS-slutpunkten för hanterad instans |
    |**Protokoll**     |TCP         |Hanterad instans använder TCP-protokollet för TDS |
    |**Åtgärd**     |Tillåt         |Tillåt inkommande trafik till managed instance via den offentliga slutpunkten |
    |**Prioritet**     |1300         |Kontrollera att den här regeln är högre prioritet än den **deny_all_inbound** regel |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 används för offentlig slutpunkt anslutningar till hanterad instans och kan inte ändras i det här läget.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Hämta anslutningssträngen för offentlig slutpunkt för hanterad instans

1. Gå till konfigurationssidan för SQL hanterade instansen har aktiverats för offentlig slutpunkt. Välj den **anslutningssträngar** fliken den **inställningar** konfiguration.
1. Observera att värdnamnet för offentliga slutpunkten kommer i formatet < mi_name >. **offentliga**. < dns_zone >. database.windows.net och att den port som används för anslutningen är 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [med Azure SQL Database-hanterad instans på ett säkert sätt med offentliga slutpunkten](sql-database-managed-instance-public-endpoint-securely.md).