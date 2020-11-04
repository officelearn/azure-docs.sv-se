---
title: VNet-slutpunkter och regler för databaser i Azure SQL Database
description: Markera ett undernät som en Virtual Network tjänst slut punkt. Sedan slut punkten som en virtuell nätverks regel till ACL-databasen. Databasen godkänner sedan kommunikationen från alla virtuella datorer och andra noder i under nätet.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 4539709dbac992979af6a56e3dae81725a35739d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325000"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Använd tjänst slut punkter och regler för virtuella nätverk för servrar i Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Regler för virtuella nätverk* är en brand Väggs säkerhetsfunktion som styr om servern för dina databaser och elastiska pooler i [Azure SQL Database](sql-database-paas-overview.md) eller för dina databaser i [Azure-Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accepterar kommunikation som skickas från särskilda undernät i virtuella nätverk. I den här artikeln förklaras varför funktionen regel för virtuella nätverk ibland det bästa alternativet för att på ett säkert sätt tillåta kommunikation till databasen i Azure SQL Database och Azure Synapse Analytics (tidigare SQL Data Warehouse).

> [!NOTE]
> Den här artikeln gäller för både Azure SQL Database-och Azure Synapse-analys. För enkelhetens skull refererar termen "databas" till båda databaserna i Azure SQL Database och Azure Synapse Analytics. På samma sätt refererar alla referenser till "Server" till den [logiska SQL-Server](logical-servers.md) som är värd för Azure SQL Database och Azure Synapse Analytics.

Om du vill skapa en regel för virtuellt nätverk måste du först vara en [tjänst slut punkt för virtuella nätverk][vm-virtual-network-service-endpoints-overview-649d] för att regeln ska referera till.

## <a name="how-to-create-a-virtual-network-rule"></a>Så här skapar du en regel för virtuella nätverk

