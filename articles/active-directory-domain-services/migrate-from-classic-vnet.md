---
title: Migrera Azure AD Domain Services från ett klassiskt virtuellt nätverk | Microsoft-dokument
description: Lär dig hur du migrerar en befintlig Azure AD Domain Services-hanterad domäninstans från den klassiska virtuella nätverksmodellen till ett Resource Manager-baserat virtuellt nätverk.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 6acf9301367ae2c6947f6935c43f420d3d7cac65
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655018"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Migrera Azure AD Domain Services från den klassiska virtuella nätverksmodellen till Resource Manager

Azure Active Directory Domain Services (AD DS) stöder en engångsföreteläggande för kunder som för närvarande använder den klassiska virtuella nätverksmodellen till Resource Manager virtuella nätverksmodellen. Azure AD DS-hanterade domäner som använder Resurshanterarens distributionsmodell innehåller ytterligare funktioner som detaljerad lösenordsprincip, granskningsloggar och kontoutelåsningsskydd.

I den här artikeln beskrivs fördelarna och övervägandena för migreringen och sedan de steg som krävs för att migrera en befintlig Azure AD DS-instans.

> [!NOTE]
> Under 2017 blev Azure AD Domain Services tillgängliga för värd i ett Azure Resource Manager-nätverk. Sedan dess har vi kunnat skapa en säkrare tjänst med hjälp av Azure Resource Manager moderna funktioner. Eftersom Azure Resource Manager-distributioner helt ersätter klassiska distributioner, kommer Azure AD DS klassiska virtuella nätverksdistributioner att dras tillbaka den 1 mars 2023.
>
> För mer information, se det [officiella meddelandet om utfasning](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>Översikt över migreringsprocessen

Migreringsprocessen tar en befintlig Azure AD DS-instans som körs i ett virtuellt klassiskt nätverk och flyttar den till ett befintligt virtuellt resurshanteraren-nätverk. Migreringen utförs med PowerShell och har två huvudstadier för körning - *förberedelse* och *migrering*.

![Översikt över migreringsprocessen för Azure AD DS](media/migrate-from-classic-vnet/migration-overview.png)

I *förberedelsefasen* tar Azure AD DS en säkerhetskopia av domänen för att få den senaste ögonblicksbilden av användare, grupper och lösenord synkroniserade till den hanterade domänen. Synkronisering inaktiveras sedan och molntjänsten som är värd för den Azure AD DS-hanterade domänen tas bort. Under förberedelsefasen kan den Hanterade Azure AD DS-domänen inte autentisera användare.

![Förberedelsefas för migrering av Azure AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

I *migreringsfasen* kopieras de underliggande virtuella diskarna för domänkontrollanterna från den klassiska Azure AD DS-hanterade domänen för att skapa de virtuella datorerna med hjälp av Resurshanterarens distributionsmodell. Den Hanterade Azure AD DS-domänen återskapas sedan, vilket inkluderar LDAPS- och DNS-konfigurationen. Synkroniseringen till Azure AD startas om och LDAP-certifikat återställs. Du behöver inte gå med i någon annan mappning till en Azure AD DS-hanterad domän – de fortsätter att vara anslutna till den hanterade domänen och köras utan ändringar.

![Migrering av Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Migrationsförmåner

När du flyttar en Azure AD DS-hanterad domän med den här migreringsprocessen undviker du behovet av att återansluta datorer till den hanterade domänen eller ta bort Azure AD DS-instansen och skapa en från grunden. Virtuella datorer fortsätter att anslutas till Azure AD DS-hanterade domänen i slutet av migreringsprocessen.

Efter migreringen innehåller Azure AD DS många funktioner som bara är tillgängliga för domäner med hjälp av virtuella resurshanteraren-nätverk, till exempel:

* Stöd för principen för detaljerade lösenordsbaserade lösenord.
* AD-kontoutelåsningsskydd.
* E-postmeddelanden om aviseringar på azure AD DS-hanterad domän.
* Granskningsloggar med Azure Monitor.
* Integrering av Azure-filer
* INTEGRERING av HD Insights

Azure AD DS-hanterade domäner som använder ett virtuellt resurshanterarenätverk hjälper dig att hålla dig uppdaterad med de senaste nya funktionerna. Stöd för Azure AD DS med hjälp av klassiska virtuella nätverk ska inaktuella i framtiden.

## <a name="example-scenarios-for-migration"></a>Exempel på scenarier för migrering

Några vanliga scenarier för att migrera en Azure AD DS-hanterad domän innehåller följande exempel.

> [!NOTE]
> Konvertera inte det virtuella nätverket Classic förrän du har bekräftat en lyckad migrering. Om du konverterar det virtuella nätverket tas alternativet att återställa eller återställa azure AD DS-hanterade domänen bort om det finns några problem under migrerings- och verifieringsfaserna.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrera Azure AD DS till ett befintligt virtuellt resurshanteraren -nätverk (rekommenderas)

Ett vanligt scenario är när du redan har flyttat andra befintliga Klassiska resurser till en Resource Manager-distributionsmodell och virtuellt nätverk. Peering används sedan från Resource Manager virtuella nätverk till classic virtuellt nätverk som fortsätter att köra Azure AD DS. Med den här metoden kan Resource Manager-program och -tjänster använda autentiserings- och hanteringsfunktionerna i azure AD DS-hanterad domän i det virtuella nätverket Classic. När alla resurser har migrerats körs de med resurshanterarens distributionsmodell och virtuella nätverk.

![Migrera Azure AD DS till ett befintligt virtuellt resurshanteraren-nätverk](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Åtgärder på hög nivå som ingår i det här exempelmigreringsscenariot omfattar följande delar:

1. Ta bort befintliga VPN-gateways eller virtuell nätverks peering konfigurerad i det virtuella nätverket Classic.
1. Migrera Azure AD DS-hanterad domän med hjälp av stegen i den här artikeln.
1. Testa och bekräfta en lyckad migrering och ta sedan bort det virtuella nätverket Klassiskt.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migrera flera resurser, inklusive Azure AD DS

I det här exemplet migrerar du Azure AD DS och andra associerade resurser från den klassiska distributionsmodellen till Resurshanterarens distributionsmodell. Om vissa resurser fortsatte att köras i det virtuella nätverket Klassisk tillsammans med azure AD DS-hanterade domänen kan de alla dra nytta av att migrera till Resurshanterarens distributionsmodell.

![Migrera flera resurser till resurshanterarens distributionsmodell](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Åtgärder på hög nivå som ingår i det här exempelmigreringsscenariot omfattar följande delar:

1. Ta bort befintliga VPN-gateways eller virtuell nätverks peering konfigurerad i det virtuella nätverket Classic.
1. Migrera Azure AD DS-hanterad domän med hjälp av stegen i den här artikeln.
1. Konfigurera virtuell nätverks peering mellan det klassiska virtuella nätverket och Resource Manager-nätverket.
1. Testa och bekräfta en lyckad migrering.
1. [Flytta ytterligare klassiska resurser som virtuella datorer.][migrate-iaas]

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrera Azure AD DS men behåll andra resurser i det virtuella nätverket Klassisk

Med det här exempelscenariot har du den minsta mängden driftstopp i en session. Du migrerar bara Azure AD DS till ett virtuellt resurshanterarenätverk och behåller befintliga resurser i den klassiska distributionsmodellen och det virtuella nätverket. Under en efterföljande underhållsperiod kan du migrera ytterligare resurser från den klassiska distributionsmodellen och det virtuella nätverket efter behov.

![Migrera endast Azure AD DS till resurshanterarens distributionsmodell](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Åtgärder på hög nivå som ingår i det här exempelmigreringsscenariot omfattar följande delar:

1. Ta bort befintliga VPN-gateways eller virtuell nätverks peering konfigurerad i det virtuella nätverket Classic.
1. Migrera Azure AD DS-hanterad domän med hjälp av stegen i den här artikeln.
1. Konfigurera virtuell nätverks peering mellan det virtuella nätverket Classic och det nya virtuella resurshanteraren.
1. Senare [migrerar du ytterligare resurser][migrate-iaas] från det virtuella nätverket Classic efter behov.

## <a name="before-you-begin"></a>Innan du börjar

När du förbereder och sedan migrerar en Azure AD DS-hanterad domän finns det vissa överväganden kring tillgängligheten för autentiserings- och hanteringstjänster. Den Azure AD DS-hanterade domänen är inte tillgänglig under en viss tid under migreringen. Program och tjänster som är beroende av Azure AD DS upplever driftstopp under migreringen.

> [!IMPORTANT]
> Läs alla den här migreringsartikeln och vägledningen innan du startar migreringsprocessen. Migreringsprocessen påverkar tillgängligheten för Azure AD DS-domänkontrollanterna under tidsperioder. Användare, tjänster och program kan inte autentisera mot den hanterade domänen under migreringsprocessen.

### <a name="ip-addresses"></a>IP-adresser

Domänkontrollantens IP-adresser för en Azure AD DS-hanterad domänändring efter migreringen. Den här ändringen innehåller den offentliga IP-adressen för den säkra LDAP-slutpunkten. De nya IP-adresserna finns inom adressintervallet för det nya undernätet i Resource Manager virtuella nätverk.

När det gäller återställning kan IP-adresserna ändras efter att de har återställts.

Azure AD DS använder vanligtvis de två första tillgängliga IP-adresserna i adressintervallet, men detta är inte garanterat. Du kan för närvarande inte ange vilka IP-adresser som ska användas efter migreringen.

### <a name="downtime"></a>Driftstopp

Migreringsprocessen innebär att domänkontrollanterna är offline under en viss tid. Domänkontrollanter är otillgängliga medan Azure AD DS migreras till Resurshanterarens distributionsmodell och virtuella nätverk. I genomsnitt är driftstoppet runt 1 till 3 timmar. Den här tidsperioden är från när domänkontrollanterna kopplas från till det ögonblick den första domänkontrollanten ansluts igen. Det här medelvärdet innehåller inte den tid det tar för den andra domänkontrollanten att replikera, eller den tid det kan ta att migrera ytterligare resurser till Resurshanterarens distributionsmodell.

### <a name="account-lockout"></a>Kontoutelåsning

Azure AD DS-hanterade domäner som körs på klassiska virtuella nätverk har inte AD-kontoutelåsningsprinciper på plats. Om virtuella datorer exponeras för internet kan angripare använda lösenordsspraymetoder för att brute-force sig in på konton. Det finns ingen princip för kontoutelåsning för att stoppa dessa försök. För Azure AD DS-hanterade domäner som använder Resurshanterarens distributionsmodell och virtuella nätverk skyddar AD-kontoutelåsningsprinciper mot dessa lösenordssprayattacker.

Som standard låser 5 felaktiga lösenordsförsök på 2 minuter ett konto i 30 minuter.

Ett utelåst konto kan inte användas för att logga in, vilket kan störa möjligheten att hantera azure AD DS-hanterade domänen eller program som hanteras av kontot. När en Azure AD DS-hanterad domän har migrerats kan konton uppleva vad som känns som en permanent utelåsning på grund av upprepade misslyckade försök att logga in. Två vanliga scenarier efter migreringen är följande:

* Ett tjänstkonto som använder ett lösenord som har upphört att gälla.
    * Tjänstkontot försöker upprepade gånger logga in med ett utgånget lösenord som låser kontot. Åtgärda detta genom att hitta programmet eller den virtuella datorn med utgångna autentiseringsuppgifter och uppdatera lösenordet.
* En skadlig entitet använder brute force-försök att logga in på konton.
    * När virtuella datorer exponeras för internet försöker angripare ofta vanliga kombinationer av användarnamn och lösenord när de försöker signera. Dessa upprepade misslyckade inloggningsförsök kan låsa ut kontona. Det rekommenderas inte att använda administratörskonton med allmänna namn som *administratör* eller *administratör,* till exempel för att minimera administrativa konton från att låsas ute.
    * Minimera antalet virtuella datorer som exponeras för internet. Du kan använda [Azure Bastion][azure-bastion] för att ansluta till virtuella datorer på ett säkert sätt med hjälp av Azure-portalen.

Om du misstänker att vissa konton kan vara utelåsta efter migreringen beskriver de slutliga migreringsstegen hur du aktiverar granskning eller ändrar de detaljerade lösenordsprincipinställningarna.

### <a name="roll-back-and-restore"></a>Rulla tillbaka och återställa

Om migreringen inte lyckas finns det en process för att återställa eller återställa en Azure AD DS-hanterad domän. Återställning är ett självbetjäningsalternativ för att omedelbart återställa tillståndet för den hanterade domänen till före migreringsförsöket. Azure-supporttekniker kan också återställa en hanterad domän från säkerhetskopiering som en sista utväg. Mer information finns i [hur du återställer eller återställer från en misslyckad migrering](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Begränsningar för tillgängliga virtuella nätverk

Det finns vissa begränsningar för de virtuella nätverk som en Azure AD DS-hanterad domän kan migreras till. Det virtuella nätverket Mål Resource Manager måste uppfylla följande krav:

* Det virtuella nätverket Resource Manager måste finnas i samma Azure-prenumeration som det virtuella klassiska nätverk som Azure AD DS för närvarande distribueras i.
* Det virtuella nätverket Resource Manager måste finnas i samma region som det virtuella klassiska nätverket som Azure AD DS för närvarande distribueras i.
* Resource Manager virtuella nätverkets undernät bör ha minst 3-5 tillgängliga IP-adresser.
* Det virtuella nätverket Resource Manager ska vara ett dedikerat undernät för Azure AD DS och bör inte vara värd för andra arbetsbelastningar.

Mer information om krav på virtuellt nätverk finns i [Hänsyn till virtual network design och konfigurationsalternativ][network-considerations].

## <a name="migration-steps"></a>Migreringsanvisningar

Migreringen till Resurshanterarens distributionsmodell och virtuella nätverk är uppdelad i fem huvudsteg:

| Steg    | Utförs genom  | Beräknad tid  | Driftstopp  | Rulla tillbaka/återställa? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Steg 1 - Uppdatera och hitta det nya virtuella nätverket](#update-and-verify-virtual-network-settings) | Azure Portal | 15 minuter | Inga driftstopp krävs | Ej tillämpligt |
| [Steg 2 – Förbered den Hanterade Azure AD DS-domänen för migrering](#prepare-the-managed-domain-for-migration) | PowerShell | 15 – 30 minuter i genomsnitt | Driftstopp för Azure AD DS startar när det här kommandot har slutförts. | Rulla tillbaka och återställ tillgängliga. |
| [Steg 3 – Flytta Azure AD DS-hanterad domän till ett befintligt virtuellt nätverk](#migrate-the-managed-domain) | PowerShell | 1 – 3 timmar i genomsnitt | En domänkontrollant är tillgänglig när det här kommandot är klart avslutas driftstopp. | Vid fel är både återställning (självbetjäning) och återställning tillgängliga. |
| [Steg 4 - Testa och vänta på replikdomänkontrollanten](#test-and-verify-connectivity-after-the-migration)| PowerShell och Azure-portal | 1 timme eller mer, beroende på antalet tester | Båda domänkontrollanterna är tillgängliga och bör fungera normalt. | Ej tillämpligt. När den första virtuella datorn har migrerats finns det inget alternativ för återställning eller återställning. |
| [Steg 5 - Valfria konfigurationssteg](#optional-post-migration-configuration-steps) | Azure-portal och virtuella datorer | Ej tillämpligt | Inga driftstopp krävs | Ej tillämpligt |

> [!IMPORTANT]
> Om du vill undvika ytterligare driftstopp läser du alla den här migreringsartikeln och vägledningen innan du startar migreringsprocessen. Migreringsprocessen påverkar tillgängligheten för Azure AD DS-domänkontrollanterna under en viss tid. Användare, tjänster och program kan inte autentisera mot den hanterade domänen under migreringsprocessen.

## <a name="update-and-verify-virtual-network-settings"></a>Uppdatera och verifiera inställningar för virtuella nätverk

Innan du påbörjar migreringsprocessen slutför du följande inledande kontroller och uppdateringar. Dessa steg kan inträffa när som helst före migreringen och påverkar inte driften av azure AD DS-hanterad domän.

1. Uppdatera din lokala Azure PowerShell-miljö till den senaste versionen. För att slutföra migreringsstegen behöver du minst version *2.3.2*.

    Information om hur du kontrollerar och uppdaterar din PowerShell-version finns i [Azure PowerShell översikt][azure-powershell].

1. Skapa eller välj ett befintligt virtuellt nätverk för Resource Manager.

    Se till att nätverksinställningarna inte blockerar nödvändiga portar som krävs för Azure AD DS. Portarna måste vara öppna i både det virtuella nätverket Classic och det virtuella resurshanteraren. Dessa inställningar inkluderar vägtabeller (även om det inte rekommenderas att använda flödestabeller) och nätverkssäkerhetsgrupper.

    Information om vilka portar som krävs finns i [Nätverkssäkerhetsgrupper och nödvändiga portar][network-ports]. För att minimera problem med nätverkskommunikationen rekommenderar vi att du väntar och tillämpar en nätverkssäkerhetsgrupp eller vägtabell i det virtuella nätverket Resource Manager när migreringen har slutförts.

    Anteckna den här målgruppen, rikta in dig på virtuellt nätverk och rikta in dig på det virtuella nätverksundernätet. Dessa resursnamn används under migreringsprocessen.

1. Kontrollera azure AD DS-hanterade domänhälsan i Azure-portalen. Om du har några aviseringar för den hanterade domänen löser du dem innan du startar migreringsprocessen.
1. Om du planerar att flytta andra resurser till Resurshanterarens distributionsmodell och virtuella nätverk kan du bekräfta att dessa resurser kan migreras. Mer information finns i [Migrering av IaaS-resurser som stöds av plattformen från Klassisk till Resurshanteraren][migrate-iaas].

    > [!NOTE]
    > Konvertera inte det virtuella nätverket Klassisk till ett virtuellt resurshanterarenätverk. Om du gör det finns det inget alternativ för att återställa eller återställa den Hanterade Azure AD DS-domänen.

## <a name="prepare-the-managed-domain-for-migration"></a>Förbereda den hanterade domänen för migrering

Azure PowerShell används för att förbereda azure AD DS-hanterad domän för migrering. Dessa steg omfattar att göra en säkerhetskopia, pausa synkroniseringen och ta bort molntjänsten som är värd för Azure AD DS. När det här steget är klart tas Azure AD DS offline under en viss tid. Om förberedelsesteget misslyckas kan du [återställa till föregående tillstånd](#roll-back).

Så här förbereder du den Hanterade Azure AD DS-domänen för migrering:

1. Installera `Migrate-Aaads` skriptet från [PowerShell Gallery][powershell-script]. Det här PowerShell-migreringsskriptet är ett digitalt signerat av Azure AD-teknikteamet.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Skapa en variabel som du vill behålla autentiseringsuppgifterna för av migreringsskriptet med cmdleten [Get-Autentiseringsuppgifter.][get-credential]

    Användarkontot som du anger behöver globala administratörsbehörighet i din Azure AD-klientorganisation för att aktivera Azure AD DS och sedan *deltagarbehörighet* i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna. *global administrator*

    Ange ett lämpligt användarkonto och lösenord när du uppmanas:

    ```powershell
    $creds = Get-Credential
    ```

1. Kör nu `Migrate-Aadds` cmdlet med parametern *-Prepare.* Ange *-ManagedDomainFqdn* för din egen Azure AD DS-hanterade domän, till exempel *aaddscontoso.com:*

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Migrera den hanterade domänen

Med Azure AD DS-hanterad domän förberedd och säkerhetskopierad kan domänen migreras. Det här steget återskapar virtuella datorer för Azure AD Domain Services-domänkontrollant med hjälp av resurshanterarens distributionsmodell. Det här steget kan ta 1 till 3 timmar att slutföra.

Kör `Migrate-Aadds` cmdleten med parametern *-Commit.* Ange *-ManagedDomainFqdn* för din egen Azure AD DS-hanterade domän som utarbetats i föregående avsnitt, till exempel *aaddscontoso.com:*

Ange den målgrupp som innehåller det virtuella nätverk som du vill migrera Azure AD DS till, till exempel *myResourceGroup*. Ange det virtuella målnätverket, till exempel *myVnet,* och undernätet, till exempel *DomainServices*.

När det här kommandot har körts kan du inte återställa:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

När skriptet har validerats som den hanterade domänen har förberetts för migrering anger du *Y* för att starta migreringsprocessen.

> [!IMPORTANT]
> Konvertera inte det virtuella nätverket Classic till ett virtuellt resurshanterarenätverk under migreringsprocessen. Om du konverterar det virtuella nätverket kan du inte återställa eller återställa azure AD DS-hanterad domän eftersom det ursprungliga virtuella nätverket inte längre finns.

Varannan minut under migreringsprocessen rapporterar en förloppsindikator aktuell status, vilket visas i följande exempelutdata:

![Förloppsindikator för migrering av Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

Migreringsprocessen fortsätter att köras, även om du stänger powershell-skriptet. I Azure-portalen rapporterar status för den hanterade domänen som *Migrera*.

När migreringen har slutförts kan du visa din första domänkontrollants IP-adress i Azure-portalen eller via Azure PowerShell. En tidsuppskattning på den andra domänkontrollanten som är tillgänglig visas också.

I det här skedet kan du eventuellt flytta andra befintliga resurser från den klassiska distributionsmodellen och det virtuella nätverket. Du kan också behålla resurserna på den klassiska distributionsmodellen och peer de virtuella nätverken till varandra när Azure AD DS-migreringen är klar.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Testa och verifiera anslutningen efter migreringen

Det kan ta lite tid för den andra domänkontrollanten att distribuera och vara tillgänglig för användning i Azure AD DS-hanterad domän.

Med resurshanterarens distributionsmodell visas nätverksresurserna för den Azure AD DS-hanterade domänen i Azure-portalen eller Azure PowerShell. Mer information om vad dessa nätverksresurser är och gör finns [i Nätverksresurser som används av Azure AD DS][network-resources].

När minst en domänkontrollant är tillgänglig utför du följande konfigurationssteg för nätverksanslutning till virtuella datorer:

* **Uppdatera DNS-serverinställningar** Om du vill att andra resurser i Resource Manager virtuella nätverk ska matcha och använda den Hanterade Azure AD DS-domänen uppdaterar du DNS-inställningarna med IP-adresserna för de nya domänkontrollanterna. Azure-portalen kan automatiskt konfigurera dessa inställningar åt dig. Mer information om hur du konfigurerar det virtuella nätverket Resource Manager finns i [Uppdatera DNS-inställningar för det virtuella Azure-nätverket][update-dns].
* **Starta om domänanslutna virtuella datorer** – När DNS-serverns IP-adresser för Azure AD DS-domänkontrollanterna ändras startar du om alla domänanslutna virtuella datorer så att de sedan använder de nya DNS-serverinställningarna. Om program eller virtuella datorer har konfigurerat DNS-inställningar manuellt uppdaterar du dem manuellt med de nya DNS-serverns IP-adresser för domänkontrollanterna som visas i Azure-portalen.

Testa nu den virtuella nätverksanslutningen och namnupplösningen. På en virtuell dator som är ansluten till det virtuella resurshanterarens virtuella nätverk eller som är peered till den kan du prova följande nätverkskommunikationstester:

1. Kontrollera om du kan pinga IP-adressen för en av domänkontrollanterna, t.ex.`ping 10.1.0.4`
    * IP-adresserna för domänkontrollanterna visas på **sidan Egenskaper** för den Azure AD DS-hanterade domänen i Azure-portalen.
1. Verifiera namnmatchning för den hanterade domänen, t.ex.`nslookup aaddscontoso.com`
    * Ange DNS-namnet för din egen Azure AD DS-hanterade domän för att kontrollera att DNS-inställningarna är korrekta och löser.

Den andra domänkontrollanten ska vara tillgänglig 1-2 timmar efter att migrerings cmdlet är klar. Om du vill kontrollera om den andra domänkontrollanten är tillgänglig tittar du på sidan **Egenskaper** för den Azure AD DS-hanterade domänen i Azure-portalen. Om två IP-adresser visas är den andra domänkontrollanten klar.

## <a name="optional-post-migration-configuration-steps"></a>Konfigurationssteg för postmigrering (tillval)

När migreringsprocessen har slutförts kan du använda några valfria konfigurationssteg som att aktivera granskningsloggar eller e-postmeddelanden eller uppdatera principen om detaljerade lösenord.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Prenumerera på granskningsloggar med Azure Monitor

Azure AD DS visar granskningsloggar för att felsöka och visa händelser på domänkontrollanterna. Mer information finns i [Aktivera och använda granskningsloggar][security-audits].

Du kan använda mallar för att övervaka viktig information som exponeras i loggarna. Granskningsloggarbetsboksmallen kan till exempel övervaka möjliga kontoutelåsningar på den Azure AD DS-hanterade domänen.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>Konfigurera e-postmeddelanden för Azure AD Domain Services

Om du vill meddelas när ett problem upptäcks på azure AD DS-hanterad domän uppdaterar du e-postmeddelandeninställningarna i Azure-portalen. Mer information finns i [Konfigurera meddelandeinställningar][notifications].

### <a name="update-fine-grained-password-policy"></a>Uppdatera principen för detaljerade lösenord

Om det behövs kan du uppdatera principen för detaljerade lösenord så att den är mindre restriktiv än standardkonfigurationen. Du kan använda granskningsloggarna för att avgöra om en mindre restriktiv inställning är meningsfull och sedan konfigurera principen efter behov. Använd följande steg på hög nivå för att granska och uppdatera principinställningarna för konton som är utelåst upprepade gånger efter migreringen:

1. [Konfigurera lösenordsprincip][password-policy] för färre begränsningar för azure AD DS-hanterad domän och observera händelserna i granskningsloggarna.
1. Om några tjänstkonton använder utgångna lösenord enligt granskningsloggarna uppdaterar du dessa konton med rätt lösenord.
1. Om en virtuell dator exponeras för internet granskar du efter allmänna kontonamn som *administratör,* *användare*eller *gäst* med avancerade inloggningsförsök. Om möjligt uppdaterar du dessa virtuella datorer så att de använder mindre allmänt namngivna konton.
1. Använd en nätverksspårning på den virtuella datorn för att hitta källan till attackerna och blockera dessa IP-adresser från att kunna försöka logga in.
1. När det finns minimala utelåsningsproblem uppdaterar du principen för detaljerade lösenord så att den är så restriktiv som behövs.

### <a name="creating-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Azure AD DS behöver en nätverkssäkerhetsgrupp för att skydda de portar som behövs för den hanterade domänen och blockera all annan inkommande trafik. Den här nätverkssäkerhetsgruppen fungerar som ett extra skyddslager för att låsa åtkomsten till den hanterade domänen och skapas inte automatiskt. Om du vill skapa nätverkssäkerhetsgruppen och öppna de portar som krävs läser du följande steg:

1. Välj din Azure AD DS-resurs i Azure-portalen. På översiktssidan visas en knapp för att skapa en nätverkssäkerhetsgrupp om det inte finns någon som är associerad med Azure AD Domain Services.
1. Om du använder säker LDAP lägger du till en regel i nätverkssäkerhetsgruppen för att tillåta inkommande trafik för *TCP-port* *636*. Mer information finns i [Konfigurera säker LDAP][secure-ldap].

## <a name="roll-back-and-restore-from-migration"></a>Återställa och återställa från migrering

Upp till en viss punkt i migreringsprocessen kan du välja att återställa eller återställa azure AD DS-hanterad domän.

### <a name="roll-back"></a>Rulla tillbaka

Om det finns ett fel när du kör PowerShell-cmdleten för att förbereda migreringen i steg 2 eller för själva migreringen i steg 3, kan Azure AD DS-hanterad domän återställa till den ursprungliga konfigurationen. Den här återställningen kräver det ursprungliga virtuella nätverket Classic. Observera att IP-adresserna fortfarande kan ändras efter återställning.

Kör `Migrate-Aadds` cmdleten med parametern *-Avbryt.* Ange *-ManagedDomainFqdn* för din egen Azure AD DS-hanterad domän som utarbetats i ett tidigare avsnitt, till exempel *aaddscontoso.com*och det klassiska virtuella nätverksnamnet, till exempel *myClassicVnet:*

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>Återställ

Som en sista utväg kan Azure AD Domain Services återställas från den senast tillgängliga säkerhetskopian. En säkerhetskopia tas i steg 1 i migreringen för att se till att den senaste säkerhetskopian är tillgänglig. Den här säkerhetskopian lagras i 30 dagar.

Om du vill återställa azure AD DS-hanterad domän från säkerhetskopiering [öppnar du en supportärende med Azure-portalen][azure-support]. Ange ditt katalog-ID, domännamn och orsak till återställning. Support- och återställningsprocessen kan ta flera dagar att slutföra.

## <a name="troubleshooting"></a>Felsökning

Om du har problem efter migreringen till resurshanterarens distributionsmodell läser du några av följande vanliga felsökningsområden:

* [Felsöka problem med domänkoppling][troubleshoot-domain-join]
* [Felsöka problem med kontoutelåsning][troubleshoot-account-lockout]
* [Felsöka inloggningsproblem för konto][troubleshoot-sign-in]
* [Felsöka problem med säker LDAP-anslutning][tshoot-ldaps]

## <a name="next-steps"></a>Nästa steg

Med din Azure AD DS-hanterade domän migrerad till Resurshanterarens distributionsmodell [skapar och domäntillträda till en Windows-virtuell dator][join-windows] och installerar sedan [hanteringsverktyg][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
