---
title: Contoso – konfigurera en infrastruktur för migrering | Microsoft Docs
description: Lär dig hur Contoso ställer in en Azure-infrastruktur för migrering till Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: bf1406c8e361e0a1433b0e26c477c3c34e987fcf
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38562766"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso - distribuera en infrastruktur för migrering

Den här artikeln går igenom hur Contoso ställer in en lokal och Azure-infrastrukturen som förberedelse för migrering till Azure och för att köra verksamheten i en hybridmiljö.

- Det är en exempelarkitektur som är specifik för Contoso.
- Om du behöver alla element som beskrivs i artikel beror på migreringsstrategin. Till exempel om du bygger endast molnbaserade appar i Azure kan behöva en mindre komplexa nätverk struktur.

Det här dokumentet är andra i serie av artiklar som dokumenterar hur det fiktiva företaget Contoso migrerar lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och en uppsättning scenarier som illustrerar hur du ställer in en infrastruktur för migrering, bedöma lokala resurser för migrering och kör olika typer av migreringar. Scenarier växer i komplexitet och vi ska lägga till ytterligare artiklar med tiden.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
Artikel 2: Distribuera en Azure-infrastruktur (den här artikeln) | Beskriver hur Contoso förbereder sina lokala och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla scenarier för migrering av Contoso. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser](contoso-migration-assessment.md) | Visar hur Contoso körs en bedömning av sina lokala tvålagers-SmartHotel-app som körs på VMware. De utvärdera app virtuella datorer med den [Azure Migrate](migrate-overview.md) -tjänsten och SQL Server-databas för appen med den [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
[Artikel 4: Rehost till Azure virtuella datorer och en hanterad SQL-instans](contoso-migration-rehost-vm-sql-managed-instance.md) | Visar hur Contoso migrerar SmartHotel appen till Azure. De migrera appen klientdelens VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och appen databasen med den [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) service för att migrera till en SQL-hanterad instans. | Tillgängligt
[Artikel 5: Byt värd till virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Visar hur Contoso migrerar SmartHotel appen virtuella datorer med Site Recovery endast.
[Artikel 6: Byt värd till virtuella Azure-datorer och SQL Server-Tillgänglighetsgrupper](contoso-migration-rehost-vm-sql-ag.md) | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera de virtuella datorerna för appen och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Visar hur Contoso migrerar Linux osTicket-app till virtuella Azure-datorer. | Tillgängligt
[Artikel 8: Rehost en Linux-app till Azure virtuella datorer och Azure MySQL-Server](contoso-migration-rehost-linux-vm-mysql.md) | Visar hur Contoso migrerar Linux osTicket appen med Site Recovery och MySQL Workbench för att migrera (säkerhetskopiering och återställning) till en Azure MySQL-Server-instans. | Tillgängligt

I den här artikeln Contoso ställa in alla infrastrukturelement måste de slutföra Migreringsscenarier. 


## <a name="overview"></a>Översikt

Innan de kan migrera till Azure, är det viktigt att Contoso förbereder sin infrastruktur.  Det finns ofta fem bred områden som de behöver för att tänka på:

**Steg 1: Azure-prenumerationer**: hur ska de köper Azure och interagera med Azure-plattformen och tjänster?  
**Steg 2: Hybrididentitet**: hur ska de hantera och styra åtkomsten till både lokala och Azure-resurser efter migreringen? Hur utökar de eller flytta Identitetshantering till molnet?  
**Steg 3: Haveriberedskap och återhämtning**: hur de garanterar att appar och infrastruktur är motståndskraftiga om avbrott och katastrofer inträffar?  
**Steg 4: Nätverk**: hur ska de utforma din nätverksinfrastruktur och upprätta en anslutning mellan sina lokala datacenter och Azure?  
**Steg 5: Säkerhet**: hur ska de skyddar sina hybrid/Azure-distribution?  
**Steg 6: Styrning**: hur ska de behåller deras distribution justeras med krav på säkerhet och styrning?

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar vi titta på infrastrukturen, kanske du vill läsa dig mer om Azure-funktioner beskrivs i den här artikeln:

- Det finns ett antal alternativ för att köpa Azure åtkomst, inklusive betala per användning, Enterprise-avtal (EA), eller Open Licensing från Microsoft-återförsäljare eller från Microsoft-Partners vet som Cloud Solution Providers (CSP). Lär dig mer om [köpalternativ](https://azure.microsoft.com/pricing/purchase-options/), och Läs om hur [Azure-prenumerationer är ordnade](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Få en översikt över Azure [identitets- och åtkomsthantering](https://www.microsoft.com/en-us/trustcenter/security/identity). Framför allt, Lär dig mer om [Azure AD och utöka lokala AD till molnet](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Det finns en användbar e-bok om [identitets- och åtkomsthantering (IAM) i en hybridmiljö](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Azure tillhandahåller en stabil nätverksinfrastruktur med alternativ för hybridanslutning. Få en översikt över [nätverks- och åtkomstkontroll](https://docs.microsoft.com/azure/security/security-network-overview).
- Få en introduktion till [Azure Security](https://docs.microsoft.com/azure/security/azure-security), och Läs om hur du skapar en plan för [styrning](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>Lokala arkitektur

Här är ett diagram som visar den aktuella Contoso lokal infrastrukturen.

 ![Contoso-arkitektur](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso har en huvuddatacentret finns i den stad i New York i östra USA.
- De har tre ytterligare lokala grenar i USA.
- Huvuddatacentret är ansluten till internet med en fiber metro Ethernet-anslutning (500 Mbit/s).
- Varje gren är lokalt ansluten till internet med företag klass anslutningar med IPSec VPN-tunnlar till huvuddatacentret. På så sätt kan hela nätverket ska anslutas permanent och optimerar Internetanslutning.
- Huvuddatacentret är fullständigt virtualiserat med VMware. De har två ESXi 6.5-virtualiseringsvärdar, hanteras av vCenter Server 6.5.
- Contoso använder Active Directory för Identitetshantering och DNS-servrar i det interna nätverket.
- Domänkontrollanterna i datacentret körs på virtuella VMware-datorer. Domänkontrollanter på lokala grenar som körs på fysiska servrar.


## <a name="step-1-buy-and-subscribe-to-azure"></a>Steg 1: Köp och prenumerera på Azure

Contoso behöver ta reda på hur du köper Azure, hur du skapar prenumerationer och hur du licensierar tjänster och resurser.

### <a name="buy-azure"></a>Köpa Azure

Contoso kommer med en [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Detta innebär att betala ett åtagandebelopp till Azure, gör att de få fantastiska förmåner – inklusive flexibla faktureringsalternativ och optimerade priser.

- Contoso beräknat vad deras varje år Azure-kostnader kommer att. När de signerade avtalet betalt de för det första året i sin helhet.
- Contoso behöver använda alla sina åtaganden innan året är över eller de förlorar värdet för dessa dollar.
- Om de överstiger åtagandet och lägga mer av någon anledning, kommer Microsoft ska fakturera dem för skillnaden.
- Eventuella kostnader som uppstår ovan åtagandet är tillgänglig med samma priser och de som finns i deras avtal. Det finns inga påföljder för att gå över.

### <a name="manage-subscriptions"></a>Hantera prenumerationer

Efter att betala för Azure, måste Contoso att ta reda på hur du hanterar sina prenumerationer. De har ett EA och därför ingen gräns för hur många Azure-prenumerationer som de kan ställa in.

- En Azure Enterprise-registrering definierar hur formen företag använder Azure-tjänster och definierar en core styrning struktur.
- Som ett första steg Contoso har bestämt en struktur (kallas en enterprise-kodskelett för sina Företagsregistrering. De används [i den här artikeln](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-governance) som hjälper dem att förstå och utforma en kodskelett.
- Contoso har beslutat att använda en funktionell metod för att hantera sina prenumerationer för tillfället.
    - I företaget har de en enskild IT-avdelningen som styr Azure budgeten. Det här är den enda gruppen med prenumerationer.
    - De att utöka den här modellen i framtiden, så att andra företagets grupper kan ansluta som avdelningar i Enterprise-registrering.
    - I IT-avdelningen har Contoso strukturerade två prenumerationer, produktion och utveckling.
    - Om Contoso kräver ytterligare prenumerationer i framtiden, behöver de hantera åtkomst, principer och efterlevnad för dessa prenumerationer. De kommer att kunna göra det genom att introducera [Azure hanteringsgrupper](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), som ett extra lager över prenumerationer.

    ![Enterprise-struktur](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Granska licensiering

Med prenumerationer som är konfigurerad, kan Contoso titta på sina Microsoft-licenser. Deras strategi för licensiering beror på de resurser som de vill migrera till Azure och hur Azure virtuella datorer och tjänster är valt och distribueras. 

#### <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

När du distribuerar virtuella datorer i Azure omfattar standard-avbildningar en licens som Contoso debiteras per minut för programvara som används. Dock Contoso har varit en långsiktig Microsoft-kund och har upprätthålls EAs och öppna licenser med software assurance (SA). 

Azure Hybrid-förmånen ger en kostnadseffektiv metod för Contoso migrering genom att tillåta att spara på virtuella datorer i Azure och SQL Server arbetsbelastningar genom att konvertera eller återanvända Windows Server Datacenter och Standard edition-licenser som täcks av Software Assurance. Detta aktiverar Contoso att betala ett lägre baserat priset för virtuella datorer och SQL Server. [Läs mer](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>Licensmobilitet

License Mobility genom SA kan Microsofts volymlicensieringskunder, exempelvis Contoso flexibiliteten att distribuera berättigade server-appar med aktiva SA på Azure. Detta eliminerar behovet av att köpa nya licenser. Med mobilitetsavgifter, kan du enkelt distribuera befintliga licenser i Azure. [Läs mer](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Reserverade instanser för förutsägbara arbetsbelastningar

Förutsägbara arbetsbelastningar är sådana som måste alltid vara tillgänglig med virtuella datorer som körs. Till exempel av branschspecifika appar, till exempel ett SAP ERP-system.  Å andra sidan är oförutsägbara arbetsbelastningar sådana som kan variera. Till exempel virtuella datorer som är på under hög efterfrågan och inaktiverat vid tidpunkter med låg belastning.

![Reserverad instans](./media/contoso-migration-infrastructure/reserved-instance.png) 

I utbyte mot med reserverade instanser för specifika VM-instanser som de redan känner till måste underhållas för stora varaktigheter tid, kan konsolen få både en rabatt och prioriterad kapacitet. Med hjälp av [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/), tillsammans med Azure Hybrid-förmånen, Contoso kan spara upp till 82% jämfört med vanliga betala per användning priser (April 2018).


## <a name="step-2-manage-hybrid-identity"></a>Steg 2: Hantera hybrididentitet

Ge och styra användarnas åtkomst till Azure-resurser med identitets- och åtkomsthantering (IAM) är ett viktigt steg i att dra ihop Azure-infrastrukturen.  

- Contoso vill utöka sin lokala Active Directory till molnet i stället för att skapa ett nytt separat system i Azure.
- De skapar en Azure-baserad Active Directory för att göra detta.
- Contoso har inte Office 365 på plats, så att de behöver för att etablera en ny Azure AD.
- Office 365 använder Azure AD för hantering av användare. Om Contoso använder Office 365, skulle de redan har en Azure AD-tenet och använda det som sin primära AD.
- [Läs mer](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) om Azure AD för Office 365, och lär dig [hur du lägger till en prenumeration](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) till en befintlig Azure AD.

### <a name="create-an-azure-ad"></a>Skapa en Azure AD

Contoso använder den kostnadsfria versionen av Azure AD som har inkluderat med en Azure-prenumeration. Lägg till en ny AD-katalog på följande sätt:

1. I den [Azure-portalen](http://portal.azure.com/), Contoso går du till **skapa en resurs** > **identitet** > **Azure Active Directory**.
2. I **Skapa katalog**, de ange ett namn för katalogen, ett första domännamn och region där Azure AD-katalogen ska skapas.

    ![Skapa Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > När de skapar katalogen har ett första domännamn i formuläret domainname.onmicrosoft.com. Namnet kan inte ändras eller tas bort. De behöver lägga till sina registrerade domännamn i Azure AD.

### <a name="add-the-domain-name"></a>Lägga till domännamnet

För att använda deras standard domännamn, Contoso måste du lägga till det som ett anpassat namn till Azure AD. Det här alternativet gör att administratörer kan tilldela välbekanta användarnamn. Exempelvis kan en användare kan logga in med e-postadress billg@contoso.com, i stället för att behöva billg@contosomigration.onmicrosoft.com. 

Du ställer in anpassat namn de lägga till den i katalogen, lägger du till en DNS-post och kontrollera namnet i Azure AD.

1. I **anpassade domännamn** > **Lägg till anpassad domän**, de lägger till domänen.
2. Du använder en DNS-post i Azure som de behöver för att registrera den med sina domänregistrator. 

    - I den **anpassade domännamn** listan, de noterar DNS-information för namnet. Contoso använder en MX-post.
    - De behöver åtkomst till namnservern för att göra detta. När det gäller Contoso, de inloggad i domänen Contoso.com och skapat en ny MX-post för DNS-posten som tillhandahålls av Azure AD, med hjälp av informationen som anges.  
1. När DNS-posterna spridas i namnet för domänen information om, de klickar på **Kontrollera** att kontrollera anpassat namn.

     ![Azure AD DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Ställa in både lokalt och Azure-grupper och användare

Nu när deras Azure AD är igång, Contoso behöver lägga till anställda till en lokal AD-grupper som ska synkroniseras till Azure AD. Vi rekommenderar att de använder den lokala gruppnamn som matchar namnet på resursgrupperna i Azure. Detta gör det lättare att identifiera matchningar för synkronisering.

#### <a name="create-resource-groups-in-azure"></a>Skapa resursgrupper i Azure

Azure-resursgrupper samla ihop Azure-resurser. Om du använder en resursgrupps-ID kan Azure för att utföra åtgärder på resurser i gruppen.

- En Azure-prenumeration kan ha flera resursgrupper, men en resursgrupp kan bara finnas i en enskild prenumeration.
- Dessutom kan en enskild resursgrupp kan ha flera resurser, men en resurs kan bara tillhöra en grupp.

Contoso ställa in Azure-resursgrupper som sammanfattas i tabellen nedan.

**Resursgrupp** | **Detaljer**
--- | ---
**ContosoCobRG** | Den här gruppen innehåller alla resurser som rör kontinuitet för företag (COB).  Den innehåller valv som Contoso skapas när du använder tjänsten Azure Site Recovery och Azure Backup-tjänsten.<br/><br/> Det omfattar även resurser som används för migrering, inklusive Azure Migrate och Database Migration Service.
**ContosoDevRG** | Den här gruppen innehåller resurser för utveckling och testning.
**ContosoFailoverRG** | Den här gruppen fungerar som en startsida zon för redundansväxlade resurser.
**ContosoNetworkingRG** | Den här gruppen innehåller alla nätverksresurser.
**ContosoRG** | Den här gruppen innehåller resurser som är relaterade till produktionsappar och databaser.

De skapa resursgrupper enligt följande:

1. I Azure portal > **resursgrupper**, de lägger till en grupp.
2. För varje grupp ange de ett namn, den prenumeration som gruppen tillhör och regionen.
3. Resursgrupper som visas i den **resursgrupper** lista.

    ![Resursgrupper](./media/contoso-migration-infrastructure/resource-groups.png) 


#### <a name="create-matching-security-groups-on-premises"></a>Skapa matchande security grupper lokalt

1. I sin lokala Active Directory konfigurera Contoso säkerhetsgrupper med namn som matchar namnen på Azure-resursgrupper.
 
    ![En lokal AD-säkerhetsgrupper](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. För hantering, kan de skapa ytterligare en grupp som ska läggas till alla de andra grupperna. Den här gruppen har behörighet att alla resursgrupper i Azure. Ett begränsat antal globala administratörer kommer att läggas till den här gruppen.

### <a name="synchronize-ad"></a>Synkronisera AD

Contoso vill tillhandahålla en gemensam identitet för åtkomst till resurser lokalt och i molnet. Om du vill göra detta måste integrera de sin lokala Active Directory med Azure AD. Med den här modellen:

- Användare och organisationer kan dra nytta av en enda identitet för att få åtkomst till lokala program och molntjänster, som Office 365 och tusentals andra webbplatser på internet.
- Administratörer kan utnyttja grupperna i AD för att implementera [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) i Azure.

Att underlätta integration, Contoso används den [verktyget Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). När du installerar och konfigurerar verktyget på en domänkontrollant, synkroniseras lokalt lokala AD-identiteter med Azure AD. 

### <a name="download-the-tool"></a>Ladda ned verktyget

1. I Azure-portalen, Azure går till **Azure Active Directory** > **Azure AD Connect**, och hämtar den senaste versionen av verktyget till den server som används för synkronisering.

    ![Hämta AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. De startar den **AzureADConnect.msi** installationen med hjälp av **Använd standardinställningar**. Detta är de vanligaste installationen och kan användas för en enda skog-topologi med synkronisering av lösenordshash för autentisering.

    ![AD Connect-guiden](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. I **Anslut till Azure AD**, de ange autentiseringsuppgifter för att ansluta till Azure AD (i formatet CONTOSO\admin eller contoso.com\admin).

    ![AD Connect-guiden](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. I **Anslut till AD DS**, de ange autentiseringsuppgifter för sina lokala AD.

     ![AD Connect-guiden](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. I **redo att konfigurera**, de klickar på **starta synkroniseringsprocessen när konfigurationen är klar** att starta synkroniseringen direkt. De installerar.


- Contoso har ingen direkt anslutning till Azure. Om din lokala AD är bakom en proxy, finns i det här [artikeln](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Efter den första synkroniseringen kan lokala AD-objekt kan ses i Azure AD.

    ![Lokala AD i Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- Contoso IT-avdelningen representeras i varje grupp baserat på deras roll.

    ![En lokal AD-medlemmar i Azure](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Konfigurera RBAC

Azure [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) Aktivera detaljerad åtkomsthantering för Azure. Med RBAC kan du bevilja exakt den åtkomstnivå som användarna behöver för att kunna utföra sitt arbete. Du kan tilldela lämpliga RBAC-roll till användare, grupper och program på en omfångsnivå. Omfattningen för en rolltilldelning kan vara en prenumeration, en resursgrupp eller en enskild resurs. 

Contoso kan du nu tilldela roller till AD-grupper som de synkroniseras från en lokal plats.

1. I den **ControlCobRG** resursgruppen, som han eller hon klickar **åtkomstkontroll (IAM)** > **Lägg till**.
2. I **lägga till behörigheter** > **rollen**, de väljer **deltagare**, och välj den **ContosoCobRG** AD-grupp i listan. Gruppen visas sedan i **valda medlemmar** lista. 
3. Upprepas de med samma behörigheter för andra resursgrupper (förutom för **ContosoAzureAdmins**), genom att lägga till behörigheterna som deltagare till AD-kontot som matchar resursgruppen.
4. För den **ContosoAzureAdmins** AD-grupp, de tilldela den **ägare** roll.

    ![En lokal AD-medlemmar i Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>Steg 3: Utforma för ökad flexibilitet och haveriberedskap

Azure-resurser distribueras inom regionerna.
- Regionerna är ordnade i områden och dataplacering, datasuveränitet, efterlevnad och elasticitet datakrav respekteras inom geografiska gränser.
- En region består av en uppsättning datacenter. Dessa Datacenter är distribuerad på en latensdefinierad perimeter och är anslutna via ett dedikerat regionalt nätverk med låg latens.
- Varje Azure-region är kopplad till en annan region för återhämtning.
- Läs mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/), och förstå [hur regioner har parats ihop](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso har bestämt dig att gå med östra USA 2 (finns i Virginia) som deras primära region och USA, centrala som deras sekundär region. Det finns några orsaker till detta:

- Contoso-datacenter finns i New York, och de anses vara fördröjning till det närmaste datacentret.
- Östra USA 2 har alla tjänster och produkter som de ska användas. Inte alla Azure-regioner är desamma när det gäller de produkter och tjänster som är tillgängliga. Du kan granska [Azure-produkter per region](https://azure.microsoft.com/global-infrastructure/services/).
- Centrala USA är Azure-parade regionen för östra USA 2.

Som de tycker om hybridmiljön behöver Contoso att tänka på hur du skapar ökad flexibilitet och en strategi för katastrofåterställning i sin design region. Allmänna ordalag strategier sträcker sig från en distribution i en region, som förlitar sig på Azure-plattformen funktioner, till exempel som feldomäner och regional länkning för elasticitet, genom att en fullständig aktiv-aktiv modell i vilken molntjänster och en databas har distribuerats och underhåll användare från två regioner.

Contoso har valt att ta en mellersta väg. De distribuera sina appar och resurser i en primär region och ha en fullständig infrastruktur i den sekundära regionen så att den är redo att fungera som en fullständig säkerhetskopiering vid fullständig app katastrof eller fel region.


## <a name="step-4-design-a-network-infrastructure"></a>Steg 4: Utforma en nätverksinfrastruktur

Contoso är redo att överväga en strategi för nätverk med deras region design på plats. De behöver för att tänka på hur sina lokala datacenter och Azure för att ansluta och kommunicera med varandra och hur du utformar din nätverksinfrastruktur i Azure. Mer specifikt behöver de för att:

**Planera hybrid nätverksanslutning**: ta reda på hur de ska ansluta nätverk mellan lokala och Azure.
**Utforma en Azure nätverksinfrastruktur**: Bestäm hur de ska distribuera nätverk via deras regioner. Hur kommunicerar nätverk inom samma region och mellan regioner.
**Utforma och skapa Azure-nätverk**: Konfigurera Azure-nätverk och undernät, och Bestäm vad kommer att finnas i dem.

### <a name="plan-hybrid-network-connectivity"></a>Planera nätverk för hybridanslutning

Contoso anses vara en [antal arkitekturer](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) för hybridnätverk mellan Azure och sina lokala datacenter. [Läs mer](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) om att jämföra alternativen.

Contoso lokala nätverkets infrastruktur består för närvarande av sina datacenter i New York som en påminnelse och lokala grenar i östra delen av USA.  Alla platser ha en business class anslutning till internet.  Varje bransch är ansluten till datacenter via en IPSec VPN-tunnel via internet.

![Contoso-nätverket](./media/contoso-migration-infrastructure/contoso-networking.png) 

Här är hur Contoso bestämde sig för att implementera hybridanslutning:

1. Konfigurera en ny plats-till-plats VPN-anslutning mellan Contoso-datacenter i New York och två Azure-regioner i USA, östra 2 och USA, centrala.
2. Gren office trafik för virtuella Azure-nätverk dirigerar via huvuddatacentret Contoso. 
3. När de skalar upp sina Azure-distribution, kommer de upprätta en ExpressRoute-anslutning mellan sina datacenter och Azure-regioner. När detta inträffar kommer de bibehåller VPN plats-till-plats-anslutningen endast för redundans.
    - [Läs mer](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) om att välja mellan en hybridlösning för VPN och ExpressRoute.
    - Kontrollera [ExpressRoute-platser och support](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Endast VPN**

![Contoso VPN](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**VPN och ExpressRoute**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Design av nätverksinfrastruktur för Azure

Det är viktigt att Contoso placerar nätverk på plats på ett sätt som gör sitt hybriddistribution säker och skalbar. Detta gör Contoso tar en långsiktig strategi och utformar virtuella nätverk (Vnet) för att vara flexibel och redo för företagsanvändning. [Läs mer](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) om planering för virtuella nätverk.

För att ansluta sina två regioner, har Contoso beslutat att implementera en hub att navnätverket modell:

- I varje region använder Contoso en nav-och-eker-modell.
- Om du vill ansluta nätverk och hubs använder Contoso Azure nätverks-peering.

#### <a name="network-peering"></a>Sedan kan kontrollera de serverinställningarna.

Azure ger nätverks-peering för att ansluta virtuella nätverk och hubs. Global peering tillåter anslutningar mellan virtuella nätverk/hubs finns i olika regioner. Lokala peering ansluter virtuella nätverk i samma region. VNet-peering ger flera fördelar:

- Nätverkstrafiken mellan peer-kopplade virtuella nätverk är privat.
- Trafik mellan virtuella nätverk sparas på Microsoft-stamnätverket. Inga offentliga internet, gatewayer eller kryptering krävs vid kommunikation mellan de virtuella nätverken.
- Peering ger en anslutning med låg latens och hög bandbredd mellan resurser i olika virtuella nätverk, som standard.

[Läs mer](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) om nätverks-peering.

#### <a name="hub-to-hub-across-regions"></a>Hub till hubben i flera regioner

Contoso distribuerar hubb i varje region. En hub är ett virtuellt nätverk (VNet) i Azure som fungerar som en central plats för anslutning till det lokala nätverket. Hubben ansluter virtuella nätverk till varandra med hjälp av global VNet-peering. Global VNet-peering ansluter virtuella nätverk i Azure-regioner.

- Hubben i varje region är peer-kopplat till dess partner-hubben i den andra regionen.
- Hubben är peer-kopplat till varje nätverk i regionen och kan ansluta till alla nätverksresurser.

    ![Global peering](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>NAV och eker inom en region

I varje region distribuerar Contoso virtuella nätverk för olika syften, som eker-nätverk från hubben region. Virtuella nätverk inom en region använda peering för att ansluta till sina hub och till varandra.

#### <a name="design-the-hub-network"></a>Utforma hub-nätverk

I NAV och ekrar modellen som Contoso har valt, de behöver för att tänka på hur trafik från sina lokala datacenter och från internet, ska dirigeras. Här är hur Contoso har valt att hantera routning för både östra USA 2 och USA, centrala hubs:

- De skapar ett nätverk som kallas ”omvänd c”, eftersom det här är den sökväg som inkommer till utgående nätverk följer du paketen.
- Deras nätverksarkitektur har två gränser, en ej betrodd frontend perimeter-zon och en backend-betrodda zonen.
- En brandvägg har ett nätverkskort i varje zon kan styra åtkomst till betrodda zoner.
- Från internet:
    - Internet-trafiken överskrids en belastningsutjämnad offentlig IP-adress i perimeternätverket.
    - Den här trafiken dirigeras via brandväggen, och omfattas av brandväggsregler.
    - När åtkomstkontroller för nätverk implementeras vidarebefordras trafik till lämplig plats i den betrodda zonen.
    - Utgående trafik från det virtuella nätverket kommer att dirigeras till internet med användardefinierade vägar (Udr). Trafiken är Tvingad genom brandväggen och kontrolleras i enlighet med principer för Contoso.
- Från Contoso-datacenter:
    - Inkommande trafik via VPN plats-till-plats (eller ExpressRoute) når den offentliga IP-adressen för Azure VPN-gatewayen.
    - Trafiken dirigeras genom brandväggen och omfattas av brandväggsregler.
    - Efter att de regler för vidarebefordras trafik till en intern belastningsutjämnare (Standard-SKU) i zonen för betrodda internt undernät.
    - Utgående trafik från betrodda undernätet till lokala datacenter via VPN dirigeras via brandväggen och regler som tillämpas innan du fortsätter via VPN plats-till-plats-anslutning.



### <a name="design-and-set-up-azure-networks"></a>Utforma och skapa Azure-nätverk

Contoso är redo att konfigurera sin Azure-nätverk och undernät med ett nätverk och routning topologin på plats.

- Contoso implementerar en klass A privat nätverk i Azure (0.0.0.0 127.255.255.255). Det här fungerar, eftersom den lokala de aktuella har en klass B privat adress utrymme 172.160.0/16 så att de kan vara säker på att det inte att finnas någon överlappning mellan adressintervall.
- De kommer att distribuera virtuella nätverk i sina primära och sekundära regioner.
- De ska använda en namngivningskonvention som innehåller prefixet **VNET** och region förkortningen **EUS2** eller **Cu: er**. Med den här standarden hub-nätverk får namnet **VNET-HUB-EUS2** (östra USA 2) och **VNET-HUB-CUS** (USA, centrala).
- Contoso har inte en [IPAM lösningen](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), så att de behöver för att planera för nätverksroutning utan NAT.


#### <a name="virtual-networks-in-east-us-2"></a>Virtuella nätverk i östra USA 2

Östra USA 2 är den primära regionen som Contoso använder för att distribuera resurser och tjänster. Här är hur de ska architect nätverk:

- **Hub**: hubbens virtuella nätverk i östra USA 2 är den centrala punkten för primära anslutning till sina lokala datacenter.
- **Virtuella nätverk**: ekrar virtuella nätverk i östra USA 2 kan användas för att isolera arbetsbelastningar om det behövs. Förutom Hubbnätverk har Contoso två eker virtuella nätverk i östra USA 2:
    - **VNET-DEV-EUS2**. Det här virtuella nätverket ger utveckling och test-teamet kommer ett fullt fungerande nätverk för utvecklingsprojekt. Den fungerar som en pilot område och förlitar sig på produktionsinfrastruktur ska fungera.
    - **VNET-PROD-EUS2**: olika komponenter i Azure IaaS ska finnas i det här nätverket. 
    -  Varje virtuellt nätverk ska ha sin egen unika adressutrymme utan överlappande. De har för avsikt att konfigurera routning utan NAT.
- **Undernät**:
    - Det blir ett undernät i varje nätverk för varje app-nivå
    - Varje undernät i produktionsmiljön har en matchande undernät i Dev-VNet.
    - Dessutom har produktionsnätverket ett undernät för domänkontrollanter.

Virtuella nätverk i östra USA 2 sammanfattas i tabellen nedan.

**Virtuellt nätverk** | **Adressintervall** | **Peer**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2, VNET-DEV-EUS2, VNET-PROD-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-PROD-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VNET-PROD-CU: ER

![Nav/eker i primär region](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Undernät i östra USA 2 Hub-nätverk (VNET-HUB-EUS2)

**Undernät/zon** | **CIDR** | ** Användbara IP-adresser
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB UntrustZone** | 10.240.2.0/24 | 251
**OB TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Undernät i östra USA 2 Dev-nätverk (VNET-DEV-EUS2)

Dev VNet används av Utvecklingsteamet som ett pilotprojekt område för produktion. Den har tre undernät.

**Undernät** | **CIDR** | **Adresser** | **I undernät**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | Klienter/virtuella datorer på webbnivå
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | App-nivå virtuella datorer
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | Virtuella


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Undernät i östra USA 2 produktionsnätverket (VNET-PROD-EUS2)

Azure IaaS-komponenter finns i produktionsmiljön. Varje app-nivå har ett eget undernät. Undernät matchar de i Dev-nätverk med hjälp av ett undernät för domänkontrollanter.

**Undernät** | **CIDR** | **Adresser** | **I undernät**
--- | --- | --- | ---
**PROD-FE-EUS2** | 10.245.32.0/22 | 1019 | Klienter/virtuella datorer på webbnivå
**PROD-APP-EUS2** | 10.245.36.0/22 | 1019 | App-nivå virtuella datorer
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | Virtuella
**PROD-DC-EUS2** | 10.245.42.0/23 | 251 | Virtuella datorer för domänkontrollanter


![Hub nätverksarkitektur](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>Virtuella nätverk i centrala USA (sekundär region)

Centrala USA är Contosos sekundär region. Här är hur de ska architect nätverk:

- **Hub**: hubben virtuellt nätverk i östra USA 2 är den centrala punkten för anslutning till sina lokala datacenter och eker virtuella nätverk i östra USA 2 kan användas för att isolera arbetsbelastningar om det behövs kan hanteras separat från andra ekrar.
- **Virtuella nätverk**: de har två virtuella nätverk i centrala USA:
    - VNET-PROD-CU: ER. Det här virtuella nätverket är ett produktionsnätverk som liknar VNET PROD_EUS2. 
    - VNET-ASR-CU: ER. Det här virtuella nätverket fungerar som en plats där virtuella datorer skapas efter en redundansväxling från den lokala eller som en plats för virtuella Azure-datorer som har redundansväxlats från primärt till den sekundära regionen. Det här nätverket är liknande till produktionsnätverk, men utan några domänkontrollanter på den.
    -  Varje virtuellt nätverk i regionen har en egen adressutrymme utan överlappande. De har för avsikt att konfigurera routning utan NAT.
- **Undernät**: undernät kommer att byggts på liknande sätt som de i östra USA 2. Undantaget är att de inte behöver ett undernät för domänkontrollanter.

De virtuella nätverken i USA, centrala sammanfattas i tabellen nedan.

**Virtuellt nätverk** | **Adressintervall** | **Peer**
--- | --- | ---
**VNET-HUB-CU: ER** | 10.250.0.0/20 | VNET-HUB-EUS2, VNET-ASR-CU: ER, VNET-PROD-CU: ER
**VNET-ASR-CU: ER** | 10.255.16.0/20 | VNET-HUB-CU: ER, VNET-PROD-CU: ER
**VNET-PROD-CU: ER** | 10.255.32.0/20 | VNET-HUB-CU: ER, VNET-ASR-CU: ER, VNET-PROD-EUS2  


![Nav/eker i länkad region](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>Undernät i det centrala USA navnätverket (VNET-HUB-CUS)

**Undernät** | **CIDR** | **Användbara IP-adresser**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB UntrustZone** | 10.250.2.0/24 | 251
**OB TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>Undernät i centrala USA produktionsnätverket (VNET-PROD-CUS)

Parallellt till företagets nätverk i den primära regionen östra USA 2 finns det ett produktionsnätverk i den sekundära regionen USA, centrala. 

**Undernät** | **CIDR** | **Adresser** | **I undernät**
--- | --- | --- | ---
**PROD-FE-CU: ER** | 10.255.32.0/22 | 1019 | Klienter/virtuella datorer på webbnivå
**PROD-APP-CU: ER** | 10.255.36.0/22 | 1019 | App-nivå virtuella datorer
**PROD-DB-CU: ER** | 10.255.40.0/23 | 507 | Virtuella
**PROD-DC-CU: ER** | 10.255.42.0/24 | 251 | Virtuella datorer för domänkontrollanter

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Undernät i nätverket för centrala USA-redundans/återställning i centrala USA (VNET-ASR-CUS)

VNET-ASR-CUS-nätverket används för redundansväxling mellan regioner. Site Recovery används för att replikera och redundansväxla virtuella Azure-datorer mellan regioner. Den fungerar även som en Contoso-datacenter till Azure-nätverk för skyddade arbetsbelastningar som förblir lokal, men redundansväxlar till Azure för haveriberedskap.

VNET-ASR-Cu: er är samma grundläggande undernät som virtuella nätverk i östra USA 2, men utan att behöva ett domain controller undernät för produktion.

**Undernät** | **CIDR** | **Adresser** | **I undernät**
--- | --- | --- | ---
**ASR-FE-CU: ER** | 10.255.16.0/22 | 1019 | Klienter/virtuella datorer på webbnivå
**ASR-APP-CU: ER** | 10.255.20.0/22 | 1019 | App-nivå virtuella datorer
**ASR-DB-CU: ER** | 10.255.24.0/23 | 507 | Virtuella

![Hub nätverksarkitektur](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Konfigurera peer-kopplade anslutningar

Hubben i varje region kommer peerkopplas till hubben i den andra regionen och för att alla virtuella nätverk i regionen hub. Det möjliggör hubs att kommunicera och för att visa alla virtuella nätverk inom en region. Tänk på följande:

- Peering skapar en dubbelsidig anslutning. Från peer-initierande datorn på det första virtuella nätverket och en annan en på den andra VNet.
- Trafik som passerar mellan peer-datorer måste ses från VPN-anslutningen mellan den lokala datacenter och Azure i en hybriddistribution. Du aktiverar det genom att det finns vissa specifika inställningar som måste anges för peer-kopplade anslutningar.

Contoso måste tillåta trafik vidarebefordras och färdas över VPN-gatewayer för alla anslutningar från virtuella ekernätverk via hubben till det lokala datacentret.

##### <a name="domain-controller"></a>Domänkontrollant

För domänkontrollanter i nätverket VNET-PROD-EUS2 företaget Contoso-trafiken flöda mellan EUS2 hub/produktionsnätverk, och via VPN-anslutning till den lokala. De måste tillåta följande för att göra detta:

1. **Tillåt vidarebefordrad trafik** och **Tillåt konfigurationer för gateway-överföring** för peer-kopplade anslutningen. I vårt exempel skulle detta vara VNET-HUB-EUS2 till VNET-PROD-EUS2 anslutning.

    ![Peering](./media/contoso-migration-infrastructure/peering1.png)

2. **Tillåt vidarebefordrad trafik** och **Använd fjärrgateway** på den andra sidan av peering på VNET PROD-EUS2 till VNET-HUB-EUS2 anslutning.

    ![Peering](./media/contoso-migration-infrastructure/peering2.png)

3. Lokala de ska ställa in en statisk väg som dirigerar lokal trafik för dirigering via VPN-tunnel till det virtuella nätverket. Konfigurationen skulle slutföras på den gateway som innehåller VPN-tunneln från Contoso till Azure. Contoso använder Windows Routning och fjärråtkomst för detta.

    ![Peering](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Produktionsnätverk 

En spoked peer-nätverket kan inte se en spoked peer-nätverket i en annan region via hub.

De behöver skapa en peer-kopplade direktanslutning för VNET-PROD-EUS2 och hindra-PROD-Cu: er för Contosos produktionsnätverk i båda regionerna att se varandra. 

![Peering](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Konfigurera DNS

När du distribuerar resurser i virtuella nätverk kan ha du ett par alternativ för namnmatchning för domänen. Du kan använda namnmatchning som tillhandahålls av Azure eller ange DNS-servrar för matchning. Typ av namnmatchning som du använder beror på hur dina resurser behöver kommunicera med varandra. Hämta [mer](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) om Azure DNS-tjänsten.

Contoso har valt att Azure DNS-tjänsten inte är ett bra val i hybridmiljön. I stället kommer de att utnyttja sina lokala DNS-servrar.

- Eftersom detta är ett hybridnätverk alla de virtuella datorer på plats och i Azure måste kunna matcha namn ska fungera korrekt. Det innebär att anpassade DNS-inställningar måste tillämpas på de virtuella nätverken.
- Contoso har för närvarande domänkontrollanter som har distribuerats i Contoso-datacenter och på lokalkontoret. Deras primära DNS-servrar är CONTOSODC1(172.16.0.10) och CONTOSODC2(172.16.0.1)
- När de virtuella nätverken har distribuerats, ställs de lokala domänkontrollanterna som ska användas som DNS-server i nätverk. 
- Om du vill konfigurera detta när du använder anpassad DNS på det virtuella nätverket, måste Azures rekursiva matchare IP-adress (till exempel 168.63.129.16) läggas till i DNS-listan.  Om du vill göra detta, konfigurerar Contoso DNS-serverinställningarna på varje virtuellt nätverk. Anpassad DNS-inställningarna för det virtuella nätverk-HUB-EUS2 nätverket skulle till exempel vara följande:
    
    ![Anpassad DNS](./media/contoso-migration-infrastructure/custom-dns.png)

Förutom sina lokala domänkontrollanter, Contoso ska implementera fyra mer för att stödja sina Azure-nätverk, två för varje region. Här är vad de ska distribuera i Azure.

**Region** | **DC** | **Virtuellt nätverk** | **Undernät** | **IP-adress**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.5
CU: ER | CONTOSODC5 | VNET-PROD-CU: ER | PROD-DC-CU: ER | 10.255.42.4
CU: ER | CONTOSODC6 | VNET-PROD-CU: ER | PROD-DC-CU: ER | 10.255.42.4

När du har distribuerat de lokala domänkontrollanterna, Contoso måste du uppdatera DNS-inställningarna på nätverk på antingen region för att inkludera de nya domänkontrollanterna i sina DNS-serverlistan.



#### <a name="set-up-domain-controllers-in-azure"></a>Konfigurera domänkontrollanter i Azure

Contoso är redo att bygga ut sina domänkontrollanter i Azure när du har uppdaterat nätverksinställningar.

1. I Azure-portalen kan distribuera de en ny Windows Server VM till lämplig VNet.
2. De kan skapa tillgänglighetsuppsättningar för varje plats för den virtuella datorn. Tillgänglighetsuppsättningar gör du följande:
    - Se till att Azure-strukturen separerar de virtuella datorerna till olika infrastrukturer i Azure-Region. 
    -  Kan Contoso ska vara tillämplig för serviceavtal på 99,95% för virtuella datorer i Azure.  [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).

    ![Tillgänglighetsgrupp](./media/contoso-migration-infrastructure/availability-group.png) 
3. När den virtuella datorn har distribuerats kan pen de nätverksgränssnittet för den virtuella datorn. Här är de angetts privata IP-adress till statisk och ange en giltig adress.

    ![VM NIC](./media/contoso-migration-infrastructure/vm-nic.png)

4. Nu kan ansluta de en ny datadisk till den virtuella datorn. Den här disken innehåller Active Directory-databasen och sysvol-resursen. 
    - Storleken på disken avgör antalet IOPS som stöds.
    - Med tiden behöva diskens storlek ökar när miljön växer.
    - Enheten får inte anges att läsa/skriva för värdcachelagring. Active Directory-databaser stöder inte detta.

     ![Active Directory-disk](./media/contoso-migration-infrastructure/ad-disk.png)

5. När disken har lagts till kan de ansluta till den virtuella datorn via fjärrskrivbord och öppna Server Manager.
6. I **fil- och lagringstjänster**, de kör guiden Ny volym, se till att enheten är angivna bokstaven F: eller senare på den lokala virtuella datorn.

     ![Guiden Ny volym](./media/contoso-migration-infrastructure/volume-wizard.png)

7. I Serverhanteraren som de lägger till den **Active Directory Domain Services** roll. Sedan kan konfigurera de den virtuella datorn som en domänkontrollant.

      ![Serverroll](./media/contoso-migration-infrastructure/server-role.png)  

9. När den virtuella datorn är konfigurerad som en Domänkontrollant och startas om kan de öppna DNS-hanteraren och konfigurera Azure DNS-matchare som vidarebefordrare.  Detta gör att domänkontrollanten ska vidarebefordra DNS-frågor som den inte kan lösa i Azure DNS.

    ![DNS-vidarebefordrare](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Nu kan sedan uppdatera anpassade DNS-inställningarna för varje virtuellt nätverk med lämpliga domänkontroller för VNet-regionen. De omfattar den lokala domänkontrollanter i listan.

### <a name="set-up-active-directory"></a>Konfigurera Active Directory

AD är en kritisk tjänst i nätverk och måste vara korrekt konfigurerad. Contoso kommer att skapa AD-webbplatser för Contoso-datacenter och för regionerna som EUS2 och Cu: er.  

1. Skapar de två nya platser (AZURE-EUS2 och AZURE Cu: er) tillsammans med webbplatsen datacenter (ContosoDatacenter).
2. Efter att platserna kan skapa de undernät på platser, på samma virtuella nätverk och datacenter.

    ![DC-undernät](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Sedan kan skapa de två länkar för att ansluta allt. Domänkontrollanter ska sedan flyttas till deras plats.

    ![DC-länkar](./media/contoso-migration-infrastructure/dc-links.png)

4. När allt har konfigurerats är Active Directory-topologin på plats.
    
    ![DC-replikering](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Med allt klar visas en lista med domänkontrollanter och webbplatser i lokala Active Directory Administrationscenter.

    ![AD-administrationscentret](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>Steg 5: Planera för styrning

Azure erbjuder en uppsättning kontroller för styrning i tjänster och Azure-plattformen. [Läs mer](https://docs.microsoft.com/azure/security/governance-in-azure) för en grundläggande förståelse för alternativ.

När de konfigurera identitet och åtkomstkontroll Contoso redan börjat att inrätta vissa aspekter av styrning och säkerhet. Det finns allmänna ordalag tre områden som de behöver tänka på:

- **Principen**: principen i Azure gäller och tillämpar regler och effekterna på resurserna, så att resurserna kompatibla med företagets krav och serviceavtal.
- **Låser**: Azure kan du låsa prenumerationer, resursgrupper och andra resurser, så att de kan bara ändras av de som har behörighet att göra detta.
- **Taggar**: resurser kan kontrolleras, granskas och hanteras med taggar. Taggar koppla metadata till resurser, tillhandahålla information om resurser eller ägare.

### <a name="set-up-policies"></a>Konfigurera principer

Azure Policy-tjänsten utvärderar dina resurser, söker efter sådana som inte är kompatibla med de principdefinitioner som du har på plats. Kan till exempel ha en princip som endast tillåter vissa typer av virtuella datorer, eller kräver resurser som har en specifik tagg. 

Azure-principer ange en principdefinition av och principtilldelningen ange området där en principen ska tillämpas. Omfånget kan variera mellan en hanteringsgrupp till en resursgrupp. [Lär dig](https://docs.microsoft.com/azure/azure-policy/create-manage-policy) om att skapa och hantera principer.

Contoso vill komma igång med några olika principer:

- Företaget en princip för att se till att resurser kan bara distribueras i regionerna EUS2 och Cu: er.
- De vill begränsa VM SKU: er till godkända SKU: er endast. Avsikten är att se till att dyra VM SKU: er inte används.

#### <a name="limit-resources-to-regions"></a>Gränsen resurser till regioner

Contoso använder inbyggda principdefinitionen **tillåtna platser** att begränsa resource regioner.

1. I Azure-portalen klickar du på **alla tjänster**, och Sök efter **princip**.
2. Välj **tilldelningar** > **tilldela princip**.
3. Välj i listan med principer, **tillåtna platser**.
4. Ange **omfång** till namnet på Azure-prenumeration och välj de två regionerna i listan över tillåtna.

    ![Princip för tillåtna regioner](./media/contoso-migration-infrastructure/policy-region.png)

5. Principen är som standard med **neka**, vilket innebär att om någon startar en distribution i prenumerationen som inte finns i EUS2 eller Cu: er, distributionen kommer att misslyckas. Här är vad händer om någon i Contoso-prenumeration försöker att konfigurera en distribution i västra USA.

    ![Principen misslyckades](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Tillåt specifika VM SKU: er

Contoso använder inbyggda principdefinitionen **att virtuella datorer SKU: er** att begränsa vilka typer av virtuella datorer som kan skapas i prenumerationen. 

![Princip för SKU: N](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Kontrollera efterlevnad av policy

Principer för att börja gälla omedelbart och Contoso kan kontrollera resurser för kompatibilitet. 

1. I Azure-portalen klickar du på den **efterlevnad** länk.
2. Instrumentpanelen för enhetsefterlevnad visas. Du kan granska nedåt för mer information.

    ![Principefterlevnad](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Konfigurera Lås

Contoso har länge använt ITIL-ramverket för hantering av sina system. En av de viktigaste aspekterna av framework är ändringskontroll och Contoso vill se till att ändringshantering har implementerats i sina Azure-distribution.

Contoso ska implementera lås på följande sätt:

- Produktions- eller redundans komponent måste vara i en resursgrupp som har ett ReadOnly-Lås.  Det innebär att om du vill ändra eller ta bort objekt för produktion, låset måste tas bort. 
- Produktion resursgrupper har CanNotDelete-Lås. Det innebär att auktoriserade användare kan läsa eller ändra en resurs, men det går inte att ta bort den.

[Läs mer](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) om Lås.

### <a name="set-up-tagging"></a>Konfigurera taggning

Om du vill spåra resurser när de läggs, blir det allt viktigare för Contoso vill associera resurser med en lämplig avdelning, kunder och miljö. 

Förutom att tillhandahålla information om resurser och ägare, kan taggar Contoso sammanställer och gruppera resurser, och kan använda dessa data för återbetalning.

Contoso behöver visualisera sina Azure-tillgångar på ett sätt som passar för sin verksamhet. Till exempel men rollen eller avdelning. Observera att resurser inte behöver finnas i samma resursgrupp att dela en tagg. Detta gör skapar Contoso en enkel taggtaxonomi så att alla använder samma taggar.

**Taggnamn** | **Värde**
--- | ---
Kostnadsställe | 12345: Det måste vara ett giltigt kostnadsställe från SAP.
Affärsenheten | Namnet på affärsenhet (från SAP). Matchningar kostnadsställe.
ApplicationTeam | E-postalias för team som äger stöd för appen.
Katalognamn | Namnet på appen eller ShareServices, per tjänstkatalogen som har stöd för resursen.
ServiceManager | E-postalias av ITIL Service Manager för resursen.
COBPriority | Prioritet som angetts av företaget för BCDR. Värden från 1 till 5.
ENV | Utveckling, STG, PROD är möjliga värden. Som representerar utveckling, mellanlagring och produktion.

Exempel: 

 ![Azure taggar](./media/contoso-migration-infrastructure/azure-tag.png)

När du har skapat taggen Contoso gå tillbaka och skapa nya Azure principdefinitioner och tilldelningar att framtvinga användningen av taggarna som krävs i organisationen.


## <a name="step-6-consider-security"></a>Steg 6: Tänk igenom säkerheten

Säkerhet är avgörande i molnet Azure ger en mängd olika säkerhetsverktyg och -funktioner. Dessa hjälper dig att skapa säkra lösningar för säkra Azure-plattformen. Läs [förtroende för det tillförlitliga molnet](https://azure.microsoft.com/overview/trusted-cloud/) vill veta mer om Azure-säkerhet.

Det några huvudsakliga skillnader för Contoso att tänka på

- **Azure Security Center**: Azure Security Center erbjuder enhetlig säkerhetshantering och Avancerat skydd mot hot i olika hybridmolnarbetsbelastningar. Med Security Center kan du tillämpa säkerhetsprinciper i arbetsbelastningarna, begränsa hotexponeringen samt identifiera och åtgärda attacker.  [Läs mer](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Nätverkssäkerhetsgrupper (NSG)**: en NSG är ett filter (brandvägg) som innehåller en lista över security regler som vid tillämpningen tillåter eller nekar nätverkstrafik till resurser som är anslutna till virtuella Azure-nätverk. [Läs mer](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Datakryptering**: Azure Disk Encryption är en funktion som hjälper dig att kryptera din Windows- och Linux IaaS VM-diskar. [Läs mer](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Arbeta med Azure Security Center

Contoso är ute efter en snabb överblick över säkerhetstillståndet för sina nya hybridmoln och specifikt sina Azure-arbetsbelastningar.  Contoso har därför valt att implementera Azure Security Center börjar med följande funktioner: 

- Centraliserad principhantering
- Kontinuerlig utvärdering
- Användbara rekommendationer 

#### <a name="centralize-policy-management"></a>Centralisera hantering av Grupprincip

Med centraliserad hantering av garanterar Contoso säkerhetskraven genom att hantera principer centralt över hela miljön. De kan enkelt och snabbt implementera en princip som gäller för alla sina Azure-resurser.

![Säkerhetsprincip](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Utvärdera och åtgärd

Contoso att utnyttja kontinuerlig säkerhetsbedömning som övervakar säkerheten för datorer, nätverk, lagring, data och program. att identifiera potentiella säkerhetsproblem. 

- Security Center analyserar säkerhetstillståndet på Contosos beräknings-, infrastruktur- och dataresurser och Azure-appar och tjänster.
- Kontinuerlig utvärdering hjälper driftsteamet Contoso att identifiera potentiella säkerhetsproblem, till exempel system som saknar säkerhetsuppdateringar eller exponerade nätverksportar. 
- I synnerhet vill Contoso Kontrollera att alla sina virtuella datorer är skyddade. Security Center hjälper med den här, verifiering av VM-hälsa och att göra rangordnade rekommendationer för att åtgärda säkerhetsproblem innan de är utnyttjas.

![Övervakning](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Arbeta med NSG: er

Contoso kan begränsa nätverkstrafiken till resurser i ett virtuellt nätverk med nätverkssäkerhetsgrupper.

- En nätverkssäkerhetsgrupp innehåller en lista över säkerhetsregler som tillåter eller nekar inkommande eller utgående nätverkstrafik baserat på käll- eller mål-IP-adress, port och protokoll.
- När tillämpas till ett undernät, tillämpas regler till alla resurser i undernätet. Förutom nätverksgränssnitt kan inkluderar detta instanser av Azure-tjänster som distribuerats i undernätet.
- Programsäkerhetsgrupper (asg) kan du konfigurera nätverkssäkerhet som ett naturligt tillägg till en app-struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper.
    - App-säkerhetsgrupper innebär att du kan återanvända din säkerhetsprincip i stor skala utan manuellt underhåll av explicita IP-adresser. Plattformen hanterar komplexiteten med explicita IP-adresser och flera regeluppsättningar så att du kan fokusera på affärslogik.
    - Du kan ange en programsäkerhetsgrupp som källa och mål i en säkerhetsregel. När din säkerhetsprincip har definierats kan du skapa virtuella datorer och tilldela VM-nätverkskort till en grupp. 


Contoso implementerar en blandning av Nätverkssäkerhetsgrupper och Programsäkerhetsgrupper. De har att göra om hantering av NSG. De oroar felaktig användning av NSG: er och komplexitet som det kan innebära för personalen åtgärder.  De har antagit två viktiga huvudkonton som de använder en allmän regel med detta i åtanke:

- All trafik till och från alla undernät (Nord-sydlig) omfattas av en NSG-regel, förutom GatewaySubnets i Hub-nätverk.
- Alla brandväggar eller domänkontrollanter kommer att skyddas av både undernät NSG: er och NSG: er för nätverkskortet.
- Alla program i produktion kommer att ha Programsäkerhetsgrupper som tillämpas.


Contoso har byggt en modell av hur detta ser ut för sina program.

![Säkerhet](./media/contoso-migration-infrastructure/asg.png)


Nätverkssäkerhetsgrupper som är associerade med Programsäkerhetsgrupper konfigureras med lägsta behörighet att se till att endast får paket kan flöda från en del av nätverket till dess mål.

**Åtgärd** | **Namn** | **Källa** | **Mål** | **Port**
--- | --- | --- | --- | --- 
Tillåt | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | APP1-FE 80, 443
Tillåt | AllowWebToApp | APP1-FE | APP1-DB | 1433
Tillåt | AllowAppToDB | APP1-APP | Alla | Alla
Neka | DenyAllInbound | Alla | Alla | Alla

### <a name="encrypt-data"></a>Kryptera data

Azure Disk Encryption kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter i key vault-prenumeration. Det innebär att alla data på Virtuella datordiskar är krypterade i vila i Azure storage.  

- Contoso har fastställt att specifika virtuella datorer kräver kryptering.
- De kommer att använda kryptering för virtuella datorer med kunden, konfidentiell, eller bildpunkter per tum data.


## <a name="conclusion"></a>Sammanfattning

I den här artikeln Contoso ställer in sina Azure-infrastrukturen och ställa in eller planerat infrastruktur princip för Azure-prenumeration, hybrid identifierar, katastrofåterställning, nätverk, styrning och säkerhet. 

Inte alla steg som Contoso har slutfört här krävs för migrering till molnet. I fallen de vill planera en nätverksinfrastruktur som kan användas för alla typer av migreringar och är säker, flexibel och skalbar. 

De är redo att gå vidare och prova att använda migrering med den här infrastrukturen på plats.

## <a name="next-steps"></a>Nästa steg

Som ett första Migreringsscenario Contoso ska [utvärdera lokala SmartHotel två skikt appen för migrering till Azure](contoso-migration-assessment.md). 