Om du bara skapar en regel för virtuella nätverk kan du gå vidare till stegen och förklaringen [längre fram i den här artikeln](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Information om regler för virtuella nätverk

I det här avsnittet beskrivs flera Detaljer om regler för virtuella nätverk.

### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje Virtual Network tjänst slut punkt gäller endast en Azure-region. Slut punkten tillåter inte att andra regioner accepterar kommunikation från under nätet.

Alla virtuella nätverks regler är begränsade till den region som den underliggande slut punkten gäller för.

### <a name="server-level-not-database-level"></a>Server nivå, inte databas nivå

Varje virtuell nätverks regel gäller hela servern, inte bara till en viss databas på servern. Med andra ord gäller regel för virtuella nätverk på server nivå, inte på databas nivå.

- Dessutom kan IP-regler tillämpas på någon av nivåerna.

### <a name="security-administration-roles"></a>Säkerhets administrations roller

Det finns en separation av säkerhets roller i administration av Virtual Network tjänstens slut punkter. Åtgärd krävs från var och en av följande roller:

- **Nätverks administratör:** &nbsp; Aktivera slut punkten.
- **Databas administratör:** &nbsp; Uppdatera åtkomst kontrol listan (ACL) för att lägga till angivet undernät till servern.

*RBAC-alternativ:*

Rollerna för nätverks administratör och databas administratörer har fler funktioner än vad som krävs för att hantera virtuella nätverks regler. Endast en delmängd av deras funktioner krävs.

Du kan välja att använda [Azures rollbaserad åtkomst kontroll (Azure RBAC)][rbac-what-is-813s] i Azure för att skapa en enskild anpassad roll som bara har de funktioner som krävs. Den anpassade rollen kan användas i stället för att involvera antingen nätverks administratören eller databas administratören. Arean av din säkerhets exponering är lägre om du lägger till en användare i en anpassad roll och lägger till användaren till de andra två större administratörs rollerna.

> [!NOTE]
> I vissa fall är databasen i Azure SQL Database och VNet-under nätet i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
>
> - Båda prenumerationerna måste vara i samma Azure Active Directory-klient.
> - Användaren har de behörigheter som krävs för att initiera åtgärder, till exempel aktivera tjänst slut punkter och lägga till ett VNet-undernät till den aktuella servern.
> - Båda prenumerationerna måste ha Microsoft. SQL-providern registrerad.

## <a name="limitations"></a>Begränsningar

För Azure SQL Database har funktionen regler för virtuellt nätverk följande begränsningar:

- I brand väggen för din databas i Azure SQL Database refererar varje virtuell nätverks regel till ett undernät. Alla dessa refererade undernät måste ligga inom samma geografiska region som är värd för databasen.

- Varje server kan ha upp till 128 ACL-poster för alla enskilda virtuella nätverk.

- Regler för virtuella nätverk gäller endast för Azure Resource Manager virtuella nätverk; och inte till [klassiska nätverk för distributions modeller][arm-deployment-model-568f] .

- Att aktivera tjänst slut punkter för virtuella nätverk till Azure SQL Database också aktivera slut punkter för de MySQL-och PostgreSQL Azure-tjänsterna. Men med slut punkter på kan försök att ansluta från slut punkterna till MySQL-eller PostgreSQL-instanserna Miss lyckas.
  - Den bakomliggande orsaken är att MySQL och PostgreSQL troligen inte har någon konfigurerad regel för virtuella nätverk. Du måste konfigurera en regel för virtuella nätverk för Azure Database for MySQL och PostgreSQL och anslutningen kommer att lyckas.

- I brand väggen gäller IP-adressintervall för följande nätverks objekt, men regler för virtuella nätverk gör inte följande:
  - [Virtuellt privat nätverk (VPN) för plats-till-plats (S2S)][vpn-gateway-indexmd-608y]
  - Lokalt via [ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-using-service-endpoints"></a>Att tänka på när du använder tjänstens slut punkter

När du använder tjänst slut punkter för Azure SQL Database bör du gå igenom följande överväganden:

- **Utgående till Azure SQL Database offentliga IP-adresser krävs** : nätverks säkerhets grupper (NSG: er) måste öppnas för att Azure SQL Database IP-adresser för att tillåta anslutning. Du kan göra detta med hjälp av NSG [service-Taggar](../../virtual-network/network-security-groups-overview.md#service-tags) för Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Om du använder [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lokalt för offentlig peering eller Microsoft-peering, måste du identifiera de NAT IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering är de NAT IP-adresser som används antingen tillhandahållna av kunden eller av tjänsteleverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Om du vill tillåta kommunikation från din krets till Azure SQL Database måste du skapa IP-nätverksnummer för de offentliga IP-adresserna för din NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Effekt av att använda virtuella nätverks slut punkter med Azure Storage

Azure Storage har implementerat samma funktion som gör att du kan begränsa anslutningen till ditt Azure Storage-konto. Om du väljer att använda den här funktionen för ett Azure Storage-konto som används av Azure SQL Database kan du stöta på problem. Härnäst är en lista och diskussion om Azure SQL Database-och Azure Synapse Analytics-funktioner som påverkas av detta.

### <a name="azure-synapse-polybase-and-copy-statement"></a>Azure Synapse PolyBase och COPY-instruktion

PolyBase och COPY-instruktionen används ofta för att läsa in data i Azure Synapse Analytics från Azure Storage konton för data inmatning med hög data flöde. Om Azure Storage-kontot som du läser in data från begränsar åtkomsten till en uppsättning VNet-undernät, kommer anslutningen när du använder PolyBase och KOPIERINGs instruktionen till lagrings kontot att brytas. Om du vill aktivera import-och export scenarier med hjälp av kopiera och PolyBase med Azure Synapse Analytics ansluter du till Azure Storage som är säkrad till VNet, följer du stegen som anges nedan:

#### <a name="prerequisites"></a>Förutsättningar

- Installera Azure PowerShell med hjälp av den här [guiden](/powershell/azure/install-az-ps).
- Om du har ett konto av typen generell användning v1 eller bloblagring måste du först uppgradera till generell användning v2 med hjälp av den här [guiden](../../storage/common/storage-account-upgrade.md).
- Du måste ha **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot** under Azure Storage konto **brand väggar och inställningar för virtuella nätverk** . Om du aktiverar den här konfigurationen tillåts PolyBase-och COPY-instruktionen att ansluta till lagrings kontot med stark autentisering där nätverks trafiken finns kvar i Azure-stamnätet. Mer information finns i den här [guiden](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020.  Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Steg

1. **Registrera en server** som är värd för Azure-Synapse med Azure Active Directory (AAD) i PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Skapa ett **Allmänt-syfte v2-lagrings konto** med hjälp av den här [guiden](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Om du har ett allmänt v1-eller Blob Storage-konto måste du **först uppgradera till v2** med hjälp av den här [guiden](../../storage/common/storage-account-upgrade.md).
   > - Information om kända problem med Azure Data Lake Storage Gen2 finns i den här [hand boken](../../storage/blobs/data-lake-storage-known-issues.md).

1. Under ditt lagrings konto navigerar du till **Access Control (IAM)** och väljer **Lägg till roll tilldelning**. Tilldela Azure-rollen **Storage BLOB data Contributor** till den server som är värd för din Azure Synapse-analys som du har registrerat med Azure Active Directory (AAD) som i steg #1.

   > [!NOTE]
   > Endast medlemmar med ägar behörighet för lagrings kontot kan utföra det här steget. Information om olika inbyggda Azure-roller finns i den här [guiden](../../role-based-access-control/built-in-roles.md).
  
1. **PolyBase-anslutning till Azure Storage kontot:**

   1. Skapa en **[huvud nyckel](/sql/t-sql/statements/create-master-key-transact-sql)** för databasen om du inte har skapat en tidigare version:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Skapa databasens begränsade autentiseringsuppgifter med **Identity = hanterad tjänstidentitet** :

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Du behöver inte ange hemlighet med Azure Storage åtkomst nyckel eftersom den här mekanismen använder [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) under försättsblad.
       > - IDENTITETS namnet måste vara **hanterad tjänstidentitet** för PolyBase-anslutningen för att fungera med Azure Storage konto som skyddas av VNet.

   1. Skapa en extern data källa med `abfss://` schema för att ansluta till ditt General-Purpose v2-lagrings konto med PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Om du redan har externa tabeller kopplade till allmänna v1-eller Blob Storage-konton bör du först ta bort de externa tabellerna och sedan släppa motsvarande externa data källa. Skapa sedan en extern data källa med `abfss://` schema som ansluter till det allmänna lagrings kontot för generell användning som ovan och återskapa alla externa tabeller med den här nya externa data källan. Du kan använda [guiden skapa och publicera skript](/sql/ssms/scripting/generate-and-publish-scripts-wizard) för att skapa skapa skript för alla externa tabeller för att under lätta.
       > - Mer information om `abfss://` schema finns i den här [hand boken](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Mer information om att skapa en extern DATA källa finns i den här [guiden](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Fråga som normal med hjälp av [externa tabeller](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL Database-BLOB-granskning

Med BLOB-granskning skickas gransknings loggar till ditt eget lagrings konto. Om det här lagrings kontot använder funktionen för VNet-tjänstens slut punkter kommer anslutningen från Azure SQL Database till lagrings kontot att brytas.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Lägga till en brand Väggs regel för VNet på servern utan att aktivera VNet-tjänstens slut punkter

Innan den här funktionen har förbättrats tidigare var du tvungen att inaktivera VNet-tjänstens slut punkter innan du kunde implementera en VNet-regel i brand väggen. Slut punkterna som relaterar ett angivet VNet-undernät till en databas i Azure SQL Database. Men nu i januari 2018 kan du kringgå det här kravet genom att ange flaggan **IgnoreMissingVNetServiceEndpoint** .

Att bara ange en brand Väggs regel skyddar inte servern. Du måste också aktivera VNet-tjänstens slut punkter för att säkerheten ska börja gälla. När du aktiverar tjänstens slut punkter aktive ras stillestånds tiden för VNet-undernät tills den slutför över gången från till på. Detta gäller särskilt i samband med stora virtuella nätverk. Du kan använda flaggan **IgnoreMissingVNetServiceEndpoint** för att minska eller eliminera stillestånds tiden under över gången.

Du kan ställa in flaggan **IgnoreMissingVNetServiceEndpoint** med hjälp av PowerShell. Mer information finns i [PowerShell för att skapa en Virtual Network tjänst slut punkt och regel för Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Fel 40914 och 40615

Anslutnings fel 40914 relaterar till *virtuella nätverks regler* , enligt vad som anges i brand Väggs fönstret i Azure Portal. Fel 40615 liknar varandra, förutom att det relaterar till *IP-adressens regler* i brand väggen.

### <a name="error-40914"></a>Fel 40914

*Meddelande text:* Det går inte att öppna servern *[Server Name]* som begärdes av inloggningen. Klienten har inte behörighet att komma åt servern.

*Fel Beskrivning:* Klienten finns i ett undernät som har Server slut punkter för virtuella nätverk. Men servern har ingen regel för virtuella nätverk som ger under nätet rätt att kommunicera med databasen.

*Fel matchning:* I fönstret brand vägg i Azure Portal använder du kontrollen regler för virtuellt nätverk för att [lägga till en virtuell nätverks regel](#anchor-how-to-by-using-firewall-portal-59j) för under nätet.

### <a name="error-40615"></a>Fel 40615

*Meddelande text:* Det går inte att öppna servern {0} som begärdes av inloggningen. Klienten med IP-adressen {1} är inte tillåten för att komma åt servern.

*Fel Beskrivning:* Klienten försöker ansluta från en IP-adress som inte har behörighet att ansluta till servern. Server brand väggen har ingen IP-serveradress som gör att en klient kan kommunicera från den tilldelade IP-adressen till-databasen.

*Fel matchning:* Ange klientens IP-adress som en IP-regel. Gör det här i brandväggsfönstret i Azure-portalen.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="portal-can-create-a-virtual-network-rule"></a>Portalen kan skapa en regel för virtuellt nätverk

I det här avsnittet beskrivs hur du kan använda [Azure Portal][http-azure-portal-link-ref-477t] för att skapa en *regel för virtuella nätverk* i din databas i Azure SQL Database. Regeln instruerar din databas att acceptera kommunikation från ett visst undernät som har taggats som en *Virtual Network tjänst slut punkt*.

> [!NOTE]
> Om du vill lägga till en tjänst slut punkt i brand Väggs reglerna för VNet för din server måste du först se till att tjänstens slut punkter är aktiverade för under nätet.
>
> Om tjänst slut punkter inte är aktiverade för under nätet ber portalen dig att aktivera dem. Klicka på knappen **Aktivera** på samma blad där du lägger till regeln.

## <a name="powershell-alternative"></a>PowerShell-alternativ

Ett skript kan också skapa virtuella nätverks regler med PowerShell **-cmdlet New-AzSqlServerVirtualNetworkRule** eller [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create). Om det intresserar sig, se [PowerShell för att skapa en Virtual Network tjänst slut punkt och regel för Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternativ

Internt anropar PowerShell-cmdletar för SQL VNet-åtgärder REST-API: er. Du kan anropa REST-API: erna direkt.

- [Virtual Network regler: åtgärder][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Förutsättningar

Du måste redan ha ett undernät som är taggat med det specifika Virtual Network tjänst slut punkts *typ namn* som är relevant för Azure SQL Database.

- Det relevanta namnet på slut punkts typen är **Microsoft. SQL**.
- Om ditt undernät kanske inte är taggat med typ namnet, se [Verifiera att ditt undernät är en slut punkt][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal steg

1. Logga in på [Azure Portal][http-azure-portal-link-ref-477t].

2. Sök efter och välj **SQL-servrar** och välj sedan din server. Under **säkerhet** väljer du **brand väggar och virtuella nätverk**.

3. Ange alternativet **Tillåt åtkomst till Azure-tjänster** till av.

    > [!IMPORTANT]
    > Om du lämnar kontrollen inställt på på, godkänner servern kommunikation från ett undernät i Azure-gränser, d.v.s. från en av de IP-adresser som identifieras som de inom intervallen som definierats för Azure-datacenter. Om du lämnar kontrollen till på aktive rad kan det vara för hög åtkomst från en säkerhets punkt. Den Microsoft Azure Virtual Network tjänstens slut punkts funktion, i samordning med funktionen för regel för virtuella nätverk i SQL Database, kan minska området för säkerhets ytan.

4. Klicka på **+ Lägg till befintlig** kontroll i avsnittet **virtuella nätverk** .

    ![Klicka på Lägg till befintlig (under näts slut punkt som en SQL-regel).][image-portal-firewall-vnet-add-existing-10-png]

5. I fönstret nytt **Skapa/uppdatera** fyller du i kontrollerna med namnen på dina Azure-resurser.

    > [!TIP]
    > Du måste inkludera rätt **adressprefix** för ditt undernät. Du kan hitta värdet i portalen.
    > Navigera **alla resurser** &gt; **alla typer** av &gt; **virtuella nätverk**. Filtret visar dina virtuella nätverk. Klicka på det virtuella nätverket och klicka sedan på **undernät**. I kolumnen **adress intervall** finns det adressprefix du behöver.

    ![Fyll i fält för ny regel.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klicka på **OK** längst ned i fönstret.

7. Se den resulterande virtuella nätverks regeln i brand Väggs fönstret.

    ![Se den nya regeln i brand Väggs fönstret.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Följande status eller tillstånd gäller för reglerna:
>
> - **Klart:** Anger att åtgärden som du initierade har slutförts.
> - **Misslyckades:** Anger att åtgärden som du initierade har misslyckats.
> - **Borttagen:** Gäller endast borttagnings åtgärden och anger att regeln har tagits bort och inte längre gäller.
> - **Förlopp:** Anger att åtgärden pågår. Den gamla regeln gäller när åtgärden är i det här läget.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Relaterade artiklar

- [Tjänstslutpunkter för virtuellt nätverk i Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Brand Väggs regler på server nivå och databas nivå][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Nästa steg

- [Använd PowerShell för att skapa en tjänst slut punkt för virtuellt nätverk och sedan en regel för virtuella nätverk för Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtual Network regler: åtgärder][rest-api-virtual-network-rules-operations-862r] med REST API: er

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
