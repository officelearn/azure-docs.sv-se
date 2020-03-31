---
title: Konfigurera offentlig slutpunkt - hanterad instans
description: Lär dig hur du konfigurerar en offentlig slutpunkt för hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256164"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Konfigurera en offentlig slutpunkt för en hanterad Azure SQL Database-instans

Offentlig slutpunkt för en [hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) möjliggör dataåtkomst till din hanterade instans utanför det [virtuella nätverket](../virtual-network/virtual-networks-overview.md). Du kan komma åt din hanterade instans från Azure-tjänster med flera innehavare som Power BI, Azure App Service eller ett lokalt nätverk. Genom att använda den offentliga slutpunkten på en hanterad instans behöver du inte använda en VPN, vilket kan hjälpa till att undvika problem med VPN-dataflöde.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> - Aktivera offentlig slutpunkt för din hanterade instans i Azure-portalen
> - Aktivera offentlig slutpunkt för din hanterade instans med PowerShell
> - Konfigurera säkerhetsgruppen för hanterad instansnätverk så att trafik till den hanterade instansens offentliga slutpunkt
> - Hämta den offentliga slutpunktssträngen för hanterad instans

## <a name="permissions"></a>Behörigheter

På grund av känsligheten för data som finns i en hanterad instans kräver konfigurationen för att aktivera offentlig slutpunkt för hanterad instans en tvåstegsprocess. Denna säkerhetsåtgärd följer åtskillnad mellan arbetsuppgifter (SoD):

- Aktivera offentlig slutpunkt på en hanterad instans måste göras av administratören för hanterade instanser. Administratören för hanterade instanser finns på **översiktssidan** för din SQL-hanterade instansresurs.
- Tillåta trafik med hjälp av en nätverkssäkerhetsgrupp som måste utföras av en nätverksadministratör. Mer information finns i [behörigheter för nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Aktivera offentlig slutpunkt för en hanterad instans i Azure-portalen

1. Starta Azure-portalen på<https://portal.azure.com/.>
1. Öppna resursgruppen med den hanterade instansen och välj den **SQL-hanterade instans** som du vill konfigurera offentlig slutpunkt på.
1. Välj fliken **Virtuellt nätverk** **på** säkerhetsinställningarna.
1. På sidan Konfiguration av virtuellt nätverk väljer du **Aktivera** och sedan ikonen **Spara** för att uppdatera konfigurationen.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Aktivera offentlig slutpunkt för en hanterad instans med PowerShell

### <a name="enable-public-endpoint"></a>Aktivera offentlig slutpunkt

Kör följande PowerShell-kommandon. Ersätt **prenumerations-ID** med ditt prenumerations-ID. Ersätt även **rg-namn** med resursgruppen för den hanterade instansen och ersätt **mi-name** med namnet på den hanterade instansen.

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

Om du vill inaktivera den offentliga slutpunkten med PowerShell kör du följande kommando (och glöm inte att stänga NSG för den inkommande porten 3342 om du har konfigurerat den):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Tillåt offentlig slutpunktstrafik i nätverkssäkerhetsgruppen

1. Om du har konfigurationssidan för den hanterade instansen fortfarande öppen navigerar du till fliken **Översikt.** Annars går du tillbaka till din **SQL-hanterade instansresurs.** Välj länken **Virtuellt nätverk/undernät,** som tar dig till sidan För konfiguration av virtuellt nätverk.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Välj fliken Undernät i den vänstra **konfigurationsfönstret** i det virtuella nätverket och anteckna **säkerhetsgruppen** för din hanterade instans.

    ![mi-vnet-undernät.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Gå tillbaka till resursgruppen som innehåller din hanterade instans. Du bör se namnet **nätverkssäkerhetsgrupp** som anges ovan. Välj namnet som ska gå till konfigurationssidan för nätverkssäkerhetsgruppen.

1. Välj fliken **Inkommande säkerhetsregler** och **Lägg till** en regel som har högre prioritet än **deny_all_inbound-regeln** med följande inställningar: </br> </br>

    |Inställning  |Föreslaget värde  |Beskrivning  |
    |---------|---------|---------|
    |**Källkod**     |Alla IP-adresser eller servicetag         |<ul><li>För Azure-tjänster som Power BI väljer du Azure Cloud Service Tag</li> <li>Använd NAT IP-adress för datorn eller Azure VM</li></ul> |
    |**Källportintervall**     |*         |Lämna detta till * (alla) som källportar är vanligtvis dynamiskt tilldelade och som sådan, oförutsägbara |
    |**Destination**     |Alla         |Lämna mål som möjligt för att tillåta trafik till det hanterade instansundernätet |
    |**Målportintervall**     |3342         |Scopemålport till 3342, som är den hanterade instansen offentlig TDS-slutpunkt |
    |**Protokollet**     |TCP         |Hanterad instans använder TCP-protokoll för TDS |
    |**Åtgärd**     |Tillåt         |Tillåt inkommande trafik till hanterad instans via den offentliga slutpunkten |
    |**Prioritet**     |1300         |Kontrollera att den här regeln har högre prioritet än **regeln deny_all_inbound** |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 används för offentliga slutpunktsanslutningar till hanterad instans och kan inte ändras i det här läget.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Hämta den offentliga slutpunktssträngen för hanterad instans

1. Navigera till konfigurationssidan för SQL-hanterade instanser som har aktiverats för offentlig slutpunkt. Välj fliken **Anslutningssträngar** under konfigurationen **Inställningar.**
1. Observera att det offentliga slutpunktsvärdens namn finns i formatet <mi_name>. **offentliga**.<dns_zone>.database.windows.net och att porten som används för anslutningen är 3342.

    ![mi-offentlig-slutpunkt-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hur du använder Azure SQL Database-hanterad instans på ett säkert sätt med offentlig slutpunkt](sql-database-managed-instance-public-endpoint-securely.md).