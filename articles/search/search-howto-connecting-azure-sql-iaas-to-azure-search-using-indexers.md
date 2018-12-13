---
title: Söka i Azure SQL VM anslutning till virtuell dator för sökindexering - Azure
description: Aktivera krypterade anslutningar och konfigurera brandväggen att tillåta anslutningar till SQL Server på en Azure-dator (VM) från en indexerare på Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5f04c98e1337c2b65c9e0bc8401dd6045a84021e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312042"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurera en anslutning från en Azure Search-indexerare till SQL Server på en Azure VM
Enligt vad som anges i [ansluter Azure SQL Database till Azure Search med indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), skapa indexerare mot **SQL Server på Azure Virtual Machines** (eller **SQL Azure virtuella datorer** för kort) stöds med Azure Search, men det finns några säkerhetsrelaterade förutsättningar för att ta hand om först. 

**Uppgiftens varaktighet:** Cirka 30 minuter, installerat förutsatt att du redan ett certifikat på den virtuella datorn.

## <a name="enable-encrypted-connections"></a>Aktivera krypterade anslutningar
Azure Search kräver en krypterad kanal för alla indexerare förfrågningar via en offentlig Internetanslutning. Det här avsnittet innehåller steg för att göra detta arbete.

1. Kontrollera egenskaperna för certifikat för att verifiera namnet på certifikatmottagaren är det fullständigt kvalificerade domännamnet (FQDN) för den virtuella Azure-datorn. Du kan använda ett verktyg som CertUtils eller snapin-modulen certifikat för att visa egenskaperna. Du kan hämta det fullständiga Domännamnet från VM-tjänsten bladet Essentials-avsnittet i den **offentliga IP-adress/DNS-namnetikett** fältet i den [Azure-portalen](https://portal.azure.com/).
   
   * För virtuella datorer som skapas med hjälp av den nyare **Resource Manager** mall, FQDN är formaterade som `<your-VM-name>.<region>.cloudapp.azure.com`. 
   * För äldre virtuella datorer som skapas som en **klassiska** VM, FQDN är formaterade som `<your-cloud-service-name.cloudapp.net>`. 
2. Konfigurera SQL Server att använda certifikat med hjälp av Registereditorn (regedit). 
   
    Även om SQL Server Configuration Manager används ofta för den här uppgiften, kan du inte använda den för det här scenariot. Det inte att hitta det importerade certifikatet eftersom det fullständiga Domännamnet för den virtuella datorn på Azure inte matchar det fullständiga Domännamnet som bestäms av den virtuella datorn (den identifierar domänen som den lokala datorn eller nätverksdomän som den är ansluten). När namnen inte matchar, kan du använda regedit för att ange certifikatet.
   
   * I regedit, bläddrar du till den här registernyckeln: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     Den `[MSSQL13.MSSQLSERVER]` del varierar beroende på version och instansnamn. 
   * Ange värdet för den **certifikat** avgörande för att den **tumavtryck** för SSL-certifikat som du har importerat till den virtuella datorn.
     
     Det finns flera sätt att hämta certifikatets tumavtryck lite bättre än andra. Om du kopierar det från den **certifikat** snapin-modulen i MMC, hämtar du förmodligen upp ett osynligt ledande tecken [enligt beskrivningen i den här supportartikeln](https://support.microsoft.com/kb/2023869/), vilket resulterar i ett fel när du försöker utföra en anslutning . Det finns flera sätt för att korrigera problemet. Den enklaste är att BACKSTEG över och sedan skriva om det första tecknet i tumavtryck för att ta bort det första tecknen i fältet nyckelvärdet i regedit. Du kan också använda ett annat verktyg för att kopiera tumavtrycket.
3. Bevilja behörigheter till kontot. 
   
    Kontrollera att SQL Server-tjänstkontot har beviljats behörighet för den privata nyckeln för SSL-certifikat. Om du missar det här steget startar inte SQL Server. Du kan använda den **certifikat** snapin-modulen eller **CertUtils** för den här uppgiften.
4. Starta om SQL Server-tjänsten.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurera SQL Server-anslutningen i den virtuella datorn
När du har konfigurerat krypterad anslutning krävs av Azure Search finns ytterligare konfigurationssteg inbyggd i SQL Server på virtuella Azure-datorer. Om du inte redan gjort det, är nästa steg att slutföra konfigurationen med hjälp av någon av följande artiklar:

* För en **Resource Manager** VM, se [Anslut till en SQL Server-dator på Azure med hjälp av Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* För en **klassiska** VM, se [Anslut till en SQL Server-dator på Azure klassiska](../virtual-machines/windows/classic/sql-connect.md).

Läs särskilt avsnittet i varje artikel för att ”ansluta via internet”.

## <a name="configure-the-network-security-group-nsg"></a>Konfigurera någon Nätverkssäkerhetsgrupp (NSG)
Det är inte ovanligt att konfigurera NSG och motsvarande Azure-slutpunkt eller åtkomstkontrollistan (ACL) för att göra dina virtuella Azure-datorer som är tillgänglig för andra parter. Risken är att du har gjort tidigare så att dina egna programlogiken till att ansluta till din SQL Azure VM. Den skiljer sig för en Azure Search-anslutning till den virtuella datorn för SQL Azure. 

Länkarna nedan innehåller anvisningar om NSG-konfiguration för VM-distributioner. Använd dessa instruktioner för att ACL: en Azure Search-slutpunkten baserat på dess IP-adress.

> [!NOTE]
> Bakgrundsinformation finns i [vad är en Nätverkssäkerhetsgrupp?](../virtual-network/security-overview.md)
> 
> 

* För en **Resource Manager** VM, se [skapa NSG: er för ARM-distributioner](../virtual-network/tutorial-filter-network-traffic.md). 
* För en **klassiska** VM, se [skapa NSG: er för klassisk distribution](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

IP-adresser kan medföra några utmaningar som enkelt kan lösa om du är medveten om problem och lösningar. De återstående avsnitten innehåller rekommendationer för att hantera problem som rör IP-adresser i Åtkomstkontrollistan.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Begränsa åtkomsten till search service IP-adress
Vi rekommenderar starkt att du begränsar åtkomsten till IP-adressen för din söktjänst i Åtkomstkontrollistan i stället för att göra dina SQL Azure virtuella datorer helt öppen för alla anslutningsbegäranden. Kan du enkelt se IP-adressen genom att skicka pingsignaler FQDN (till exempel `<your-search-service-name>.search.windows.net`) för search-tjänsten.

#### <a name="managing-ip-address-fluctuations"></a>Hantera IP-adress variationer
Om söktjänsten har endast en sökenheten (det vill säga en replik och en partition), ändras IP-adressen under rutinmässig tjänsten startas om, ogiltigförklara en befintlig ACL med IP-adressen för din söktjänst.

Ett sätt att undvika efterföljande anslutningsfel är att använda fler än en replik och en partition i Azure Search. Detta ökar kostnaden, men den också löste problemet för IP-adress. I Azure Search ändras inte IP-adresser när du har mer än en sökenheten.

En andra metoden är att anslutningen misslyckas och sedan konfigurera om ACL: er i NSG: N. I genomsnitt kan du förvänta dig IP-adresser för att ändra varje par veckor. Den här metoden kan vara användbara för kunder som gör kontrollerad indexering per sporadiska. Detta kan.

En tredje lönsamma (men inte särskilt säkert)-metod är att ange IP-adressintervall för Azure-region där din söktjänst har etablerats. Listan över IP-adressintervall som offentliga IP-adresser allokeras till Azure-resurser finns i avsnittet [Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Inkludera IP-adresser för Azure Search-portalen
Om du använder Azure-portalen för att skapa en indexerare, måste Azure Search portal logic också åtkomst till din SQL Azure virtuell dator under skapandeprocessen. Azure search portal IP-adresser kan hittas genom att skicka pingsignaler `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Nästa steg
Med undan den konfigurationen kan ange du nu en SQL Server på Azure VM som datakälla för en Azure Search-indexerare. Se [ansluter Azure SQL Database till Azure Search med indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) för mer information.

