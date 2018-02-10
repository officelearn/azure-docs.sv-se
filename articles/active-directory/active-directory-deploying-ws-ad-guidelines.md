---
title: "Riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines | Microsoft Docs"
description: "Om du vet hur du distribuerar AD DS och AD federationstjänster lokalt lär du dig hur de fungerar på virtuella Azure-datorer."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: femila
ms.openlocfilehash: 2a6ac8d9c2f3694cf08357d6ccec874f7e076514
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Riktlinjer för att distribuera Windows Server Active Directory på Azure virtual machines
Den här artikeln beskriver viktiga skillnader mellan distribuera Windows Server Active Directory Domain Services (AD DS) och Active Directory Federation Services (AD FS) lokalt och distribuera dem på Microsoft Azure-datorer.

## <a name="scope-and-audience"></a>Omfång och målgrupp
Artikeln är avsedd för de redan erfarenhet av distribution av Active Directory lokalt. Den omfattar skillnaderna mellan distribution av Active Directory på virtuella nätverk för virtuella datorer/Azure för Microsoft Azure och traditionella lokala Active Directory-distributioner. Virtuella Azure-datorer och virtuella Azure-nätverk är en del av en infrastruktur-som-en-tjänst (IaaS för organisationer att använda resurser i molnet).

