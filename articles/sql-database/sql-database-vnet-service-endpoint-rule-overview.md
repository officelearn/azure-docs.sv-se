---
title: VNet-slutpunkter och regler för enskilda och poolade databaser
description: Markera ett undernät som en slutpunkt för tjänsten Virtuellt nätverk. Sedan slutpunkten som en virtuell nätverksregel till ACL din Azure SQL Database. Du SQL Database accepterar sedan kommunikation från alla virtuella datorer och andra noder i undernätet.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 7032f9e8f57ea9400bf6a92f89b13fa1866f8fc1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414396"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Använda slutpunkter och regler för virtuella nätverkstjänster för databasservrar

*Virtuella nätverksregler* är en brandväggssäkerhetsfunktion som styr om databasservern för dina enskilda databaser och elastiskpool i Azure [SQL Database](sql-database-technical-overview.md) eller för dina databaser i SQL [Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accepterar kommunikation som skickas från vissa undernät i virtuella nätverk. Den här artikeln förklarar varför den virtuella nätverksregelfunktionen ibland är det bästa alternativet för att på ett säkert sätt tillåta kommunikation till din Azure SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller Azure SQL-server och både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse. Den här artikeln gäller *inte* för en **hanterad instansdistribution** i Azure SQL Database eftersom den inte har en tjänstslutpunkt associerad med den.

Om du vill skapa en regel för virtuellt nätverk måste det först finnas en slutpunkt för [en virtuell nätverkstjänst][vm-virtual-network-service-endpoints-overview-649d] som regeln ska referera till.

## <a name="how-to-create-a-virtual-network-rule"></a>Så här skapar du en regel för virtuellt nätverk

Om du bara skapar en regel för virtuellt nätverk kan du gå vidare till stegen och förklaringen [senare i den här artikeln](#anchor-how-to-by-using-firewall-portal-59j).

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>Information om regler för virtuella nätverk

I det här avsnittet beskrivs flera detaljer om virtuella nätverksregler.

### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje slutpunkt för tjänsten För virtuellt nätverk gäller endast en Azure-region. Slutpunkten gör det inte möjligt för andra regioner att acceptera kommunikation från undernätet.

Alla virtuella nätverksregel är begränsade till den region som den underliggande slutpunkten gäller för.

### <a name="server-level-not-database-level"></a>Servernivå, inte databasnivå

Varje virtuell nätverksregel gäller för hela Azure SQL Database-servern, inte bara för en viss databas på servern. Med andra ord gäller regeln för virtuellt nätverk på servernivå, inte på databasnivå.

- Däremot kan IP-regler gälla på båda nivåer.

### <a name="security-administration-roles"></a>Roller för säkerhetsadministration

Det finns en separation av säkerhetsroller i administrationen av slutpunkter för tjänsten Virtuellt nätverk. Åtgärder krävs från var och en av följande roller:

- **Nätverksadministratör:** &nbsp; Aktivera slutpunkten.
- **Databasadministratör:** &nbsp; Uppdatera åtkomstkontrollistan (ACL) för att lägga till det angivna undernätet på SQL Database-servern.

*RBAC alternativ:*

Rollerna för nätverksadministratör och databasadministratör har fler funktioner än vad som behövs för att hantera virtuella nätverksregler. Endast en delmängd av deras kapacitet behövs.

Du har möjlighet att använda [rollbaserad åtkomstkontroll (RBAC)][rbac-what-is-813s] i Azure för att skapa en enda anpassad roll som bara har den nödvändiga delmängden av funktioner. Den anpassade rollen kan användas i stället för att involvera nätverksadministratören eller databasadministratören. Ytan på säkerhetsexponeringen är lägre om du lägger till en användare i en anpassad roll, jämfört med att lägga till användaren i de andra två stora administratörsrollerna.

> [!NOTE]
> I vissa fall finns Azure SQL Database och VNet-undernätet i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
>
> - Båda prenumerationerna måste finnas i samma Azure Active Directory-klientorganisation.
> - Användaren har de behörigheter som krävs för att initiera åtgärder, till exempel aktivera tjänstslutpunkter och lägga till ett VNet-undernät till den angivna servern.
> - Båda prenumerationerna måste ha Microsoft.Sql-providern registrerad.

## <a name="limitations"></a>Begränsningar

För Azure SQL Database har funktionen för virtuella nätverksregler följande begränsningar:

- I brandväggen för SQL-databasen refererar varje virtuell nätverksregel till ett undernät. Alla dessa refererade undernät måste vara värd i samma geografiska region som är värd för SQL-databasen.

- Varje Azure SQL Database-server kan ha upp till 128 ACL-poster för ett visst virtuellt nätverk.

- Virtuella nätverksregler gäller endast för virtuella Azure Resource Manager-nätverk. och inte till [klassiska distributionsmodellnätverk.][arm-deployment-model-568f]

- Om du aktiverar slutpunkter för virtuella nätverkstjänster till Azure SQL Database kan slutpunkterna för MySQL- och PostgreSQL Azure-tjänsterna aktiveras. Men med slutpunkter PÅ kan försök att ansluta från slutpunkterna till dina MySQL- eller PostgreSQL-instanser misslyckas.
  - Den underliggande orsaken är att MySQL och PostgreSQL sannolikt inte har en virtuell nätverksregel konfigurerad. Du måste konfigurera en virtuell nätverksregel för Azure Database for MySQL och PostgreSQL och anslutningen kommer att lyckas.

- I brandväggen gäller IP-adressintervall för följande nätverksobjekt, men regler för virtuella nätverk gäller inte:
  - [Virtuellt privat nätverk (Plats till plats) (VPN)][vpn-gateway-indexmd-608y]
  - Lokalt via [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Överväganden vid användning av tjänstslutpunkter

När du använder tjänstslutpunkter för Azure SQL Database läser du följande överväganden:

- **Utgående till offentliga IPs för Azure SQL-databas krävs:** NSG (Network Security Groups) måste öppnas till Azure SQL Database IPs för att tillåta anslutning. Du kan göra detta genom att använda [NSG-tjänsttaggar](../virtual-network/security-overview.md#service-tags) för Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lokalt för offentlig peering eller Microsoft-peering, måste du identifiera de NAT IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering är de NAT IP-adresser som används antingen tillhandahållna av kunden eller av tjänsteleverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Om du vill tillåta kommunikation från din krets till Azure SQL Database måste du skapa IP-nätverksregler för de offentliga IP-adresserna för din NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Effekten av att använda slutpunkter för VNet-tjänst med Azure-lagring

Azure Storage har implementerat samma funktion som gör att du kan begränsa anslutningen till ditt Azure Storage-konto. Om du väljer att använda den här funktionen med ett Azure Storage-konto som används av Azure SQL Server kan du stöta på problem. Nästa är en lista och diskussion om Azure SQL Database och Azure SQL Data Warehouse funktioner som påverkas av detta.

### <a name="azure-sql-data-warehouse-polybase"></a>Polybas i Azure SQL Data Warehouse

PolyBase används ofta för att läsa in data i Azure SQL Data Warehouse från Azure Storage-konton. Om Azure Storage-kontot som du läser in data från begränsar åtkomsten endast till en uppsättning virtuella nätnät bryts anslutningen från PolyBase till kontot. För att aktivera både PolyBase import och export scenarier med Azure SQL Data Warehouse ansluter till Azure Storage som är säkrad till VNet, följ stegen nedan:

#### <a name="prerequisites"></a>Krav

- Installera Azure PowerShell med den här [guiden](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Om du har ett allmänt v1- eller blob-lagringskonto måste du först uppgradera till allmänt v2 med den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
- Du måste tillåta **betrodda Microsoft-tjänster för att komma åt det här lagringskontot** aktiverat under Azure Storage-kontobrand brandväggar och inställningar **för virtuella nätverk.** Se den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) för mer information.

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Steg

1. I PowerShell **registrerar du din Azure SQL Server** som är värd för din Azure SQL Data Warehouse-instans med Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Skapa ett **allmänt v2-lagringskonto** med den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Om du har ett allmänt v1- eller blob-lagringskonto måste du **först uppgradera till v2** med den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - Information om kända problem med Azure Data Lake Storage Gen2 finns i den här [guiden](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).

1. Under ditt lagringskonto navigerar du till **Åtkomstkontroll (IAM)** och klickar på **Lägg till rolltilldelning**. Tilldela RBAC-rollen **för storage blob data contributor** till din Azure SQL Server som är värd för ditt Azure SQL Data Warehouse som du har registrerat med Azure Active Directory (AAD) som i steg#1.

   > [!NOTE]
   > Endast medlemmar med ägarbehörighet kan utföra det här steget. För olika inbyggda roller för Azure-resurser finns i den här [guiden](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Polybase-anslutning till Azure Storage-kontot:**

   1. Skapa en **[databashuvudnyckel](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** om du inte har skapat någon tidigare:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Skapa databasscoped-autentiseringsuppgifter med **IDENTITY = "Managed Service Identity":**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       > - Det finns ingen anledning att ange SECRET med Azure Storage-åtkomstnyckel eftersom den här mekanismen använder [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) under omfattar.
       > - IDENTITETSNAMNET ska vara **"Managed Service Identity"** för PolyBase-anslutning för att fungera med Azure Storage-konto som är skyddat till VNet.

   1. Skapa extern datakälla med `abfss://` schema för anslutning till ditt virtuella lagringskonto för allmänt ändamål med PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       > - Om du redan har externa tabeller som är associerade med det allmänna v1- eller bloblagringskontot bör du först släppa dessa externa tabeller och sedan släppa motsvarande externa datakälla. Skapa sedan extern `abfss://` datakälla med schema som ansluter till allmänt v2-lagringskonto enligt ovan och återskapa alla externa tabeller med den här nya externa datakällan. Du kan använda [guiden Generera och publicera skript](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) för att skapa skapa skript för alla externa tabeller för enkelhetens skull.
       > - Mer information `abfss://` om schemat finns i den här [guiden](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Mer information om SKAPA EXTERN DATAKÄLLA finns i den här [guiden](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Fråga som vanligt med hjälp av [externa tabeller](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL Database Blob Granskning

Blob-granskning skickar granskningsloggar till ditt eget lagringskonto. Om det här lagringskontot använder slutpunktsfunktionen för VNet-tjänst bryts anslutningen från Azure SQL Database till lagringskontot.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Lägga till en VNet-brandväggsregel på servern utan att aktivera VNet-tjänstslutpunkter

För länge sedan, innan den här funktionen förbättrades, var du tvungen att aktivera VNet-tjänstslutpunkter innan du kunde implementera en live VNet-regel i brandväggen. Slutpunkterna relaterade ett visst VNet-undernät till en Azure SQL-databas. Men nu från och med januari 2018 kan du kringgå det här kravet genom att ange flaggan **IgnoreMissingVNetServiceEndpoint.**

Att bara ange en brandväggsregel hjälper inte till att skydda servern. Du måste också aktivera slutpunkter för VNet-tjänsten för att säkerheten ska börja gälla. När du aktiverar tjänstslutpunkter visas i ditt virtuella nätverks undernätsstopp tills övergången är klar till På. Detta gäller särskilt i samband med stora virtuella nätverk. Du kan använda flaggan **IgnoreMissingVNetServiceEndpoint** för att minska eller eliminera stilleståndstiden under övergången.

Du kan ange flaggan **IgnoreMissingVNetServiceEndpoint** med hjälp av PowerShell. Mer information finns i [PowerShell för att skapa en slutpunkt för tjänsten Virtuellt nätverk och regel för Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Fel 40914 och 40615

Anslutningsfel 40914 avser *virtuella nätverksregler*, enligt vad som anges i brandväggsfönstret i Azure-portalen. Fel 40615 är liknande, förutom att det gäller *IP-adressregler* på brandväggen.

### <a name="error-40914"></a>Fel 40914

*Meddelandetext:* Det går inte att öppna servern '*[servernamn]*' som begärs av inloggningen. Klienten har inte åtkomst till servern.

*Felbeskrivning:* Klienten finns i ett undernät som har slutpunkter för virtuella nätverksserver. Azure SQL Database-servern har däremot ingen regel för det virtuella nätverket som ger undernätet behörighet att kommunicera med SQL-databasen.

*Felmatchning:* I brandväggsfönstret i Azure-portalen använder du den virtuella nätverksreglerskontrollen för att lägga till [en virtuell nätverksregel](#anchor-how-to-by-using-firewall-portal-59j) för undernätet.

### <a name="error-40615"></a>Fel 40615

*Meddelandetext:* Det går{0}inte att öppna servern ' ' som begärs av inloggningen. Klient med IP-adress '{1}' har inte åtkomst till servern.

*Felbeskrivning:* Klienten försöker ansluta från en IP-adress som inte har behörighet att ansluta till Azure SQL Database-servern. Serverbrandväggen har ingen IP-adressregel som gör att en klient kan kommunicera från den givna IP-adressen till SQL-databasen.

*Felmatchning:* Ange klientens IP-adress som en IP-regel. Gör det här i brandväggsfönstret i Azure-portalen.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portalen kan skapa en regel för virtuellt nätverk

Det här avsnittet illustrerar hur du kan använda [Azure-portalen][http-azure-portal-link-ref-477t] för att skapa en *virtuell nätverksregel* i din Azure SQL-databas. Regeln talar om för SQL-databasen att acceptera kommunikation från ett visst undernät som har taggats som en *slutpunkt för tjänsten Virtuellt nätverk*.

> [!NOTE]
> Om du tänker lägga till en tjänstslutpunkt i VNet-brandväggsreglerna för din Azure SQL Database-server, kontrollera först att tjänstslutpunkter är aktiverade för undernätet.
>
> Om tjänstslutpunkter inte är aktiverade för undernätet ber portalen dig att aktivera dem. Klicka på knappen **Aktivera** på samma blad som du lägger till regeln på.

## <a name="powershell-alternative"></a>PowerShell alternativ

Ett skript kan också skapa virtuella nätverksregler med PowerShell cmdlet **New-AzSqlServerVirtualNetworkRule** eller [az-nätverksvnät skapa](/cli/azure/network/vnet#az-network-vnet-create). Om du är intresserad läser du [PowerShell för att skapa en slutpunkt för tjänsten Virtuellt nätverk och regel för Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>ALTERNATIV FÖR REST API

Internt anropar PowerShell-cmdlets för SQL VNet-åtgärder REST API:er. Du kan ringa REST-API:erna direkt.

- [Regler för virtuella nätverk: Åtgärder][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Krav

Du måste redan ha ett undernät som är taggat med det specifika *slutpunktstypnamnet för* tjänsten För virtuellt nätverk som är relevant för Azure SQL Database.

- Det relevanta namn på slutpunktstypen är **Microsoft.Sql**.
- Om undernätet kanske inte är taggat med typnamnet läser [du Kontrollera att undernätet är en slutpunkt][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure-portalens steg

1. Logga in på [Azure-portalen][http-azure-portal-link-ref-477t].

2. Sök efter och välj **SQL-servrar**och välj sedan servern. Under **Säkerhet**väljer du **Brandväggar och virtuella nätverk**.

3. Ange kontrollen **Tillåt åtkomst till Azure-tjänster** till OFF.

    > [!IMPORTANT]
    > Om du lämnar kontrolluppsättningen till ON accepterar din Azure SQL Database-server kommunikation från alla undernät inom Azure-gränsen, d.v.s. som kommer från en av IP-adresserna som känns igen som de inom intervall som definierats för Azure-datacenter. Att lämna kontrollen inställd på PÅ kan vara överdriven åtkomst från säkerhetssynpunkt. Slutpunktsfunktionen för Tjänsten Microsoft Azure Virtual Network, i samordning med den virtuella nätverksregelfunktionen i SQL Database, kan tillsammans minska din säkerhetsyta.

4. Klicka på **+ Lägg till befintlig** kontroll i avsnittet Virtuella **nätverk.**

    ![Klicka på Lägg till befintlig (undernätslutpunkt, som en SQL-regel).][image-portal-firewall-vnet-add-existing-10-png]

5. I det nya fönstret **Skapa/uppdatera** fyller du i kontrollerna med namnen på dina Azure-resurser.

    > [!TIP]
    > Du måste inkludera rätt **adressprefix** för ditt undernät. Du hittar värdet i portalen.
    > Navigera **alla resurser** &gt; **Alla typer** &gt; **virtuella nätverk**. Filtret visar dina virtuella nätverk. Klicka på det virtuella nätverket och sedan på **Undernät**. Kolumnen **ADRESSINTERVALL** har det adressprefix du behöver.

    ![Fyll i fält för ny regel.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klicka **på** OK-knappen längst ned i fönstret.

7. Se den resulterande regeln för virtuella nätverk i brandväggsfönstret.

    ![Se den nya regeln i brandväggsfönstret.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Följande status eller tillstånd gäller för reglerna:
>
> - **Redo:** Anger att åtgärden som du initierade har slutförts.
> - **Det gick inte att:** Anger att åtgärden som du initierade har misslyckats.
> - **Utgår:** Gäller endast för åtgärden Ta bort och anger att regeln har tagits bort och inte längre gäller.
> - **InProgress:** Anger att åtgärden pågår. Den gamla regeln gäller när åtgärden är i det här tillståndet.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Relaterade artiklar

- [Tjänstslutpunkter för virtuellt nätverk i Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Brandväggsregler på Azure SQL Database-servernivå och brandvägg på databasnivå][sql-db-firewall-rules-config-715d]

Den virtuella nätverksregelfunktionen för Azure SQL Database blev tillgänglig i slutet av september 2017.

## <a name="next-steps"></a>Nästa steg

- [Använd PowerShell för att skapa en slutpunkt för virtuella nätverkstjänster och sedan en virtuell nätverksregel för Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regler för virtuella nätverk: Åtgärder][rest-api-virtual-network-rules-operations-862r] med REST-API:er

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]: ../expressroute/index.yml
[rbac-what-is-813s]:../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
