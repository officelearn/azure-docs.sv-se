---
title: Azure SQL Database Managed Instance Anslutningsarkitektur | Microsoft Docs
description: Den här artikeln innehåller kommunikationsöversikt för Azure SQL Database Managed Instance och beskriver anslutningsarkitektur samt hur de olika komponenterna fungerar därigenom dirigera trafik till den hanterade instansen.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: 964f91f412645e141ca003d511480f6f6eb438a3
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343316"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL Database Managed Instance Anslutningsarkitektur

Den här artikeln innehåller kommunikationsöversikt för Azure SQL Database Managed Instance och beskriver anslutningsarkitektur samt hur de olika komponenterna fungerar därigenom dirigera trafik till den hanterade instansen.  

Azure SQL Database Managed Instance är placerad i virtuella Azure-nätverket och undernätet dedikerad till hanterade instanser. Den här distributionen gör det möjligt för följande scenarier: 
- Skydda privata IP-adressen.
- Ansluta till en hanterad instans direkt från ett lokalt nätverk.
- Ansluta en hanterad instans till länkad server eller en annan lokala datalager.
- Ansluta en hanterad instans till Azure-resurser.

## <a name="communication-overview"></a>Översikt över tjänstkommunikation

Följande diagram visar de enheter som ansluter till Managed Instance samt resurser som hanterad instans har kontakta för att fungera korrekt.

