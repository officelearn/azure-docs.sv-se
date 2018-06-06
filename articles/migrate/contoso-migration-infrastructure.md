---
title: Contoso-konfigurera en infrastruktur för migrering | Microsoft Docs
description: Lär dig hur Contoso ställer in en Azure-infrastrukturen för migrering till Azure.
services: azure-migrate
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: raynew
ms.openlocfilehash: 9256f7af5b4d5aa8e523dd1b5b1d8d3eaebfd626
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34805051"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso - distribuerar en infrastruktur för migrering

Den här artikeln innehåller en närmare titt på hur Contoso ställer in sina lokala och Azure-infrastrukturen, del av förberedelserna för migreringen till Azure och för att köra verksamheten i en hybridmiljö. Det är en exempelarkitektur som är specifik för Contoso. Om du behöver dessa element beror på din migreringsstrategi. Om du sammanställer endast moln-ursprungliga appar i Azure, till exempel kanske du måste en mindre komplex nätverk struktur.

Det här dokumentet är andra i serie med artiklar i hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien finns bakgrundsinformation och ett antal distributionsscenarier som illustrerar hur du ställer in en infrastruktur för migrering, bedöma lokala resurser för migrering och kör olika typer av migreringar. Scenarier växa i komplexitet och vi ska lägga till ytterligare artiklar över tid.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
Artikel 1: översikt | En översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
Artikel 2: Distribuera en Azure-infrastrukturen (den här artikeln) | Beskriver hur Contoso förbereder dess lokalt och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla Contoso-Migreringsscenarier. | Tillgängligt
Artikel 3: Utvärdera lokala resurser | Visar hur Contoso körs en bedömning av sina lokala två nivåer SmartHotel app som körs på VMware. De utvärdera app virtuella datorer med den [Azure migrera](migrate-overview.md) tjänsten och SQL Server-databasen app med den [Azure databasen Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
Artikel 4: Flytta (lift och SKIFT) till virtuella Azure-datorer och en hanterad SQL-instans | Visar hur Contoso migrerar SmartHotel appen till Azure. Migrerar appen klientdel VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och app databasen med hjälp av [Azure Databasmigrering](https://docs.microsoft.com/azure/dms/dms-overview) tjänsten för att migrera till en hanterad SQL-instans. | Tillgängligt
Artikel 5: Flytta (lift och SKIFT) till virtuella Azure-datorer | Visar hur Contoso migrera SmartHotel appen VMs endast med Site Recovery.
Artikel 6: Flytta (lift och SKIFT) till virtuella datorer i Azure och SQL Server-Tillgänglighetsgrupper | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera appen virtuella datorer och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
Artikel 7: Flytta (lift och SKIFT) till virtuella Azure-datorer och Azure MySQL-Server | Visar hur Contoso migrerar SmartHotel app virtuella datorer med hjälp av Site Recovery och MySQL-arbetsstationen för att migrera (säkerhetskopiering och återställning) till en Azure MySQL Server-instans. | Tillgängligt

Om du vill använda sample-appen används i den här artikeln anges som öppen källkod och du kan ladda ned det från [github](https://github.com/Microsoft/SmartHotel360).


## <a name="overview"></a>Översikt

Det är viktigt att Contoso förbereder sin infrastruktur innan de kan migrera till Azure.  Vanligtvis finns fem bred områden som de behöver för att tänka på:

1. **Azure-prenumerationer**: hur ska de köpa och interagera med Azure-plattformen och tjänster?
2. **Hybrididentitet**: hur ska de hantera och styra åtkomsten till lokala och Azure-resurser efter migreringen? Hur utöka de eller flytta Identitetshantering i molnet?
3. **Katastrofåterställning och återhämtning**: hur de garanterar att sina appar och sin infrastruktur är flexibel om avbrott och katastrofer sker?
4. **Nätverk**: hur ska de utforma din nätverksinfrastruktur och upprätta anslutningen på lokalt och i Azure?
5. **Säkerhet och styrning**: hur ska de distributionen hybrid-/ Azure säker, och i enlighet med krav för säkerhet och styrning?

## <a name="before-you-start"></a>Innan du börjar

Innan vi börjar titta på infrastrukturen, kanske du vill läsa vissa bakgrundsinformation om Azure funktionerna som vi diskutera i den här artikeln:

- Det finns ett antal alternativ för att köpa Azure access, inklusive betala per användning, Enterprise avtal (EA), eller öppna licensiering från Microsoft återförsäljare eller från Microsoft-Partners vet som Cloud Solution Providers (CSP). Lär dig mer om [köpalternativ](https://azure.microsoft.com/pricing/purchase-options/), och Läs om hur [Azure-prenumerationer är ordnade](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Få en översikt över Azure [identitets- och åtkomsthantering](https://www.microsoft.com/en-us/trustcenter/security/identity). I synnerhet Lär dig mer om [Azure AD och utöka lokala AD till molnet](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Det finns en användbar e-bok om [identitets- och åtkomsthantering (IAM) i en hybridmiljö](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Azure tillhandahåller en stabil nätverksinfrastruktur med alternativ för hybridanslutning. Få en översikt över [nätverks- och åtkomstkontroll](https://docs.microsoft.com/azure/security/security-network-overview).
- Få en introduktion till [Azure-säkerhet](https://docs.microsoft.com/azure/security/azure-security), och Läs om hur du skapar en plan för [styrning](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>Lokal-arkitektur

Här är ett diagram som visar den aktuella Contoso lokal infrastrukturen.

 ![Contoso-arkitektur](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso har en huvudsakliga datacenter finns i den stad i New York i östra USA.
- De har tre ytterligare lokala grenar i USA.
- Det huvudsakliga datacentret är ansluten till internet via en fibre metro Ethernet-anslutning (500 Mbit/s).
- Varje gren är lokalt anslutet till internet med business klass anslutningar med IPSec VPN-tunnlar tillbaka till det huvudsakliga datacentret. Detta gör att hela nätverket anslutas permanent och optimerar Internetanslutning.
- Det huvudsakliga datacentret är fullständigt virtualiserat med VMware. De har två ESXi 6.5 virtualiseringsvärdar, hanteras av vCenter Server 6.5.
- Contoso använder Active Directory för Identitetshantering och DNS-servrar i det interna nätverket.
- Domänkontrollanter i datacenter kör på virtuella VMware-datorer. Domänkontrollanter på lokala grenar som körs på fysiska servrar.


## <a name="step-1-buy-and-subscribe-to-azure"></a>Steg 1: Köpa och prenumerera på Azure

Contoso behöver ta reda på hur du köper du Azure, hur du skapa prenumerationer och hur tjänster och resurser.

### <a name="buy-azure"></a>Köpa Azure

Contoso ska med en [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Detta innebär att en summa i förskott till Azure, gör att de få stora fördelar, inklusive flexibla alternativ för fakturering och optimerad priser.

- Contoso uppskattad vad deras varje år Azure tillbringar blir. När de loggat avtalet betalt de för det första året i sin helhet.
- Contoso måste använda alla sina åtaganden innan året är över eller de förlorar värdet för dessa dollar.
- Om du av någon anledning de överskrida sitt åtagande och använda faktureras Microsoft dem för skillnaden.
- Alla kostnaden ovanför åtaganden som kommer att vara samma priser och de som finns i deras avtal. Det finns inga böter för att gå över.

### <a name="manage-subscriptions"></a>Hantera prenumerationer

Efter att betala för Azure, Contoso måste du ta reda på hur du hanterar sina prenumerationer. De har ett EA och därmed ingen gräns för antalet Azure-prenumerationer som de kan konfigurera.

- En Azure-Företagsregistrering definierar hur formen företaget använder Azure-tjänster och definierar en core styrningsstrukturen.
- Contoso har bestämt en struktur (kallas även en enterprise-kodskelett) för sina Enterprise-registrering som ett första steg. De använde [i den här artikeln](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-governance) hjälper dem att förstå och utforma en kodskelett.
- För tillfället har Contoso vill använda en fungerande metod för att hantera sina prenumerationer.
    - I företaget får de en enskild IT-avdelningen som styr Azure budget. Det här är den enda gruppen med prenumerationer.
    - De kommer utökar den här modellen i framtiden, så att andra företagets grupper kan gå som avdelningar i Enterprise-registrering.
    - Contoso har strukturerade två prenumerationer, produktion och utveckling i IT-avdelningen.
    - Om Contoso kräver ytterligare prenumerationer i framtiden, behöver de hantera åtkomst, principer och efterlevnad för dessa prenumerationer. De kommer att kunna göra det genom att introducera [Azure Hanteringsgrupper](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), som ett ytterligare lager över prenumerationer.

    ![Enterprise-struktur](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Granska licensiering

Med prenumerationer konfigurerats titta Contoso på de Microsoft-licenser. Deras licensiering strategi beror på de resurser som de vill migrera till Azure och hur Azure VM och tjänster är markerat och distribueras. 

#### <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

När du distribuerar virtuella datorer i Azure kan innehålla en licens som Contoso debiteras per minut för programvara som används i standard bilder. Dock Contoso har en långsiktig Microsoft-kund och har underhålls EAs och och öppna licenser med software assurance (SA). 

Azure Hybrid förmånen ger en kostnadseffektiv metod för Contoso migrering genom att tillåta att spara på virtuella Azure-datorer och SQL Server arbetsbelastningar genom att konvertera eller återanvända Windows Server Datacenter och Standard edition licenser som omfattas med Software Assurance. Detta aktiverar Contoso att betala en lägre baserat beräknings-hastighet för virtuella datorer och SQL Server. [Läs mer](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>Licensmobilitet

Licensera Mobility via SA ger Microsoft Volume Licensing-kunder som Contoso möjlighet att distribuera berättigade server appar med aktiva SA på Azure. Detta eliminerar behovet av att skaffa nya licenser. Med några associerade mobility avgifter distribueras befintliga licenser enkelt i Azure. [Läs mer](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Reservera instanser för förutsägbar arbetsbelastningar

Förutsägbar arbetsbelastningar är de som måste alltid vara tillgänglig med virtuella datorer som körs. Till exempel branschspecifika appar, till exempel ett SAP ERP-system.  Å andra sidan är oförutsägbart arbetsbelastningar de som kan variera. Till exempel virtuella datorer som är på under hög efterfrågan och inaktiverat vid låg belastning tidpunkter.

![Reserverad instans](./media/contoso-migration-infrastructure/reserved-instance.png) 

Konsolen får mot använder reserverade instanser för specifika VM-instanser som de redan känner till måste underhållas för stora varaktigheter tid, både en rabatt och prioriterad kapacitet. Med hjälp av [Azure reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/), tillsammans med Azure Hybrid fördelar, Contoso kan spara upp till 82% rabatt reguljära betala per användning priser (April 2018).


## <a name="step-2-manage-hybrid-identity"></a>Steg 2: Hantera hybrididentitet

Ger och styra användarnas åtkomst till Azure-resurser med identitets- och åtkomsthantering (IAM) är ett viktigt steg i Dra ihop dina Azure-infrastrukturen.  

- Contoso vill utöka sina lokala Active Directory i molnet, i stället för att skapa ett nytt separat system i Azure.
- De skapar en Azure-baserade Active Directory för att göra detta.
- Contoso inte har Office 365, så att de behöver för att etablera en ny Azure AD.
- Office 365 använder Azure AD för användarhantering. Om Contoso var de skulle redan har en Azure AD-tenet och bör kontrollera att de är primära AD.
- [Lär dig mer](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) om Azure AD för Office 365, och Läs [hur du lägger till en prenumeration](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) till en befintlig Azure AD.

### <a name="create-an-azure-ad"></a>Skapa en Azure AD

Contoso använder den kostnadsfria Azure AD-version som har inkluderat med en Azure-prenumeration. Lägg till en ny AD-katalog på följande sätt:

1. I den [Azure-portalen](http://portal.azure.com/), Contoso går du till **skapar du en resurs** > **identitet** > **Azure Active Directory**.
2. I **skapa Directory**, de ange ett namn för katalogen, ett första domännamn och region där Azure AD-katalog som ska skapas.

    ![Skapa Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > När de skapar katalogen har ett första domännamn i formuläret domainname.onmicrosoft.com. Namnet kan inte ändras eller tas bort. I stället behöver de för att lägga till sina registrerade domännamn i Azure AD.

### <a name="add-the-domain-name"></a>Lägga till domännamnet

Om du vill använda deras standard domännamn måste Contoso att lägga till den som ett anpassat namn till Azure AD. Det här alternativet kan administratörer tilldela bekant användarnamn. Till exempel en användare kan logga in med e-postadress billg@contoso.com, i stället för att behöva billg@contosomigration.onmicrosoft.com. 

För att ställa in anpassat namn de lägger till den i katalogen, lägger du till en DNS-post och kontrollera namnet i Azure AD.

1. I **anpassade domännamn** > **Lägg till anpassad domän**, de lägger till domänen.
2. Att använda en DNS-post i Azure som de behöver för att registrera den med sin domänregistrator. 

    - I den **anpassade domännamn** listan de noterar DNS-information för namnet. Contoso använder en MX-post.
    - De behöver åtkomst till namnservern för att göra detta. Vid Contoso, de loggat in på domänen Contoso.com och skapa en ny MX-post för DNS-posten som tillhandahålls av Azure AD, med den information som anges.  
1. När DNS-posterna sprida information namn på domänen, klickar de på **Kontrollera** att kontrollera namn.

     ![Azure AD-DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Konfigurera lokalt och Azure grupper och användare

Nu när sina Azure AD är igång, Contoso behöver inte lägga till anställda till lokalt AD-grupper som ska synkroniseras till Azure AD. Vi rekommenderar att de använder lokalt namn som matchar namnen på resursgrupper i Azure. Detta gör det enklare att identifiera matchar för synkronisering.

#### <a name="create-resource-groups-in-azure"></a>Skapa resursgrupper i Azure

Azure-resursgrupper gruppera Azure-resurser. Om du använder ett resurs-ID kan Azure för att utföra åtgärder på resurser i gruppen.

- En Azure-prenumeration kan ha flera resursgrupper, men en resursgrupp får endast finnas i en enda prenumeration.
- Dessutom kan en enskild resursgrupp kan ha flera resurser, men en resurs kan bara tillhöra en grupp.

Contoso ställa in Azure-resursgrupper som sammanfattas i följande tabell.

**Resursgrupp** | **Detaljer**
--- | ---
**ContosoCobRG** | Den här gruppen innehåller alla resurser som rör kontinuitet för företag (COB).  Den omfattar valv Contoso skapas när du använder tjänsten Azure Site Recovery och Azure Backup-tjänsten.<br/><br/> Den omfattar också resurser som används för migrering, inklusive Azure migrera och databastjänster för migrering.
**ContosoDevRG** | Den här gruppen innehåller resurser för utveckling och testning.
**ContosoFailoverRG** | Den här gruppen fungerar som en stannplan för redundansväxlade resurser.
**ContosoNetworkingRG** | Den här gruppen innehåller alla nätverksresurser.
**ContosoRG** | Den här gruppen innehåller resurser som är relaterade till produktion appar och databaser.

De skapa resursgrupper enligt följande:

1. I Azure-portalen > **resursgrupper**, de lägger till en grupp.
2. För varje grupp ange de ett namn och prenumerationen som gruppen tillhör regionen.
3. Resursgrupper visas i den **resursgrupper** lista.

    ![Resursgrupper](./media/contoso-migration-infrastructure/resource-groups.png) 


#### <a name="create-matching-security-groups-on-premises"></a>Skapa matchande säkerhet grupper lokalt

1. Contoso Konfigurera säkerhetsgrupper med namn som matchar namnen på Azure-resursgrupper i sina lokala Active Directory.
 
    ![Lokala AD-säkerhetsgrupper](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. De skapa ytterligare en grupp som ska läggas till alla andra grupper för hantering. Den här gruppen har behörighet att alla resursgrupper i Azure. Ett begränsat antal globala administratörer kommer att läggas till den här gruppen.

### <a name="synchronize-ad"></a>Synkronisera AD

Contoso vill tillhandahålla en gemensam identitet för åtkomst till resurser på lokalt och i molnet. Om du vill göra detta måste integrera de sina lokala Active Directory med Azure AD. Med den här modellen:

- Användare och företag kan dra nytta av en enda identitet för åtkomst till lokala program och molntjänster som Office 365 eller tusentals andra webbplatser på internet.
- Administratörer kan utnyttja grupperna i AD att implementera [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) i Azure.

Att underlätta integration Contoso används den [Azure AD Connect-verktyget](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). När du installerar och konfigurerar verktyget på en domänkontrollant, synkronisering av lokalt lokala AD identiteter med Azure AD. 

### <a name="download-the-tool"></a>Ladda ned verktyget

1. I Azure-portalen, Azure går till **Azure Active Directory** > **Azure AD Connect**, och laddar ned den senaste versionen av verktyget till den server som används för synkronisering.

    ![Hämta AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. De startar den **AzureADConnect.msi** installationen med hjälp av **Använd standardinställningar**. Detta är de vanligaste installationen och kan användas för en sammanhållna-topologi med synkronisering av lösenords-hash för autentisering.

    ![AD Connect-guiden](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. I **Anslut till Azure AD**, de ange autentiseringsuppgifter för att ansluta till Azure AD (i formatet CONTOSO\admin eller contoso.com\admin).

    ![AD Connect-guiden](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. I **Anslut till AD DS**, de ange autentiseringsuppgifter för sina lokala AD.

     ![AD Connect-guiden](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. I **redo att konfigurera**, de klickar på **starta synkroniseringsprocessen när konfigurationen är klar** att starta synkroniseringen direkt. De installerar.


- Contoso har en direkt anslutning till Azure. Om din lokala AD finns bakom en proxyserver läsa det här [artikel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Efter den första synkroniseringen lokala AD-objekt kan ses i Azure AD.

    ![Lokala AD i Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- I varje grupp representeras anställda på Contoso IT-teamet, baserat på deras roll.

    ![Lokala AD-medlemmar i Azure](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Ställ in RBAC

Azure [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) Aktivera detaljerad åtkomsthantering för Azure. Med RBAC kan du bevilja exakt den åtkomstnivå som användarna behöver för att kunna utföra sitt arbete. Du kan tilldela rollen RBAC till användare, grupper och program på en omfångsnivå. Omfånget för en rolltilldelning kan vara en prenumeration, resursgrupp eller en enskild resurs. 

Contoso nu tilldela roller till AD-grupper som de synkroniseras lokalt.

1. I den **ControlCobRG** resursgrupp, de klickar på **åtkomstkontroll (IAM)** > **Lägg till**.
2. I **lägga till behörigheter** > **rollen**, markerar du de **deltagare**, och välj den **ContosoCobRG** AD-grupp i listan. Gruppen visas sedan i **markerade medlemmar** lista. 
3. Upprepas de med samma behörigheter för andra resursgrupper (förutom för **ContosoAzureAdmins**), genom att lägga till behörigheterna som deltagare AD-kontot som matchar resursgruppen.
4. För den **ContosoAzureAdmins** AD-grupp som de tilldela den **ägare** roll.

    ![Lokala AD-medlemmar i Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>Steg 3: Utforma för återhämtning och katastrofåterställning

Azure-resurser har distribuerats inom regioner.
- Regioner är uppdelade i geografiska områden och land, suveränitet, efterlevnad och återhämtning datakrav gäller inom geografisk gränser.
- En region består av en uppsättning datacenter. Dessa Datacenter distribueras inom en fördröjning definierats perimeternätverket och anslutna via ett dedikerat nätverk för regional låg latens.
- Varje Azure-region paras ihop med en annan region för återhämtning.
- Läs mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/), och förstå [hur regioner länkas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso har valt att gå med östra USA 2 (finns i Virginia) som sina primära region och centrala USA som deras sekundär region. Det finns ett antal orsaker:

- Contoso-datacenter finns i New York och de anses vara fördröjning till närmaste datacenter.
- Östra USA 2 har alla tjänster och produkter som de behöver för att använda. Inte alla Azure-regioner är desamma som de produkter och tjänster som är tillgängliga. Du kan granska [Azure produkter efter region](https://azure.microsoft.com/global-infrastructure/services/).
- Centrala USA är den parad Azure-regionen för östra USA 2.

Eftersom de tycker om deras hybridmiljöer måste Contoso att tänka på hur du skapar återhämtning och en strategi för katastrofåterställning i sin design region. Allmänna ordalag strategier mellan en enskild region-distribution som förlitar sig på Azure-plattformen funktioner så som feldomäner och regionala länkar för återhämtning, genom att en fullständig aktiv-aktiv modell i vilken molntjänster och databas är distribuerade och underhåll användare från två regioner.

Contoso har valt att ta en mellersta väg. De ska distribuera sina appar och resurser i en primär region och förvara hela infrastruktur på den sekundära regionen så att den är redo att fungera som en fullständig säkerhetskopiering vid fullständig app katastrofåterställning eller fel region.


## <a name="step-4-design-a-network-infrastructure"></a>Steg 4: Skapa en nätverksinfrastruktur

Contoso är redo att överväga en strategi för nätverk med deras region designen på plats. De behöver tänka på hur sina lokala datacenter och Azure för att ansluta och kommunicera med varandra och hur du utformar din nätverksinfrastruktur i Azure. Mer specifikt behöver de för att:

**Planera hybrid nätverksanslutning**: ta reda på hur de ska du ansluter till nätverk över lokalt och Azure.
**Skapa en Azure-nätverksinfrastruktur**: Bestäm hur de ska distribuera nätverk över regioner. Hur kommunicerar nätverk inom samma region och över regioner.
**Utforma och konfigurera Azure-nätverk**: Konfigurera Azure-nätverk och undernät, och bestämma vad kommer att finnas i dem.

### <a name="plan-hybrid-network-connectivity"></a>Planera hybrid-nätverksanslutning

Contoso anses vara en [antal arkitekturer](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) för hybridnätverk mellan Azure och sina lokala datacenter. [Läs mer](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) om att jämföra alternativen.

Contoso lokala nätverkets infrastruktur för närvarande består av sina datacenter i New York som en påminnelse och lokala filialer i östra delen av USA: S.  Alla platser har en business klass anslutning till internet.  Varje bransch är anslutna till datacenter via en IPSec-VPN-tunnel via internet.

![Contoso-nätverket](./media/contoso-migration-infrastructure/contoso-networking.png) 

Här är hur Contoso beslutat att implementera hybridanslutning:

1. Skapa en ny plats-till-plats VPN-anslutning mellan Contoso datacenter i New York och två Azure-regioner i östra USA 2 och centrala USA.
2. Branch office trafik för virtuella Azure-nätverk ska vidarebefordra via det huvudsakliga Contoso-datacentret. 
3. De ska upprätta en ExpressRoute-anslutning mellan sina datacenter och Azure-regioner som de skala upp sina Azure-distribution. När detta inträffar kommer de behåller plats-till-plats VPN-anslutningen endast för växling vid fel.
    - [Lär dig mer](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) om att välja mellan en VPN- och ExpressRoute-hybridlösning.
    - Kontrollera [ExpressRoute platser och support](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Endast VPN**

![Contoso VPN](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**VPN- och ExpressRoute**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Design av nätverksinfrastruktur för Azure

Det är viktigt att Azure get nätverk på plats på ett sätt som gör att deras hybriddistribution säker och skalbar. Om du vill göra detta Contoso tar en långsiktig strategi och utformar virtuella nätverk (Vnet) för återhämtning och enterprise-redo. [Lär dig mer](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) om hur du planerar Vnet.

Contoso har beslutat att implementera en modell med nav-hubb-nätverk för att ansluta två regioner:

- I varje region använder Contoso en nav och eker-modell.
- Om du vill ansluta nätverk och hubbar använder Contoso Azure-nätverk peering.

#### <a name="network-peering"></a>Nätverk peering

Azure tillhandahåller nätverk-peering för att ansluta Vnet och hubbar. Globala peering tillåter anslutningar mellan Vnet/NAV i olika regioner. Lokala peering ansluter Vnet i samma region. VNet-peering ger många fördelar:

- Nätverkstrafiken mellan peerkoppla Vnet är privat.
- Trafik mellan till Vnet sparas på Microsoft stamnät network. Inga offentliga internet, gateways eller kryptering krävs i kommunikationen mellan till Vnet.
- Peering tillhandahåller en standard, låg latens och hög bandbredd anslutning mellan resurser i olika Vnet.

[Lär dig mer](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) om nätverket peering.

#### <a name="hub-to-hub-across-regions"></a>Hubben till hubben över regioner

Contoso vill distribuera en hubb i varje region. Ett nav är ett virtuellt nätverk (VNet) i Azure som fungerar som en central punkt för anslutning till lokalt nätverk. Hubben Vnet kommer att ansluta till varandra med globala VNet-peering. Globala VNet-peering ansluter Vnet i Azure-regioner.

- Hubben i varje region är peerkopplat till dess partner-hubben i andra region.
- Hubben peerkoppla till varje nätverk i regionen och kan ansluta till alla nätverksresurser.

    ![Globala peering](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>NAV och ekrar inom en region

I varje region distribuerar Contoso Vnet för olika ändamål som ekrar nätverk från region-hubben. Vnet inom en region använder peering för att ansluta till sina NAV och till varandra.

#### <a name="design-the-hub-network"></a>Design hub-nätverk

Inom NAV och ekrar modellen som Contoso har valt de behöver för att tänka på hur trafiken från sina lokala datacenter och från internet, vidarebefordras. Här är hur Contoso har valt att hantera routning för både östra USA 2 och centrala USA hubs:

- De skapar ett nätverk som kallas ”omvänd c”, eftersom det är den sökväg som följer du paketen från inkommande till utgående nätverket.
- Deras nätverksarkitektur har två gränser, en ej betrodd frontend perimeter zon och en serverdel betrodda zonen.
- En brandvägg har ett nätverkskort i varje zon och hur du styr åtkomst till betrodda zoner.
- Från internet:
    - Internet-trafik, träffar belastningsutjämnade offentlig IP-adress i perimeternätverket.
    - Den här trafiken dirigeras via brandväggen och omfattas brandväggsregler.
    - När nätverket åtkomstkontroller implementeras vidarebefordras trafik till lämplig plats i den betrodda zonen.
    - Utgående trafik från VNet kommer att dirigeras till internet med användardefinierade vägar (udr: er). Trafiken är tvingas genom brandväggen och kontrolleras i enlighet med principer för Contoso.
- Från Contoso-datacenter:
    - Inkommande trafik via VPN plats-till-plats (eller ExpressRoute) träffar offentliga IP-adressen för Azure VPN-gatewayen.
    - Trafik dirigeras via brandväggen och omfattas brandväggsregler.
    - När du använder regler vidarebefordras trafik till en intern belastningsutjämnare (Standard SKU) i zonen för betrodda internt undernät.
    - Utgående trafik från undernätet som betrodda lokalt datacenter via VPN dirigeras via brandväggen och regler som används innan du fortsätter via plats-till-plats VPN-anslutningen.



### <a name="design-and-set-up-azure-networks"></a>Utforma och konfigurera Azure-nätverk

Contoso är redo att konfigurera Azure-nätverk och undernät med ett nätverk och routning-topologi på plats.

- Contoso kommer att implementera ett privat nätverk klass A i Azure (0.0.0.0 127.255.255.255). Det här fungerar, eftersom lokalt de aktuella har en klass B privat adressutrymme 172.160.0/16 så att de kan vara säker på att det finns inte någon överlapp mellan adressintervall.
- De ska du distribuera Vnet i sina primära och sekundära regioner.
- De ska använda en namngivningskonvention som innehåller prefixet **VNET** och region förkortningen **EUS2** eller **anpassad Communi**. Med den här standarden hub-nätverk får namnet **VNET-hubb-EUS2** (östra USA 2) och **VNET-hubb-anpassad Communi** (centrala USA).
- Contoso har inte en [IPAM lösning](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), så att de behöver för att planera för nätverksroutning utan NAT.


#### <a name="virtual-networks-in-east-us-2"></a>Virtuella nätverk i östra USA 2

Östra USA 2 är den primära region som Contoso använder för att distribuera resurser och tjänster. Här är hur de ska du systemarkitekt nätverk:

- **Hubb**: hubben VNet i östra USA 2 är en central plats primära anslutningen till sina lokala datacenter.
- **Vnet**: pinnar Vnet i östra USA 2 kan användas för att isolera arbetsbelastningar om det behövs. Förutom Hub-VNet har Contoso två ekrar Vnet i östra USA 2:
    - **VNET-DEV-EUS2**. Detta virtuella nätverk tillhandahåller utveckling och test-teamet kommer ett fullt fungerande nätverk för dev projekt. Den fungerar som en pilot område och förlitar sig på produktionsinfrastruktur ska fungera.
    - **VNET-PRODUKTPRENUMERATION-EUS2**: Azure IaaS olika komponenter kommer att finnas i det här nätverket. 
    -  Varje virtuellt nätverk har sin egen unika adressutrymme, utan överlappande. De vill konfigurera routning utan NAT.
- **Undernät**:
    - Det ska finnas ett undernät i varje nätverk för varje app-nivå
    - Varje undernät i nätverket för produktion har en matchande undernät i Dev VNet.
    - Dessutom har produktionsnätverket ett undernät för domänkontrollanter.

Vnet i östra USA 2 sammanfattas i följande tabell.

**Virtuella nätverk** | **intervallet** | **peer**
--- | --- | ---
**VNET-HUBB-EUS2** | 10.240.0.0/20 | VNET-HUBB-CUS2, VNET-DEV-EUS2, VNET-PRODUKTPRENUMERATION-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUBB-EUS2
**VNET-PRODUKTPRENUMERATION-EUS2** | 10.245.32.0/20 | VNET-HUBB-EUS2 VNET-PRODUKTPRENUMERATION-ANPASSAD COMMUNI

![Nav/eker i primära region](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Undernät i East oss 2 Hub-nätverk (VNET-hubb-EUS2)

**Undernät eller en zon** | **CIDR** | ** Användbara IP-adresser
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB UntrustZone** | 10.240.2.0/24 | 251
**OB TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Undernät i East oss 2 Dev-nätverk (VNET-DEV-EUS2)

Dev VNet används av Utvecklingsteamet som en pilot område. Den har tre undernät.

**Undernät** | **CIDR** | **adresser** | **I undernät**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1 019 | Frontends/webbnivå virtuella datorer
**DEV-APP-EUS2** | 10.245.20.0/22 | 1 019 | App-nivå virtuella datorer
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | Databasen virtuella datorer


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Undernät i East oss 2 produktionsnätverket (VNET-PRODUKTPRENUMERATION-EUS2)

Azure IaaS-komponenter finns i produktionsmiljön. Varje app-nivå har sitt eget undernät. Undernät stämmer Dev-nätverket med att lägga till ett undernät för domänkontrollanter.

**Undernät** | **CIDR** | **adresser** | **I undernät**
--- | --- | --- | ---
**PROD-FE-EUS2** | 10.245.32.0/22 | 1 019 | Frontends/webbnivå virtuella datorer
**PROD-APP-EUS2** | 10.245.36.0/22 | 1 019 | App-nivå virtuella datorer
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | Databasen virtuella datorer
**PROD-DC-EUS2** | 10.245.42.0/23 | 251 | Domänkontrollanten virtuella datorer


![Hubb nätverksarkitektur](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>Virtuella nätverk i centrala USA (sekundär region)

Centrala USA är Contosos sekundär region. Här är hur de ska du systemarkitekt nätverk:

- **Hubb**: hubben Vnet i östra USA 2 är den centrala anslutning till sina lokala datacenter och ekrar Vnet i östra USA 2 kan användas för att isolera arbetsbelastningar om så krävs, hanteras separat från andra ekrar.
- **Vnet**: de har två Vnet i centrala USA:
    - VNET-PRODUKTPRENUMERATION-ANPASSAD COMMUNI. Detta virtuella nätverk är ett produktionsnätverk som liknar VNET PROD_EUS2. 
    - VNET-ASR-ANPASSAD COMMUNI. Detta virtuella nätverk ska fungera som en plats där virtuella datorer skapas efter växling från lokalt eller som en plats för Azure virtuella datorer som har redundansväxlats från den primära servern till den sekundära regionen. Det här nätverket är liknande till produktionsnätverk, men utan några domänkontrollanter på den.
    -  Varje virtuellt nätverk i regionen ska ha sin egen adressutrymme utan överlappande. De vill konfigurera routning utan NAT.
- **Undernät**: undernäten ska vara konstruerad på liknande sätt som i dessa i östra USA 2. Undantaget är att de inte behöver ett undernät för domänkontrollanter.

Vnet i centrala USA sammanfattas i följande tabell.

**Virtuella nätverk** | **intervallet** | **peer**
--- | --- | ---
**VNET-HUBB-ANPASSAD COMMUNI** | 10.250.0.0/20 | VNET-HUBB-EUS2, VNET-ASR-ANPASSAD COMMUNI, VNET-PRODUKTPRENUMERATION-ANPASSAD COMMUNI
**VNET-ASR-ANPASSAD COMMUNI** | 10.255.16.0/20 | VNET-HUBB-ANPASSAD COMMUNI, VNET-PRODUKTPRENUMERATION-ANPASSAD COMMUNI
**VNET-PRODUKTPRENUMERATION-ANPASSAD COMMUNI** | 10.255.32.0/20 | VNET-HUBB-ANPASSAD COMMUNI, VNET-ASR-ANPASSAD COMMUNI, VNET-PRODUKTPRENUMERATION-EUS2  


![Nav/eker i parad region](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>Undernät i Central oss hubb-nätverk (VNET-hubb-anpassad Communi)

**Undernät** | **CIDR** | **Användbara IP-adresser**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB UntrustZone** | 10.250.2.0/24 | 251
**OB TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>Undernät i centrala oss produktionsnätverket (VNET-PRODUKTPRENUMERATION-anpassad Communi)

Parallellt med produktionsnätverket i den primära regionen östra USA 2 att det finns ett produktionsnätverk i den sekundära regionen centrala USA. 

**Undernät** | **CIDR** | **adresser** | **I undernät**
--- | --- | --- | ---
**PROD-FE-ANPASSAD COMMUNI** | 10.255.32.0/22 | 1 019 | Frontends/webbnivå virtuella datorer
**PROD-PROGRAM-ANPASSAD COMMUNI** | 10.255.36.0/22 | 1 019 | App-nivå virtuella datorer
**PROD-DB-ANPASSAD COMMUNI** | 10.255.40.0/23 | 507 | Databasen virtuella datorer
**PROD-DC-ANPASSAD COMMUNI** | 10.255.42.0/24 | 251 | Domänkontrollanten virtuella datorer

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Undernät i centrala USA/återställning av failover-nätverk i centrala USA (VNET-ASR-anpassad Communi)

VNET-ASR-anpassad Communi nätverket används för växling mellan regioner. Site Recovery används för att replikera och redundansväxla virtuella Azure-datorer mellan regioner. Den fungerar även som en Contoso datacenter till Azure-nätverk för skyddade arbetsbelastningar som förblir lokalt men växla över till Azure för katastrofåterställning.

VNET-ASR-anpassad Communi är samma grundläggande undernät som produktion VNET i östra USA 2, men utan att behöva ett domain controller undernät.

**Undernät** | **CIDR** | **adresser** | **I undernät**
--- | --- | --- | ---
**ASR-FE-ANPASSAD COMMUNI** | 10.255.16.0/22 | 1 019 | Frontends/webbnivå virtuella datorer
**ASR-PROGRAM-ANPASSAD COMMUNI** | 10.255.20.0/22 | 1 019 | App-nivå virtuella datorer
**ASR-DB-ANPASSAD COMMUNI** | 10.255.24.0/23 | 507 | Databasen virtuella datorer

![Hubb nätverksarkitektur](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Konfigurera peerkoppla anslutningar

Att kommer peerkoppla hubben i varje region till hubben i andra region och alla Vnet för regionen hubb. Detta ger hubbar kommunicera och för att visa alla Vnet inom en region. Tänk på följande:

- Peering skapar en dubbelsidig anslutning. En från den initierande peer i det första VNet och ett annat namn på den andra VNet.
- I en hybriddistribution behöver trafik som skickas mellan peer-datorer kan ses från VPN-anslutning mellan lokala datacenter och Azure. Om du vill aktivera det finns vissa specifika inställningar som måste anges för peerkoppla anslutningar.

Contoso måste tillåta trafik vidarebefordras och färdas över VPN-gatewayer för alla anslutningar från ekrar Vnet genom navet till lokala datacenter.

##### <a name="domain-controller"></a>Domänkontrollant

Contoso vill trafik mellan EUS2 hubb/produktionsnätverk, och via VPN-anslutning till lokalt för domänkontrollanter i VNET-PRODUKTPRENUMERATION-EUS2-nätverk. De måste tillåta följande för att göra detta:

1. **Tillåt vidarebefordrad trafik** och **Tillåt gateway överföring konfigurationer** på peered anslutningen. I vårt exempel skulle detta vara VNET-hubb-EUS2 till VNET-PRODUKTPRENUMERATION-EUS2 anslutning.

    ![Peering](./media/contoso-migration-infrastructure/peering1.png)

2. **Tillåt vidarebefordrad trafik** och **använder fjärråtkomst gateways** på andra sidan av peering på VNET PRODUKTPRENUMERATION-EUS2 till VNET-hubb-EUS2 anslutning.

    ![Peering](./media/contoso-migration-infrastructure/peering2.png)

3. Lokalt de ställer in en statisk väg som leder lokal trafik att dirigera över VPN-tunnel till VNet. Konfigurationen skulle slutföras på den gateway som innehåller VPN-tunnel från Contoso till Azure. Contoso använder Windows Routing och fjärråtkomst för den här.

    ![Peering](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Produktionsnätverk 

Ett spoked peer-nätverk inte kan se ett spoked peer-nätverk i en annan region via en hub.

De behöver för att skapa en direktanslutning peered för VNET-PRODUKTPRENUMERATION-EUS2 och VÄNDNING-PROD-anpassad Communi för Contosos produktionsnätverk i båda områden med se varandra. 

![Peering](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Konfigurera DNS

När du distribuerar resurser i virtuella nätverk har ett antal olika alternativ för namnmatchning för domänen. Du kan använda namnmatchning som tillhandahålls av Azure eller ange DNS-servrar för namnmatchning. Typ av namnmatchning som du använder beror på hur dina resurser måste kommunicera med varandra. Hämta [mer](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) om tjänsten Azure DNS.

Contoso har valt att Azure DNS-tjänsten inte är ett bra val i sina hybridmiljö. I stället ska de använda sina lokala DNS-servrar.

- Eftersom det är en hybrid-nätverket alla de virtuella datorerna lokalt och i Azure måste kunna matcha namn ska fungera korrekt. Det innebär att anpassade DNS-inställningar måste tillämpas på alla Vnet.
- Contoso har för närvarande domänkontrollanter som distribueras i Contoso-datacenter och på avdelningskontor. Deras primära DNS-servrar är CONTOSODC1(172.16.0.10) och CONTOSODC2(172.16.0.11)
- När Vnet distribueras anges lokalt domänkontrollanter som ska användas som DNS-servern i nätverk. 
- Om du vill konfigurera det här, när du använder när du använder anpassade DNS på VNet, måste du lägga till Azures rekursiv matchare IP-adress (till exempel 168.63.129.16) i listan med DNS.  Om du vill göra detta, konfigurera Contoso DNS-serverinställningarna för varje virtuella nätverk. Anpassade DNS-inställningar för nätverkets VNET-hubb-EUS2 skulle till exempel vara följande:
    
    ![Anpassad DNS](./media/contoso-migration-infrastructure/custom-dns.png)

Förutom sina lokala domänkontrollanter kommer Contoso att implementera fyra mer för att stödja sina Azure-nätverk för varje region. Det här är vad de ska distribuera i Azure.

**Region** | **DC** | **Virtuella nätverk** | **Undernät** | **IP-adress**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PRODUKTPRENUMERATION-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PRODUKTPRENUMERATION-EUS2 | PROD-DC-EUS2 | 10.245.42.5
ANPASSAD COMMUNI | CONTOSODC5 | VNET-PRODUKTPRENUMERATION-ANPASSAD COMMUNI | PROD-DC-ANPASSAD COMMUNI | 10.255.42.4
ANPASSAD COMMUNI | CONTOSODC6 | VNET-PRODUKTPRENUMERATION-ANPASSAD COMMUNI | PROD-DC-ANPASSAD COMMUNI | 10.255.42.4

När du har distribuerat lokalt domänkontrollanter Contoso måste du uppdatera DNS-inställningarna för nätverk på antingen region för att inkludera de nya domänkontrollanterna i DNS-serverlistan.



#### <a name="set-up-domain-controllers-in-azure"></a>Konfigurera domänkontrollanter i Azure

När du har uppdaterat nätverksinställningar Contoso är redo att bygga ut sina domänkontrollanter i Azure.

1. I Azure-portalen distribuera de en ny Windows virtuell dator till lämpligt virtuellt nätverk.
2. Tillgänglighetsuppsättningar skapas på varje plats för den virtuella datorn. Tillgänglighetsuppsättningar gör du följande:
    - Se till att Azure-strukturen separerar de virtuella datorerna till olika infrastrukturer i Azure-Region. 
    -  Tillåter Contoso ska vara tillämplig för 99,95% SLA för virtuella datorer i Azure.  [Läs mer](https://docs.microsoftcom/azure/virtual-machines/windows/regions-and-availability#availability-sets).

    ![Tillgänglighetsgruppen](./media/contoso-migration-infrastructure/availability-group.png) 
3. När den virtuella datorn distribueras pennan de nätverksgränssnittet för den virtuella datorn. Här är de angetts privata IP-adress till statisk och ange en giltig adress.

    ![VM-NÄTVERKSKORT](./media/contoso-migration-infrastructure/vm-nic.png)

4. Nu kan ansluta de en ny datadisk till den virtuella datorn. Den här disken innehåller Active Directory-databasen och SYSVOL-resursen. 
    - Antalet IOPS som stöder avgör storleken på disken.
    - Med tiden behöva diskstorleken öka när miljön växer.
    - Enheten får inte anges till läsning och skrivning för cachelagring av värden. Active Directory-databaser stöder inte detta.

     ![Active Directory-disk](./media/contoso-migration-infrastructure/ad-disk.png)

5. När disken läggs de ansluta till den virtuella datorn via fjärrskrivbord och öppna Server Manager.
6. I **fil- och lagringstjänster**, de kör guiden Ny volym, se till att enheten är angivna bokstav F: eller senare på den lokala virtuella datorn.

     ![Guiden Ny volym](./media/contoso-migration-infrastructure/volume-wizard.png)

7. I Serverhanteraren som de lägger till den **Active Directory Domain Services** roll. Sedan kan konfigurera de den virtuella datorn som en domänkontrollant.

      ![Serverroll](./media/contoso-migration-infrastructure/server-role.png)  

9. När den virtuella datorn är konfigurerad som en Domänkontrollant och startas om, öppna DNS-hanteraren och konfigurera Azure DNS-matchare som vidarebefordrare.  Detta gör att domänkontrollanten ska vidarebefordra DNS-frågor som den inte kan lösa i Azure DNS.

    ![DNS-vidarebefordrare](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Nu kan sedan uppdatera anpassade DNS-inställningar för varje virtuellt nätverk med lämpliga domänkontroller för VNet-region. De omfattar lokala domänkontrollanter i listan.

### <a name="set-up-active-directory"></a>Konfigurera Active Directory

AD är en kritisk tjänst för nätverk och måste vara korrekt konfigurerad. Contoso att skapa AD-platser för Contoso-datacenter och EUS2 och anpassad Communi regioner.  

1. De skapar två nya webbplatser (AZURE EUS2 och AZURE-anpassad Communi) tillsammans med webbplatsen datacenter (ContosoDatacenter).
2. Efter att platserna kan skapa de undernät på platser, så att den matchar Vnet och datacenter.

    ![DC-undernät](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Sedan skapar de två platslänkar för att ansluta allt. Domänkontrollanterna flyttas sedan till deras plats.

    ![DC länkar](./media/contoso-migration-infrastructure/dc-links.png)

4. När allting har konfigurerats, är Active Directory-topologin på plats.
    
    ![DC-replikering](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Med allt klar visas en lista med domänkontrollanter och platser i lokala Active Directory Administrationscenter.

    ![AD Administrationscenter](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>Steg 5: Planera för styrning

Azure tillhandahåller en uppsättning styrning kontroller över tjänster och Azure-plattformen. [Läs mer](https://docs.microsoft.com/azure/security/governance-in-azure) för en grundläggande förståelse för alternativ.

Eftersom de konfigurera identitet och åtkomstkontroll Contoso har redan börjat gjorda vissa aspekter av styrning och säkerhet. Det finns allmänna ordalag tre områden som de behöver tänka på:

- **Principen**: principen i Azure gäller och tillämpar regler och effekter över din reosurces så att resurser vara kompatibla med företagets krav och serviceavtal.
- **Låser**: Azure kan du låsa prenumerationer, resursgrupper och andra resurser, så att de kan bara ändras av de som har behörighet till detta.
- **Taggar**: resurser kan kontrolleras, granskas och hanteras med taggar. Taggar bifoga metadata till resurser med information om resurser eller ägare.

### <a name="set-up-policies"></a>Konfigurera principer

Princip för Azure-tjänsten utvärderar dina resurser, söka efter de inte är kompatibla med principdefinitioner som du har på plats. Till exempel kanske en princip som endast tillåter endast vissa typer av virtuella datorer eller kräver resurser som har en specifik tagg. 

Azure-principer ange en principdefinition av och tilldelning av principer ange omfånget som en princip som ska användas. Omfånget kan variera mellan en hanteringsgrupp till en resursgrupp. [Läs](https://docs.microsoft.com/azure/azure-policy/create-manage-policy) om att skapa och hantera principer.

Contoso vill komma igång med några principer:

- De vill ha en princip för att se till att resurser kan bara distribueras i EUS2 och anpassad Communi regioner.
- De vill begränsa VM SKU: er till godkända SKU. Avsikten är att säkerställa att dyra VM SKU: er inte används.

#### <a name="limit-resources-to-regions"></a>Gränsen resurser till regioner

Contoso används den inbyggda principdefinitionen **tillåtna platser** att begränsa resurs regioner.

1. I Azure-portalen klickar du på **alla tjänster**, och searh för **princip**.
2. Välj **tilldelningar** > **tilldela principen**.
3. I principlistan väljer **tillåtna platser**.
4. Ange **omfång** till namnet på Azure-prenumeration och välj de två regionerna i listan över tillåtna.

    ![Princip för tillåtna regioner](./media/contoso-migration-infrastructure/policy-region.png)

5. Principen är som standard med **neka**, vilket innebär att om någon startar en distribution i den prenumeration som inte är i EUS2 eller anpassad Communi, distributionen misslyckas. Det här är vad händer om någon i Contoso-prenumeration försöker ställa in en distribution i USA, västra.

    ![Principen misslyckades](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Tillåt specifika VM SKU: er

Contoso använder inbyggda principdefinitionen **att virtuella datorer SKU: er** att begränsa vilka typer av virtuella datorer som kan skapas i prenumerationen. 

![Principen SKU](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Kontrollera efterlevnad av policy

Principer träda i kraft omedelbart och Contoso kan kontrollera resurser för kompatibilitet. 

1. I Azure-portalen klickar du på den **kompatibilitet** länk.
2. Kompatibilitet instrumentpanelen visas. Du kan öka detaljnivån för mer information.

    ![Principefterlevnad](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Ställ in Lås

Contoso har länge använt ITIL-ramverket för hanteringen av sina system. En av de viktigaste aspekterna av framework är ändringskontroll och Contoso vill se till att ändra kontroll är implementerad i sin Azure-distribution.

Contoso ska implementera lås på följande sätt:

- Produktions- eller failover-komponenten måste vara i en resursgrupp som har en ReadOnly-Lås.  Det innebär att om du vill ändra eller ta bort objekt för produktion, låset måste tas bort. 
- Icke-produktionsmiljö resursgrupper har CanNotDelete Lås. Detta innebär att behöriga användare kan läsa eller ändra en resurs, men det går inte att ta bort den.

[Lär dig mer](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) om Lås.

### <a name="set-up-tagging"></a>Ställ in taggning

Om du vill spåra resurser som de har lagts till, kommer det att allt viktigare för Contoso vill associera resurser med en lämplig avdelning, kund och miljö. 

Förutom att tillhandahålla information om resurser och ägare aktiverar taggar Contoso till sammanställd och gruppera resurser och använda dessa data för återbetalning.

Contoso behöver visualisera deras Azure tillgångar på ett sätt som passar för verksamheten. Till exempel men rollen eller avdelning. Observera att resurser inte behöver finnas i samma resursgrupp att dela en tagg. Om du vill göra detta skapar Contoso en enkel taggtaxonomi så att alla använder samma taggar.

**Taggnamn** | **Värde**
--- | ---
Kostnadsställe | 12345: Det måste vara ett giltigt kostnadsställe från SAP.
Affärsenheten | Namnet på affärsenhet (från SAP). Matchar CostCenter.
ApplicationTeam | E-alias för det team som äger stöd för appen.
Katalognamn | Namnet på appen eller ShareServices, per tjänstkatalogen som har stöd för resursen.
ServiceManager | E-alias av ITIL Service Manager för resursen.
COBPriority | Ange av verksamheten för BCDR prioritet. Värden från 1 till 5.
ENV | DEV-, STG, PRODUKTPRENUMERATION är möjliga värden. Som representerar utveckling, mellanlagring och produktion.

Exempel: 

 ![Azure taggar](./media/contoso-migration-infrastructure/azure-tag.png)

När du har skapat taggen Contoso gå tillbaka och skapa nya Azure principdefinitioner och tilldelningar för att framtvinga användningen av taggarna som krävs i organisationen.


## <a name="step-6-consider-security"></a>Steg 6: Tänka på säkerheten

Säkerhet är viktigt i molnet och Azure tillhandahåller en mängd olika säkerhetsverktyg och funktioner. Dessa hjälpa dig att skapa säkra lösningar på säker Azure-plattformen. Läs [förtroende betrodda molnet](https://azure.microsoft.com/overview/trusted-cloud/) lära dig mer om Azure-säkerhet.

Det några huvudsakliga skillnader för Contoso att tänka på

- **Azure Security Center**: Azure Security Center ger enhetlig hantering och skydd avancerade över hybrid cloud arbetsbelastningar. Med Security Center kan du tillämpa säkerhetsprinciper i arbetsbelastningarna, begränsa hotexponeringen samt identifiera och åtgärda attacker.  [Läs mer](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Nätverkssäkerhetsgrupper (NSG: er)**: en NSG är ett filter (brandvägg) som innehåller en lista med regler som, när tillämpas, tillåter eller nekar nätverkstrafik till resurser som är anslutna till virtuella Azure-nätverk. [Läs mer](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Datakryptering**: Azure Disk Encryption är en funktion som hjälper dig att kryptera din Windows- och Linux IaaS virtuella diskar. [Läs mer](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Arbeta med Azure Security Center

Contoso är ute efter en snabb överblick över säkerhetsläget i sina nya hybridmoln och specifikt sina Azure arbetsbelastningar.  Contoso har därför valt att implementera Azure Security Center från och med följande funktioner: 

- Centraliserad principhantering
- Kontinuerlig kontroll
- Användbara rekommendationer 

#### <a name="centralize-policy-management"></a>Centralisera hantering av Grupprincip

Med centraliserad hantering av säkerställer Contoso säkerhetskraven genom att hantera principer centralt över hela miljön. De kan enkelt och snabbt implementera en princip som gäller för alla sina Azure-resurser.

![Säkerhetsprincip](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Utvärdera och åtgärd

Contoso använder kontinuerlig bedömningen som övervakar säkerheten för datorer, nätverk, lagring, data och program. identifiera potentiella säkerhetsproblem. 

- Security Center analyserar säkerhetsstatusen på Contosos beräkning, infrastruktur och dataresurser och Azure appar och tjänster.
- Kontinuerlig kontroll kan driftteamet Contoso att upptäcka potentiella säkerhetsproblem, till exempel system som saknar säkerhetsuppdateringar eller exponeras nätverksportar. 
- I synnerhet Contoso vill se till att alla sina virtuella datorer är skyddade. Security Center hjälper med detta kan verifiera VM hälsa och göra prioriterad och tillämplig rekommendationer för att åtgärda säkerhetsproblem innan de är de utnyttjas.

![Övervakning](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Arbeta med NSG: er

Contoso kan begränsa nätverkstrafik till resurser i ett virtuellt nätverk med nätverkssäkerhetsgrupper.

- En nätverkssäkerhetsgrupp innehåller en lista över säkerhetsregler som tillåter eller nekar inkommande eller utgående nätverkstrafik baserat på käll- eller mål-IP-adress, port och protokoll.
- När det används ett undernät, tillämpas regler för alla resurser i undernätet. Förutom nätverksgränssnitt inkluderar instanser av Azure-tjänster distribuerats i undernätet.
- Programmet säkerhetsgrupper (ASGs) kan du konfigurera nätverkssäkerhet som en naturlig förlängning av en app, så att du kan gruppera virtuella datorer och definiera principer för säkerhet för nätverk baserat på de grupperna.
    - Appen säkerhetsgrupper innebär att du kan återanvända säkerhetsprinciperna i skala, utan manuell underhåll av explicita IP-adresser. Plattformen hanterar komplexiteten med explicita IP-adresser och flera regeluppsättningar så att du kan fokusera på affärslogik.
    - Du kan ange en programsäkerhetsgrupp som källa och mål i en säkerhetsregel. När din säkerhetsprincip har definierats kan du skapa virtuella datorer och tilldela VM-nätverkskort till en grupp. 


Contoso kommer att implementera en blandning av NSG: er och ASGs. De är orolig NSG management. De oroar felaktig användning av NSG: er och komplexitet som det kan innebära för personalen åtgärder.  De har antagit två viktiga säkerhetsobjekt som de använder en allmän regel med detta i åtanke:

- All trafik till och från alla undernät (Nord-sydlig) omfattas av en regel på NSG, förutom GatewaySubnets i hubben nätverk.
- Alla brandväggar eller domänkontrollanter kommer att skyddas av både undernät NSG: er och NSG: er för nätverkskortet.
- Alla program i produktion har ASGs tillämpas.


Contoso har skapat en modell av hur det ser ut för sina program.

![Säkerhet](./media/contoso-migration-infrastructure/asg.png)


NSG: er som som är associerade med ASGs konfigureras med minsta behörighet att se till att endast får paket kan flöda från en del av nätverket till dess mål.

**Åtgärd** | **Namn** | **Källa** | **mål** | **Port**
--- | --- | --- | --- | --- 
Tillåt | AllowiInternetToFE | VNET-hubb-EUS1/IB-TrustZone | APP1-FE 80, 443
Tillåt | AllowWebToApp | APP1-FE | APP1-DB | 1433
Tillåt | AllowAppToDB | APP1-APP | Alla | Alla
Neka | DenyAllInbound | Alla | Alla | Alla

### <a name="encrypt-data"></a>Kryptera data

Azure Disk Encryption integreras med Azure Key Vault hjälper dig att kontrollera och hantera disk krypteringsnycklar och hemligheter i nyckelvalvet-prenumerationen. Det innebär att krypteras alla data på Virtuella diskar i vila i Azure storage.  

- Contoso har fastställt att specifika virtuella datorer kräver kryptering.
- De ska du använda kryptering för virtuella datorer med konfidentiella, kund eller bildpunkter per tum data.


## <a name="conclusion"></a>Sammanfattning

I den här artikeln Contoso ställer in sina Azure-infrastrukturen och ställa in eller planerade infrastruktur princip för Azure-prenumeration, hybrid identifierar, katastrofåterställning, nätverk, styrning och säkerhet. 

Inte alla steg som Contoso slutförda här krävs för en migrering till molnet. I fallen de vill planera en nätverksinfrastruktur som kan användas för alla typer av migreringar och är säker, flexibel och skalbar. 

Med den här infrastrukturen som det placeras redo de att gå vidare och prova att använda migrering.

## <a name="next-steps"></a>Nästa steg

Som ett första Migreringsscenario kommer Contoso att migrera sina lokala SmartHotel två skikt app som körs på virtuella VMware-datorer till Azure. De ska migrera app virtuella datorer till virtuella Azure-datorer och app-databasen till en Azure SQL hanteras-instans.
