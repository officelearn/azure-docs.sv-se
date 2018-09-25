---
title: Azure SQL Database Managed instanskonfiguration av virtuellt nätverk | Microsoft Docs
description: Det här avsnittet beskriver konfigurationsalternativ för ett virtuellt nätverk (VNet) med en Azure SQL Database Managed Instance.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: dfcd61abd9f995a9bb848c23143adb99b0620956
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042167"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurera ett virtuellt nätverk för Azure SQL Database Managed Instance

Azure SQL Database Managed Instance måste distribueras i en Azure [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Den här distributionen gör det möjligt för följande scenarier: 
- Ansluta till en hanterad instans direkt från ett lokalt nätverk 
- Ansluta en hanterad instans till länkad server eller en annan lokala datalager 
- Ansluta en hanterad instans till Azure-resurser  

## <a name="plan"></a>Planera

Planera hur du distribuerar en hanterad instans i virtuellt nätverk med dina svar på följande frågor: 
- Planerar du att distribuera en eller flera hanterade instanser? 

  Antalet instanser som hanteras av anger den minsta storleken på undernätet för att allokera för dina hanterade instanser. Mer information finns i [avgör storleken på undernätet för hanterad instans](#determine-the-size-of-subnet-for-managed-instances). 
- Behöver du distribuera din hanterade instans i ett befintligt virtuellt nätverk eller du skapar ett nytt nätverk? 

   Om du planerar att använda ett befintligt virtuellt nätverk måste du ändra den nätverkskonfigurationen för att hantera din hanterade instans. Mer information finns i [ändra befintligt virtuellt nätverk för hanterad instans](#modify-an-existing-virtual-network-for-managed-instances). 

   Om du tänker skapa nytt virtuellt nätverk finns i [Skapa nytt virtuellt nätverk för hanterad instans](#create-a-new-virtual-network-for-a-managed-instance).

## <a name="requirements"></a>Krav

Om du vill skapa en hanterad instans, skapar du ett dedikerat undernät (hanterad instans-undernät) i det virtuella nätverket som uppfyller följande krav:
- **Dedikerade undernät**: The Managed Instance undernätet får inte innehålla andra molntjänst som är associerade med det och det får inte vara ett Gateway-undernät. Du kommer inte att kunna skapa en hanterad instans i ett undernät som innehåller resurser än Managed Instance och du kan inte senare lägga till andra resurser i undernätet.
- **Kompatibel Nätverkssäkerhetsgrupp (NSG)**: en NSG som är associerade med en hanterad instans-undernätet måste innehålla regler som visas i följande tabeller (obligatorisk inkommande säkerhetsregler och obligatoriska utgående säkerhetsregler) framför andra regler. Du kan använda en NSG för att fullständigt kontrollera åtkomsten till slutpunkten för hanterad instans-data genom att filtrera trafik på port 1433. 
- **Tabellen kompatibla användardefinierad väg (UDR)**: The Managed Instance undernätet måste ha en routningstabell för användare med **0.0.0.0/0 nästa hopp till Internet** som obligatoriska UDR tilldelade till den. Dessutom kan du lägga till en UDR som dirigerar trafik som har lokala privata IP-adressintervall som ett mål via vnet-gateway eller virtuell nätverksinstallation (NVA). 
- **Valfri anpassad DNS**: om en anpassad DNS har angetts på den virtuella nät nätverken nätverk, Azures rekursiva matchare IP-adress (till exempel 168.63.129.16) måste läggas till i listan. Mer information finns i [konfigurera anpassad DNS](sql-database-managed-instance-custom-dns.md). Anpassad DNS-server måste kunna matcha värdnamn i följande domäner och deras underdomäner: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, och *microsoftonline p.com*. 
- **Tjänsten har inga slutpunkter**: The Managed Instance undernätet får inte ha en tjänstslutpunkt som är associerade med den. Kontrollera att tjänsten slutpunkter alternativet inaktiveras när du skapar det virtuella nätverket.
- **Tillräckligt med IP-adresser**: The Managed Instance undernätet måste ha minst 16 IP-adresser (rekommenderas minst är 32 IP-adresser). Mer information finns i [avgör storleken på undernätet för hanterade instanser](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Du kan inte distribuera en ny hanterad instans om målundernätet inte är kompatibel med alla dessa krav. När en hanterad instans skapas en *avsikt nätverksprincip* har tillämpats på undernätet för att förhindra att icke-kompatibla ändringar till konfiguration av nätverk. När den senaste instansen har tagits bort från undernätet på *avsikt nätverksprincip* tas också bort

### <a name="mandatory-inbound-security-rules"></a>Obligatorisk inkommande säkerhetsregler 

| NAMN       |PORT                        |PROTOKOLL|KÄLLA           |MÅL|ÅTGÄRD|
|------------|----------------------------|--------|-----------------|-----------|------|
|hantering  |9000, 9003, 1438, 1440, 1452|TCP     |Alla              |Alla        |Tillåt |
|. mi_subnet   |Alla                         |Alla     |MI – UNDERNÄT        |Alla        |Tillåt |
|health_probe|Alla                         |Alla     |AzureLoadBalancer|Alla        |Tillåt |

### <a name="mandatory-outbound-security-rules"></a>Obligatorisk utgående säkerhetsregler 

| NAMN       |PORT          |PROTOKOLL|KÄLLA           |MÅL|ÅTGÄRD|
|------------|--------------|--------|-----------------|-----------|------|
|hantering  |80, 443, 12000|TCP     |Alla              |Alla        |Tillåt |
|. mi_subnet   |Alla           |Alla     |Alla              |MI – UNDERNÄT  |Tillåt |

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Avgör storleken på undernätet för hanterade instanser

När du skapar en hanterad instans, tilldelar Azure ett antal virtuella datorer beroende på vilken nivå som du valde under etableringen. Eftersom de virtuella datorerna är kopplade till ditt undernät, kräver de IP-adresser. För att säkerställa hög tillgänglighet under normal drift och underhåll, kan Azure allokera fler virtuella datorer. Därför kan är antalet begärda IP-adresser i ett undernät större än antalet instanser som hanteras i det undernätet. 

Enligt design, en hanterad instans måste ha minst 16 IP-adresser i ett undernät och använder upp till 256 IP-adresser. Du kan därmed använda nätmasker /28 /24 när du definierar dina IP-adressintervall för undernätet. 

> [!IMPORTANT]
> Storleken med 16 IP-adresser i undernätet är minst med begränsade möjligheter för att ytterligare Managed Instance-skala ut. Välja undernät med prefixet /27 eller under rekommenderas starkt. 

Om du planerar att distribuera flera hanterade instanser i undernätet och behöver för att optimera på undernätets storlek, använder du parametrarna för att bilda en beräkning: 

- Azure använder fem IP-adresser i undernätet för sin egen behov 
- Varje instans generell användning måste två adresser 
- Varje affärskritisk instans behöver fyra adresserna

**Exempel**: du planerar att ha tre generella och två företag kritiska hanterade instanser. Att innebär att du behöver 5 + 3 * 2 + 2 * 4 = 19 IP-adresser. När IP-intervall är definierade i potensen 2, måste IP-adressintervall 32 (2 ^ 5) IP-adresser. Du måste därför reservera undernätet med nätmask på/27. 

> [!IMPORTANT]
> Beräkningen ovan kommer att bli föråldrad med ytterligare förbättringar. 

## <a name="create-a-new-virtual-network-for-a-managed-instance"></a>Skapa ett nytt virtuellt nätverk för en hanterad instans

Det enklaste sättet att skapa och konfigurera ett virtuellt nätverk är att använda Azure Resource Manager-mall för distribution.

1. Logga in på Azure Portal.

2. Använd **distribuera till Azure** knappen för att distribuera virtuella nätverk i Azure-molnet:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Den här knappen öppnas ett formulär som du kan använda för att konfigurera nätverket där du kan distribuera Managed Instance.

  > [!Note]
  > Azure Resource Manager-mallen distribuerar virtuellt nätverk med två undernät. Ett undernät som kallas **ManagedInstances** är reserverad för hanterade instanser och har förkonfigurerade routningstabellen, medan andra undernätet kallas **standard** används för andra resurser som ska få åtkomst till hanterad Instans (till exempel Azure virtuella datorer). Du kan ta bort **standard** undernät om du inte behöver den.

3. Konfigurera nätverksmiljön. Du kan konfigurera parametrar för din nätverksmiljö på följande format:

![Konfigurera azure-nätverk](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

Du kan ändra namnen på VNet och undernät och justera IP-adressintervall som är kopplade till dina nätverksresurser. När du trycker på ”Köp”-knappen, kommer det här formuläret Skapa och konfigurera din miljö. Om du inte behöver två undernät kan du ta bort standardvärdet. 

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Ändra ett befintligt virtuellt nätverk för hanterade instanser 

Frågor och svar i det här avsnittet visar hur du lägger till en hanterad instans till befintligt virtuellt nätverk. 

**Använder du klassisk eller Resource Manager-distributionsmodellen för det befintliga virtuella nätverket?** 

Du kan bara skapa en hanterad instans i Resource Manager-nätverk. 

**Vill du skapa ett nytt undernät för SQL-hanterad instans eller Använd befintlig?**

Om du vill skapa ny: 

- Beräkna undernätets storlek genom att följa riktlinjerna i den [avgör storleken på undernätet för hanterade instanser](#determine-the-size-of-subnet-for-managed-instances) avsnittet.
- Följ stegen i [lägga till, ändra eller ta bort ett virtuellt nätverksundernät](../virtual-network/virtual-network-manage-subnet.md). 
- Skapa en routingtabell som innehåller en enda post **0.0.0.0/0**, enligt nästa hopp-Internet och koppla den till undernätet för den hanterade instansen.  

Om du vill skapa en hanterad instans i ett befintligt undernät, rekommenderar vi följande PowerShell-skript för att förbereda undernätet.
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
Förberedelse av undernät görs i tre enkla steg:

- Verifiera – valda virtuella: inget delat och undernät verifieras för hanterad instans nätverkskraven
- Bekräfta – användare visas en uppsättning ändringar som görs för att förbereda undernät för hanterad instans-distribution och bad om medgivande
- Förbereda – virtuellt nätverk och undernät har konfigurerats korrekt

**Har du anpassad DNS-server konfigurerad?** 

Om Ja, gå [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md). 

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md)
- Se en självstudie som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställa en databas från en säkerhetskopia av databasen, [skapar en Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md)