![anslutningen arkitektur entiteter](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Kommunikation som visas längst ned i diagrammet representerar kundprogram och verktyg ansluter till Managed Instance som datakälla.  

Eftersom Managed Instance är plattform-som-en-services (PaaS) hanterar Microsoft-produkt den här tjänsten med hjälp av automatisk agenter (hantering, distribution och underhåll) baserat på data telemetriströmmar. Eftersom Managed Instance är ensamt ansvar för Microsoft har kunder inte åtkomst till Managed Instance virtuellt kluster datorer via RDP.

Vissa SQL Server-åtgärder som initieras av användare eller program kan kräva Managed Instance kan interagera med plattformen. Ett ärende är att skapa en hanterad instans-databas – en resurs som exponeras via portalen, PowerShell och Azure CLI.

Hanterade instansen är beroende av andra Azure-tjänster för dess fungerar (till exempel Azure Storage för säkerhetskopiering, Azure Service Bus för telemetri, Azure AD för autentisering, Azure Key Vault för transparent Datakryptering och så vidare) och initierar anslutningar till dem enlighet med detta.

All kommunikation som anges ovan, är krypterat och signerat med certifikat. Om du vill se till att kommunicerande parterna är betrodd, verifierar Managed Instance ständigt dessa certifikat genom att kontakta certifikatutfärdare. Om certifikaten har återkallats eller hanterad instans kunde inte verifiera dem, stänger anslutningar för att skydda data.

## <a name="high-level-connectivity-architecture"></a>Övergripande anslutningsarkitektur

Managed Instance är en uppsättning tjänstkomponenter finns på en dedikerad uppsättning isolerade virtuella datorer som körs i det virtuella undernätet för kunden och skapa ett virtuellt kluster på en hög nivå.

Flera hanterade instanser kan finnas i ett enda virtuellt kluster. Klustret är automatiskt utökat eller om det behövs när kunden ändras antalet instanser som etablerats i undernätet.

Kundprogram kunde ansluta till databaser för hanterad instans, fråge- och endast om de körs i det virtuella nätverket eller peerkopplade virtuella nätverk eller VPN / Express Route anslutet nätverk med hjälp av slutpunkt med privat IP-adress.  

![Arkitekturdiagram för anslutning](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft-tjänster för hantering och distribution kör utanför det virtuella nätverket så att anslutningen mellan Managed Instance och Microsoft-tjänster går över slutpunkter med offentliga IP-adresser. När hanterad instans skapar utgående anslutning, på mottagande sidan verkar det som det kommer från den här offentliga IP-Adressen på grund av Network adress Translation (NAT).

Hantering av trafik flödar genom kundens virtuella nätverk. Som innebär att element i virtuell nätverksinfrastruktur påverkar och kan skada hanteringstrafik orsaka instans att ange felaktigt tillstånd och blir otillgänglig.

> [!IMPORTANT]
> För att förbättra kundupplevelsen och tjänsttillgänglighet, tillämpar Microsoft avsikt nätverksprincip på Azure-nätverk infrastrukturelement som kan påverka hanterad instans fungerar. Det här är en plattform-mekanism för att kommunicera transparent nätverkskraven för slutanvändare med huvudsakliga målet att förhindra Nätverkskonfigurationsfel och se till normal drift för hanterad instans. När hanterad instans raderas avsikt nätverksprincip tas bort även.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuellt kluster anslutningsarkitektur

Låt oss ta en djupdykning i anslutningsarkitektur för hanterad instans. Följande diagram visar begreppsmässig layouten för det virtuella klustret.

![anslutningen arkitektur diagram virtuellt kluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienterna ansluter till hanterad instans med det värdnamn som har ett formulär `<mi_name>.<dns_zone>.database.windows.net`. Värddatorns namn motsvarar privata IP-adressen även om den är registrerad i offentliga DNS-zon och är offentligt matchat. Den `zone-id` genereras automatiskt när klustret har skapats. Om ett nyskapat kluster värd för en sekundär hanterad instans, delar dess zons-id med det primära klustret. Mer information finns i [automatisk redundans grupper](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)

Privata IP-adressen hör till den hanterade instansen interna belastningsutjämnaren (ILB) som dirigerar trafik till den hanterade instansen Gateway (GW). Eftersom flera hanterade instanser kan potentiellt körs i samma kluster, använder GW värdnamn för hanterad instans för att omdirigera trafik till rätt SQL-motor-tjänst.

Hanterings-och ansluta till Managed Instance med [hanteringsslutpunkten](#management-endpoint) som mappar till extern belastningsutjämnare. Trafiken dirigeras till noderna endast om togs emot på en fördefinierad uppsättning portar som används uteslutande av Managed Instance komponenterna. Inbyggda brandväggen på noderna är konfigurerad för att tillåta trafik enbart från Microsoft specifika IP-intervall. All kommunikation mellan komponenterna och Hanteringsplanet är ömsesidigt autentiserad-certifikat.

## <a name="management-endpoint"></a>Hanteringsslutpunkt

Det virtuella klustret i Azure SQL Database Managed Instance innehåller en hanteringsslutpunkt som Microsoft använder för att hantera den hanterade instansen. Hanteringsslutpunkten skyddas med inbyggda brandväggen på nätverket och det ömsesidig certifikatverifiering på programnivå. Du kan [hitta ip-adress för hantering av slutpunkten](sql-database-managed-instance-find-management-endpoint-ip-address.md).

När anslutningar öppnas från inuti den hanterade instansen (säkerhetskopiering, granskningsloggen) visas den som trafiken kommer ifrån från management endpoint offentlig IP-adress. Du kan begränsa åtkomst till offentliga tjänster från hanterad instans genom att ställa in brandväggsregler för att tillåta hanterad instans-IP-adressen. Hitta mor einformation om den metod som kan [verifiera Managed Instance inbyggda brandväggen](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Detta gäller inte ställa in brandväggsregler för Azure-tjänster som finns i samma region som hanterad instans som Azure-plattformen har en optimering för trafik som går mellan de tjänster som är samordnad.

## <a name="network-requirements"></a>Nätverkskrav

Du kan distribuera hanterade instanser i ett dedikerat undernät (hanterad instans-undernät) i det virtuella nätverket som uppfyller följande krav:
- **Dedikerade undernät**: Managed Instance undernätet får inte innehålla andra molntjänst som är associerade med det och det får inte vara ett Gateway-undernät. Du kommer inte att kunna skapa en hanterad instans i ett undernät som innehåller resurser än Managed Instance och du kan inte senare lägga till andra resurser i undernätet.
- **Kompatibla Nätverkssäkerhetsgrupp (NSG)**: En NSG som är associerade med en hanterad instans-undernätet måste innehålla regler som visas i följande tabeller (obligatorisk inkommande säkerhetsregler och obligatoriska utgående säkerhetsregler) framför andra regler. Du kan använda en NSG för att fullständigt kontrollera åtkomsten till slutpunkten för hanterad instans-data genom att filtrera trafik på port 1433. 
- **Tabellen kompatibla användardefinierad väg (UDR)**: Hanterad instans-undernätet måste ha en routningstabell för användare med **0.0.0.0/0 nästa hopp till Internet** som obligatoriska UDR tilldelade till den. Dessutom kan du lägga till en UDR som dirigerar trafik som har lokala privata IP-adressintervall som ett mål via vnet-gateway eller virtuell nätverksinstallation (NVA). 
- **Valfri anpassad DNS**: Om en anpassad DNS har angetts i det virtuella nätverket, måste Azures rekursiva matchare IP-adress (till exempel 168.63.129.16) läggas till i listan. Mer information finns i [konfigurera anpassad DNS](sql-database-managed-instance-custom-dns.md). Anpassad DNS-server måste kunna matcha värdnamn i följande domäner och deras underdomäner: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, och *microsoftonline p.com*. 
- **Tjänsten har inga slutpunkter**: Managed Instance undernätet får inte ha en tjänstslutpunkt som är associerade med den. Kontrollera att tjänsten slutpunkter alternativet inaktiveras när du skapar det virtuella nätverket.
- **Tillräckligt med IP-adresser**: Hanterad instans-undernätet måste ha minst 16 IP-adresser (rekommenderas minst är 32 IP-adresser). Mer information finns i [avgör storleken på undernätet för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md). Du kan distribuera hanterade instanser i [det befintliga nätverket](sql-database-managed-instance-configure-vnet-subnet.md) när du konfigurerar den att uppfylla [Managed Instance nätverkskraven](#network-requirements), eller skapa en [nya nätverk och undernät](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Du kan inte distribuera en ny hanterad instans om målundernätet inte är kompatibel med alla dessa krav. När en hanterad instans skapas en *avsikt nätverksprincip* har tillämpats på undernätet för att förhindra att icke-kompatibla ändringar till konfiguration av nätverk. När den senaste instansen har tagits bort från undernätet på *avsikt nätverksprincip* tas också bort

### <a name="mandatory-inbound-security-rules"></a>Obligatorisk inkommande säkerhetsregler 

| Namn       |Port                        |Protokoll|Källa           |Mål|Åtgärd|
|------------|----------------------------|--------|-----------------|-----------|------|
|hantering  |9000, 9003, 1438, 1440, 1452|TCP     |Alla              |Alla        |Tillåt |
|. mi_subnet   |Alla                         |Alla     |MI – UNDERNÄT        |Alla        |Tillåt |
|health_probe|Alla                         |Alla     |AzureLoadBalancer|Alla        |Tillåt |

### <a name="mandatory-outbound-security-rules"></a>Obligatorisk utgående säkerhetsregler 

| Namn       |Port          |Protokoll|Källa           |Mål|Åtgärd|
|------------|--------------|--------|-----------------|-----------|------|
|hantering  |80, 443, 12000|TCP     |Alla              |Alla        |Tillåt |
|. mi_subnet   |Alla           |Alla     |Alla              |MI – UNDERNÄT  |Tillåt |

  > [!Note]
  > Även om obligatoriska inkommande säkerhetsregler som tillåter trafik från _alla_ källa på portar 9000, 9003, 1438, 1440, 1452 portarna skyddas av inbyggda brandvägg. Detta [artikeln](sql-database-managed-instance-find-management-endpoint-ip-address.md) visar hur du kan identifiera hantering slutpunktens IP-adress och kontrollera brandväggsregler. 

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md)
- Lär dig hur du [konfigurera nya VNet](sql-database-managed-instance-create-vnet-subnet.md) eller [konfigurera befintliga VNet](sql-database-managed-instance-configure-vnet-subnet.md) där du kan distribuera hanterade instanser.
- [Beräkna ut storleken på undernätet](sql-database-managed-instance-determine-size-vnet-subnet.md) där du ska distribuera hanterade instanser. 
- För en Snabbstart Lär dig hur du skapar hanterad instans:
  - Från den [Azure-portalen](sql-database-managed-instance-get-started.md)
  - med hjälp av [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - med hjälp av [Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - med hjälp av [Azure Resource Manager-mall (jumpbox med SSMS som ingår)](https://portal.azure.com/)
