---
title: Azure SQL Database Managed Instance Anslutningsarkitektur | Microsoft Docs
description: Den här artikeln innehåller kommunikationsöversikt för Azure SQL Database Managed Instance och beskriver anslutningsarkitektur samt hur de olika komponenterna fungerar därigenom dirigera trafik till den hanterade instansen.
keywords: ''
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.subservice: managed instance
ms.custom: ''
ms.date: 08/16/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.topic: conceptual
ms.openlocfilehash: 54917c6548c7f0bfacad6408732c5619e6346683
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "40177684"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL Database Managed Instance Anslutningsarkitektur 

Den här artikeln innehåller kommunikationsöversikt för Azure SQL Database Managed Instance och beskriver anslutningsarkitektur samt hur de olika komponenterna fungerar därigenom dirigera trafik till den hanterade instansen.  

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
> För att förbättra kundernas expirience och tjänstens tillgänglighet, tillämpar Microsoft avsikt nätverksprincip på Azure-nätverk infrastrukturelement som kan påverka fungerar som den hanterade instansen. Det här är en plattform-mekanism för att kommunicera transparent nätverkskraven för slutanvändare med huvudsakliga målet att förhindra Nätverkskonfigurationsfel och se till normal drift för hanterad instans. När hanterad instans raderas avsikt nätverksprincip tas bort även. 

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuellt kluster anslutningsarkitektur 

Låt oss ta en djupdykning i anslutningsarkitektur för hanterad instans. Följande diagram visar begreppsmässig layouten för det virtuella klustret. 

![anslutningen arkitektur diagram virtuellt kluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienterna ansluter till hanterad instans med det värdnamn som har ett formulär < mi_name >. <clusterid>. database.windows.net. Värddatorns namn motsvarar privata IP-adressen även om den är registrerad i offentliga DNS-zon och är offentligt matchat. 

Privata IP-adressen hör till den hanterade instansen interna belastningsutjämnaren (ILB) som dirigerar trafik till den hanterade instansen Gateway (GW). Eftersom flera hanterade instanser kan potentiellt körs i samma kluster, använder GW värdnamn för hanterad instans för att omdirigera trafik till rätt SQL-motor-tjänst. 

Hanterings-och ansluta till Managed Instance med offentlig slutpunkt som mappar till extern belastningsutjämnare. Trafik dirigeras till noderna bara om togs emot på fördefinierade en uppsättning portar som används uteslutande av Managed Instance komponenterna. All kommunikation mellan komponenterna och Hanteringsplanet är ömsesidigt autentiserad-certifikat. 

## <a name="next-steps"></a>Nästa steg 

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md) 
- Mer information om konfiguration av virtuellt nätverk finns i [hanteras VNet instanskonfiguration](sql-database-managed-instance-vnet-configuration.md). 
- För en Snabbstart Lär dig hur du skapar hanterad instans: 
  - från den [Azure-portalen](sql-database-managed-instance-create-tutorial-portal.md) 
  - med hjälp av [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) 
  - med hjälp av [Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) 
  - med hjälp av [Azure Resource Manager-mall (jumpbox med SSMS som ingår)](https://portal.azure.com/) 