De som inte är bekant med AD-distribution finns i [Distributionsguide](https://technet.microsoft.com/library/cc753963) eller [planera distributionen av AD FS](https://technet.microsoft.com/library/dn151324.aspx) efter behov.

Den här artikeln förutsätter att läsaren känner till följande begrepp:

* Windows Server AD DS-distribution och hantering
* Distribution och konfiguration av DNS för att stödja en Windows Server AD DS-infrastruktur
* Windows Server AD FS-distribution och hantering
* Distribuera, konfigurera och hantera förlitande parters program (webbplatser och webbprogram services) som kan använda Windows Server AD FS-token
* Allmän virtuella begrepp, till exempel hur du konfigurerar en virtuell dator, virtuella diskar och virtuella nätverk

Den här artikeln beskrivs kraven för ett scenario med hybridanvändning distribution där Windows Server AD DS eller AD FS är delvis distribuerade lokalt och delvis distribueras på virtuella Azure-datorer. Dokumentet innehåller först viktiga skillnaderna mellan Windows Server AD DS och AD FS som körs på Azure virtual machines jämfört med lokalt och viktiga punkter som påverkar design och distribution. Resten av dokumentet beskriver riktlinjer för varje beslutspunkterna i detalj och så här installerar du riktlinjerna för olika distributionsscenarier.

Den här artikeln innehåller ingen information om konfigurationen av [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), vilket är en REST-baserad tjänst som tillhandahåller identity management och kontroll åtkomstfunktioner för molnprogram. Azure Active Directory (Azure AD) och Windows Server AD DS kan emellertid för att hjälpa dig för att skapa en identitets- och hanteringslösning för hybrid dagens IT-miljöer och moderna program. Om du vill förstå skillnaderna och relationer mellan Windows Server AD DS och AD Azure bör du tänka på följande:

1. Du kan köra Windows Server AD DS i molnet på virtuella Azure-datorer när du använder Azure för att utöka ditt lokala datacenter till molnet.
2. Du kan använda Azure AD för att ge dina användare enkel inloggning till programvara som en-tjänst (SaaS)-program. Microsoft Office 365 använder den här tekniken, till exempel och program som körs på Azure eller andra molnplattformar kan också använda den.
3. Du kan använda Azure AD (dess åtkomstkontrolltjänsten) så att användare kan logga in med identiteter från Facebook, Google, Microsoft och andra identitetsleverantörer till program som finns i molnet eller lokalt.

Mer information om skillnaderna finns [Azure Identity](fundamentals-identity.md).

## <a name="related-resources"></a>Relaterade resurser
Du kan hämta och köra den [Azure virtuella Readiness Assessment](https://www.microsoft.com/download/details.aspx?id=40898). Bedömning automatiskt kontrollera din lokala miljö och skapa en anpassad rapport som baseras på de riktlinjer som finns i det här avsnittet hjälper dig att migrera miljön till Azure.

Vi rekommenderar att du först granska självstudier, guider och videor som omfattar följande avsnitt:

* [Konfigurera ett virtuellt nätverk som endast molnbaserad i Azure-portalen](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
* [Konfigurera en plats-till-plats-VPN i Azure-portalen](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Installera en ny Active Directory-skog på Azure-nätverk](active-directory-new-forest-virtual-machine.md)
* [Installera en replik Active Directory-domänkontrollant på Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IT Pro IaaS: (01) virtuella grunderna](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) att skapa virtuella nätverk och anslutning](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introduktion
De grundläggande kraven för att distribuera Windows Server Active Directory på Azure virtual machines skiljer sig något från den distribution i lokala virtuella datorer (och till viss del, fysiska datorer). Till exempel för Windows Server AD DS om domänkontrollanter (DC) som du distribuerar på Azure virtual machines repliker i en befintlig lokal företagets domänen/skogen och sedan Azure-distribution kan i hög grad behandlas på samma sätt som du kan hantera alla andra ytterligare Windows Server Active Directory-plats. Det vill säga måste undernät definieras i Windows Server AD DS, en webbplats som har skapats, undernät länkad till den platsen och ansluten till andra platser med lämpliga platslänkar. Det finns emellertid vissa skillnader som är gemensamma för alla Azure-distributioner och vissa som varierar beroende på det specifika distributionsscenariot. Nedan beskrivs två viktiga skillnader:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Virtuella Azure-datorer kan behöver ansluta till företagets lokala nätverk.
Ansluta virtuella Azure-datorer till ett lokalt företagsnätverk kräver Azure-nätverk, som innehåller en plats-till-plats eller plats-till-punkt virtuella privata nätverk (VPN)-komponent sömlöst ansluta virtuella datorer i Azure och lokala datorer. Den här VPN-komponenten kan även aktivera lokala domänmedlemsdatorer att få åtkomst till en Windows Server Active Directory-domän vars domänkontrollanter finns endast på virtuella Azure-datorer. Det är viktigt att Observera att om VPN-anslutningen misslyckas, autentisering och andra åtgärder som är beroende av Windows Server Active Directory fungerar inte. När användare kanske loggar in med befintliga cachelagrade autentiseringsuppgifter för alla peer-to-peer- eller klient-till-server autentiseringsförsök som biljetter ännu eller som har blivit inaktuella misslyckas.

Se [virtuellt nätverk](http://azure.microsoft.com/documentation/services/virtual-network/) för en video demonstration och en lista med stegvisa självstudier, inklusive [konfigurera en plats-till-plats-VPN i Azure portal](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Du kan också distribuera Windows Server Active Directory på Azure-nätverk som inte har anslutning med ett lokalt nätverk. Riktlinjerna i det här avsnittet förutsätter dock att Azure-nätverk används eftersom det ger IP-adressering funktioner som är nödvändiga för Windows Server.
> 
> 

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Statiska IP-adresser konfigureras med Azure PowerShell.
Dynamiska adresser tilldelas som standard, men Använd cmdlet Set-AzureStaticVNetIP för att tilldela en statisk IP-adress i stället. Som anger en statisk IP-adress som behålls via tjänsten återställning och VM avstängning/restart. Mer information finns i [Statiska interna IP-adress för virtuella datorer](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Termer och definitioner
Följande är en ofullständig lista över villkor för olika Azure tekniker som kommer att referera till i den här artikeln.

* **Virtuella Azure-datorer**: den IaaS erbjudande i Azure som gör det möjligt för kunder att distribuera virtuella datorer som kör nästan alla traditionellt lokalt serverbelastning.
* **Virtuella Azure-nätverket**: nätverkstjänst i Azure som gör att kunderna kan skapa och hantera virtuella nätverk i Azure och länka dem på ett säkert sätt till sina egna lokalt nätverksinfrastruktur med hjälp av ett virtuellt privat nätverk (VPN).
* **Virtuella IP-adressen**: en extern IP-adress som inte är bunden till en viss dator eller ett nätverk för nätverkskortet. Molntjänster tilldelas en virtuell IP-adress för att ta emot nätverkstrafik som omdirigeras till en Azure VM. En virtuell IP-adress är en egenskap för en molntjänst som kan innehålla en eller flera virtuella Azure-datorer. Observera också att Azure-nätverk kan innehålla en eller flera molntjänster. Virtuella IP-adresser innehåller inbyggda funktioner för belastningsutjämning.
* **Dynamisk IP-adress**: IP-adress som endast är interna. Det bör konfigureras som en statisk IP-adress (med hjälp av cmdlet Set-AzureStaticVNetIP) för virtuella datorer som är värd för DC/DNS-server-roller.
* **Tjänsten återställning**: processen där Azure automatiskt igen en tjänst körs igen efter att tjänsten misslyckades. Tjänsten återställning är en av aspekterna i Azure som har stöd för tillgänglighet och flexibilitet. Medan osannolik, liknar en oplanerad omstart resultatet efter en tjänst som återställning incident för en Domänkontrollant som körs på en virtuell dator, men har några sidoeffekter:
  
  * Det virtuella nätverkskortet på den virtuella datorn kommer att ändras
  * MAC-adressen för det virtuella nätverkskortet kommer att ändras
  * Processor/CPU-ID för den virtuella datorn kommer att ändras
  * IP-adresskonfigurationen för det virtuella nätverkskortet kommer inte att ändra så länge som den virtuella datorn är ansluten till ett virtuellt nätverk och IP-adressen för den virtuella datorn är statisk.
  
  Ingen av dessa beteenden påverkar Windows Server Active Directory eftersom det finns inga beroende på MAC-adress eller Processor/CPU-ID och alla Windows Server Active Directory-distributioner på Azure rekommenderas för att köras på Azure-nätverk som beskrivs ovan.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>Är det säkert att virtualisera domänkontrollanter i Windows Server Active Directory?
Distribuera Windows Server Active Directory-domänkontrollanter på virtuella Azure-datorer regleras av samma riktlinjer som körs domänkontrollanter på lokalt på en virtuell dator. Kör virtualiserade domänkontrollanter är säker så länge som riktlinjer för att säkerhetskopiera och återställa domänkontrollanter följs. Mer information om begränsningar och riktlinjer för att köra virtualiserade domänkontrollanter finns [köra Domänkontrollers i Hyper-V](https://technet.microsoft.com/library/dd363553).

Hypervisorer tillhandahåller eller trivialize tekniker som kan orsaka problem för många distribuerade system, inklusive Windows Server Active Directory. Till exempel på en fysisk server du klona en disk eller använda stöds inte för att återställa tillståndet för en server, inklusive användning av SAN-nätverk och så vidare, men gör på en fysisk server är mycket svårare än att återställa en ögonblicksbild av virtuell dator i en hypervisor. Azure erbjuder funktioner som kan leda till samma främmande villkoret. Du bör till exempel inte kopiera VHD-filer av domänkontrollanter i stället för att utföra regelbundna säkerhetskopieringar eftersom återställa dem kan resultera i en liknande situation med funktioner för återställning av ögonblicksbild.

Sådana återställningar införa USN-bubblor som kan leda till permanent avvikande tillstånd mellan domänkontrollanter. Som kan orsaka problem, till exempel:

* Kvarvarande objekt
* Inkonsekvent lösenord
* Inkonsekvent attributvärden
* Schemamatchningsfel om schemahanteraren återställs

Mer information om hur domänkontrollanter påverkas finns [USN och USN-återställning](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

Från och med Windows Server 2012 [ytterligare skydd är inbyggda i AD DS](https://technet.microsoft.com/library/hh831734.aspx). Dessa skydd skydda virtualiserade domänkontrollanter mot dessa problem, så länge den underliggande hypervisor-plattformen stöder VM-Generationsid. Azure stöder VM-Generationsid, vilket innebär att domänkontrollanter som kör Windows Server 2012 eller senare på Azure virtuella datorer har fler skyddsmekanismer.

> [!NOTE]
> Du bör avsluta och starta om en virtuell dator som kör domänkontrollantrollen i Azure gästoperativsystem istället för att använda den **avsluta** alternativ i Azure får. Idag, gör med hjälp av portalen för att stänga av en virtuell dator den virtuella datorn att frigöras. En frigjord virtuell dator har fördelen att inte medför kostnader, men också återställer VM-GenerationID, vilket är önskvärt för en Domänkontrollant. När VM-GenerationID återställs invocationID för AD DS-databasen återställs även, RID-poolen förkastas och SYSVOL är markerad som icke-auktoritativ. Mer information finns i [introduktion till Active Directory Domain Services (AD DS)-virtualisering](https://technet.microsoft.com/library/hh831734.aspx) och [säker virtualisering av DFSR](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Varför distribuerar Windows Server AD DS på virtuella datorer i Azure?
Många scenarier för distribution av Windows Server AD DS lämpar sig väl för distribution som virtuella datorer i Azure. Anta att du har ett företag i Europa som krävs för att autentisera användare på en fjärrplats i Asien. Företaget har inte tidigare har distribuerat Windows Server Active Directory-domänkontrollanter i Asien på grund av kostnaden för att distribuera dem och begränsade kunskaper för att hantera servrar efter distributionen. Därmed behandlas autentiseringsbegäranden från Asien av domänkontrollanter i Europa med något sämre resultat. I det här fallet kan du distribuera en Domänkontrollant på en virtuell dator som du har angett måste köras i Azure-datacenter i Asien. Bifoga den DC till Azure-nätverk som är anslutna direkt till fjärrplatsen kommer att förbättra prestanda.

Azure passar också bra som ersättning för annars kostsamma katastrofåterställningsplatser (DR). Den relativt billig vara värd för ett litet antal domänkontrollanter och ett enda virtuellt nätverk i Azure representerar ett bra alternativ.

Slutligen kan du vill distribuera ett nätverksprogram i Azure, t.ex SharePoint, som kräver Windows Server Active Directory men har inga beroende på det lokala nätverket eller företagets Windows Server Active Directory. I det här fallet distribuera en isolerad skog i Azure för att uppfylla SharePoint-serverns krav är optimalt. Igen och stöds distribuera nätverksprogram som kräver anslutning till det lokala nätverket och företagets Active Directory också.

> [!NOTE]
> Eftersom den innehåller en nivå 3-anslutning, kan VPN-komponent som tillhandahåller anslutningar mellan Azure-nätverk och ett lokalt nätverk också aktivera medlemsservrar som kör lokalt för att kunna utnyttja domänkontrollanter som körs som virtuella Azure-datorer i Azure virtual network. Men om VPN är tillgänglig, kommunikation mellan lokala datorer och Azure-baserade domänkontrollanter kommer inte att fungera, vilket resulterar i autentisering och andra fel.  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Skiljer från mellan distribution av Windows Server Active Directory-domänkontrollanter på Azure Virtual Machines jämfört med lokalt
* För alla Windows Server Active Directory-distributionsscenariot som innehåller fler än en enda virtuell dator, är det nödvändigt att använda ett virtuellt Azure-nätverk för konsekvens för IP-adress. Observera att den här guiden förutsätter att domänkontrollanter körs på Azure-nätverk.
* Precis som med lokala domänkontrollanter, rekommenderas statiska IP-adresser. En statisk IP-adress kan endast konfigureras med hjälp av Azure PowerShell. Se [Statiska interna IP-adress för virtuella datorer](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) för mer information. Om du har övervakning system eller andra lösningar som kontrollerar för statisk IP-adresskonfiguration i gästoperativsystemet kan tilldela du samma statiska IP-adress till nätverkskortets egenskaper på den virtuella datorn. Men tänk på att nätverkskortet ignoreras om den virtuella datorn har tjänsten återställning eller är avstängd i portalen och har frigjorts dess adress. I så fall den statiska IP-adressen på gästen kommer att behöva återställas.
* Distribuera virtuella datorer på ett virtuellt nätverk inte innebär (eller kräver) ansluten till ett lokalt nätverk. det virtuella nätverket kan bara möjligheter. Du måste skapa ett virtuellt nätverk för privata kommunikation mellan Azure och ditt lokala nätverk. Du behöver distribuera en VPN-slutpunkten på det lokala nätverket. VPN-anslutningen har öppnats från Azure till det lokala nätverket. Mer information finns i [översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md) och [konfigurera en plats-till-plats-VPN i Azure Portal](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Ett alternativ för att [skapa en punkt-till-plats-VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) är tillgänglig för enskilda Windows-baserade datorer direkt anslutning till ett virtuellt Azure-nätverk.
> 
> 

* Oavsett om du skapar en virtuell nätverks- eller inte, Azure kostnader för utgående trafik, men inte ingång. Olika alternativ för Windows Server Active Directory-designen kan påverka hur mycket utgående trafik som genereras av en distribution. Till exempel begränsar distribuera en skrivskyddad domänkontrollant (RODC) utgående trafik eftersom den inte replikeras utgående. Men beslut att distribuera en RODC måste vägas mot behovet av att utföra skrivåtgärder mot domänkontrollanten och [kompatibilitet](https://technet.microsoft.com/library/cc755190) att program och tjänster på platsen har med skrivskyddade domänkontrollanter. Mer information om trafik avgifter finns [Azure priser på snabb](http://azure.microsoft.com/pricing/).
* När du har fullständig kontroll över vilken server diskstorlek för resurser som ska användas för virtuella datorer lokalt, till exempel RAM, och så vidare på Azure du måste välja en lista med förinställda server. För en Domänkontrollant krävs en datadisk förutom operativsystemdisken för att spara Windows Server Active Directory-databasen.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Kan du distribuera Windows Server AD FS på virtuella Azure-datorer?
Ja, du kan distribuera Windows Server AD FS på Azure virtual machines och [bästa praxis för AD FS-distribution](https://technet.microsoft.com/library/dn151324.aspx) lokalt gäller både AD FS-distribution på Azure. Men som bästa praxis belastningsutjämning och hög tillgänglighet kräver tekniker utöver vad AD FS erbjuder sig själv. De måste tillhandahållas av underliggande den infrastrukturen. Nu ska vi gå igenom några av de bästa praxis och se hur de kan uppnås genom att använda virtuella Azure-datorer och Azure-nätverk.

1. **Aldrig exponera säkerhet token-tjänsten (STS) servrar direkt till Internet.**
   
    Detta är viktigt eftersom STS utfärdar säkerhetstoken. STS-servrar, till exempel AD FS-servrarna behandlas med samma nivå av skydd som en domänkontrollant. Om en STS äventyras, har angripare möjlighet att ge åtkomst-token som potentiellt innehåller anspråk de önskar förlitande parters program och andra STS-servrar i betrodda organisationer.
2. **Distribuera Active Directory-domänkontrollanter för alla användardomäner i samma nätverk som AD FS-servrarna.**
   
    AD FS-servrar använda Active Directory Domain Services för att autentisera användare. Det rekommenderas att distribuera domänkontrollanter i samma nätverk som AD FS-servrarna. Detta ger affärskontinuitet om länken mellan Azure-nätverk och ditt lokala nätverk har brutits och aktiverar kortare svarstid och bättre prestanda för inloggningar.
3. **Distribuera flera AD FS-noder för hög tillgänglighet och belastningsutjämning i.**
   
    I de flesta fall är fel på ett program som gör att AD FS acceptabelt eftersom uppdragskritiska program som kräver säkerhetstoken är ofta. Därför och eftersom AD FS finns nu i kritiska åtkomst till verksamhetskritiska program, AD FS-tjänsten måste ha hög tillgänglighet via flera AD FS-proxyservrarna och AD FS-servrarna. För att uppnå distribution av begäranden, distribueras vanligtvis belastningsutjämnare framför både AD FS-proxyservrarna och AD FS-servrarna.
4. **Distribuera en eller flera Web Application Proxy-noder för internet-åtkomst.**
   
    När användare behöver åtkomst till program som skyddas av AD FS-tjänsten, måste AD FS-tjänsten vara tillgänglig från internet. Detta uppnås genom att distribuera tjänsten för Webbprogramproxy. Det rekommenderas att distribuera fler än en nod för hög tillgänglighet och belastningsutjämning.
5. **Begränsa åtkomsten från Web Application Proxy-noder till interna nätverksresurser.**
   
    Om du vill tillåta externa användare att komma åt AD FS från internet, måste du distribuera Webbprogramproxy noder (eller AD FS-Proxy i tidigare versioner av Windows Server). Web Application proxy noder exponeras direkt till Internet. De behöver inte vara ansluten till domänen och de bara behöver åtkomst till AD FS-servrar via TCP-portarna 443 och 80. Du rekommenderas att kommunikation till andra datorer (särskilt domänkontrollanter) är blockerad.
   
    Detta är vanligtvis uppnås lokalt med hjälp av en DMZ. Brandväggar använder en godkänd lista läge för att begränsa trafik från Perimeternätverket till det lokala nätverket (det vill säga endast tillåts trafik från de angivna IP-adresserna och över angivna portar, och alla andra trafik blockeras).

Följande diagram visar en traditionell lokal AD FS-distribution.

![Diagram över en traditionella lokala Active Directory Federation Services-distribution](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Men eftersom Azure inte innehåller inbyggda, kompletta brandväggen funktion, andra alternativ behöver som används för att begränsa trafik. I följande tabell visas varje alternativ och dess fördelar och nackdelar.

| Alternativ | Nytta | Nackdelen |
| --- | --- | --- |
| [ACL: er för Azure-nätverk](../virtual-network/virtual-networks-acl.md) |Mindre kostsamma och enklare inledande konfiguration |Ytterligare ACL nätverkskonfigurationen krävs om eventuella nya virtuella datorer läggs till i distributionen |
| [Barracuda NG brandväggen](https://www.barracuda.com/products/ngfirewall) |Godkända läge för åtgärden och kräver ingen nätverket ACL-konfiguration |Ökad kostnad och komplexitet för installationen |

Anvisningar för att distribuera AD FS är i det här fallet:

1. Skapa ett virtuellt nätverk med korsanslutningar, med hjälp av antingen en VPN-anslutning eller [ExpressRoute](http://azure.microsoft.com/services/expressroute/).
2. Distribuera domänkontrollanter på det virtuella nätverket. Det här steget är valfritt men rekommenderas.
3. Distribuera AD FS domänanslutna servrar i det virtuella nätverket.
4. Skapa en [interna belastningsutjämnade uppsättningen](http://azure.microsoft.com/blog/internal-load-balancing/) som innehåller AD FS-servrarna och använder en ny privat IP-adress i det virtuella nätverket (dynamisk IP-adress).
   
   1. Uppdatera DNS för att skapa FQDN att peka till privata (dynamisk) IP-adressen för den interna belastningsutjämnade uppsättningen.
5. Skapa en tjänst i molnet (eller ett separat virtuellt nätverk) för Web Application Proxy-noder.
6. Distribuera Web Application Proxy-noder i Molntjänsten eller virtuellt nätverk
   
   1. Skapa en extern belastningsutjämnad uppsättning som innehåller Web Application Proxy-noder.
   2. Uppdatera det externa DNS-namnet (FQDN) för att peka till cloud service offentlig IP-adress (virtuell IP-adress).
   3. Konfigurera AD FS-proxyservrar för att använda det FQDN som motsvarar den interna belastningsutjämnad uppsättningen för AD FS-servrarna.
   4. Uppdatera anspråksbaserad webbplatser för att använda externa FQDN för sina anspråksleverantör.
7. Begränsa åtkomsten mellan Web Application Proxy till någon dator i AD FS virtuella nätverk.

Om du vill begränsa trafik belastningsutjämnade uppsättningen för Azure interna belastningsutjämnaren måste konfigureras för endast trafik till TCP-portarna 80 och 443 och all trafik till interna dynamisk IP-adressen för den belastningsutjämnade uppsättningen har släppts.

![Diagram över ACL: er i AD FS nätverk med TCP 443 + 80 tillåts.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Trafik till AD FS-servrarna skulle vara tillåten endast av följande källor:

* Azure intern belastningsutjämnare.
* IP-adressen för en administratör på det lokala nätverket.

> [!WARNING]
> Designen förhindra Web Application Proxy-noder från att nå alla andra virtuella datorer i Azure-nätverket eller alla platser i det lokala nätverket. Som kan göras genom att konfigurera brandväggsregler i den lokala enheten för Express Route-anslutningar eller VPN-enhet för plats-till-plats VPN-anslutningar.
> 
> 

En nackdel till det här alternativet är behovet av att konfigurera nätverket ACL: er för flera enheter, inklusive interna belastningsutjämnare, AD FS-servrar och andra servrar som läggs till det virtuella nätverket. Om alla enheter läggs till i distributionen utan att konfigurera nätverket ACL: er för att begränsa trafik till den, kan hela distributionen vara i fara. Om IP-adresserna för Web Application Proxy-noder någonsin ändra nätverkets ACL: er måste återställas (vilket innebär att proxyservrarna ska konfigureras för att använda [Statiska IP-adresser som dynamiska](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![AD FS på Azure med nätverket ACL: er.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Ett annat alternativ är att använda den [Barracuda NG brandväggen](https://www.barracuda.com/products/ngfirewall) anordning för trafiken mellan AD FS-proxyservrarna och AD FS-servrarna. Det här alternativet uppfyller bästa praxis för säkerhet och hög tillgänglighet och kräver mindre administration efter den första installationen eftersom Barracuda NG brandväggsinstallation ger en godkänd läge för brandväggen administration och den kan installeras direkt på Azure-nätverk. Som eliminerar behovet av att konfigurera nätverket ACL: er varje gång en ny server läggs till i distributionen. Men det här alternativet ökar första distributionen komplexiteten och kostnaden.

I det här fallet distribueras två virtuella nätverk i stället för en. Vi kallar dem VNet1 och VNet2. VNet1 innehåller proxyservrarna och VNet2 innehåller STSs och nätverksanslutning till företagets nätverk. VNet1 är därför fysiskt (men praktiskt taget) isolerade från VNet2 och i sin tur, från företagets nätverk. VNet1 sedan är ansluten till VNet2 med en särskild tunneling teknik kända som Transport oberoende nätverk arkitektur (TINA). TINA tunneln är kopplad till var och en av de virtuella nätverk som använder en Barracuda NG brandvägg – en Barracuda på var och en av de virtuella nätverken.  För hög tillgänglighet rekommenderar vi att du distribuerar två Barracuda på varje virtuellt nätverk; en aktiv, den andra passivt. De erbjuder mycket omfattande firewalling funktioner som gör det möjligt för oss att efterlikna driften av en traditionella lokala DMZ i Azure.

![AD FS på Azure med brandväggen.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Mer information finns i [AD FS: utöka en frontend-anspråksmedvetna lokalt program till Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Ett alternativ till AD FS-distribution om målet är Office 365 SSO enbart
Det finns ett annat alternativ till att distribuera AD FS helt och hållet om målet är bara för att aktivera inloggning för Office 365. I så fall kan du bara distribuera DirSync med lösenord synkronisering lokalt och uppnå samma slutresultatet med minimal distribution komplexitet eftersom den här metoden inte kräver AD FS eller Azure.

I följande tabell jämförs hur processer för inloggning fungerar med och utan att distribuera AD FS.

| Office 365 enkel inloggning med AD FS och DirSync | Office 365 samma inloggning med DirSync + Lösenordssynkronisering |
| --- | --- |
| 1. Användaren loggar in på ett företagsnätverk och autentiseras till Windows Server Active Directory. |1. Användaren loggar in på ett företagsnätverk och autentiseras till Windows Server Active Directory. |
| 2. Användaren försöker få åtkomst till Office 365 (jag @contoso.com). |2. Användaren försöker få åtkomst till Office 365 (jag @contoso.com). |
| 3. Office 365 omdirigeras användaren till Azure AD. |3. Office 365 omdirigeras användaren till Azure AD. |
| 4. Eftersom Azure AD kan autentisera användaren och förstår att det finns ett förtroende med AD FS på lokalt, omdirigeras användaren till AD FS. |4. Azure AD kan inte acceptera Kerberos-biljetter direkt och det finns ingen betrodd relation så begär att användaren anger autentiseringsuppgifter. |
| 5. Användaren skickar en Kerberos-biljett till AD FS STS. |5. Användaren anger samma lokala lösenord och Azure AD verifierar dem mot användarnamn och lösenord som synkroniserades av DirSync. |
| 6. AD FS omvandlar Kerberos-biljetten för nödvändiga token format/anspråk och dirigerar användaren till Azure AD. |6. Azure AD omdirigeras användaren till Office 365. |
| 7. Användaren autentiseras mot Azure AD (omvandling av en annan sker). |7. Användaren kan logga in på Office 365 och OWA med Azure AD-token. |
| 8. Azure AD omdirigeras användaren till Office 365. | |
| 9. Användaren är inloggad på Office 365 tyst. | |

I Office 365 med DirSync med scenario för Lösenordssynkronisering (ingen AD FS) ersättas enkel inloggning med ”samma inloggning” där ”samma” innebär bara att användare måste ange sina autentiseringsuppgifter för samma lokala igen när du använder Office 365. Observera att dessa data kan registreras av användarens webbläsare för att minska efterföljande frågor.

### <a name="additional-food-for-thought"></a>Ytterligare mat for idé
* Om du distribuerar en AD FS-proxy på en virtuell Azure-dator, krävs anslutning till AD FS-servrarna. Om de finns lokalt, bör du använda plats-till-plats-VPN-anslutning som tillhandahålls av det virtuella nätverket så att Web Application Proxy-noder kommunicerar med sina AD FS-servrarna.
* Om du distribuerar AD FS-servern på en virtuell dator i Azure krävs anslutning till Windows Server Active Directory-domänkontrollanter, Attributarkiv och databaserna konfiguration och kan också kräva en ExpressRoute eller en plats-till-plats VPN-anslutning mellan Azure-nätverket och det lokala nätverket.
* Avgifter som tillämpas på all trafik från virtuella Azure-datorer (utgående trafik). Om kostnaden är drivande faktor, rekommenderas du att distribuera Webbprogramproxy noderna på Azure, lämnar den AD FS-servrar lokalt. Om AD FS-servrar distribueras på virtuella Azure-datorer samt påförs ytterligare kostnader för att autentisera lokala användare. Utgående trafik innebär en kostnad oavsett om huruvida den går igenom ExpressRoute eller plats-till-plats VPN-anslutningen.
* Om du väljer att använda Azures inbyggda server belastningsutjämning för hög tillgänglighet i AD FS-servrarna, Observera att nätverksbelastning ger avsökningar som används för att fastställa hälsotillståndet för virtuella datorer i Molntjänsten. När det gäller Azure-datorer (i stället för webb- eller arbetarroll roller), en anpassad avsökning användas eftersom den agent som svarar på standard-avsökningar inte finns på virtuella Azure-datorer. Du kan använda en anpassad TCP-avsökning för enkelhetens skull – detta endast kräver att en TCP-anslutning (ett TCP SYN segment skickas och svarade med ett segment TCP SYN ACK) kunna upprättas för att fastställa hälsotillståndet för den virtuella datorn. Du kan konfigurera anpassade avsökningen om du vill använda en TCP-port som lyssnar aktivt dina virtuella datorer.

> [!NOTE]
> Datorer som behöver för att exponera samma uppsättning portar direkt till Internet (till exempel port 80 och 443) kan inte dela samma tjänst i molnet. Det är därför bör du skapa ett dedikerat moln service för Windows Server AD FS-servrar för att undvika risken överlappar mellan krav på nätverksportar för ett program och Windows Server AD FS.
> 
> 

## <a name="deployment-scenarios"></a>Distributionsscenarier
I följande avsnitt beskrivs tar över som standard distributionsscenarier för att uppmärksamma viktiga överväganden som måste vidtas i beräkningen. Varje scenario innehåller länkar till mer information om beslut och faktorer att tänka på.

1. [AD DS: Distribuera ett AD DS-medvetna program med inga krav på företagets nätverksanslutning](#BKMK_CloudOnly)
   
    Till exempel distribueras en Internet-riktade SharePoint-tjänsten på en virtuell Azure-dator. Programmet har inga beroenden på företagets nätverksresurser. Programmet kräver Windows Server AD DS men kräver inte företagets Windows Server AD DS.
2. [AD FS: Utöka en frontend-anspråksmedvetna lokalt program till Internet](#BKMK_CloudOnlyFed)
   
    Till exempel behöver ett anspråksmedvetet program som har distribuerats lokalt och används av företagsanvändare bli tillgänglig från Internet. Programmet behöver komma åt direkt via Internet efter affärspartner som använder sina egna företagets identiteter och efter befintliga företagsanvändare.
3. [AD DS: Distribuera en Windows Server AD DS-medvetna program som kräver anslutning till företagets nätverk](#BKMK_HybridExt)
   
    Till exempel distribueras ett LDAP-medvetna program som stöder Windows-integrerad autentisering och använder Windows Server AD DS som en lagringsplats för konfiguration och användarprofil data på en virtuell Azure-dator. Det är önskvärt för programmet att utnyttja befintliga företagets Windows Server AD DS och tillhandahålla enkel inloggning. Programmet är inte medveten om anspråket.

### <a name="BKMK_CloudOnly"></a>1. AD DS: Distribuera ett AD DS-medvetna program med inga krav på företagets nätverksanslutning
![Endast molnbaserad AD DS-distributionen](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**bild 1**

#### <a name="description"></a>Beskrivning
SharePoint har distribuerats på en virtuell Azure-dator och programmet har inga beroenden på företagets nätverksresurser. Programmet kräver Windows Server AD DS men *inte* kräver företagets Windows Server AD DS. Inga Kerberos eller federerade förtroenden krävs eftersom användare kan själva etablerade via program till Windows Server AD DS-domän som även finns i molnet på virtuella Azure-datorer.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Överväganden för scenariot och hur teknikområden gäller för scenariot
* [Nätverkstopologi](#BKMK_NetworkTopology): skapa en Azure-nätverk utan korsanslutningar (även kallat plats-till-plats-anslutning).
* [DC distributionskonfiguration](#BKMK_DeploymentConfig): distribuera en ny domänkontrollant i en ny, domän, Windows Server Active Directory-skog. Detta bör distribueras tillsammans med Windows DNS-servern.
* [Windows Server Active Directory-platstopologi](#BKMK_ADSiteTopology): använda Windows Server Active Directory standardwebbplatsen (alla datorer är i standard-First-Site-Name).
* [IP-adresser och DNS-](#BKMK_IPAddressDNS):
  
  * Ange en statisk IP-adress för domänkontrollanten med hjälp av cmdleten Set-AzureStaticVNetIP Azure PowerShell.
  * Installera och konfigurera Windows Server DNS på domänkontrollers på Azure.
  * Konfigurera egenskaper för virtuellt nätverk med namn och IP-adressen för den virtuella datorn som är värd för rollerna Domänkontrollant och DNS-server.
* [Global katalog](#BKMK_GC): den första domänkontrollanten i skogen måste vara en global katalogserver. Ytterligare domänkontrollanter bör också konfigureras som global katalog eftersom i en enda domän i skogen den globala katalogen inte kräver några ytterligare arbete från domänkontrollanten.
* [Placering av Windows Server AD DS-databasen och SYSVOL](#BKMK_PlaceDB): lägger till en datadisk till domänkontrollanter som körs som virtuella Azure-datorer för att kunna lagra Windows Server Active Directory-databasen, loggfilerna och SYSVOL.
* [Säkerhetskopiera och återställa](#BKMK_BUR): fastställa där du vill lagra säkerhetskopiering av systemtillståndet. Om det behövs kan du lägga till en annan datadisk för DC-VM för lagring av säkerhetskopior.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: utöka en frontend-anspråksmedvetna lokalt program till Internet
![Federation med korsanslutningar](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**bild 2**

#### <a name="description"></a>Beskrivning
Ett anspråksmedvetet program som har distribuerats lokalt och används av företagsanvändare behöver för att bli tillgänglig direkt från Internet. Programmet fungerar som en webbtjänst klientdel till en SQL-databas som den lagrar data. SQL-servrar som används av programmet finns också i företagsnätverket. Två Windows Server AD FS STSs och en belastningsutjämnare varit distribuerade lokalt att ge åtkomst till företagets användare. Programmet nu behöver dessutom komma åt direkt via Internet efter affärspartner som använder sina egna företagets identiteter och efter befintliga företagsanvändare.

I syfte att förenkla och uppfyller behoven för distribution och konfiguration av det nya kravet, valt två ytterligare web frontends och två Windows Server AD FS-proxyservrar installeras på virtuella Azure-datorer. Alla fyra virtuella datorer kommer att exponeras direkt till Internet och kommer att tillhandahållas anslutning till det lokala nätverket med Azure Virtual Network plats-till-plats VPN-funktionen.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Överväganden för scenariot och hur teknikområden gäller för scenariot
* [Nätverkstopologi](#BKMK_NetworkTopology): skapa en Azure-nätverk och [konfigurera korsanslutningar](../vpn-gateway/vpn-gateway-site-to-site-create.md).
  
  > [!NOTE]
  > Se till att den URL som definierats i certifikatmallen och de resulterande certifikaten kan nås av Windows Server AD FS-instanser körs på Azure för varje Windows Server AD FS-certifikat. Det här kan kräva anslutning till delar av PKI-infrastrukturen. För exempel om den CRL-slutpunkt är LDAP-baserade och finns endast lokalt, sedan mellan lokala anslutningen kommer att krävas. Om detta inte är önskvärt, kan det vara nödvändigt att använda certifikat som utfärdats av en Certifikatutfärdare vars CRL är tillgänglig via Internet.
  > 
  > 
* [Cloud services-konfiguration](#BKMK_CloudSvcConfig): Kontrollera att du har två molntjänster i ordning tillhandahåller två belastningsutjämnade virtuella IP-adresser. Första Molntjänsten virtuella IP-adressen dirigeras till två Windows Server AD FS-proxy VM: ar på portarna 80 och 443. Windows Server AD FS-proxy VMs konfigureras för att peka mot IP-adressen för den lokala belastningsutjämnaren som rör Windows Server AD FS STSs. Andra Molntjänsten virtuella IP-adressen ska dirigeras till de två virtuella datorerna kör webb-klientdel igen på portarna 80 och 443. Konfigurera en anpassad avsökningsåtgärd för att säkerställa att belastningsutjämnaren-endast dirigerar trafik till fungerar Windows Server AD FS-proxy och web klientdel virtuella datorer.
* [Konfigurationen av federationsservern](#BKMK_FedSrvConfig): Konfigurera Windows Server AD FS som en federationsserver (STS) för att generera säkerhetstoken för Windows Server Active Directory-skog som skapats i molnet. Konfigurera förtroenden för förlitande part med olika program som du vill generera tokens för att ange federation anspråk providern betrodda relationer med de olika partner som du vill acceptera identiteter från.
  
    I de flesta fall distribueras Windows Server AD FS-proxyservrar i en Internet-riktade kapacitet av säkerhetsskäl samtidigt som deras motsvarigheter i Windows Server AD FS federationstjänsten förbli isolerad från direkt Internetanslutning. Oavsett distributionsscenariot, måste du konfigurera din molntjänst med en virtuell IP-adress som ger en offentligt exponerade IP-adress och port som kan belastningsutjämna mellan din två Windows Server AD FS STS instanser eller en proxy-instanser.
* [Windows Server AD FS-konfiguration för hög tillgänglighet](#BKMK_ADFSHighAvail): rekommenderas att distribuera en Windows Server AD FS-servergrupp med minst två servrar för redundans och belastningsutjämning. Du kan överväga att använda Windows interna databas (WID) för Windows Server AD FS-konfigurationsdata och använda intern belastningsutjämning möjligheterna för Azure för att fördela inkommande begäranden mellan servrar i servergruppen.

Mer information finns i [Distributionsguide](https://technet.microsoft.com/library/cc753963).

### <a name="BKMK_HybridExt"></a>3. AD DS: Distribuera en Windows Server AD DS-medvetna program som kräver anslutning till företagets nätverk
![Anslutningar mellan lokala AD DS-distributionen](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**bild 3**

#### <a name="description"></a>Beskrivning
LDAP-medvetna program distribueras på en virtuell Azure-dator. Den stöder Windows-integrerad autentisering och använder Windows Server AD DS som en lagringsplats för konfiguration och användaren profildata. Målet är för programmet att utnyttja befintliga företagets Windows Server AD DS och tillhandahålla enkel inloggning. Programmet är inte medveten om anspråket. Användarna måste även ha åtkomst till programmet direkt från Internet. För att optimera prestanda och kostnad, beslutas att två ytterligare domänkontrollanter som ingår i företagsdomänen distribueras tillsammans med programmet på Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Överväganden för scenariot och hur teknikområden gäller för scenariot
* [Nätverkstopologi](#BKMK_NetworkTopology): skapa en Azure-nätverk med [mellan lokal anslutning](../vpn-gateway/vpn-gateway-site-to-site-create.md).
* [Installationsmetoden](#BKMK_InstallMethod): distribuera domänkontrollanter för repliken från företagets Windows Server Active Directory-domän. För en replikdomänkontrollant kan du installera Windows Server AD DS på den virtuella datorn och du kan också använda funktionen installera från Media (IFM) för att minska mängden data som måste replikeras till den nya domänkontrollanten under installationen. En självstudiekurs finns [installerar en replik Active Directory-domänkontrollant på Azure](active-directory-install-replica-active-directory-domain-controller.md). Även om du använder IFM, kan det vara mer effektivt att skapa den virtuella Domänkontrollant lokalt och flytta hela virtuell hårddisk (VHD) till molnet i stället för att replikera Windows Server AD DS under installationen. För säkerhet rekommenderas det att du tar bort den virtuella Hårddisken från det lokala nätverket när det har kopierats till Azure.
* [Windows Server Active Directory-platstopologi](#BKMK_ADSiteTopology): skapa en ny Azure-webbplats i Active Directory-platser och tjänster. Skapa ett Windows Server Active Directory-undernätsobjekt för att representera virtuella Azure-nätverket och lägga till undernätet på webbplatsen. Skapa en ny platslänk som innehåller den nya Azure platsen och den plats där det virtuella Azure-nätverket VPN-slutpunkten finns för att styra och optimera Windows Server Active Directory-trafik till och från Azure.
* [IP-adresser och DNS-](#BKMK_IPAddressDNS):
  
  * Ange en statisk IP-adress för domänkontrollanten med hjälp av cmdleten Set-AzureStaticVNetIP Azure PowerShell.
  * Installera och konfigurera Windows Server DNS på domänkontrollers på Azure.
  * Konfigurera egenskaper för virtuellt nätverk med namn och IP-adressen för den virtuella datorn som är värd för rollerna Domänkontrollant och DNS-server.
* [Fördelade domänkontrollanter](#BKMK_DistributedDCs): konfigurera ytterligare virtuella nätverk efter behov. Om din Active Directory-platstopologi kräver domänkontrollanter i geografiska områden som motsvarar olika Azure-regioner, än du vill skapa Active Directory-platser i enlighet med detta.
* [Skrivskyddade domänkontrollanter](#BKMK_RODC): du kan distribuera en RODC i Azure site, beroende på dina krav för att utföra skriva åtgärder mot domänkontrollanten och kompatibilitet för program och tjänster på plats med skrivskyddade domänkontrollanter. Mer information om programkompatibilitet finns i [guide för skrivskyddade domänkontrollanter kompatibilitet](https://technet.microsoft.com/library/cc755190).
* [Global katalog](#BKMK_GC): GC-generationer krävs vid inloggning tjänstbegäranden i flera domäner skogar. Om du inte distribuera en global katalog i Azure site påförs kostnader för utgående trafik som autentiseringsbegäranden orsaka frågor GC-generationer på andra platser. För att minimera trafiken, kan du aktivera cachelagring av gruppmedlemskap för Azure platsen i Active Directory-platser och tjänster.
  
    Om du distribuerar en GC konfigurera platslänkar och länkar platskostnaderna så att den globala katalogen i Azure site inte rekommenderas som en källdomänkontrollant av andra GC-generationer som du behöver för att replikera samma partiella domänpartitioner.
* [Placering av Windows Server AD DS-databasen och SYSVOL](#BKMK_PlaceDB): lägger till en datadisk till domänkontrollanter som körs på virtuella Azure-datorer för att kunna lagra Windows Server Active Directory-databasen, loggfilerna och SYSVOL.
* [Säkerhetskopiera och återställa](#BKMK_BUR): fastställa där du vill lagra säkerhetskopiering av systemtillståndet. Om det behövs kan du lägga till en annan datadisk för DC-VM för lagring av säkerhetskopior.

## <a name="deployment-decisions-and-factors"></a>Distributionsbeslut och faktorer
Den här tabellen sammanfattas områden för Windows Server Active Directory-teknik som påverkas i föregående scenarier och motsvarande beslut att överväga med länkar till mer information nedan. Vissa teknikområden kanske inte gäller för varje scenario för distribution och vissa teknikområden kan vara mer kritiska till ett scenario för distribution än andra teknikområden.

Till exempel om du distribuerar en replikdomänkontrollant på ett virtuellt nätverk och din skog har bara en enda domän, blir sedan välja att distribuera en global katalogserver i så fall inte viktigt att distributionsscenariot eftersom det inte att skapa några ytterligare replikeringskrav. Å andra sidan, om skogen har flera domäner, kan sedan beslut att distribuera en global katalog på ett virtuellt nätverk påverka tillgänglig bandbredd, prestanda, autentisering, katalogen och så vidare.

| Windows Server Active Directory teknikområde | Beslut | Faktorer |
| --- | --- | --- |
| [Nätverkstopologi](#BKMK_NetworkTopology) |Skapar du ett virtuellt nätverk? |<li>Krav för att komma åt resurser i Corp</li> <li>Autentisering</li> <li>Kontohantering</li> |
| [DC distributionskonfiguration](#BKMK_DeploymentConfig) |<li>Distribuera en separat skog utan några förtroenden?</li> <li>Distribuera en ny skog med federation?</li> <li>Distribuera en ny skog med Windows Server Active Directory skogsförtroende eller Kerberos?</li> <li>Utöka Corp-skogen genom att distribuera en replikdomänkontrollant?</li> <li>Utöka Corp-skogen genom att distribuera en ny underordnad domän eller ett domänträd?</li> |<li>Säkerhet</li> <li>Efterlevnad</li> <li>Kostnad</li> <li>Återhämtning och feltolerans</li> <li>Programkompatibilitet</li> |
| [Windows Server Active Directory-platstopologi](#BKMK_ADSiteTopology) |Hur konfigurerar du undernät, platser och platslänkar med Azure Virtual Network för att optimera trafik och minska? |<li>Definitioner av undernät och plats</li> <li>Plats länkegenskaper och ändringsmeddelande</li> <li>Replikering komprimering</li> |
| [IP-adresser och DNS](#BKMK_IPAddressDNS) |Så här konfigurerar du IP-adresser och namnmatchning? |<li>Använd Använd Set-AzureStaticVNetIP-cmdlet för att tilldela en statisk IP-adress</li> <li>Installera Windows Server DNS-servern och konfigurera egenskaper för virtuellt nätverk med namn och IP-adressen för den virtuella datorn som är värd för domänkontrollanten och DNS-serverroller</li> |
| [Fördelade domänkontrollanter](#BKMK_DistributedDCs) |Hur ska replikeras till domänkontrollanter på separata virtuella nätverk? |Om din Active Directory-platstopologi kräver domänkontrollanter i geografiska områden som motsvarar olika Azure-regioner, än du vill skapa Active Directory-platser i enlighet med detta. [Konfigurera virtuellt nätverk till det virtuella nätverket anslutningen](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) för replikering mellan domänkontrollanter på separata virtuella nätverk. |
| [Skrivskyddade domänkontrollanter](#BKMK_RODC) |Använda skrivskyddade eller skrivbara domänkontrollanter? |<li>Filtrera HBI/PII attribut</li> <li>Filtrera hemligheter</li> <li>Gränsen för utgående trafik</li> |
| [Global katalog](#BKMK_GC) |Installera global katalog? |<li>För enkel domänskog, se alla domänkontrollanter global katalog</li> <li>För skog krävs GC-generationer för autentisering</li> |
| [Installationsmetod](#BKMK_InstallMethod) |Hur du installerar domänkontrollanten i Azure? |Antingen: <li>Installera AD DS med Windows PowerShell eller Dcpromo</li> <li>Flytta virtuell Hårddisk på en lokal virtuell Domänkontrollant</li> |
| [Placering av Windows Server AD DS-databasen och SYSVOL](#BKMK_PlaceDB) |Var du vill lagra Windows Server AD DS-databasen, loggar och SYSVOL? |Ändra Dcpromo.exe standardvärden. Dessa viktiga Active Directory-filer *måste* placeras på Azure Datadiskar i stället för operativsystemet diskar som implementerar skrivcache. |
| [Säkerhetskopiering och återställning](#BKMK_BUR) |Hur vill skydda och återställa data? |Säkerhetskopiera system tillstånd |
| [Konfigurationen av federationsservern](#BKMK_FedSrvConfig) |<li>Distribuera en ny skog med federation i molnet?</li> <li>Distribuera AD FS lokala och exponera en proxy i molnet?</li> |<li>Säkerhet</li> <li>Efterlevnad</li> <li>Kostnad</li> <li>Åtkomst till program för affärspartners</li> |
| [Cloud services-konfiguration](#BKMK_CloudSvcConfig) |En tjänst i molnet distribueras implicit första gången du skapar en virtuell dator. Behöver du distribuera ytterligare molntjänster? |<li>En virtuell dator eller på virtuella datorer krävs för direkt exponering till Internet?</li> <li> Kräver tjänsten belastningsutjämning?</li> |
| [Federation server-krav för offentliga och privata IP-adresser (dynamisk IP jämfört med virtuella IP-adresser)](#BKMK_FedReqVIPDIP) |<li>Behöver Windows Server AD FS-instansen nås direkt från Internet?</li> <li>Kräver program som distribueras i molnet sin egen mot Internet IP-adress och port?</li> |Skapa en molntjänst för varje virtuell IP-adress som krävs av din distribution |
| [Windows Server AD FS-konfiguration för hög tillgänglighet](#BKMK_ADFSHighAvail) |<li>Hur många noder i min Windows Server AD FS-serverkluster?</li> <li>Hur många noder du distribuerar i Windows Server AD FS-proxy servergruppen?</li> |Återhämtning och feltolerans |

### <a name="BKMK_NetworkTopology"></a>Nätverkstopologi
För att uppfylla konsekvent för IP-adress och DNS-krav för Windows Server AD DS, det är nödvändigt att först skapa en [virtuella Azure-nätverket](../virtual-network/virtual-networks-overview.md) och kopplar den till dina virtuella datorer. När skapandet måste du bestämma om eventuellt utökar anslutning till ditt lokala företagsnätverk som ansluter transparent virtuella Azure-datorer till lokala datorer – detta uppnås med hjälp av traditionella VPN-teknik och kräver att en VPN-slutpunkten exponeras i utkanten av företagets nätverk. Det vill säga initieras VPN från Azure till företagets nätverk, inte tvärtom.

Observera att ytterligare kostnader kan tillkomma när du utökar ett virtuellt nätverk till ditt lokala nätverk utöver standard avgifterna som gäller för varje virtuell dator. Det finns mer specifikt avgifter för CPU-tid för Azure Virtual Network-gateway och den utgående trafik som genereras av varje virtuell dator som kommunicerar med lokala datorer via VPN. Mer information om nätverket trafik avgifter finns [Azure priser på snabb](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>DC distributionskonfiguration
Hur du konfigurerar domänkontrollanten beror på kraven för tjänsten som du vill köra på Azure. Du kan till exempel distribuera en ny skog isoleras från din egen företagsskogen för att testa en--konceptbevis, ett nytt program eller några andra kortvarigt projekt som kräver katalogtjänster men inte specifika åtkomst till interna företagsresurser.

Som en fördel en isolerad skog Domänkontrollant inte replikerar med lokala domänkontrollanter, vilket resulterar i mindre utgående nätverkstrafik som genereras av systemet, direkt minskar kostnaderna. Mer information om nätverket trafik avgifter finns [Azure priser på snabb](http://azure.microsoft.com/pricing/).

Ett annat exempel anta att du har sekretesskrav för en tjänst, men tjänsten är beroende av åtkomst till din interna Windows Server Active Directory. Om du är värd för data för tjänsten i molnet, kan du distribuera en ny underordnad domän för den interna skogen på Azure. I det här fallet kan du distribuera en Domänkontrollant för den nya underordnade domänen (utan den globala katalogen för att hjälpa adress sekretessen). Det här scenariot, tillsammans med en replik DC-distribution kräver ett virtuellt nätverk för anslutning till din lokala domänkontrollanter.

Om du skapar en ny skog måste du välja att använda [Active Directory-förtroenden](https://technet.microsoft.com/library/cc771397) eller [federationsförtroenden](https://technet.microsoft.com/library/dd807036). Balansera kraven enligt kompatibilitet, säkerhet, kompatibilitet, kostnad och återhämtning. Till exempel för att dra nytta av [Selektiv autentisering](https://technet.microsoft.com/library/cc755844) kanske du vill distribuera en ny skog i Azure och skapa ett Windows Server Active Directory-förtroende mellan den lokala skogen och moln-skogen. Om programmet är medveten om anspråket, men kan du distribuera federationsförtroenden i stället för Active Directory skogsförtroenden. En annan faktor blir kostnaden för att replikera mer data genom att utöka din lokala Windows Server Active Directory till molnet eller skapa fler utgående trafik på grund av autentisering och frågebelastningen.

Krav för tillgänglighet och feltolerans även påverka ditt val. Till exempel om länken avbryts lita programmen med antingen en Kerberos-förtroende eller ett federationsförtroende på alla sannolikt helt bryts om du har distribuerat tillräcklig infrastruktur på Azure. Alternativa konfigurationer, till exempel domänkontrollanter för repliken (skrivbar eller RODC: ar) öka sannolikheten för att kunna tolerera länken avbrott.

### <a name="BKMK_ADSiteTopology"></a>Windows Server Active Directory-platstopologi
Du måste definiera korrekt platser och platslänkar för att optimera trafik och minimerar kostnaden. Påverka topologin för replikering mellan domänkontrollanter och autentisering trafikflödet platser, platslänkar och undernät. Överväg följande avgifterna för trafiken och distribuera och konfigurera domänkontrollanter enligt kraven för din distributionsscenario:

* Det finns en avgift per timme på själva gatewayen:
  
  * Det kan startas och stoppas efter önskemål
  * Om Stoppad är virtuella Azure-datorer isolerade från företagets nätverk
* Inkommande trafik är ledig
* Utgående trafik debiteras enligt [Azure priser på snabb](http://azure.microsoft.com/pricing/). Du kan optimera webbplatsen länkegenskaper mellan lokala platser och moln-platser på följande sätt:
  
  * Om du använder flera virtuella nätverk, konfigurera platslänkar och deras kostnader på lämpligt sätt för att förhindra att Windows Server AD DS prioritera Azure plats över något som kan ge samma servicenivå utan kostnad. Du kan också inaktivera bryggan alla platsen länk (BASL) alternativet (som är aktiverad som standard). Detta säkerställer att endast direkt anslutna platser replikerar med varandra. Domänkontrollanter i transitivt anslutna platser inte längre kunna replikera med varandra, men måste replikera via en gemensam plats eller platser. Om mellanliggande platser inaktiveras av någon anledning replikering mellan domänkontrollanter i transitivt anslutna platser kommer inte att ske även om anslutningen mellan platserna är tillgänglig. Slutligen där delar av transitiv replikering beteende vara önskvärt, Skapa platslänksbryggor som innehåller rätt platslänkar och platser, till exempel lokalt, företagets nätverksplatser.
  * [Konfigurera platslänkkostnader](https://technet.microsoft.com/library/cc794882) på lämpligt sätt att undvika oväntade trafik. Till exempel om **försök nästa närmaste platsen** inställningen är aktiverad, kontrollera att de virtuella nätverksplatserna inte nästa närmast genom att öka kostnaden för för platslänk objektet som ansluter Azure platsen tillbaka till företagets nätverk.
  * Konfigurera platslänk [intervall](https://technet.microsoft.com/library/cc794878) och [scheman](https://technet.microsoft.com/library/cc816906) enligt konsekvenskontroll krav och frekvensen för objektet ändras. Justera replikeringsschema med latens tolerans. Domänkontrollanter replikeras endast det senaste tillståndet för ett värde så att minska replikeringsintervall kan du spara kostnader om det finns ett tillräckligt objekt ändra hastighet.
* Om minimera kostnaderna är en prioritet, se till att replikeringen är schemalagd och meddelanden om ändringar har inte aktiverats. Det här är standardkonfigurationen vid replikering mellan platser. Detta är inte viktigt om du distribuerar en RODC på ett virtuellt nätverk eftersom den skrivskyddade domänkontrollanten inte replikeras ändringarna utgående. Men om du distribuerar en skrivbar Domänkontrollant, bör du kontrollera platslänken inte har konfigurerats för att replikera uppdateringar med onödiga frekvens. Om du distribuerar en global katalogserver (GC) måste du kontrollera att varje plats som innehåller en GC replikerar domänpartitioner från en källdomänkontrollant på en plats som är kopplad till en plats-länk eller platslänkar som har en lägre kostnad än GC i Azure site.
* Det är möjligt att minska fortfarande ytterligare nätverkstrafik som genereras av replikering mellan platser genom att ändra komprimeringsalgoritm replikering. Komprimeringsalgoritmen styrs av REG_DWORD registret posten HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator-komprimeringsalgoritmen. Standardvärdet är 3, vilket motsvarar algoritmen Xpress komprimera. Du kan ändra värdet till 2, som ändrar algoritmen till MSZip. I de flesta fall är detta ökar komprimeringen, men detta sker på bekostnad av CPU-användning. Mer information finns i [hur Active Directory-replikeringstopologi fungerar](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>IP-adresser och DNS
Virtuella Azure-datorer tilldelas ”DHCP-hyrd adresser” som standard. Kraven för Windows Server AD DS uppfylls eftersom virtuella Azure-nätverket dynamiska adresser kvarstår med en virtuell dator för livslängden för den virtuella datorn.

Därför när du använder en dynamisk adress på Azure fungerar du med en statisk IP-adress eftersom det är dirigerbart för perioden för lånet och lånet är lika med livslängden för Molntjänsten.

Dynamisk adress frigörs om den virtuella datorn har stängts av. Du kan förhindra IP-adressen från flyttats [använda Set-AzureStaticVNetIP för att tilldela en statisk IP-adress](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

För namnmatchning, distribuera din egen (eller använda din befintliga) DNS-infrastruktur; Azure-tillhandahållna DNS uppfyller inte avancerade namnmatchningen för Windows Server AD DS. Till exempel den inte stöder dynamiska SRV-poster och så vidare. Det är ett kritiskt konfigurationsobjekt för domänkontrollanter och domänanslutna klienter för namnmatchning. Domänkontrollanter måste kunna registrera resursposter och lösa andra DC-resursposter.
För feltolerans och prestanda felorsaker är det optimala för att installera Windows Server DNS-tjänsten på domänkontrollanter som körs på Azure. Konfigurera egenskaper för virtuella Azure-nätverket med namn och IP-adress för DNS-servern. När andra virtuella datorer på det virtuella nätverket startar konfigureras deras DNS-matchare klientinställningar med DNS-server som en del av dynamisk IP-adressallokering.

> [!NOTE]
> Du kan inte ansluta lokala datorer till en Windows Server AD DS Active Directory-domän som ligger på Azure direkt via Internet. Portkraven för Active Directory och åtgärden domänanslutning återge den opraktiska att direkt exponera de nödvändiga portarna och i praktiken en helt DC till Internet.
> 
> 

Virtuella datorer att registrera sina DNS-namn automatiskt vid start eller när det finns en namnändring.

Läs mer om det här exemplet och ett annat exempel som visar hur du konfigurerar den första virtuella datorn och installera AD DS på den [installera en ny Active Directory-skog i Microsoft Azure](active-directory-new-forest-virtual-machine.md). Mer information om hur du använder Windows PowerShell finns [installera Azure PowerShell](/powershell/azureps-cmdlets-docs) och [Azure Management-Cmdlets](/powershell/module/azurerm.compute/#virtual_machines).

### <a name="BKMK_DistributedDCs"></a>Fördelade domänkontrollanter
Azure ger fördelar när värd för flera domänkontrollanter i olika virtuella nätverk:

* Flera platser feltolerans
* Fysisk närhet till avdelningskontor (kortare svarstid)

Information om hur du konfigurerar direkt kommunikation mellan virtuella nätverk finns i [Konfigurera virtuella nätverk för virtuell nätverksanslutning](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Skrivskyddade domänkontrollanter
Du måste välja om du vill distribuera skrivskyddade eller skrivbara domänkontrollanter. Du kanske har att distribuera skrivskyddade domänkontrollanter eftersom du inte har fysisk kontroll över dem., men RODC: er är utformade för att distribueras på platser där deras fysisk säkerhet är i fara, till exempel avdelningskontor.

Fysiska säkerhetsrisk på ett filialkontor finns inte i Azure, men skrivskyddade domänkontrollanter kan fortfarande visar sig vara mer kostnadseffektivt eftersom de funktioner som de tillhandahåller är väl lämpade för dessa miljöer än för mycket olika skäl. Skrivskyddade domänkontrollanter har ingen utgående replikering och kunna fylla selektivt hemligheter (lösenord). Bristen på dessa hemligheter kan kräva på begäran utgående trafik för att validera dem som en användare eller datorn autentiserar på Nackdelen med. Men hemligheter kan selektivt förinställd och cachelagras.

Skrivskyddade domänkontrollanter ger ytterligare en fördel i och runt HBI-och personligt identifierbar information eftersom du kan lägga till attribut som innehåller känsliga data till RODC filtrerad attributuppsättning (FAS). FAS motsvarar anpassningsbara attribut som inte replikeras till RODC. Du kan använda FAS som ett skydd om du inte har behörighet eller inte vill lagra personligt identifierbar information eller Affärskritiska på Azure. Mer information finns i [RODC filtrerade attributuppsättningen [(https://technet.microsoft.com/library/cc753459)].

Se till att program kommer att vara kompatibel med RODC: er som du tänker använda. Många Windows Server Active Directory-aktiverade program fungerar bra med RODC: er, men vissa program kan utföra ineffektivt eller misslyckas om de inte har åtkomst till en skrivbar Domänkontrollant. Mer information finns i [guide för skrivskyddade domänkontrollanter kompatibilitet](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Global katalog
Du måste välja att installera en global katalog (GC). Du bör konfigurera alla domänkontrollanter som globala katalogservrar i en enda domän i skogen. Den kommer inte att öka kostnader eftersom det finns inga ytterligare replikeringstrafik.

Global katalog är nödvändiga för att visa medlemskap i universell grupp under autentiseringsprocessen i en skog. Om du inte distribuera en GC genererar arbetsbelastningar på virtuella nätverk som autentiseras mot en Domänkontrollant på Azure indirekt utgående autentiseringstrafik om du vill fråga GC-generationer lokala under varje autentiseringsförsök.

Kostnaderna för global katalog är mindre förutsägbar eftersom de värd för varje domän (i del). Om arbetsbelastningen är värd för en tjänst mot Internet och autentiserar användare mot Windows Server AD DS, kan det vara helt oförutsägbart kostnaderna. Om du vill reducera GC frågor utanför molnet platsen under autentiseringen, kan du [aktivera cachelagring av universella gruppmedlemskap](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Installationsmetod
Du måste välja hur du installerar domänkontrollanter på det virtuella nätverket:

* Främja nya domänkontrollanter. Mer information finns i [installera en ny Active Directory-skog på Azure-nätverk](active-directory-new-forest-virtual-machine.md).
* Flytta den virtuella Hårddisken för en virtuell Domänkontrollant lokalt till molnet. I det här fallet måste du se till att den lokala virtuella domänkontrollanten ”flyttas”, inte ”kopierade” eller ”klonade”.

Använd endast Azure virtuella datorer för domänkontrollanter (i stället för Azure ”web” eller ”worker” roll virtuella datorer). De är beständiga och hållbarhet för tillstånd för en Domänkontrollant krävs. Virtuella Azure-datorer är utformade för arbetsbelastningar som till exempel domänkontrollanter.

Använd inte SYSPREP för att distribuera eller klona domänkontrollanter. Möjligheten att klona domänkontrollanter är endast tillgängliga från och med Windows Server 2012. Funktionen kloning kräver stöd för VMGenerationID i underliggande hypervisor-programmet. Hyper-V i Windows Server 2012 och Azure virtuella nätverk både stöder VMGenerationID, precis som virtualiseringsleverantörer programvara från tredje part.

### <a name="BKMK_PlaceDB"></a>Placering av Windows Server AD DS-databasen och SYSVOL
Välj var du vill söka efter Windows Server AD DS-databasen, loggfilerna och SYSVOL. De måste distribueras på Azure datadiskar.

> [!NOTE]
> Azure datadiskar är begränsade till 4 TB.
> 
> 

Datadiskenheterna göra inte cache skrivningar som standard. Diskenheter för data som är kopplade till en virtuell dator använda skrivning via cachelagring. Skriv ner cachelagring gör att skrivning värnar varaktiga Azure storage innan transaktionen har slutförts för den Virtuella datorns operativsystem. Det ger hållbarhet på bekostnad av något långsammare skrivningar.

Detta är viktigt för Windows Server AD DS eftersom skrivåtgärder bakomliggande disk-cachelagring upphäver bedömning gjorda av domänkontrollanten. Windows Server AD DS försöker inaktivera skrivning till cacheminnet men det är upp till disk-i/o-system ta hänsyn till den. Det gick inte att inaktivera skrivcache, i vissa fall kan utgöra en USN-återställning ledde kvarstående objekt och andra problem.

Som bästa praxis för virtuella domänkontrollanter, gör du följande:

* Ange värden Cache-inställningen på Azure-datadisk för ingen. Detta förhindrar problem med skrivning till cacheminnet för AD DS-åtgärder.
* Lagrar databasen, loggar och SYSVOL på antingen samma datadisk eller separata hårddiskar. Detta är vanligtvis en annan disk än den disk som används för själva operativsystemet. Viktiga takeaway är att Windows Server AD DS-databasen och SYSVOL inte måste lagras i en typ av operativsystem för Azure disk. Installationen av AD DS installeras som standard komponenterna i mappen % systemroot %, vilket inte rekommenderas för Azure.

### <a name="BKMK_BUR"></a>Säkerhetskopiering och återställning
Ta reda på vad som och stöds inte i allmänhet för säkerhetskopiering och återställning av en Domänkontrollant och mer specifikt de körs i en virtuell dator. Se [säkerhetskopierar och återställer överväganden för virtualiserade domänkontrollanter](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Skapa säkerhetskopior med hjälp av endast programvara för säkerhetskopiering som är specifikt medveten om säkerhetskopiering krav för Windows Server AD DS, till exempel Windows Server Backup.

Kopiera inte eller klona domänkontrollanter VHD-filer i stället för att utföra regelbundna säkerhetskopieringar. En återställning ska någonsin krävs, gör det klonade eller kopierade virtuella hårddiskar utan Windows Server 2012 och en hypervisor som stöds, ger dig USN-bubblor.

### <a name="BKMK_FedSrvConfig"></a>Konfigurationen av federationsservern
Konfigurationen av Windows Server AD FS-federationsservrar (STSs) beror delvis på om de program som du vill distribuera på Azure som behöver åtkomst till resurser i ditt lokala nätverk.

Om program som uppfyller följande kriterier, kan du distribuera program avskilt från ditt lokala nätverk.

* De accepterar SAML säkerhetstoken
* De är exposable till Internet
* De inte komma åt lokala resurser

I detta fall konfigurera Windows Server AD FS STSs enligt följande:

1. Konfigurera en isolerad domän-skog på Azure.
2. Ge federerad åtkomst till skogen genom att konfigurera en Windows Server AD FS-federationsservergrupp.
3. Konfigurera Windows Server AD FS (federationsservergruppen och federationsserverproxygrupp?) i den lokala skogen.
4. Upprätta en federationsförtroenderelation mellan lokala och Azure AD FS i Windows Server-instanser.

Å andra sidan, om program som kräver åtkomst till lokala resurser, kan du konfigurera Windows Server AD FS med program på Azure på följande sätt:

1. Konfigurera anslutningen mellan lokala nätverk och Azure.
2. Konfigurera en federationsservergrupp för Windows Server AD FS i den lokala skogen.
3. Konfigurera en Windows Server AD FS federationsservergrupp på Azure.

Den här konfigurationen har fördelen att minska risken för lokala resurser, som liknar hur du konfigurerar Windows Server AD FS med program i ett perimeternätverk.

Observera att i båda fallen måste du upprätta förtroenden relationer med flera identitetsleverantörer om business-to-business samarbete krävs.

### <a name="BKMK_CloudSvcConfig"></a>Cloud services-konfiguration
Molntjänster behövs om du vill att exponera en virtuell dator direkt till Internet eller att exponera ett Internet-riktade belastningsutjämnade program. Det är möjligt att varje tjänst i molnet erbjuder en konfigurerbar virtuella IP-adress.

### <a name="BKMK_FedReqVIPDIP"></a>Federation server-krav för offentliga och privata IP-adresser (dynamisk IP jämfört med virtuella IP-adresser)
Varje virtuell Azure-dator tar emot en dynamisk IP-adress. En dynamisk IP-adress är en privat adress som är tillgängliga i Azure. I de flesta fall, men kan den du konfigurera en virtuell IP-adress för Windows Server AD FS-distributioner. Den virtuella IP är nödvändigt att exponera Windows Server AD FS-slutpunkter till Internet och ska användas av externa partners och av klienter för autentisering och den löpande hanteringen. En virtuell IP-adress är en egenskap för en molnbaserad tjänst som innehåller en eller flera virtuella Azure-datorer. Om anspråksmedvetet program som distribueras på Azure och Windows Server AD FS mot Internet och dela vanliga portar, varje kräver en virtuell IP-adressen för sin egen och därför blir det nödvändigt att skapa en molntjänst för programmet och en andra för Windows Server AD FS.

Definitioner av termer virtuell IP-adress och dynamiska IP-adress finns [termer och definitioner](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Windows Server AD FS-konfiguration för hög tillgänglighet
Det är möjligt att distribuera fristående federationstjänster för Windows Server AD FS, rekommenderas att distribuera en grupp med minst två noder för både AD FS STS och proxyservrar för produktionsmiljöer.

Se [topologiöverväganden för AD FS 2.0-distribution](https://technet.microsoft.com/library/gg982489) i den [AD FS 2.0-designguide](https://technet.microsoft.com/library/dd807036) att bestämma vilken konfiguration distributionsalternativ bäst passar dina specifika behov.

> [!NOTE]
> Konfigurera alla medlemmar i Windows Server AD FS-servergrupp i samma molntjänst för att få belastningsutjämning för Windows Server AD FS-slutpunkter på Azure och använder belastningen belastningsutjämning funktion i Azure för HTTP (standard: 80) och HTTPS-portar (standard: 443). Mer information finns i [Azure belastningsutjämnare avsökningen](https://msdn.microsoft.com/library/azure/jj151530).
> Windows Server Utjämning av nätverksbelastning (NLB) stöds inte på Azure.
> 
> 

