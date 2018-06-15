---
title: SQL-VM anslutningen till Azure Search | Microsoft Docs
description: Aktivera krypterade anslutningar och konfigurera brandväggen att tillåta anslutningar till SQL Server på en Azure-dator (VM) från en indexerare på Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: heidist
ms.openlocfilehash: 7800e83891cb336bb896299b8fd4d6b3ba590178
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366468"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurera en anslutning från en indexerare för Azure Search till SQL Server på en Azure VM
Enligt beskrivningen i [ansluter Azure SQL Database på Azure Sök med hjälp av indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), skapa indexerare mot **SQL Server på Azure Virtual Machines** (eller **SQL Azure Virtual Machines** för kort) stöds av Azure Search men det finns några säkerhetsrelaterade förutsättningar för att ta hand om först. 

**Aktivitetens varaktighet:** cirka 30 minuter, under förutsättning att du redan installerat ett certifikat på den virtuella datorn.

## <a name="enable-encrypted-connections"></a>Aktivera krypterade anslutningar
Azure Search kräver en krypterad kanal för indexeraren begäranden under en offentlig Internetanslutning. Det här avsnittet innehåller steg för att göra detta arbete.

1. Kontrollera egenskaperna för certifikat för att verifiera att ämnesnamnet är fullständigt kvalificerade domännamnet (FQDN) för Azure VM. Du kan använda ett verktyg som CertUtils eller snapin-modulen certifikat för att visa egenskaperna. Får du det fullständiga Domännamnet i VM-tjänsten bladet Essentials avsnitt i den **offentlig IP-adress/DNS-namnetikett** i den [Azure-portalen](https://portal.azure.com/).
   
   * För virtuella datorer som skapats med hjälp av den nyare **Resource Manager** mallen FQDN formateras som `<your-VM-name>.<region>.cloudapp.azure.com`. 
   * För äldre virtuella datorer skapas som en **klassiska** VM, FQDN formateras som `<your-cloud-service-name.cloudapp.net>`. 
2. Konfigurera SQL Server att använda certifikat med hjälp av Registereditorn (regedit). 
   
    Även om SQL Server Configuration Manager används ofta för den här uppgiften, kan du använda den för det här scenariot. Den kommer inte att hitta det importerade certifikatet eftersom det fullständiga Domännamnet för den virtuella datorn på Azure inte matchar FQDN som bestäms av den virtuella datorn (den identifierar domänen som den lokala datorn eller nätverksdomänen som den är ansluten). När namnen inte matchar, kan du använda regedit för att ange certifikatet.
   
   * Bläddra till den här registernyckeln i regedit: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     Den `[MSSQL13.MSSQLSERVER]` del varierar beroende på version och instansnamn. 
   * Ange värdet för den **certifikat** nyckel till den **tumavtrycket** för SSL-certifikat som du har importerat till den virtuella datorn.
     
     Det finns flera sätt att hämta certifikatets tumavtryck vissa bättre än andra. Om du kopierar det från den **certifikat** snapin-modulen i MMC, hämtar du förmodligen upp ett osynligt inledande tecken [som beskrivs i den här supportartikeln](https://support.microsoft.com/kb/2023869/), vilket resulterar i ett fel när en anslutning. Det finns flera lösningar för att korrigera problemet. Den enklaste är att BACKSTEG över och sedan skriva det första tecknet med tumavtrycket för att ta bort det första tecknen i fältet nyckelvärdet i regedit. Du kan också använda ett annat verktyg för att kopiera tumavtrycket.
3. Bevilja behörigheter till kontot. 
   
    Kontrollera att SQL Server-tjänstkontot har beviljats behörighet för den privata nyckeln för SSL-certifikat. Om du missar det här steget startar inte SQL Server. Du kan använda den **certifikat** snapin-modulen eller **CertUtils** för den här uppgiften.
4. Starta om SQL Server-tjänsten.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurera SQL Server-anslutningen i den virtuella datorn
När du har skapat krypterad anslutning krävs för Azure Search finns ytterligare konfigurationssteg inre till SQL Server på virtuella Azure-datorer. Om du inte redan gjort det, är nästa steg att slutföra konfigurationen med hjälp av någon av följande artiklar:

* För en **Resource Manager** VM, se [Anslut till en SQL Server-dator på Azure med hjälp av hanteraren för filserverresurser](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* För en **klassiska** VM, se [Anslut till en SQL Server-dator på Azure klassiska](../virtual-machines/windows/classic/sql-connect.md).

Läs särskilt avsnittet i varje artikel för ”som ansluter via internet”.

## <a name="configure-the-network-security-group-nsg"></a>Konfigurera Nätverkssäkerhetsgrupp (NSG)
Det är inte ovanligt att konfigurera NSG och motsvarande Azure endpoint eller åtkomstkontrollista (ACL) för att göra din Azure VM tillgängliga för andra parter. Risken är att du har gjort detta innan så att dina egna programlogiken till att ansluta till din SQL Azure VM. Det är inte annorlunda för ett Azure Search-anslutning till din SQL Azure VM. 

Med länkarna nedan ger instruktioner för NSG-konfiguration för distribueringar av Virtuella datorer. Följ dessa instruktioner åtkomstkontrollistan en slutpunkt för Azure Search baserat på dess IP-adress.

> [!NOTE]
> Bakgrund, se [vad är en Nätverkssäkerhetsgrupp?](../virtual-network/security-overview.md)
> 
> 

* För en **Resource Manager** VM, se [hur du skapar NSG: er för ARM-distributioner](../virtual-network/tutorial-filter-network-traffic.md). 
* För en **klassiska** VM, se [hur du skapar NSG: er för klassiska distributioner](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

IP-adresser kan utgöra några utmaningar som enkelt kan lösa om du är medveten om problemet och möjliga lösningar. De återstående avsnitten ger rekommendationer för att hantera problem relaterade till IP-adresser i Åtkomstkontrollistan.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Begränsa åtkomsten till tjänsten IP-adress för sökning
Vi rekommenderar starkt att du begränsar åtkomsten till IP-adressen för din söktjänst i Åtkomstkontrollistan i stället för att göra din SQL Azure VM: ar för några anslutningsbegäranden öppet. Du kan enkelt ta reda IP-adress genom att skriva det fullständiga Domännamnet (till exempel `<your-search-service-name>.search.windows.net`) för din söktjänst.

#### <a name="managing-ip-address-fluctuations"></a>Hantera variationer för IP-adress
Om din söktjänst har endast en sökenheten (det vill säga en replik och en partition), ändras IP-adressen under rutinunderhåll tjänsten har startats om upphäva en befintlig ACL med IP-adressen för din söktjänst.

Ett sätt att undvika efterföljande anslutningsfel är att använda fler än en replik och en partition i Azure Search. Då ökar kostnaden, men den också löste problemet för IP-adress. I Azure Search ändras inte IP-adresser när du har mer än en sökning-enhet.

En andra sättet är att tillåta anslutning misslyckas och sedan konfigurera om ACL: er i NSG: N. I genomsnitt kan du förvänta dig IP-adresser och ändra varje några veckor. Den här metoden kan vara användbart för kunder som har kontrollerat indexering på ett ovanligt sätt.

En tredje lönsam (men inte särskilt säkert)-metoden är att ange IP-adressintervall för Azure-regionen där din söktjänst etableras. Listan över IP-adressintervall som offentliga IP-adresser som är allokerade till Azure-resurser som har publicerats på [Azure Datacenter-IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Inkludera IP-adresserna för Azure Search-portal
Om du använder Azure-portalen för att skapa en indexerare måste Azure Search portal logik också åtkomst till din SQL Azure VM under skapandeprocessen. Azure search portal IP-adresser kan hittas genom att skriva `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Nästa steg
Med undan konfiguration kan ange du nu en SQL Server på Azure VM som datakälla för en indexerare för Azure Search. Se [ansluter Azure SQL Database på Azure Sök med hjälp av indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) för mer information.

