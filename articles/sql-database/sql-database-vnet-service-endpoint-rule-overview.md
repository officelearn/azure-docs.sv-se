---
title: Tjänstslutpunkter i virtuella nätverk och regler för Azure SQL Database | Microsoft Docs
description: Markera ett undernät som en tjänstslutpunkt för virtuellt nätverk. Sedan slutpunkten som en virtuell nätverksregel i åtkomstkontrollistan din Azure SQL Database. Du SQL-databasen sedan godkänner kommunikation från alla virtuella datorer och andra noder i undernät.
services: sql-database
ms.service: sql-database
ms.prod_service: sql-database, sql-data-warehouse
author: DhruvMsft
manager: craigg
ms.custom: VNet Service endpoints
ms.topic: conceptual
ms.date: 08/28/2018
ms.reviewer: carlrab
ms.author: dmalik
ms.openlocfilehash: 38806b3dbf47b21885ef704cfec8be3c491a66a0
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055394"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database-and-sql-data-warehouse"></a>Använda tjänstslutpunkter i virtuella nätverk och regler för Azure SQL Database och SQL Data Warehouse

*Virtuella Nätverksregler* är en säkerhetsfunktion för brandväggen som styr om din Azure [SQL Database](sql-database-technical-overview.md) eller [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) servern tar emot meddelanden som skickas från specifika undernät i virtuella nätverk. Den här artikeln förklarar varför funktionen virtuellt nätverk regeln ibland är det bästa alternativet för att tillåta kommunikation till din Azure SQL-databas på ett säkert sätt.

> [!NOTE]
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

Skapa en virtuell nätverksregel det måste du först ta en [tjänstslutpunkt för virtuellt nätverk] [ vm-virtual-network-service-endpoints-overview-649d] för regeln för att referera till.

#### <a name="how-to-create-a-virtual-network-rule"></a>Så här skapar du en regel för virtuella nätverk

