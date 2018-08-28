---
title: Azure SQL Database Managed instanskonfiguration av virtuellt nätverk | Microsoft Docs
description: Det här avsnittet beskriver konfigurationsalternativ för ett virtuellt nätverk (VNet) med en Azure SQL Database Managed Instance.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: b17749999f7903746651403c5948933332dbee5d
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047940"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurera ett virtuellt nätverk för Azure SQL Database Managed Instance

Azure SQL Database Managed Instance (förhandsversion) måste distribueras i en Azure [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Den här distributionen gör det möjligt för följande scenarier: 
- Ansluta till en hanterad instans direkt från ett lokalt nätverk 
- Ansluta en hanterad instans till länkad server eller en annan lokala datalager 
- Ansluta en hanterad instans till Azure-resurser  

## <a name="plan"></a>Planera

Planera hur du distribuerar en hanterad instans i virtuellt nätverk med dina svar på följande frågor: 
- Planerar du att distribuera en eller flera hanterade instanser? 

  Antalet instanser som hanteras av anger den minsta storleken på undernätet för att allokera för dina hanterade instanser. Mer information finns i [avgör storleken på undernätet för hanterad instans](#determine-the-size-of-subnet-for-managed-instances). 
- Behöver du distribuera din hanterade instans i ett befintligt virtuellt nätverk eller du skapar ett nytt nätverk? 

   Om du planerar att använda ett befintligt virtuellt nätverk måste du ändra den nätverkskonfigurationen för att hantera din hanterade instans. Mer information finns i [ändra befintligt virtuellt nätverk för hanterad instans](#modify-an-existing-virtual-network-for-managed-instances). 

   Om du tänker skapa nytt virtuellt nätverk finns i [Skapa nytt virtuellt nätverk för hanterad instans](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Krav

För att skapa en hanterad instans måste du anger ett undernät i det virtuella nätverket som uppfyller följande krav:
- **Dedikerade undernät**: undernätet får inte innehålla andra molntjänst som är associerade med den och det får inte vara Gateway-undernätet. Du kan inte skapa Managed Instance i undernät som innehåller resurser än hanterad instans eller lägga till andra resurser i undernätet senare.
- **Inga Nätverkssäkerhetsgrupper är**: undernätet får inte ha en Nätverkssäkerhetsgrupp som är kopplade till den. 
- **Har specifika routningstabellen**: undernätet måste ha en användare väg tabell (UDR) med 0.0.0.0/0 nästa hopp till Internet som den enda vägen tilldelade till den. Mer information finns i [skapar nödvändiga routningstabell och associerar den](#create-the-required-route-table-and-associate-it)
3. **Valfri anpassad DNS**: om anpassad DNS anges i det virtuella nätverket, Azures rekursiva matchare IP-adress (till exempel 168.63.129.16) måste läggas till i listan. Mer information finns i [konfigurera anpassad DNS](sql-database-managed-instance-custom-dns.md).
4. **Tjänsten har inga slutpunkter**: undernätet får inte ha en tjänstslutpunkt som är associerade med den. Kontrollera att tjänsten slutpunkter alternativet inaktiveras när du skapar virtuella nätverk.
5. **Tillräckligt med IP-adresser**: undernätet måste ha minst 16 IP-adresser (rekommenderas minst är 32 IP-adresser). Mer information finns i [avgör storleken på undernätet för hanterade instanser](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Du kan inte distribuera nya Managed Instance om målundernätet inte är kompatibel med alla föregående krav. Målets virtuella nätverket och undernätet måste hållas i enlighet med kraven för hanterad instans (före och efter distributionen), eftersom varje överträdelse kan medföra att instansen att ange felaktigt tillstånd och blir otillgänglig. Återställa från att tillstånd måste du skapa ny instans i ett virtuellt nätverk med efterlevnadsprinciperna för nätverk, återskapa nivå instansdata och Återställ dina databaser. Det här medför betydande driftavbrott för dina program.

Med introduktionen av _avsikt nätverksprincip_, du kan lägga till en nätverkssäkerhetsgrupp (NSG) i ett undernät för hanterad instans när den hanterade instansen har skapats.

Du kan nu använda en NSG för att begränsa IP-adressintervall som program och användare kan söka i och hantera data genom att filtrera trafik som skickas till port 1433. 

> [!IMPORTANT]
> När du konfigurerar NSG-regler som kommer fasthålla åtkomst till port 1433, måste du också att infoga den högsta prioriteten regler för inkommande trafik som visas i tabellen nedan. Annars nätverksprincip avsikt blockerar ändringen som inte är kompatibla.

| NAMN       |PORT                        |PROTOKOLL|KÄLLA           |MÅL|ÅTGÄRD|
|------------|----------------------------|--------|-----------------|-----------|------|
|hantering  |9000, 9003, 1438, 1440, 1452|Alla     |Alla              |Alla        |Tillåt |
|. mi_subnet   |Alla                         |Alla     |MI – UNDERNÄT        |Alla        |Tillåt |
|health_probe|Alla                         |Alla     |AzureLoadBalancer|Alla        |Tillåt |

Routning upplevelsen har också förbättrats så att förutom 0.0.0.0/0 nexthop-typen Internet-väg, kan du nu lägga till UDR för att dirigera trafik skickas till din lokala privata IP-adressintervall via vnet-gateway eller virtuell nätverksinstallation (NVA).

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

## <a name="create-a-new-virtual-network-for-managed-instance-using-azure-resource-manager-deployment"></a>Skapa ett nytt virtuellt nätverk för hanterad instans med Azure Resource Manager-distribution

Det enklaste sättet att skapa och konfigurera ett virtuellt nätverk är att använda Azure Resource Manager-mall för distribution.

1. Logga in på Azure Portal.

2. Använd **distribuera till Azure** knappen för att distribuera virtuella nätverk i Azure-molnet:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Den här knappen öppnas ett formulär som du kan använda för att konfigurera nätverket där du kan distribuera Managed Instance.

  > [!Note]
  > Azure Resource Manager-mallen distribuerar virtuellt nätverk med två undernät. Ett undernät som kallas **ManagedInstances** är reserverad för hanterade instanser och har förkonfigurerade routningstabellen, medan andra undernätet kallas **standard** används för andra resurser som ska få åtkomst till hanterad Instans (till exempel Azure virtuella datorer). Du kan ta bort **standard** undernät om du inte behöver den.

3. Konfigurera nätverksmiljön. Du kan konfigurera parametrar för din nätverksmiljö på följande format:

![Konfigurera azure-nätverk](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

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

- Skapa nödvändiga routningstabellen och associera den: se [skapar nödvändiga routningstabell och associerar den](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md)
- Se en självstudie som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställa en databas från en säkerhetskopia av databasen, [skapar en Azure SQL Database Managed Instance](sql-database-managed-instance-create-tutorial-portal.md).
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md)
