---
title: VNet-slutpunkter och regler för databaser med enkel och pool i Azure SQL | Microsoft Docs
description: 'Markera ett undernät som en Virtual Network tjänst slut punkt. Sedan kan slut punkten som en virtuell nätverks regel till ACL: en Azure SQL Database. Du SQL Database acceptera sedan kommunikation från alla virtuella datorer och andra noder i under nätet.'
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: vanto, genemi
ms.date: 03/12/2019
ms.openlocfilehash: fbe6c4cc82272c7ab82931b089dbc3c70b07bee0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566234"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Använd tjänst slut punkter och regler för virtuella nätverk för databas servrar

*Regler för virtuella nätverk* är en brand Väggs säkerhetsfunktion som styr om databas servern för dina enskilda databaser och elastisk pool i Azure [SQL Database](sql-database-technical-overview.md) eller för dina databaser i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accepterar kommunikation som skickas från särskilda undernät i virtuella nätverk. I den här artikeln förklaras varför funktionen för regel för virtuella nätverk ibland det bästa alternativet för att på ett säkert sätt tillåta kommunikation till din Azure SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller för Azure SQL Server och för både SQL Database och SQL Data Warehouse databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse. Den här artikeln gäller *inte* för distribution av **hanterade instanser** i Azure SQL Database eftersom den inte har någon associerad tjänst slut punkt.

Om du vill skapa en regel för virtuellt nätverk måste du först vara en [tjänst slut punkt för virtuella nätverk][vm-virtual-network-service-endpoints-overview-649d] för att regeln ska referera till.

## <a name="how-to-create-a-virtual-network-rule"></a>Så här skapar du en regel för virtuella nätverk

