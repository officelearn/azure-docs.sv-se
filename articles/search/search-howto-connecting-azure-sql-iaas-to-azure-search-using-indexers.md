---
title: Virtuell Azure SQL-dator-anslutning för Sök indexering – Azure Search
description: Aktivera krypterade anslutningar och konfigurera brand väggen för att tillåta anslutningar till SQL Server på en virtuell Azure-dator från en indexerare på Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7629750da8f58c2c62f15102b60b5b562689f087
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656707"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurera en anslutning från en Azure Search indexerare till SQL Server på en virtuell Azure-dator
Som anges i [ansluta Azure SQL Database för att Azure Search med hjälp av indexerare kan du](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)skapa indexerare mot **SQL Server på virtuella Azure-datorer** (eller **SQL Azure virtuella datorer** för kort) stöds av Azure Search, men det finns några säkerhetsrelaterade krav för att ta hand om det första. 

Anslutningar från Azure Search till SQL Server på en virtuell dator är en offentlig Internet anslutning. Alla säkerhets åtgärder som du normalt följer för dessa anslutningar gäller även följande:

+ Hämta ett certifikat från en [Provider för certifikat utfärdare](https://en.wikipedia.org/wiki/Certificate_authority#Providers) för det fullständigt kvalificerade domän namnet för SQL Server-instansen på den virtuella Azure-datorn.
+ Installera certifikatet på den virtuella datorn och aktivera och konfigurera sedan krypterade anslutningar på den virtuella datorn med hjälp av anvisningarna i den här artikeln.

## <a name="enable-encrypted-connections"></a>Aktivera krypterade anslutningar
Azure Search kräver en krypterad kanal för alla indexerare begär Anden via en offentlig Internet anslutning. Det här avsnittet innehåller stegen för att utföra det här arbetet.

1. Kontrol lera egenskaperna för certifikatet för att verifiera att ämnes namnet är det fullständigt kvalificerade domän namnet (FQDN) för den virtuella Azure-datorn. Du kan använda ett verktyg som CertUtil eller snapin-modulen certifikat för att visa egenskaperna. Du kan hämta det fullständiga domän namnet från bladet grundläggande för VM-tjänsten i fältet **offentlig IP-adress/DNS-namn etikett** i [Azure Portal](https://portal.azure.com/).
   
   * För virtuella datorer som skapats med den nyare **Resource Manager** -mallen formateras FQDN som`<your-VM-name>.<region>.cloudapp.azure.com`
   * För äldre virtuella datorer som skapats som en **klassisk** virtuell dator formateras FQDN `<your-cloud-service-name.cloudapp.net>`som.

2. Konfigurera SQL Server att använda certifikatet med Registereditorn (regedit). 
   
    Även om Konfigurationshanteraren för SQL Server ofta används för den här uppgiften kan du inte använda den i det här scenariot. Det går inte att hitta det importerade certifikatet eftersom det fullständiga domän namnet för den virtuella datorn i Azure inte matchar FQDN som bestäms av den virtuella datorn (det identifierar domänen som antingen den lokala datorn eller den nätverks domän som den är ansluten till). När namnen inte matchar använder du Regedit för att ange certifikatet.
   
   * I regedit bläddrar du till den här register `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`nyckeln:.
     
     `[MSSQL13.MSSQLSERVER]` Delen varierar beroende på version och instans namn. 
   * Ange värdet för **certifikat** nyckeln till **TUMAVTRYCKET** för det SSL-certifikat som du har IMPORTERAT till den virtuella datorn.
     
     Det finns flera sätt att hämta tumavtrycket, lite bättre än andra. Om du kopierar den från snapin-modulen **certifikat** i MMC kommer du förmodligen att hämta ett osynligt rad matnings tecken [enligt beskrivningen i den här Support artikeln](https://support.microsoft.com/kb/2023869/), vilket resulterar i ett fel när du försöker ansluta. Det finns flera lösningar för att åtgärda det här problemet. Det enklaste är att skriva över och sedan skriva det första tecken i tumavtrycket för att ta bort det inledande tecken i fältet nyckel värde i regedit. Alternativt kan du använda ett annat verktyg för att kopiera tumavtrycket.

3. Bevilja behörighet till tjänst kontot. 
   
    Kontrol lera att SQL Server tjänst kontot har beviljats rätt behörighet för SSL-certifikatets privata nyckel. Om du förbise det här steget kommer SQL Server inte att starta. Du kan använda snapin-modulen **certifikat** eller **CertUtils** för den här uppgiften.
    
4. Starta om SQL Server-tjänsten.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurera SQL Server anslutning på den virtuella datorn
När du har konfigurerat den krypterade anslutningen som krävs av Azure Search finns ytterligare konfigurations steg som är inbyggda för SQL Server på virtuella Azure-datorer. Om du inte redan har gjort det är nästa steg att slutföra konfigurationen med hjälp av någon av följande artiklar:

* En virtuell **Resource Manager** -dator finns i [ansluta till en SQL Server virtuell dator på Azure med Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* En **klassisk** virtuell dator finns i [ansluta till en SQL Server virtuell dator på den klassiska Azure-datorn](../virtual-machines/windows/classic/sql-connect.md).

Läs särskilt avsnittet i varje artikel för "ansluta via Internet".

## <a name="configure-the-network-security-group-nsg"></a>Konfigurera nätverks säkerhets gruppen (NSG)
Det är inte ovanligt att konfigurera NSG och motsvarande Azure-slutpunkt eller Access Control List (ACL) för att göra din virtuella Azure-dator tillgänglig för andra parter. Du har gjort det här innan du kan använda din egen program logik för att ansluta till din SQL Azure virtuella dator. Det är ingen annan för en Azure Search anslutning till din SQL Azure virtuella dator. 

Länkarna nedan innehåller instruktioner om NSG-konfiguration för distribution av virtuella datorer. Använd de här anvisningarna för att ACL en Azure Search-slutpunkt baserat på dess IP-adress.

> [!NOTE]
> I bakgrunden, se [Vad är en nätverks säkerhets grupp?](../virtual-network/security-overview.md)
> 
> 

* En virtuell **Resource Manager** -dator finns i [så här skapar du NSG: er för arm](../virtual-network/tutorial-filter-network-traffic.md)-distributioner. 
* En **klassisk** virtuell dator finns i [så här skapar du NSG: er för klassiska distributioner](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

IP-adresser kan innebära några utmaningar som är lätta att lösa om du är medveten om problemet och möjliga lösningar. I de återstående avsnitten finns rekommendationer för att hantera problem som rör IP-adresser i åtkomst kontrol listan.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Begränsa åtkomsten till Sök tjänstens IP-adress
Vi rekommenderar starkt att du begränsar åtkomsten till Sök tjänstens IP-adress i ACL i stället för att göra dina SQL Azure virtuella datorer breda för alla anslutnings begär Anden. Du kan enkelt ta reda på IP-adressen genom att pinga FQDN (till exempel `<your-search-service-name>.search.windows.net`) för Sök tjänsten.

#### <a name="managing-ip-address-fluctuations"></a>Hantera IP-adressernas fluktuationer
Om Sök tjänsten bara har en Sök enhet (det vill säga en replik och en partition) ändras IP-adressen när rutin tjänsten startas om, vilket gör att en befintlig ACL verifieras med Sök tjänstens IP-adress.

Ett sätt att undvika det efterföljande anslutnings felet är att använda mer än en replik och en partition i Azure Search. Detta ökar kostnaden, men löser även problemet med IP-adressen. I Azure Search ändras inte IP-adresser när du har mer än en Sök enhet.

En andra metod är att tillåta anslutningen att sluta fungera och sedan konfigurera om åtkomst kontrol listorna i NSG. I genomsnitt kan du vänta på att IP-adresser ändras med några veckor. Den här metoden kan vara livskraftig för kunder som använder kontrollerad indexering på ett ovanligt sätt.

En tredje livskraftig (men inte särskilt säker) metod är att ange IP-adressintervallet för den Azure-region där Sök tjänsten är etablerad. Listan över IP-adressintervall från vilka offentliga IP-adresser tilldelas till Azure-resurser publiceras i [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Ta med IP-adresser för Azure Search Portal
Om du använder Azure Portal för att skapa en indexerare behöver Azure Search Portal logik också åtkomst till din SQL Azure virtuella dator när den skapas. Du hittar IP-adresser för Azure Search Portal via Pingning `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Nästa steg
Med konfigurationen av kan du nu ange en SQL Server på den virtuella Azure-datorn som data källa för en Azure Search indexerare. Mer information finns i [ansluta Azure SQL Database till Azure Search med indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .

