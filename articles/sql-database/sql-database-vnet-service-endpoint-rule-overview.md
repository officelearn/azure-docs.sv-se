---
title: Virtuella nätverksslutpunkter och regler för Azure SQL Database | Microsoft Docs
description: Markera ett undernät som ett virtuellt nätverk tjänstslutpunkten. Sedan slutpunkten som en regel för virtuellt nätverk i åtkomstkontrollistan Azure SQL Database. Du SQL-databasen sedan godkänner kommunikation från alla virtuella datorer och andra noder i undernät.
services: sql-database
ms.service: sql-database
author: MightyPen
manager: craigg
ms.custom: VNet Service endpoints
ms.topic: article
ms.date: 03/15/2018
ms.reviewer: genemi
ms.author: dmalik
ms.openlocfilehash: 6037659eb419a785b01d4cbb6a2428cbd7f852da
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Använd virtuella nätverk slutpunkter och regler för Azure SQL Database

*Virtuellt Nätverksregler* är en säkerhetsfunktion i brandväggen som styr om din Azure SQL Database-server ska acceptera meddelanden som skickas från särskilt undernät i virtuella nätverk. Den här artikeln förklarar varför funktionen virtuellt nätverk regeln ibland är det bästa alternativet för att tillåta kommunikation till Azure SQL Database på ett säkert sätt.

Att skapa en regel för virtuellt nätverk, det måste först vara en [virtuellt nätverk tjänstslutpunkten] [ vm-virtual-network-service-endpoints-overview-649d] för regeln för att referera till.


#### <a name="how-to-create-a-virtual-network-rule"></a>Så här skapar du en regel för virtuellt nätverk

Om du bara skapar en regel för virtuellt nätverk, du kan gå vidare till steg och förklaring [senare i den här artikeln](#anchor-how-to-by-using-firewall-portal-59j).






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Virtuellt nätverk:** du kan ha virtuella nätverk som är associerade med din Azure-prenumeration.

**Undernät:** ett virtuellt nätverk innehåller **undernät**. Alla virtuella Azure-datorer (VM) som du har har tilldelats till undernät. Ett undernät kan innehålla flera virtuella datorer eller andra compute-noder. Compute-noder som är utanför det virtuella nätverket inte kan komma åt ditt virtuella nätverk såvida du inte konfigurerar din säkerhet för att tillåta åtkomst.

**Virtuella nätverk tjänstslutpunkten:** A [virtuellt nätverk tjänstslutpunkten] [ vm-virtual-network-service-endpoints-overview-649d] är ett undernät vars egenskapsvärden innehåller en eller flera formella Azure-tjänst-typnamn. I den här artikeln vi vill ange namnet på **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas.

**Virtuellt nätverk regel:** en regel för virtuellt nätverk för SQL Database-server är ett undernät som anges i åtkomstkontrollistan (ACL) för SQL Database-server. För att vara i Åtkomstkontrollistan för SQL-databas, undernätet måste innehålla den **Microsoft.Sql** typnamn.

En regel för virtuellt nätverk visar SQL Database-server för att acceptera kommunikation från varje nod i undernätet.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Fördelarna med en regel för virtuellt nätverk

Tills du vidta åtgärder kommunicera de virtuella datorerna på dina undernät inte med SQL-databasen. En åtgärd som upprättar kommunikation är att skapa en regel för virtuellt nätverk. Grund för att välja metod för VNet-regeln kräver en Jämför och kontrast diskussion som involverar konkurrerande säkerhetsalternativ som erbjuds av brandväggen.

#### <a name="a-allow-access-to-azure-services"></a>A. Tillåt åtkomst till Azure-tjänster

Fönstret brandväggen har en **på/av** knapp med etiketten **Tillåt åtkomst till Azure-tjänster**. Den **ON** inställningen tillåter kommunikation från alla Azure-IP-adresser och undernät för alla Azure. Dessa Azure IP-adresser eller undernät kan inte ägas av du. Detta **ON** inställningen är förmodligen mer öppna än du vill att SQL-databasen ska vara. Funktionen virtuellt nätverk regeln erbjuder mycket avancerat hjälpmedel granulär kontroll.

#### <a name="b-ip-rules"></a>B. IP-regler

SQL Database-brandvägg kan du ange IP-adressintervall som accepteras kommunikation till SQL-databasen. Den här metoden fungerar bra för stabil IP-adresser som ligger utanför Azure privata nätverk. Men många noder i Azure privata nätverk har konfigurerats med *dynamiska* IP-adresser. Dynamiska IP-adresser kan ändras, till exempel när den virtuella datorn startas om. Det vore fluga att ange en dynamisk IP-adress i en brandväggsregel i en produktionsmiljö.

Du kan rädda IP-alternativet genom att hämta en *Statiska* IP-adress för den virtuella datorn. Mer information finns i [Konfigurera privat IP-adresser för en virtuell dator med hjälp av Azure portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Men den statiska IP-metoden kan bli svårt att hantera och det är kostsamma när du är klar i större skala. Virtuella nätverk är enklare att skapa och hantera.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. Kan inte ännu har SQL-databasen i ett undernät

Om din Azure SQL Database-server är en nod i ett undernät i det virtuella nätverket, kan alla noder i det virtuella nätverket kommunicera med SQL-databasen. I det här fallet kan dina virtuella datorer kommunicera med SQL-databas utan att behöva några regler för virtuellt nätverk eller IP-regler.

Men från och med September 2017 Azure SQL Database-tjänsten är ännu inte bland de tjänster som kan tilldelas till ett undernät.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Information om regler för virtuellt nätverk

Det här avsnittet beskrivs flera detaljer om regler för virtuellt nätverk.

#### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje virtuellt nätverk tjänstslutpunkten gäller endast en Azure-region. Slutpunkten kan inte andra regioner att godta kommunikation från undernätet.

Alla virtuella nätverk regeln är begränsad till den region som sin underliggande slutpunkt gäller.

#### <a name="server-level-not-database-level"></a>Servernivå kan inte databasnivå

Varje virtuellt nätverk regeln gäller för hela Azure SQL Database servern, inte bara till en viss databas på servern. Med andra ord regeln virtuellt nätverk på servernivå inte på databas-nivå.

- Däremot kan IP-regler tillämpas på båda nivåerna.

#### <a name="security-administration-roles"></a>Säkerhetsroller för administration

Det finns en uppdelning av säkerhetsroller i administrationen av slutpunkter för virtuellt nätverk. Åtgärd krävs från var och en av följande roller:

- **Nätverket Admin:** &nbsp; aktivera slutpunkten.
- **Databasen Admin:** &nbsp; uppdatera åtkomstkontrollistan (ACL) för att lägga till det angivna undernätet i SQL Database-servern.

*RBAC alternativ:*

Roller i nätverket Admin och databasen Admin har mer än vad som behövs för att hantera virtuella Nätverksregler. Endast en del av deras funktioner krävs.

Du har möjlighet att använda [rollbaserad åtkomstkontroll (RBAC)] [ rbac-what-is-813s] i Azure för att skapa en anpassad roll som har den nödvändiga delmängden av funktioner. Den anpassade rollen som kan användas i stället för som omfattar nätverk Admin eller administratören för databasen. Ytan på din sårbart är lägre om du lägger till en användare till en anpassad roll, jämfört med att lägga till användaren i de andra två huvudsakliga administratörsroller.

> [!NOTE]
> I vissa fall är Azure SQL-databasen och VNet-undernätet för olika prenumerationer. I dessa fall måste du kontrollera följande konfigurationer:
> - Båda prenumerationer måste vara i samma Azure Active Directory-klienten.
> - Användaren har behörigheterna som krävs för att initiera åtgärder, till exempel aktivera slutpunkter och lägga till ett VNet-undernät i den angivna servern.

## <a name="limitations"></a>Begränsningar

För Azure SQL Database har funktionen virtuellt nätverk regler följande begränsningar:

- Ett webbprogram kan mappas till en privat IP-adress i ett VNet/undernät. Även om Tjänsteslutpunkter är aktiverade från det angivna VNet/undernätet, har anslutningar från Webbappen till servern en Azure offentliga IP-källa, inte en VNet /-undernätet källa. Om du vill tillåta anslutningar från en Webbapp till en server som har brandväggsregler för virtuella nätverk måste du **ge alla Azure-tjänster** på servern.

- Varje virtuellt nätverk regel refererar till ett undernät i brandväggen för SQL-databas. Dessa refererade undernät måste finnas i samma geografiska region som är värd för SQL-databasen.

- Varje Azure SQL Database-server kan ha upp till 128 ACL-posterna för alla angivna virtuella nätverket.

- Virtuellt Nätverksregler gäller bara för Azure Resource Manager virtuella nätverk. och inte [klassiska distributionsmodellen] [ arm-deployment-model-568f] nätverk.

- Aktivering av virtuella nätverksslutpunkter till Azure SQL Database kan också slutpunkter för MySQL och PostgreSQL Azure-tjänster. Men med slutpunkter på misslyckas försöker att ansluta från slutpunkterna till din MySQL eller PostgreSQL instanser.
    - Den underliggande orsaken är att MySQL och PostgreSQL för närvarande inte stöder ACLing.

- IP-adressintervall gäller följande nätverk i brandväggen, men gör inte det virtuella Nätverksregler:
    - [Plats-till-plats (S2S) virtuellt privat nätverk (VPN)][vpn-gateway-indexmd-608y]
    - Lokalt [ExpressRoute][expressroute-indexmd-744v]

#### <a name="considerations-when-using-service-endpoints"></a>Att tänka på när du använder Tjänsteslutpunkter
Granska följande när du använder Tjänsteslutpunkter för Azure SQL Database:

- **Utgående till Azure SQL Database offentliga IP-adresser krävs**: Nätverkssäkerhetsgrupper (NSG: er) måste öppnas för Azure SQL Database IP-adresser som tillåter anslutningar. Du kan göra detta med hjälp av NSG [Service taggar](../virtual-network/security-overview.md#service-tags) för Azure SQL Database.
- **Azure-databas för PostgreSQL och MySQL stöds inte**: Tjänsteslutpunkter stöds inte för Azure-databas för PostgreSQL eller MySQL. Aktivera Tjänsteslutpunkter till SQL Database bryts anslutningen till dessa tjänster. Vi har en minskning. Kontakta *dmalik@microsoft.com*.

#### <a name="expressroute"></a>ExpressRoute

Om nätverket är anslutet till Azure-nätverk med hjälp av [ExpressRoute][expressroute-indexmd-744v], varje kretsen har konfigurerats med två offentliga IP-adresser i Microsoft Edge. Två IP-adresser används för att ansluta till Microsoft Services, som till Azure Storage med hjälp av Azure offentlig Peering.

Om du vill tillåta kommunikation från kretsen till Azure SQL Database, måste du skapa IP-Nätverksregler för offentliga IP-adresserna för dina kretsar. Öppna ett supportärende för att hitta de offentliga IP-adresserna för ExpressRoute-kretsen med ExpressRoute med hjälp av Azure portal.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-all-azure-services"></a>Effekt vid borttagning av ”Tillåt alla Azure-tjänster:

Många användare vill du ta bort **Tillåt alla Azure-tjänster** från Azure SQL-servrar och ersätta den med en brandväggsregel för virtuella nätverk.
Men att ta bort detta påverkar följande funktioner i Azure SQLDB:

#### <a name="import-export-service"></a>Importera Export Service
Azure SQLDB importera exportera-tjänsten körs på virtuella datorer i Azure. Dessa virtuella datorer är inte i ditt virtuella nätverk och därför hämta en Azure-IP-adress vid anslutning till databasen. För att ta bort **Tillåt alla Azure-tjänster** dessa virtuella datorer kommer inte att komma åt dina databaser.
Du kan undvika problemet. Kör BACPAC importera eller exportera direkt i koden med DACFx API. Se till att det är distribuerat i en virtuell dator som är i VNet-undernät som du har angett brandväggsregeln.

#### <a name="sql-database-query-editor"></a>Frågeredigeraren för SQL-databas
Azure SQL Database Query Editor distribueras på virtuella datorer i Azure. Dessa virtuella datorer som inte ingår i ditt VNet. De virtuella datorerna får därför en Azure-IP-adress vid anslutning till databasen. För att ta bort **Tillåt alla Azure-tjänster**, dessa virtuella datorer kommer inte att komma åt dina databaser.

#### <a name="table-auditing"></a>Granskning av tabell
Det finns två sätt att aktivera granskning på SQL-databasen för närvarande. Det går inte att granskning av tabellen när du har aktiverat Tjänsteslutpunkter i Azure SQL Server. Här lösning är att flytta till blobbgranskning.

#### <a name="impact-on-data-sync"></a>Påverkan på datasynkronisering
Azure SQLDB har funktionen datasynkronisering som ansluter till dina databaser med hjälp av Azure IP-adresser. När du använder Tjänsteslutpunkter, det är troligt att du stänger av **Tillåt alla Azure-tjänster** åtkomst till din logiska server. Funktionen datasynkronisering bryts.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Effekten av att använda slutpunkter för virtuellt nätverk med Azure storage

Azure Storage har implementerat samma funktion som låter dig begränsa anslutning till ditt lagringskonto.
Om du väljer att använda den här funktionen med ett lagringskonto som används av en Azure SQL Server stöter du på problem. Nästa är en lista och en beskrivning av Azure SQLDB funktioner som påverkas av detta.

#### <a name="azure-sqldw-polybase"></a>Azure SQLDW PolyBase
PolyBase är vanligt att läsa in data i Azure SQLDW från Storage-konton. Om det lagringskonto som du läser in data från begränsar åtkomsten till en uppsättning VNet-undernät, bryts anslutningen från PolyBase till kontot. Det finns en lösning. Kontakta *dmalik@microsoft.com* för mer information.

#### <a name="azure-sqldb-blob-auditing"></a>Azure SQLDB Blob granskning
Blobbgranskning skickar granskningsloggar till ditt eget lagringskonto. Om det här lagringskontot använder funktionen för slutpunkter VÄNDNING tjänsten bryts anslutningen från Azure SQLDB till lagringskontot.


## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Lägga till en brandväggsregel på VNET till servern utan att slå på VNET slutpunkter

Länge sedan innan den här funktionen har förbättrats du behövde du aktivera VNet tjänstens slutpunkter på innan du kan implementera en levande VNet-regel i brandväggen. Slutpunkterna relaterade till ett visst VNet-undernät till en Azure SQL Database. Men nu från och med januari 2018, du kan kringgå det här kravet genom att ange den **IgnoreMissingServiceEndpoint** flaggan.

Bara ställa en brandväggsregel inte att skydda servern. Du måste också aktivera VNet slutpunkter för säkerhet ska börja gälla. När du aktiverar slutpunkter upplevelser ditt VNet-undernät driftstopp förrän den har slutfört övergången från av till på. Detta är särskilt viktigt i samband med stora Vnet. Du kan använda den **IgnoreMissingServiceEndpoint** flagga för att minska eller eliminera avbrottstid under övergång.

Du kan ange den **IgnoreMissingServiceEndpoint** flaggan med hjälp av PowerShell. Mer information finns i [PowerShell för att skapa ett virtuellt nätverk tjänstslutpunkten och regeln för Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].


## <a name="errors-40914-and-40615"></a>Fel 40914 och 40615

Anslutningsfel 40914 avser *virtuellt Nätverksregler*, som anges i rutan brandväggen i Azure-portalen. Fel 40615 är liknande, förutom att den avser *IP-adressregler* i brandväggen.

#### <a name="error-40914"></a>Fel 40914

*Meddelandetext:* går inte att öppna server '*[servernamn]*som begärdes vid inloggningen. Klienten är inte tillåtet att ansluta till servern.

*Felbeskrivning:* klienten finns i ett undernät som har virtuella nätverk server slutpunkter. Men Azure SQL Database-servern har ingen regel för virtuella nätverk som ger behörighet att kommunicera med SQL-databasen till undernätet.

*Fel vid lösning:* på brandvägg rutan i Azure-portalen, Använd regler för virtuellt nätverk Kontrollera till [lägga till en regel för virtuellt nätverk](#anchor-how-to-by-using-firewall-portal-59j) för undernätet.

#### <a name="error-40615"></a>Fel 40615

*Meddelandetext:* går inte att öppna servern '{0}' som begärdes vid inloggningen. Klienten med IP-adressen '{1}' är inte tillåtet att ansluta till servern.

*Felbeskrivning:* klienten försöker ansluta från en IP-adress som inte har behörighet att ansluta till Azure SQL Database-server. Server-brandväggen har ingen regel för IP-adress som gör att en klient kan kommunicera från den angivna IP-adressen till SQL-databasen.

*Fel vid lösning:* Ange klientens IP-adress som en IP-regel. Du kan göra detta med hjälp av fönstret brandväggen i Azure-portalen.


En lista över flera felmeddelanden i SQL-databas dokumenteras [här][sql-database-develop-error-messages-419g].





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portalen kan skapa en regel för virtuellt nätverk

Det här avsnittet beskriver hur du kan använda den [Azure-portalen] [ http-azure-portal-link-ref-477t] att skapa en *virtuellt nätverk regeln* i Azure SQL-databasen. Regeln anger SQL-databasen att godta kommunikation från ett visst undernät som har taggats som en *virtuellt nätverk tjänstslutpunkten*.

> [!NOTE]
> Kontrollera att tjänsten slutpunkter är aktiverade för VNET/undernät som du vill lägga till VNET brandväggsregler för servern.
> Om Tjänsteslutpunkter inte är aktiverad för VNET/undernätet ombeds du i portalen för att aktivera dem, klickar du på Aktivera på bladet som du lägger till regeln.

#### <a name="powershell-alternative"></a>PowerShell-alternativ

Ett PowerShell-skript kan även skapa regler för virtuellt nätverk. Cmdleten avgörande **ny AzureRmSqlServerVirtualNetworkRule**. Om du vill använda, se [PowerShell för att skapa ett virtuellt nätverk tjänstslutpunkten och regeln för Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

#### <a name="rest-api-alternative"></a>REST API-alternativ

PowerShell-cmdlets för SQL-VNet åtgärder anropa internt REST API: er. Du kan anropa REST-API: er direkt.

- [Regler för virtuellt nätverk: åtgärder][rest-api-virtual-network-rules-operations-862r]

#### <a name="prerequisites"></a>Förutsättningar

Det måste finnas ett undernät som är märkta med viss virtuellt nätverk tjänstslutpunkten *typnamn* relevanta för Azure SQL Database.

- Namnet på relevant slutpunkt är **Microsoft.Sql**.
- Om ditt undernät inte kan taggas med typnamnet, se [verifiera ditt undernät är en slutpunkt][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Azure portal steg

1. Logga in på den [Azure-portalen][http-azure-portal-link-ref-477t].

2. Navigera till portalen **SQL-servrar** &gt; **brandvägg / virtuella nätverk**.

3. Ange den **Tillåt åtkomst till Azure-tjänster** kontrollen till OFF.

    > [!IMPORTANT]
    > Om du lämnar kontrollen inställt på ON accepterar kommunikation från alla undernät i Azure SQL Database-server. Lämna kontrollen inställt på ON kan vara mycket åtkomst från en säkerhetssynpunkt. Microsoft Azure Virtual Network service endpoint funktionen, tillsammans med funktionen virtuellt nätverk regeln för SQL-databas kan tillsammans minska din säkerhet ytan.

4. Klicka på den **+ Lägg till befintliga** styra i den **virtuella nätverk** avsnitt.

    ![Klicka på Lägg till befintliga (som en SQL-regel slutpunkt undernät).][image-portal-firewall-vnet-add-existing-10-png]

5. I den nya **skapa/uppdatera** rutan, fyller du i kontroller med namnen på Azure-resurser.

    > [!TIP]
    > Du måste inkludera rätt **adressprefixet** för undernätet. Du hittar värdet i portalen.
    > Navigera **alla resurser** &gt; **alla typer av** &gt; **virtuella nätverken**. Filtret visar dina virtuella nätverk. Klicka på det virtuella nätverket och sedan på **undernät**. Den **ADRESSINTERVALLET** kolumnen har adressprefixet som du behöver.

    ![Fyll i fälten för nya regeln.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klicka på den **OK** knappen längst ned i fönstret.

7.  Finns den resulterande virtuella nätverk regeln i fönstret brandväggen.

    ![Om fönstret brandväggen finns i den nya regeln.][image-portal-firewall-vnet-result-rule-30-png]


> [!NOTE]
> Följande statusar eller tillstånd som gäller reglerna:
> - **Klar:** anger att åtgärden som du har startat har lyckats.
> - **Misslyckades:** anger åtgärden som du har startat misslyckades.
> - **Ta bort:** endast gäller för borttagningen och anger att regeln har tagits bort och inte längre gäller.
> - **InProgress:** anger åtgärden pågår. Gamla regeln gäller när åtgärden är i det här tillståndet.


<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Relaterade artiklar

- [Slutpunkter för virtuella Azure-nätverket][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database server- och databasnivå brandväggsregler][sql-db-firewall-rules-config-715d]

Funktionen för regeln virtuellt nätverk för Azure SQL Database blev tillgängliga i sen September 2017.

## <a name="next-steps"></a>Nästa steg

- [Använd PowerShell för att skapa en tjänstslutpunkt för virtuellt nätverk och en regel för virtuellt nätverk för Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regler för virtuellt nätverk: Operations] [ rest-api-virtual-network-rules-operations-862r] med REST API: er



<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules



<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