Om du bara skapar en regel för virtuella nätverk kan du gå vidare till steg och förklaring [senare i den här artikeln](#anchor-how-to-by-using-firewall-portal-59j).






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Virtuellt nätverk:** kan du ha virtuella nätverk som är associerade med din Azure-prenumeration.

**Undernät:** ett virtuellt nätverk innehåller **undernät**. Alla Azure virtuella datorer (VM) som du har har tilldelats till undernät. Ett undernät kan innehålla flera virtuella datorer eller andra compute-noder. Compute-noder som är utanför ditt virtuella nätverk inte kan komma åt det virtuella nätverket om du inte konfigurerar din säkerhet för att tillåta åtkomst.

**Tjänstslutpunkt för virtuellt nätverk:** A [tjänstslutpunkt för virtuellt nätverk] [ vm-virtual-network-service-endpoints-overview-649d] är ett undernät som egenskaper innehåller en eller flera formella Azure-tjänst-typnamn. I den här artikeln vi vill att ange namnet på **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas.

**Regel för virtuella nätverk:** en virtuell nätverksregel för din SQL Database-server är ett undernät som anges i åtkomstkontrollistan (ACL) för din SQL Database-server. För att vara i Åtkomstkontrollistan för SQL-databasen, undernätet måste innehålla den **Microsoft.Sql** typnamn.

En virtuell nätverksregel talar om för din SQL Database-server att acceptera kommunikation från varje nod i undernätet.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Fördelarna med en regel för virtuella nätverk

Tills du vidta åtgärder kan inte de virtuella datorerna på dina undernät kommunicera med din SQL-databas. En åtgärd som upprättar kommunikation är att skapa en regel för virtuella nätverk. Anledningen till att välja metod för VNet-regeln kräver Jämför och kontrast diskussioner som involverar konkurrerande säkerhetsalternativ som erbjuds av brandväggen.

#### <a name="a-allow-access-to-azure-services"></a>A. Tillåt åtkomst till Azure-tjänster

Fönstret brandväggen har en **på/av** knapp med etiketten **Tillåt åtkomst till Azure-tjänster**. Den **på** inställningen tillåter kommunikation från alla Azure-IP-adresser och undernät för alla Azure. De här Azure IP-adresser eller undernät kan inte ägas av dig. Detta **på** är förmodligen mer öppet än vad du vill att din SQL-databasen ska vara. Virtuellt nätverk regeln funktionen erbjuder mycket använda detaljerad styrning.

#### <a name="b-ip-rules"></a>B. IP-regler

SQL Database-brandväggen kan du ange IP-adressintervall som accepteras kommunikation till SQL Database. Den här metoden är bra för stabil IP-adresser som ligger utanför Azure privat nätverk. Men många noder i Azure privat nätverk har konfigurerats med *dynamisk* IP-adresser. Dynamiska IP-adresser ändras, till exempel när den virtuella datorn startas. Det vore fluga att ange en dynamisk IP-adress i en brandväggsregel i en produktionsmiljö.

Du kan rädda IP-alternativet genom att hämta en *Statiska* IP-adress för den virtuella datorn. Mer information finns i [konfigurera privata IP-adresser för en virtuell dator med hjälp av Azure-portalen][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Dock kan det vara svårt att hantera den statiska IP-metoden och det är kostsamt om det görs i stor skala. Virtual network-regler är lättare att upprätta och hantera.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. Ännu har inte SQL-databas på ett undernät

Om din Azure SQL Database-server har en nod i ett undernät i det virtuella nätverket, kan alla noder i det virtuella nätverket kommunicera med din SQL-databas. I det här fallet kan dina virtuella datorer kommunicera med SQL-databas utan några regler för virtuellt nätverk eller en IP-regler.

Men från och med September 2017, Azure SQL Database-tjänsten är ännu inte mellan tjänster som kan tilldelas till ett undernät.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Information om virtual network-regler

Det här avsnittet beskrivs flera detaljer om regler för virtuellt nätverk.

#### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje tjänstslutpunkt för virtuellt nätverk gäller endast en Azure-region. Slutpunkten kan inte andra regioner att godta kommunikation från undernätet.

Regel för virtuella nätverk är begränsad till den region som dess underliggande slutpunkten som gäller för.

#### <a name="server-level-not-database-level"></a>På servernivå, inte på databasnivå

Varje virtuellt nätverk regeln gäller för hela Azure SQL Database-servern, inte bara till en viss databas på servern. Med andra ord gäller virtuell nätverksregel på servernivå inte på databasnivå.

- IP-regler kan däremot tillämpas på båda nivåerna.

#### <a name="security-administration-roles"></a>Administration säkerhetsroller

Det finns en uppdelning av säkerhetsroller i administrationen av tjänstslutpunkter i virtuella nätverk. Åtgärd krävs från var och en av följande roller:

- **Administratör för nätverk:** &nbsp; aktivera slutpunkten.
- **Administratör för databasen:** &nbsp; uppdatera åtkomstkontrollistan (ACL) för att lägga till det angivna undernätet i SQL Database-servern.

*RBAC-alternativ:*

Rollerna för nätverk och databas delad Admin har fler funktioner än vad som behövs för att hantera virtuella Nätverksregler. Endast en delmängd av deras funktioner krävs.

Har du möjlighet att använda [rollbaserad åtkomstkontroll (RBAC)] [ rbac-what-is-813s] i Azure för att skapa en enda anpassad roll som har endast nödvändiga delmängd av funktionerna. Den anpassade rollen som kan användas i stället för som involverar nätverk administratören eller administratören för databasen. Ytan på exponeringen säkerhet är lägre om du lägger till en användare till en anpassad roll, jämfört med att lägga till användaren i de andra två större administratörsroller.

> [!NOTE]
> I vissa fall Azure SQL Database och VNet-undernät som finns i olika prenumerationer. I dessa fall måste du kontrollera att följande konfigurationer:
> - Båda prenumerationerna måste finnas i samma Azure Active Directory-klient.
> - Användaren har behörigheterna som krävs för att initiera åtgärder, till exempel aktiverar Tjänsteslutpunkter och lägga till ett VNet-undernät i den angivna servern.

## <a name="limitations"></a>Begränsningar

Funktionen för regler för virtuellt nätverk har följande begränsningar för Azure SQL Database:

- En Webbapp kan mappas till en privat IP-adress i ett virtuellt nätverk/undernät. Även om tjänstslutpunkter är aktiverade från de angivna VNet/undernät, har anslutningar från Webbappen till servern en Azure offentlig IP-källa, inte en virtuellt nätverk/undernät källa. Om du vill aktivera anslutningen från en Webbapp till en server med VNet-brandväggsreglerna, måste du **Tillåt alla Azure-tjänster** på servern.

- Refererar till ett undernät i brandväggen för SQL-databasen, varje regel för virtuella nätverk. Alla dessa refererade undernät måste finnas i samma geografiska region som är värd för SQL-databasen.

- Varje Azure SQL Database-server kan ha upp till 128 ACL-posterna för alla angivna virtuella nätverket.

- Virtual network-regler gäller endast för Azure Resource Manager-nätverk; och inte till [klassiska distributionsmodellen] [ arm-deployment-model-568f] nätverk.

- Tjänstslutpunkter för att aktivera på virtuella nätverk till Azure SQL Database kan också slutpunkter för MySQL och PostgreSQL-Azure-tjänster. Men med slutpunkter på försöker ansluta från slutpunkterna till din MySQL- eller PostgreSQL-instanser kommer att misslyckas.
    - Den underliggande orsaken är att MySQL och PostgreSQL för närvarande inte stöder ACLing.

- IP-adressintervall gäller följande nätverk i brandväggen, men inte av virtuella Nätverksregler:
    - [Plats-till-plats (S2S) virtuellt privat nätverk (VPN)][vpn-gateway-indexmd-608y]
    - Lokalt via [ExpressRoute][expressroute-indexmd-744v]

#### <a name="considerations-when-using-service-endpoints"></a>Att tänka på när du använder Tjänsteslutpunkter
När du använder Tjänsteslutpunkter för Azure SQL Database kan du granska följande överväganden:

- **Utgående till Azure SQL Database offentliga IP-adresser krävs**: måste vara öppna Nätverkssäkerhetsgrupper (NSG) till Azure SQL Database IP-adresser som tillåter anslutningar. Du kan göra detta med hjälp av NSG [Tjänsttaggar](../virtual-network/security-overview.md#service-tags) för Azure SQL Database.

#### <a name="expressroute"></a>ExpressRoute

Om nätverket är anslutet till det Azure-nätverket med hjälp av [ExpressRoute][expressroute-indexmd-744v], varje krets har konfigurerats med två offentliga IP-adresser i Microsoft Edge. Två IP-adresser används för att ansluta till Microsoft Services, till exempel till Azure Storage, med hjälp av Azure offentlig Peering.

Om du vill tillåta kommunikation från din krets till Azure SQL Database, måste du skapa IP-Nätverksregler för offentliga IP-adresserna för dina kretsar. För att kunna hitta ExpressRoute-kretsen offentliga IP-adresser, öppnar du ett supportärende hos ExpressRoute med hjälp av Azure portal.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-all-azure-services"></a>Effekt vid borttagning av ”Tillåt alla Azure-tjänster.

Många användare vill du ta bort **ge alla Azure-tjänster** från sin Azure SQL-servrar och Ersätt den med en VNet-brandväggsregeln.
Men ta bort detta påverkar följande Azure-SQLDB-funktioner:

#### <a name="import-export-service"></a>Import Export-tjänsten
Azure SQLDB Import Export-tjänsten körs på virtuella datorer i Azure. Dessa virtuella datorer är inte i ditt virtuella nätverk och kan därför få en Azure-IP-adress när du ansluter till din databas. För att ta bort **ge alla Azure-tjänster** dessa virtuella datorer kan inte komma åt dina databaser.
Du kan undvika problemet. Kör BACPAC importera eller exportera direkt i din kod med hjälp av DACFx-API. Se till att det distribueras i en virtuell dator som är i VNet-undernät som du har angett brandväggsregeln.

#### <a name="sql-database-query-editor"></a>SQL Database Query Editor
Azure SQL Database Query Editor distribueras på virtuella datorer i Azure. Dessa virtuella datorer är inte i ditt virtuella nätverk. De virtuella datorerna får därför en Azure-IP-adress när du ansluter till din databas. För att ta bort **ge alla Azure-tjänster**, dessa virtuella datorer kan inte komma åt dina databaser.

#### <a name="table-auditing"></a>Tabellgranskning
För närvarande finns det två sätt att aktivera granskning på SQL-databasen. Tabellgranskning misslyckas när du har aktiverat Tjänsteslutpunkter på din Azure SQL-Server. Här lösning är att flytta till blobbgranskning.

#### <a name="impact-on-data-sync"></a>Påverkan på datasynkronisering
Azure SQLDB innehåller Data Sync-funktion som ansluter till dina databaser med hjälp av Azure IP-adresser. När du använder Tjänsteslutpunkter, är det troligt att du stänger av **ge alla Azure-tjänster** åtkomst till din logiska server. Detta bryter funktionen datasynkronisering.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Effekten av att använda tjänstslutpunkter för virtuellt nätverk med Azure storage

Azure Storage har implementerats i samma funktion som låter dig begränsa anslutningsmöjligheten till ditt lagringskonto.
Om du väljer att använda den här funktionen med ett Storage-konto som används av en Azure SQL Server, kan du stöter på problem. Nästa är en lista och en beskrivning av Azure-SQLDB-funktioner som påverkas av detta.

#### <a name="azure-sqldw-polybase"></a>Azure SQLDW PolyBase
PolyBase är vanligt att läsa in data i Azure SQLDW från Storage-konton. Om lagringskontot som du läser in data från begränsar endast åtkomst till en uppsättning VNet-undernät, bryter anslutningen från PolyBase till kontot. Det finns en lösning för det här och du kan kontakta Microsoft support för mer information.

#### <a name="azure-sqldb-blob-auditing"></a>Azure SQLDB Blob granskning
Blobbgranskning skickar granskningsloggar till ditt eget lagringskonto. Om det här lagringskontot använder funktionen VNet Service slutpunkter bryts anslutningar från Azure SQLDB till lagringskontot.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Att lägga till en brandväggsregel för virtuellt nätverk till din server utan att slå på VNET-tjänstslutpunkter

Innan den här funktionen har förbättrats tvungen du länge sedan att aktivera tjänstslutpunkter i virtuella nätverk innan du kan implementera en levande VNet-regel i brandväggen. Ett särskilt VNet-undernät till en Azure SQL Database-relaterade slutpunkterna. Men nu från och med januari 2018, du kan kringgå det här kravet genom att ange den **IgnoreMissingServiceEndpoint** flaggan.

Bara ställa en brandväggsregel inte att skydda servern. Du måste också aktivera tjänstslutpunkter för virtuellt nätverk för att skydda ska börja gälla. När du aktiverar Tjänsteslutpunkter upplevelser ditt VNet-undernät driftstopp tills den är klar övergången från av till på. Detta gäller särskilt i samband med stora virtuella nätverk. Du kan använda den **IgnoreMissingServiceEndpoint** flagga för att minska eller eliminera nedtiden under övergång.

Du kan ange den **IgnoreMissingServiceEndpoint** flaggan med hjälp av PowerShell. Mer information finns i [PowerShell för att skapa en tjänstslutpunkt för virtuellt nätverk och en regel för Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].


## <a name="errors-40914-and-40615"></a>Fel 40914 och 40615

Anslutningsfel 40914 relaterar till *virtuella Nätverksregler*, som anges på fönstret brandväggen i Azure-portalen. Fel 40615 är liknande, förutom den relaterar till *regler för IP-address* i brandväggen.

#### <a name="error-40914"></a>Fel 40914

*Meddelandetext:* går inte att öppna server '*[servernamn]*' begärdes vid inloggningen. Klienten är inte tillåtet att ansluta till servern.

*Felbeskrivning:* klienten finns i ett undernät som har serverslutpunkter för virtuellt nätverk. Men Azure SQL Database-servern har ingen regel för virtuella nätverk som ger behörighet att kommunicera med SQL-databasen till undernätet.

*Fel vid matchning:* på brandväggen fönstret i Azure Portal, Använd de virtuella nätverksreglerna kontroll till [lägga till en virtuell nätverksregel](#anchor-how-to-by-using-firewall-portal-59j) för undernätet.

#### <a name="error-40615"></a>Fel 40615

*Meddelandetext:* går inte att öppna server '{0}' begärdes vid inloggningen. Klienten med IP-adressen{1}' tillåts inte att ansluta till servern.

*Felbeskrivning:* klienten försöker ansluta från en IP-adress som inte har behörighet att ansluta till Azure SQL Database-server. Serverbrandväggen har ingen regel för IP-adress som gör att en klient för att kommunicera från angiven IP-adress till SQL-databasen.

*Fel vid matchning:* anger klientens IP-adress som en IP-regel. Du kan göra detta med hjälp av fönstret brandväggen i Azure-portalen.


En lista över flera felmeddelanden för SQL Database dokumenteras [här][sql-database-develop-error-messages-419g].





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portalen kan skapa en regel för virtuella nätverk

Det här avsnittet beskriver hur du kan använda den [Azure-portalen] [ http-azure-portal-link-ref-477t] att skapa en *virtuell nätverksregel* i din Azure SQL Database. Regeln talar om för din SQL-databas att godta kommunikation från ett visst undernät som har taggats som en *tjänstslutpunkt för virtuellt nätverk*.

> [!NOTE]
> Om du vill lägga till en tjänstslutpunkt i VNet-brandväggsreglerna för din Azure SQL Database-server du först se till att tjänsten slutpunkter är aktiverade för undernätet.
>
> Om tjänstslutpunkter inte är aktiverade för undernätet, ber dig att aktivera dem i portalen. Klicka på den **aktivera** knappen på samma blad där du lägger till regeln.

#### <a name="powershell-alternative"></a>PowerShell-alternativ

Ett PowerShell-skript kan även skapa regler för virtuellt nätverk. Cmdleten avgörande **New-AzureRmSqlServerVirtualNetworkRule**. Om du vill använda, se [PowerShell för att skapa en tjänstslutpunkt för virtuellt nätverk och en regel för Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

#### <a name="rest-api-alternative"></a>REST API-alternativ

Internt, anropa PowerShell-cmdlets för SQL VNet åtgärder REST API: er. Du kan anropa REST-API: er direkt.

- [Regler för Virtual Network: åtgärder][rest-api-virtual-network-rules-operations-862r]

#### <a name="prerequisites"></a>Förutsättningar

Du måste redan ha ett undernät som är taggade med viss tjänstslutpunkt för virtuellt nätverk *typnamn* relevant för Azure SQL Database.

- Namnet på relevant slutpunkt är **Microsoft.Sql**.
- Om ditt undernät inte kan taggas med namnet på, se [verifiera ditt undernät är en slutpunkt][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Steg för Azure portal

1. Logga in på [Azure Portal][http-azure-portal-link-ref-477t].

2. Gå sedan portalen för att **SQL-servrar** &gt; **brandvägg / virtuella nätverk**.

3. Ange den **Tillåt åtkomst till Azure-tjänster** kontroll på OFF.

    > [!IMPORTANT]
    > Om du lämnar den kontroll som är inställt på på godkänner kommunikation från alla undernät i Azure SQL Database-servern. Lämna inställt på på kontrollen kan vara långa åtkomst från en säkerhetssynpunkt. Microsoft Azure Virtual Network service endpoint-funktionen, tillsammans med virtuellt nätverk regeln funktionen i SQL Database kan tillsammans minska ytan din säkerhet.

4. Klicka på den **+ Lägg till befintliga** styr i den **virtuella nätverk** avsnittet.

    ![Klicka på Lägg till befintliga (som en SQL-regel undernät slutpunkt).][image-portal-firewall-vnet-add-existing-10-png]

5. I den nya **skapa/uppdatera** rutan, fyller du i kontroller med namnen på dina Azure-resurser.

    > [!TIP]
    > Du måste inkludera rätt **adressprefix** för ditt undernät. Du hittar värdet i portalen.
    > Navigera **alla resurser** &gt; **alla typer av** &gt; **virtuella nätverk**. Filtret visar dina virtuella nätverk. Klicka på det virtuella nätverket och klicka sedan på **undernät**. Den **ADRESSINTERVALL** kolumnen har adressprefix som du behöver.

    ![Fyll i fälten för nya regeln.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klicka på den **OK** knappen längst ned i fönstret.

7.  Resulterande virtuellt nätverk regeln visas i fönstret brandväggen.

    ![Se den nya regeln i fönstret brandväggen.][image-portal-firewall-vnet-result-rule-30-png]


> [!NOTE]
> Följande statusar eller tillstånd gäller reglerna:
> - **Klart:** anger att åtgärden som du har initierat har lyckats.
> - **Misslyckades:** anger åtgärden som du har initierat misslyckades.
> - **Tas bort:** endast gäller för åtgärden ta bort och anger att regeln har tagits bort och inte längre gäller.
> - **Pågår:** anger åtgärden pågår. Den gamla regeln gäller när åtgärden är i det här tillståndet.


<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Relaterade artiklar

- [Tjänstslutpunkter i virtuella Azure-nätverket][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database-servernivå och databasnivå brandväggsregler][sql-db-firewall-rules-config-715d]

Funktionen virtuellt nätverk regeln för Azure SQL Database blev tillgängliga i slutet av September 2017.

## <a name="next-steps"></a>Nästa steg

- [Skapa en tjänstslutpunkt för virtuellt nätverk och en virtuell nätverksregel för Azure SQL Database med hjälp av PowerShell.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtuella Nätverksregler: Åtgärder] [ rest-api-virtual-network-rules-operations-862r] med REST API: er



<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules



<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