Om du bara skapar en regel för virtuella nätverk kan du gå vidare till stegen och förklaringen [längre fram i den här artikeln](#anchor-how-to-by-using-firewall-portal-59j).

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Virtuellt nätverk:** Du kan ha virtuella nätverk kopplade till din Azure-prenumeration.

**Delnät** Ett virtuellt nätverk innehåller **undernät**. Alla virtuella datorer i Azure (VM) som du har tilldelats till undernät. Ett undernät kan innehålla flera virtuella datorer eller andra Compute-noder. Compute-noder utanför det virtuella nätverket kan inte komma åt ditt virtuella nätverk om du inte konfigurerar din säkerhet att tillåta åtkomst.

**Virtual Network tjänst slut punkt:** En [Virtual Network tjänst slut punkt][vm-virtual-network-service-endpoints-overview-649d] är ett undernät vars egenskaps värden innehåller ett eller flera formella namn för Azure-tjänst typ. I den här artikeln är vi intresserade av typ namnet **Microsoft. SQL**, som refererar till Azure-tjänsten med namnet SQL Database.

**Regel för virtuellt nätverk:** En regel för virtuella nätverk för din SQL Database-Server är ett undernät som listas i åtkomst kontrol listan (ACL) för din SQL Database-Server. För att det ska finnas i ACL: en för din SQL Database måste under nätet innehålla namnet **Microsoft. SQL** -typ.

En regel för virtuella nätverk instruerar SQL Database servern att acceptera kommunikation från varje nod som finns på under nätet.

<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Fördelar med en virtuell nätverks regel

De virtuella datorerna i under näten kan inte kommunicera med din SQL Database förrän du vidtar åtgärder. En åtgärd som upprättar kommunikationen är att skapa en regel för virtuella nätverk. Anledningen till att du väljer regel metoden för VNet kräver en jämförelse-och-kontrast-diskussion som involverar de konkurrerande säkerhets alternativ som erbjuds av brand väggen.

### <a name="a-allow-access-to-azure-services"></a>A. Tillåt åtkomst till Azure-tjänster

Fönstret brand vägg har en **på/av-** knapp med etiketten **Tillåt åtkomst till Azure-tjänster**. Inställningen **on** tillåter kommunikation från alla Azure IP-adresser och alla Azure-undernät. Dessa Azure IP-adresser eller undernät kanske inte ägs av dig. Den **här** inställningen är förmodligen mer öppen än du vill att din SQL Database ska vara. Funktionen för regel för virtuella nätverk ger en mycket noggrannare detaljerad kontroll.

### <a name="b-ip-rules"></a>B. IP-regler

Med SQL Database brand väggen kan du ange IP-adressintervall från vilka kommunikationen godkänns i SQL Database. Den här metoden är bra för stabila IP-adresser som ligger utanför Azures privata nätverk. Men många noder i det privata Azure-nätverket har kon figurer ATS med *dynamiska* IP-adresser. Dynamiska IP-adresser kan ändras, till exempel när den virtuella datorn startas om. Det skulle vara Folly att ange en dynamisk IP-adress i en brand Väggs regel i en produktions miljö.

Du kan hämta IP-alternativet genom att skaffa en *statisk* IP-adress för den virtuella datorn. Mer information finns i [Konfigurera privata IP-adresser för en virtuell dator med hjälp av Azure Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Den statiska IP-metoden kan dock bli svår att hantera, och den är kostsam när den görs i stor skala. Det är enklare att upprätta och hantera virtuella nätverks regler.

> [!NOTE]
> Du kan inte ha SQL Database i ett undernät än. Om din Azure SQL Database-Server var en nod i ett undernät i det virtuella nätverket kan alla noder i det virtuella nätverket kommunicera med din SQL Database. I det här fallet kan de virtuella datorerna kommunicera med SQL Database utan att behöva några regler för virtuella nätverk eller IP-regler.

Från och med september 2017 är Azure SQL Database tjänsten ännu inte bland de tjänster som kan tilldelas till ett undernät.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Information om regler för virtuella nätverk

I det här avsnittet beskrivs flera Detaljer om regler för virtuella nätverk.

### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje Virtual Network tjänst slut punkt gäller endast en Azure-region. Slut punkten tillåter inte att andra regioner accepterar kommunikation från under nätet.

Alla virtuella nätverks regler är begränsade till den region som den underliggande slut punkten gäller för.

### <a name="server-level-not-database-level"></a>Server nivå, inte databas nivå

Varje virtuell nätverks regel gäller hela Azure SQL Database-servern, inte bara till en viss databas på servern. Med andra ord gäller regel för virtuella nätverk på server nivå, inte på databas nivå.

- Dessutom kan IP-regler tillämpas på någon av nivåerna.

### <a name="security-administration-roles"></a>Säkerhets administrations roller

Det finns en separation av säkerhets roller i administration av Virtual Network tjänstens slut punkter. Åtgärd krävs från var och en av följande roller:

- **Nätverks administratör:** &nbsp;Aktivera slut punkten.
- **Databas administratör:** &nbsp;Uppdatera åtkomst kontrol listan (ACL) för att lägga till angivet undernät till SQL Database servern.

*RBAC-alternativ:*

Rollerna för nätverks administratör och databas administratörer har fler funktioner än vad som krävs för att hantera virtuella nätverks regler. Endast en delmängd av deras funktioner krävs.

Du kan välja att använda [rollbaserad åtkomst kontroll (RBAC)][rbac-what-is-813s] i Azure för att skapa en enskild anpassad roll som bara har de funktioner som krävs. Den anpassade rollen kan användas i stället för att involvera antingen nätverks administratören eller databas administratören. Arean av din säkerhets exponering är lägre om du lägger till en användare i en anpassad roll och lägger till användaren till de andra två större administratörs rollerna.

> [!NOTE]
> I vissa fall finns Azure SQL Database och VNet-under nätet i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
> - Båda prenumerationerna måste vara i samma Azure Active Directory-klient.
> - Användaren har de behörigheter som krävs för att initiera åtgärder, till exempel aktivera tjänst slut punkter och lägga till ett VNet-undernät till den aktuella servern.
> - Båda prenumerationerna måste ha Microsoft. SQL-providern registrerad.

## <a name="limitations"></a>Begränsningar

För Azure SQL Database har funktionen regler för virtuellt nätverk följande begränsningar:

- En webbapp kan mappas till en privat IP-adress i ett VNet/undernät. Även om tjänstens slut punkter är påslagna från det virtuella nätverket/under nätet kommer anslutningar från webbappen till-servern att ha en offentlig Azure-IP-källa, inte en källa för VNet/undernät. Om du vill aktivera anslutning från en webbapp till en server som har VNet brand Väggs regler måste du **ge Azure-tjänster åtkomst till servern** på servern.

- I brand väggen för din SQL Database refererar varje virtuell nätverks regel till ett undernät. Alla dessa refererade undernät måste ligga inom samma geografiska region som är värd för SQL Database.

- Varje Azure SQL Database Server kan ha upp till 128 ACL-poster för ett angivet virtuellt nätverk.

- Regler för virtuella nätverk gäller endast för Azure Resource Manager virtuella nätverk; och inte till [klassiska][arm-deployment-model-568f] nätverk för distributions modeller.

- Att aktivera tjänst slut punkter för virtuella nätverk till Azure SQL Database också aktivera slut punkter för de MySQL-och PostgreSQL Azure-tjänsterna. Men med slut punkter på kan försök att ansluta från slut punkterna till MySQL-eller PostgreSQL-instanserna Miss lyckas.
  - Den bakomliggande orsaken är att MySQL och PostgreSQL troligen inte har någon konfigurerad regel för virtuella nätverk. Du måste konfigurera en regel för virtuella nätverk för Azure Database for MySQL och PostgreSQL och anslutningen kommer att lyckas.

- I brand väggen gäller IP-adressintervall för följande nätverks objekt, men regler för virtuella nätverk gör inte följande:
  - [Virtuellt privat nätverk (VPN) för plats-till-plats (S2S)][vpn-gateway-indexmd-608y]
  - Lokalt via [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Att tänka på när du använder tjänstens slut punkter

När du använder tjänst slut punkter för Azure SQL Database bör du gå igenom följande överväganden:

- **Utgående till Azure SQL Database offentliga IP-adresser krävs**: Nätverks säkerhets grupper (NSG: er) måste öppnas för att Azure SQL Database IP-adresser för att tillåta anslutning. Du kan göra detta med hjälp av NSG [service-Taggar](../virtual-network/security-overview.md#service-tags) för Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lokalt för offentlig peering eller Microsoft-peering, måste du identifiera de NAT IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering är de NAT IP-adresser som används antingen tillhandahållna av kunden eller av tjänsteleverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du ett supportärende hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Om du vill tillåta kommunikation från din krets till Azure SQL Database måste du skapa IP-nätverksnummer för de offentliga IP-adresserna för din NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-azure-services-to-access-server"></a>Effekt av att ta bort Tillåt Azure-tjänster åtkomst till servern

Många användare vill ta bort **ge Azure-tjänster åtkomst till servern** från sina Azure SQL-servrar och ersätta dem med en brand Väggs regel för VNet.
Borttagning av detta påverkar dock följande funktioner:

### <a name="import-export-service"></a>Importera export tjänst

Azure SQL Database importera export tjänsten körs på virtuella datorer i Azure. De här virtuella datorerna finns inte i ditt VNet och kan därför få en Azure IP-adress när du ansluter till databasen. Om du tar bort **Tillåt Azure-tjänster åtkomst till servern** kommer de virtuella datorerna inte att kunna komma åt dina databaser.
Du kan undvika problemet. Kör BACPAC import eller export direkt i din kod med hjälp av DACFx-API: et. Se till att detta distribueras i en virtuell dator som finns i det VNet-undernät som du har angett brand Väggs regeln för.

### <a name="sql-database-query-editor"></a>Redigera Frågeredigeraren SQL Database

Den Azure SQL Database Frågeredigeraren distribueras på virtuella datorer i Azure. De här virtuella datorerna finns inte i ditt VNet. De virtuella datorerna får därför en Azure IP-adress när de ansluter till din databas. Om du tar bort **Tillåt Azure-tjänster åtkomst till servern**kommer de virtuella datorerna inte att kunna komma åt dina databaser.

### <a name="table-auditing"></a>Tabell granskning

För närvarande finns det två sätt att aktivera granskning på SQL Database. Tabell granskningen Miss lyckas när du har aktiverat tjänstens slut punkter på Azure-SQL Server. Minskning här är att gå vidare till BLOB-granskning.

### <a name="impact-on-data-sync"></a>Inverkan på datasynkronisering

Azure SQL Database har funktionen datasynkronisering som ansluter till dina databaser med hjälp av Azure IP-adresser. När du använder tjänst slut punkter är det troligt att du inaktiverar Tillåt att **Azure-tjänster får åtkomst till Server** åtkomst till din SQL Database-Server. Funktionen för synkronisering av data kommer att brytas.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Effekt av att använda virtuella nätverks slut punkter med Azure Storage

Azure Storage har implementerat samma funktion som gör det möjligt att begränsa anslutningen till ditt Azure Storage-konto. Om du väljer att använda den här funktionen med ett Azure Storage-konto som används av Azure SQL Server kan du stöta på problem. Härnäst är en lista och en diskussion om Azure SQL Database och Azure SQL Data Warehouse funktioner som påverkas av detta.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse PolyBase

PolyBase används ofta för att läsa in data i Azure SQL Data Warehouse från Azure Storage-konton. Om Azure Storage konto som du läser in data från begränsar åtkomsten till en uppsättning VNet-undernät, kommer anslutningen från PolyBase till kontot att avbrytas. Följ stegen nedan för att aktivera både polybases import-och export scenarier med Azure SQL Data Warehouse ansluter till Azure Storage som skyddas av VNet:

#### <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

1.  Installera Azure PowerShell med hjälp av den här [guiden](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.  Om du har ett allmänt v1-eller Blob Storage-konto måste du först uppgradera till General-Purpose v2 med hjälp av den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  Du måste ha **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot** under Azure Storage konto **brand väggar och inställningar för virtuella nätverk** . Mer information hittar du i den här [hand boken](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) .
 
#### <a name="steps"></a>Steg
1. **Registrera din SQL Database-Server** med Azure Active Directory (AAD) i PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
    
   1. Skapa ett **Allmänt-syfte v2-lagrings konto** med hjälp av den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Om du har ett allmänt v1-eller Blob Storage-konto måste du **först uppgradera till v2** med hjälp av den här [guiden](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - Information om kända problem med Azure Data Lake Storage Gen2 finns i den här [hand boken](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1. Under ditt lagrings konto navigerar du till **Access Control (IAM)** och klickar på **Lägg till roll tilldelning**. Tilldela RBAC-rollen **Storage BLOB data Contributor** till din SQL Database-Server.

   > [!NOTE] 
   > Endast medlemmar med ägar behörighet kan utföra det här steget. De olika inbyggda rollerna för Azure-resurser finns i den här [guiden](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **PolyBase-anslutning till Azure Storage kontot:**

   1. Skapa en **[huvud nyckel](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** för databasen om du inte har skapat en tidigare version:
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. Skapa databasens begränsade autentiseringsuppgifter med **Identity = hanterad tjänstidentitet**:

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Du behöver inte ange hemlighet med Azure Storage åtkomst nyckel eftersom den här mekanismen använder [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) under försättsblad.
       > - IDENTITETS namnet måste vara **hanterad tjänstidentitet** för PolyBase-anslutningen för att fungera med Azure Storage konto som skyddas av VNet.    
    
   1. Skapa en extern data källa med abfss://-schema för att ansluta till ditt General-Purpose v2-lagrings konto med PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - Om du redan har externa tabeller kopplade till allmänna v1-eller Blob Storage-konton bör du först ta bort de externa tabellerna och sedan släppa motsvarande externa data källa. Skapa sedan en extern data källa med abfss://-schema som ansluter till allmänt-syfte v2-lagrings konto enligt ovan och återskapa alla externa tabeller med den här nya externa data källan. Du kan använda [guiden skapa och publicera skript](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) för att skapa skapa skript för alla externa tabeller för att under lätta.
       > - Mer information om abfss://-schemat finns i den här [hand boken](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Mer information om att skapa en extern DATA källa finns i den här [guiden](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
   1. Fråga som normal med hjälp av [externa tabeller](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL Database-BLOB-granskning

Med BLOB-granskning skickas gransknings loggar till ditt eget lagrings konto. Om det här lagrings kontot använder funktionen för VNet-tjänstens slut punkter kommer anslutningen från Azure SQL Database till lagrings kontot att brytas.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Lägga till en brand Väggs regel för VNet på servern utan att aktivera VNet-tjänstens slut punkter

Innan den här funktionen har förbättrats tidigare var du tvungen att inaktivera VNet-tjänstens slut punkter innan du kunde implementera en VNet-regel i brand väggen. Slut punkterna som relaterar ett angivet VNet-undernät till en Azure SQL Database. Men nu i januari 2018 kan du kringgå det här kravet genom att ange flaggan **IgnoreMissingVNetServiceEndpoint** .

Att bara ange en brand Väggs regel skyddar inte servern. Du måste också aktivera VNet-tjänstens slut punkter för att säkerheten ska börja gälla. När du aktiverar tjänstens slut punkter aktive ras stillestånds tiden för VNet-undernät tills den slutför över gången från till på. Detta gäller särskilt i samband med stora virtuella nätverk. Du kan använda flaggan **IgnoreMissingVNetServiceEndpoint** för att minska eller eliminera stillestånds tiden under över gången.

Du kan ställa in flaggan **IgnoreMissingVNetServiceEndpoint** med hjälp av PowerShell. Mer information finns i [PowerShell för att skapa en Virtual Network tjänst slut punkt och regel för Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Fel 40914 och 40615

Anslutnings fel 40914 relaterar till *virtuella nätverks regler*, enligt vad som anges i brand Väggs fönstret i Azure Portal. Fel 40615 liknar varandra, förutom att det relaterar till *IP-adressens regler* i brand väggen.

### <a name="error-40914"></a>Fel 40914

*Meddelande text:* Det går inte att öppna servern *[Server Name]* som begärdes av inloggningen. Klienten har inte behörighet att komma åt servern.

*Fel Beskrivning:* Klienten finns i ett undernät som har Server slut punkter för virtuella nätverk. Men Azure SQL Database servern har ingen regel för virtuella nätverk som ger under nätet rätt att kommunicera med SQL Database.

*Fel matchning:* I fönstret brand vägg i Azure Portal använder du kontrollen regler för virtuellt nätverk för att [lägga till en virtuell nätverks regel](#anchor-how-to-by-using-firewall-portal-59j) för under nätet.

### <a name="error-40615"></a>Fel 40615

*Meddelande text:* Det går inte att{0}öppna servern som begärdes av inloggningen. Klienten med IP-adressen{1}är inte tillåten för att komma åt servern.

*Fel Beskrivning:* Klienten försöker ansluta från en IP-adress som inte har behörighet att ansluta till den Azure SQL Database servern. Serverbrandväggen har ingen IP-adressregel som gör att en klient kan kommunicera från den givna IP-adressen till SQL-databasen.

*Fel matchning:* Ange klientens IP-adress som en IP-regel. Gör detta med hjälp av brand Väggs fönstret i Azure Portal.

En lista över flera SQL Database fel meddelanden dokumenteras [här][sql-database-develop-error-messages-419g].

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portalen kan skapa en regel för virtuellt nätverk

I det här avsnittet beskrivs hur du kan använda [Azure Portal][http-azure-portal-link-ref-477t] för att skapa en *regel för virtuella nätverk* i Azure SQL Database. Regeln instruerar din SQL Database att acceptera kommunikation från ett visst undernät som har taggats som en *Virtual Network tjänst slut punkt*.

> [!NOTE]
> Om du vill lägga till en tjänst slut punkt i brand Väggs reglerna för VNet i Azure SQL Database-servern ska du först se till att tjänstens slut punkter är aktiverade för under nätet.
>
> Om tjänst slut punkter inte är aktiverade för under nätet ber portalen dig att aktivera dem. Klicka på knappen **Aktivera** på samma blad där du lägger till regeln.

## <a name="powershell-alternative"></a>PowerShell-alternativ

Ett PowerShell-skript kan också skapa regler för virtuella nätverk. Den viktiga cmdleten **New-AzSqlServerVirtualNetworkRule**. Om det intresserar sig, se [PowerShell för att skapa en Virtual Network tjänst slut punkt och regel för Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternativ

Internt anropar PowerShell-cmdletar för SQL VNet-åtgärder REST-API: er. Du kan anropa REST-API: erna direkt.

- [Virtual Network regler: Verksamhetshanteraren][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Förutsättningar

Du måste redan ha ett undernät som är taggat med det specifika Virtual Network tjänst slut punkts *typ namn* som är relevant för Azure SQL Database.

- Det relevanta namnet på slut punkts typen är **Microsoft. SQL**.
- Om ditt undernät kanske inte är taggat med typ namnet, se [Verifiera att ditt undernät är en slut punkt][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure Portal steg

1. Logga in på [Azure Portal][http-azure-portal-link-ref-477t].

2. Navigera sedan till portalen för **SQL Server** &gt; **-brandvägg/Virtual Networks**.

3. Ange alternativet **Tillåt åtkomst till Azure-tjänster** till av.

    > [!IMPORTANT]
    > Om du lämnar kontrollen inställd till på, accepterar Azure SQL Database servern kommunikation från vilket undernät som helst. Om du lämnar kontrollen till på aktive rad kan det vara för hög åtkomst från en säkerhets punkt. Den Microsoft Azure Virtual Network tjänstens slut punkts funktion, i samordning med funktionen för regel för virtuella nätverk i SQL Database, kan minska området för säkerhets ytan.

4. Klicka på **+ Lägg till befintlig** kontroll i avsnittet **virtuella nätverk** .

    ![Klicka på Lägg till befintlig (under näts slut punkt som en SQL-regel).][image-portal-firewall-vnet-add-existing-10-png]

5. I fönstret nytt **Skapa/uppdatera** fyller du i kontrollerna med namnen på dina Azure-resurser.

    > [!TIP]
    > Du måste inkludera rätt **adressprefix** för ditt undernät. Du kan hitta värdet i portalen.
    > Navigera **alla resurser** &gt; **alla typer** &gt; av **virtuella nätverk**. Filtret visar dina virtuella nätverk. Klicka på det virtuella nätverket och klicka sedan på **undernät**. I kolumnen **adress intervall** finns det adressprefix du behöver.

    ![Fyll i fält för ny regel.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klicka på **OK** längst ned i fönstret.

7. Se den resulterande virtuella nätverks regeln i brand Väggs fönstret.

    ![Se den nya regeln i brand Väggs fönstret.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Följande status eller tillstånd gäller för reglerna:
> - **Redo** Anger att åtgärden som du initierade har slutförts.
> - **Bröt** Anger att åtgärden som du initierade har misslyckats.
> - **Tar** Gäller endast borttagnings åtgärden och anger att regeln har tagits bort och inte längre gäller.
> - **Pågår** Anger att åtgärden pågår. Den gamla regeln gäller när åtgärden är i det här läget.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Relaterade artiklar

- [Tjänst slut punkter för Azure Virtual Network][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database brand Väggs regler på server nivå och databas nivå][sql-db-firewall-rules-config-715d]

Funktionen för regel för virtuella nätverk för Azure SQL Database blev tillgänglig i slutet av den 2017 september.

## <a name="next-steps"></a>Nästa steg

- [Använd PowerShell för att skapa en tjänst slut punkt för virtuellt nätverk och sedan en regel för virtuella nätverk för Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtual Network regler: Åtgärder][rest-api-virtual-network-rules-operations-862r] med REST API: er

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

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

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
