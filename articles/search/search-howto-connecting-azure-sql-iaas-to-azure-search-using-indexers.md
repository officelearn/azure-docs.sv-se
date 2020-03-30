---
title: Azure SQL VM-anslutning för sökindexering
titleSuffix: Azure Cognitive Search
description: Aktivera krypterade anslutningar och konfigurera brandväggen så att anslutningar till SQL Server på en virtuell Azure-dator (VM) från en indexerare på Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1ab2b7860e8a75da5f8acef2fc4fa54d4b73a30d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256971"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurera en anslutning från en Azure Cognitive Search-indexerare till SQL Server på en virtuell Azure-dator

Som anges i [Ansluta Azure SQL Database till Azure Cognitive Search med indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), skapa indexerare mot **SQL Server på Azure virtuella datorer** (eller SQL Azure **virtuella datorer** för korta) stöds av Azure Cognitive Search, men det finns några säkerhetsrelaterade förutsättningar för att ta hand om först. 

Anslutningar från Azure Cognitive Search till SQL Server på en virtuell dator är en offentlig internetanslutning. Alla säkerhetsåtgärder som du normalt skulle följa för dessa anslutningar gäller även här:

+ Hämta ett certifikat från en [certifikatutfärdare](https://en.wikipedia.org/wiki/Certificate_authority#Providers) för det fullständigt kvalificerade domännamnet för SQL Server-instansen på Den virtuella Azure-datorn.
+ Installera certifikatet på den virtuella datorn och aktivera och konfigurera sedan krypterade anslutningar på den virtuella datorn med hjälp av instruktionerna i den här artikeln.

## <a name="enable-encrypted-connections"></a>Aktivera krypterade anslutningar
Azure Cognitive Search kräver en krypterad kanal för alla indexeringsbegäranden via en offentlig internetanslutning. I det här avsnittet visas stegen för att få det här att fungera.

1. Kontrollera egenskaperna för certifikatet för att verifiera att ämnesnamnet är det fullständigt kvalificerade domännamnet (FQDN) för Den virtuella Azure-datorn. Du kan använda ett verktyg som CertUtils eller snapin-modulen Certifikat för att visa egenskaperna. Du kan hämta FQDN från avsnittet Essentials för VM-tjänsten i fältet **Offentlig IP-adress/DNS-namnetikett** i [Azure-portalen](https://portal.azure.com/).
   
   * För virtuella datorer som skapats med den nyare **resurshanteraren-mallen** formateras FQDN som`<your-VM-name>.<region>.cloudapp.azure.com`
   * För äldre virtuella datorer som skapats som en **klassisk** virtuell `<your-cloud-service-name.cloudapp.net>`dator formateras FQDN som .

2. Konfigurera SQL Server så att certifikatet används med registereditorn (regedit). 
   
    Även om SQL Server Configuration Manager ofta används för den här uppgiften kan du inte använda den för det här scenariot. Det kommer inte att hitta det importerade certifikatet eftersom FQDN för den virtuella datorn på Azure inte matchar FQDN som bestäms av den virtuella datorn (den identifierar domänen som antingen den lokala datorn eller nätverksdomänen som den är ansluten till). När namn inte matchar använder du regedit för att ange certifikatet.
   
   * I regedit bläddrar du till `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`den här registernyckeln: .
     
     Delen `[MSSQL13.MSSQLSERVER]` varierar beroende på versions- och förekomstnamn. 
   * Ange värdet för **certifikatnyckeln** till **tumavtrycket** för TLS/SSL-certifikatet som du importerade till den virtuella datorn.
     
     Det finns flera sätt att få tumavtrycket, vissa bättre än andra. Om du **kopierar** det från snapin-modulen Certifikat i MMC kommer du förmodligen att plocka upp ett osynligt huvudtecken [enligt beskrivningen i den här supportartikeln](https://support.microsoft.com/kb/2023869/), vilket resulterar i ett fel när du försöker ansluta. Det finns flera lösningar för att åtgärda problemet. Det enklaste är att backsteg över och sedan skriva in det första tecknet i tumavtrycket för att ta bort huvudtecknet i nyckelvärdefältet i regedit. Du kan också använda ett annat verktyg för att kopiera tumavtrycket.

3. Bevilja behörigheter till tjänstkontot. 
   
    Kontrollera att SQL Server-tjänstkontot har rätt behörighet för den privata nyckeln till TLS/SSL-certifikatet. Om du förbiser det här steget startar inte SQL Server. Du kan använda snapin-modulen **Certifikat** eller **CertUtils** för den här uppgiften.
    
4. Starta om SQL Server-tjänsten.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurera SQL Server-anslutning i den virtuella datorn
När du har konfigurerat den krypterade anslutning som krävs av Azure Cognitive Search finns det ytterligare konfigurationssteg som är inneboende i SQL Server på virtuella Azure-datorer. Om du inte redan har gjort det är nästa steg att slutföra konfigurationen med någon av dessa artiklar:

* En virtuell **resurshanterare** finns i [Ansluta till en virtuell SQL Server-dator på Azure med Hjälp av Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* En **klassisk** virtuell dator finns i [Ansluta till en virtuell SQL Server-dator på Azure Classic](../virtual-machines/windows/classic/sql-connect.md).

I synnerhet granska avsnittet i varje artikel för "ansluta via Internet".

## <a name="configure-the-network-security-group-nsg"></a>Konfigurera nätverkssäkerhetsgruppen (NSG)
Det är inte ovanligt att konfigurera NSG och motsvarande Azure-slutpunkt eller åtkomstkontrollista (ACL) för att göra din Azure VM tillgänglig för andra parter. Chansen är stor att du har gjort detta tidigare för att tillåta din egen programlogik att ansluta till din VIRTUELLA SQL Azure-dator. Det är inte annorlunda för en Azure Cognitive Search-anslutning till din VIRTUELLA SQL Azure-dator. 

Länkarna nedan innehåller instruktioner om NSG-konfiguration för VM-distributioner. Använd dessa instruktioner för att ACL en Azure Cognitive Search-slutpunkt baserat på dess IP-adress.

> [!NOTE]
> Bakgrund finns i [Vad är en nätverkssäkerhetsgrupp?](../virtual-network/security-overview.md)
> 
> 

* En virtuell **resurshanterare** finns i [Så här skapar du NSG:er för ARM-distributioner](../virtual-network/tutorial-filter-network-traffic.md). 
* En **klassisk** virtuell dator finns i [Så här skapar du NSG:er för klassiska distributioner](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

IP-adressering kan innebära några utmaningar som lätt kan övervinnas om du är medveten om problemet och potentiella lösningar. De återstående avsnitten innehåller rekommendationer för hantering av problem relaterade till IP-adresser i åtkomstkontrollistan.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Begränsa åtkomsten till Den kognitiva azure-sökningen
Vi rekommenderar starkt att du begränsar åtkomsten till IP-adressen för `AzureCognitiveSearch` söktjänsten och IP-adressintervallet för [tjänsttaggen i åtkomstkontrollistan](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistan i åtkomstkontrollistans åtkomst till alla anslutningsbegäranden.

Du kan ta reda på IP-adressen genom att pinga FQDN (till `<your-search-service-name>.search.windows.net`exempel) för din söktjänst.

Du kan ta reda på `AzureCognitiveSearch` IP-adressintervallet [för servicetag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) genom att antingen använda [nedladdningsbara JSON-filer](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) eller via [Api för Service Tag Discovery](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview). IP-adressintervallet uppdateras varje vecka.

#### <a name="managing-ip-address-fluctuations"></a>Hantera fluktuationer i IP-adresser
Om söktjänsten bara har en sökenhet (det vill en replik och en partition) ändras IP-adressen under omstart av rutintjänsten och en befintlig åtkomstkontrollista med söktjänstens IP-adress.

Ett sätt att undvika det efterföljande anslutningsfelet är att använda mer än en replik och en partition i Azure Cognitive Search. Detta ökar kostnaden, men det löser också IP-adress problemet. I Azure Cognitive Search ändras inte IP-adresser när du har mer än en sökenhet.

En andra metod är att tillåta anslutningen att misslyckas och sedan konfigurera om ACL:er i NSG. I genomsnitt kan du förvänta dig att IP-adresser ändras med några veckors mellanrum. För kunder som gör kontrollerad indexering på en sällan förekommande basis, kan detta tillvägagångssätt vara lönsamt.

En tredje genomförbar (men inte särskilt säker) metod är att ange IP-adressintervallet för Azure-regionen där söktjänsten är etablerad. Listan över IP-intervall från vilka offentliga IP-adresser allokeras till Azure-resurser publiceras på [Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Inkludera IP-adresserna för Azure Cognitive Search-portalen
Om du använder Azure-portalen för att skapa en indexerare behöver Azure Cognitive Search portallogik också åtkomst till din SQL Azure-virtuella dator under skapandetiden. Azure Cognitive Search portal IP-adresser `stamp2.search.ext.azure.com`kan hittas genom att pinga .

## <a name="next-steps"></a>Nästa steg
Med konfiguration ur vägen kan du nu ange en SQL Server på Azure VM som datakälla för en Azure Cognitive Search-indexerare. Mer information [finns i Ansluta Azure SQL Database till Azure Cognitive Search med indexerare.](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

