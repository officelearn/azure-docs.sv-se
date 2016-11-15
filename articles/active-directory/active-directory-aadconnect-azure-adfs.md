---
title: Active Directory Federation Services i Azure | Microsoft Docs
description: "I det här dokumentet lär du dig hur du distribuerar AD FS i Azure för hög tillgänglighet."
keywords: distribuera AD FS i azure, distribuera azure adfs, azure adfs, azure ad fs, distribuera adfs, distribuera ad fs, adfs i azure, distribuera adfs i azure, distribuera AD FS i azure, adfs azure, introduktion till AD FS, Azure, AD FS i Azure, iaas, ADFS, flytta adfs till azure
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 692a188c-badc-44aa-ba86-71c0e8074510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/03/2016
ms.author: anandy;billmath
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8ed71ccd07385eb74f7afecf04590c2c00372c34


---
# <a name="ad-fs-deployment-in-azure"></a>AD FS-distribution i Azure
AD FS tillhandahåller förenklad, säker identitetsfederation och funktioner för enkel inloggning (SSO). Federation med Azure AD eller O365 gör att användarna kan autentiseras med lokala autentiseringsuppgifter och få åtkomst till alla resurser i molnet. Därför är det viktigt att du har en AD FS-infrastruktur med hög tillgänglighet för att säkerställa åtkomsten till resurser både lokalt och i molnet. Genom att distribuera AD FS i Azure kan du uppnå den höga tillgänglighet som krävs med minimalt arbete.
Det finns flera fördelar med att distribuera AD FS i Azure, några av dem anges nedan:

* **Hög tillgänglighet** – Med kraften i Azures tillgänglighetsuppsättningar säkerställer du en infrastruktur med hög tillgänglighet.
* **Lätt att skala** – Behöver du bättre prestanda? Migrera enkelt till kraftfullare datorer med bara några klick i Azure
* **Global geo-redundans** – med Azure Geo Redundancy kan du vara säker på att din infrastruktur har hög tillgänglighet i hela världen.
* **Lätt att hantera** – Med mycket förenklade hanteringsalternativ på Azure-portalen är det väldigt lätt och okomplicerat att hantera infrastrukturen. 

## <a name="design-principles"></a>Designprinciper
![Distributionsdesign](./media/active-directory-aadconnect-azure-adfs/deployment.png)

Diagrammet ovan illustrerar den rekommenderade grundläggande topologin för distribution av en AD FS-infrastruktur i Azure. Principerna bakom de olika komponenterna i topologin anges nedan:

* **DC/ADFS-servrar**: Om du har färre än 1 000 användare behöver du bara installera AD FS-rollen på domänkontrollanterna. Om du vill undvika en prestandaförsämring på domänkontrollanterna eller om du har fler än 1 000 användare distribuerar du AD FS på separata servrar.
* **WAP-server** – Du måste distribuera WAP-servrar (webbprogramproxyservrar) så att användarna kan nå AD FS även när de inte är anslutna till företagets nätverk.
* **DMZ**: WAP-servrarna placeras i DMZ och endast TCP/443-åtkomst tillåts mellan DMZ och det interna undernätet.
* **Belastningsutjämnare**: Om du vill garantera hög tillgänglighet för AD FS- och WAP-servrar rekommenderar vi att du använder en intern belastningsutjämnare för AD FS-servrar och Azure Load Balancer för WAP-servrar.
* **Tillgänglighetsuppsättningar**: Om du vill tillhandahålla redundans i AD FS-distributionen rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning för liknande arbetsbelastningar. Den här konfigurationen garanterar att minst en virtuell dator är tillgänglig under planerat eller oplanerat underhåll.
* **Lagringskonton**: Vi rekommenderar att du har två lagringskonton. Om du bara har ett lagringskonto kan det ge upphov till en felkritisk systemdel och göra att distributionen blir otillgänglig i det osannolika scenariot att lagringskontot skulle krascha. Två lagringskonton innebär att ett lagringskonto kan associeras med varje felrad.
* **Nätverkssegregering**: WAP-servrar bör distribueras i ett separat DMZ-nätverk. Du kan dela upp ett virtuellt nätverk i två undernät och sedan distribuera WAP-servrarna i ett isolerat undernät. Konfigurera bara inställningar för nätverkssäkerhetsgrupper för varje undernät och tillåt endast nödvändig kommunikation mellan de två undernäten. Mer information finns i distributionsscenarierna nedan.

## <a name="steps-to-deploy-ad-fs-in-azure"></a>Steg för att distribuera AD FS i Azure
Stegen i det här avsnittet beskriver hur AD FS-infrastrukturen nedan distribueras i Azure.

### <a name="1-deploying-the-network"></a>1. Distribuera nätverket
Som vi nämnt ovan kan du antingen skapa två undernät i ett enda virtuellt nätverk eller skapa två olika virtuella nätverk (VNet). I den här artikeln fokuserar vi på distributionen av ett virtuellt nätverk som delas in i två undernät. Den här metoden är enklare eftersom två separata virtuella nätverk kräver en ”VNet till VNet”-gateway för kommunikation.

**1.1 Skapa det virtuella nätverket**

![Skapa det virtuella nätverket](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)

Välj alternativet för virtuella nätverk på Azure-portalen så kan du distribuera det virtuella nätverket och ett undernät direkt med ett enkelt klick. INT-undernätet definieras också och är nu redo att ta emot virtuella datorer.
Nu ska du lägga till ett till undernät i nätverket, dvs. DMZ-undernätet. Så här skapar du DMZ-undernätet:

* Välj det nya nätverket.
* Välj Undernät i egenskaperna.
* Klicka på knappen Lägg till på panelen för undernätet.
* Ange undernätets namn och information om adressutrymmet för att skapa undernätet.

![Undernät](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)

![DMZ-undernät](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. Skapa nätverkssäkerhetsgrupper**

En nätverkssäkerhetsgrupp (NSG) innehåller en lista över regler för åtkomstkontrollistan (ACL) som tillåter eller nekar nätverkstrafik till dina VM-instanser i ett virtuellt nätverk. NSG:er kan antingen associeras med undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet.
I den här guiden skapar vi två nätverkssäkerhetsgrupper: en var för ett internt nätverk och en DMZ. De tilldelas namnen NSG_INT och NSG_DMZ.

![Skapa en nätverkssäkerhetsgrupp](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

När nätverkssäkerhetsgruppen skapas finns det inga regler för inkommande eller utgående trafik. När rollerna på respektive server har installerats och fungerar som de ska kan reglerna för inkommande och utgående trafik skapas utifrån kraven på säkerhetsnivån.

![Initiera nätverkssäkerhetsgruppen](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

När nätverkssäkerhetsgrupperna har skapats associerar du NSG_INT med INT-undernätet och NSG_DMZ med DMZ-undernätet. Här är ett skärmbildsexempel:

![Konfigurera nätverkssäkerhetsgruppen](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Öppna panelen för undernät genom att klicka på Undernät.
* Välj undernätet som ska associeras med nätverkssäkerhetsgruppen. 

Efter konfigurationen bör panelen för undernät se ut så här:

![Undernät efter NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. Skapa anslutning till den lokala infrastrukturen**

Vi behöver en anslutning till den lokala infrastrukturen för att distribuera domänkontrollanten (DC) i Azure. Azure erbjuder olika anslutningsmöjligheter för att ansluta den lokala infrastrukturen till Azure-infrastrukturen.

* Punkt-till-plats
* Plats-till-plats för Virtual Network
* ExpressRoute

Vi rekommenderar att du använder ExpressRoute. Med ExpressRoute kan du skapa privata anslutningar mellan Azures datacenter och infrastruktur som finns lokalt eller i en samplaceringsmiljö. ExpressRoute-anslutningar går inte via offentligt Internet. De ger bättre tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.
Vi rekommenderar att du använder ExpressRoute, men du kan välja valfri anslutningsmetod beroende på vad som passar bäst för din organisation. Om du vill veta mer om ExpressRoute och de olika anslutningsalternativen när du använder ExpressRoute läser du [Teknisk översikt över ExpressRoute](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. Skapa lagringskonton
För att upprätthålla hög tillgänglighet och undvika beroende av ett enda lagringskonto kan du skapa två lagringskonton. Dela in datorerna i varje tillgänglighetsuppsättning i två grupper och tilldela sedan varje grupp ett separat lagringskonto.

![Skapa lagringskonton](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. Skapa tillgänglighetsuppsättningar
För varje roll (DC/AD FS och WAP) skapar du tillgänglighetsuppsättningar som ska innehålla minst två datorer var. På så sätt kan du uppnå högre tillgänglighet för varje roll. När du skapar tillgänglighetsuppsättningarna måste du fatta beslut om följande:

* **Feldomäner**: Virtuella datorer i samma feldomän delar samma strömkälla och fysiska nätverksväxel. Minst två feldomäner rekommenderas. Standardvärdet är 3 och du kan lämna det som det är i den här distributionen.
* **Uppdateringsdomäner**: Datorer som tillhör samma uppdateringsdomän startas om tillsammans under en uppdatering. Du bör ha minst två uppdateringsdomäner. Standardvärdet är 5 och du kan lämna det som det är i den här distributionen.

![Tillgänglighetsuppsättningar](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Skapa följande tillgänglighetsuppsättningar

| Tillgänglighetsuppsättning | Roll | Feldomäner | Uppdateringsdomäner |
|:---:|:---:|:---:|:--- |
| contosodcset |DC/ADFS |3 |5 |
| contosowapset |WAP |3 |5 |

### <a name="4-deploy-virtual-machines"></a>4.    Distribuera virtuella datorer
Nästa steg är att distribuera virtuella datorer som ska vara värdar för de olika rollerna i infrastrukturen. Minst två datorer rekommenderas i varje tillgänglighetsuppsättning. Skapa sex virtuella datorer för den grundläggande distributionen.

| Dator | Roll | Undernät | Tillgänglighetsuppsättning | Lagringskonto | IP-adress |
|:---:|:---:|:---:|:---:|:---:|:---:|
| contosodc1 |DC/ADFS |INT |contosodcset |contososac1 |Statisk |
| contosodc2 |DC/ADFS |INT |contosodcset |contososac2 |Statisk |
| contosowap1 |WAP |DMZ |contosowapset |contososac1 |Statisk |
| contosowap2 |WAP |DMZ |contosowapset |contososac2 |Statisk |

Som du kanske har märkt har ingen nätverkssäkerhetsgrupp (NSG) angetts. Det beror på att du kan använda NSG på undernätverksnivån i Azure. Sedan kan du styra datorns nätverkstrafik med den enskilda nätverkssäkerhetsgruppen som är associerad antingen med undernätet eller med NIC-objektet. Läs mer i [Vad är en nätverkssäkerhetsgrupp (NSG)?](https://aka.ms/Azure/NSG)
En statisk IP-adress rekommenderas om du hanterar DNS. Du kan använda Azure DNS och i stället referera till de nya datorerna i DNS-posterna för din domän med deras fullständiga domännamn i Azure.
Fönstret för din virtuella dator bör se ut så här när distributionen är klar:

![Distribuerade virtuella datorer](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller-ad-fs-servers"></a>5. Konfigurera domänkontrollanten/AD FS-servrar
 För att kunna autentisera en inkommande begäran måste AD FS kontakta domänkontrollanten. Om du vill spara kostsamma turer från Azure till den lokala domänkontrollanten för autentisering rekommenderar vi att du distribuerar en replik av domänkontrollanten i Azure. För att uppnå hög tillgänglighet rekommenderar vi att du skapar en tillgänglighetsuppsättning på minst två domänkontrollanter.

| Domänkontrollant | Roll | Lagringskonto |
|:---:|:---:|:---:|
| contosodc1 |Replik |contososac1 |
| contosodc2 |Replik |contososac2 |

* Flytta upp de två servrarna som replikeringsdomänkontrollanter med DNS
* Konfigurera AD FS-servrarna genom att installera AD FS-rollen med hjälp av Serverhanteraren.

### <a name="6-deploying-internal-load-balancer-ilb"></a>6.    Distribuera en intern belastningsutjämnare (ILB)
**6.1.    Skapa den interna belastningsutjämnaren**

Om du vill distribuera en intern belastningsutjämnare väljer du Belastningsutjämning på Azure-portalen och klickar på Lägg till (+).

> [!NOTE]
> Om du inte ser **Belastningsutjämning** på menyn klickar du på **Bläddra** längst ned till vänster på portalen och rullar tills du ser **Belastningsutjämning**.  Sedan klickar du på den gula stjärnan för att lägga till den på menyn. Välj sedan ikonen för den nya belastningsutjämnaren för att öppna panelen och börja konfigurera belastningsutjämningen.
> 
> 

![Bläddra till belastningsutjämnaren](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Namn**: Ge belastningsutjämnaren ett lämpligt namn.
* **Schema**: Eftersom den här belastningsutjämnaren ska placeras framför AD FS-servrarna och är avsedd ENDAST för interna nätverksanslutningar så väljer du ”Intern”.
* **Virtual Network**: Välj det virtuella nätverket där du distribuerar AD FS.
* **Undernät**: Välj det interna undernätet.
* **IP-adresstilldelning**: Dynamisk.

![Intern belastningsutjämnare](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)

När du klickar på Skapa och när den interna belastningsutjämnaren har distribuerats bör du se den i listan med belastningsutjämnare:

![Belastningsutjämnare efter ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)

Nästa steg är att konfigurera serverdelspoolen och serverdelsavsökningen.

**6.2.    Konfigurera serverdelspoolen för den interna belastningsutjämnaren**

Välj den nya interna belastningsutjämnaren på panelen Belastningsutjämning. Nu öppnas inställningspanelen. 

1. Välj Serverdelspooler från inställningspanelen.
2. Klicka på Lägg till virtuell dator på panelen Lägg till serverdelspool.
3. Nu visas en panel där du kan välja tillgänglighetsuppsättning.
4. Välj AD FS-tillgänglighetsuppsättningen.

![Konfigurera serverdelspoolen för den interna belastningsutjämnaren](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)

**6.3.    Konfigurera avsökning**

Välj Avsökningar på panelen för ILB-inställningar.

1. Klicka på Lägg till.
2. Ange information för avsökningen a. **Namn**: Avsökningens namn. b. **Protokoll**: TCP. c. **Port**: 443 (HTTPS). d. **Intervall**: 5 (standardvärde) – Den interna belastningsutjämnaren söker av datorerna i serverdelspoolen enligt detta intervall. e. **Tröskelvärde för ohälsosamt tillstånd**: 2 (standardvärde) – Det här är tröskelvärdet för upprepade avsökningsfel, varefter den interna belastningsutjämnaren deklarerar att en dator i serverdelspoolen inte svarar och slutar att skicka trafik till den.

![Konfigurera ILB-avsökning](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)

**6.4.    Skapa regler för belastningsutjämning**

För att effektivt belastningsutjämna trafiken bör belastningsutjämnaren konfigureras med belastningsutjämningsregler. Så här skapar du en belastningsutjämningsregel: 

1. Välj Belastningsutjämningsregel från panelen för ILB-inställningar.
2. Klicka på Lägg till på panelen Belastningsutjämningsregel.
3. På panelen Lägg till belastningsutjämningsregel: a. **Namn**: Ange ett namn för regeln. b. **Protokoll**: Välj TCP. c. **Port**: 443. d. **Serverport**: 443. e. **Serverdelspool**: Välj poolen som du tidigare skapade för AD FS-klustret. f. **Avsökning**: Välj avsökningen som du tidigare skapade för AD FS-servrar.

![Konfigurera ILB-belastningsutjämningsregler](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5.    Uppdatera DNS med ILB**

Gå till din DNS-server och skapa en CNAME-post för den interna belastningsutjämnaren. CNAME ska anges för federationstjänsten och IP-adressen ska peka på den interna belastningsutjämnarens IP-adress. Om den interna belastningsutjämnarens DIP till exempel är 10.3.0.8 och den installerade federationstjänsten är fs.contoso.com skapar du en CNAME-post för fs.contoso.com som pekar på 10.3.0.8.
På så sätt säkerställer du att alla kommunikation relaterad till fs.contoso.com kommer till den interna belastningsutjämnaren och dirigeras korrekt.

### <a name="7-configuring-the-web-application-proxy-server"></a>7.    Konfigurera WAP-servern (webbprogramproxyserver)
**7.1.    Konfigurera WAP-servrarna så att de kan nå AD FS-servrar**

Säkerställ att WAP-servrarna kan nå AD FS-servarna bakom den interna belastningsutjämnaren genom att skapa en post i %systemroot%\system32\drivers\etc\hosts för den interna belastningsutjämnaren. Observera att det unika namnet (DN) ska vara federationstjänstnamnet, till exempel fs.contoso.com. Och IP-posten ska vara den för den interna belastningsutjämnarens IP-adress (10.3.0.8 som i exemplet).

**7.2.    Installera WAP-rollen**

När du har kontrollerat att WAP-servrarna kan nå AD FS-servarna bakom den interna belastningsutjämnaren kan du gå vidare och installera WAP-servrarna. WAP-servarna ska inte anslutas till domänen. Installera WAP-rollerna på två WAP-servrar genom att välja fjärråtkomstrollen. Serverhanteraren vägleder dig genom WAP-installationen.
Mer information om hur du distribuerar WAP finns i [Installera och konfigurera WAP (webbprogramproxyserver)](https://technet.microsoft.com/library/dn383662.aspx).

### <a name="8-deploying-the-internet-facing-public-load-balancer"></a>8.    Distribuera den Internetuppkopplade (offentliga) belastningsutjämnaren
**8.1.    Skapa en Internetuppkopplad (offentlig) belastningsutjämnare**

Välj Belastningsutjämning på Azure-portalen och klicka sedan på Lägg till. Ange följande information på panelen Skapa belastningsutjämnare:

1. **Namn**: Belastningsutjämnarens namn.
2. **Schema**: Offentligt – det här alternativet anger att belastningsutjämnaren behöver en offentlig adress.
3. **IP-adress**: Skapa en ny IP-adress (dynamisk).

![Internetuppkopplad belastningsutjämnare](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Efter distributionen visas belastningsutjämnaren i listan över belastningsutjämnare.

![Lista med belastningsutjämnare](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)

**8.2.    Ange en DNS-etikett för den offentliga IP-adressen**

Öppna konfigurationspanelen genom att klicka på den nya posten för belastningsutjämnaren på panelen Belastningsutjämning. Konfigurera DNS-etiketten för den offentliga IP-adressen genom att följa stegen nedan:

1. Klicka på den offentliga IP-adressen. Nu öppnas panelen för den offentliga IP-adressen och dess inställningar.
2. Klicka på Konfiguration.
3. Ange en DNS-etikett. Den här etiketten blir den offentliga DNS-etiketten som du kan komma åt överallt, till exempel contosofs.westus.cloudapp.azure.com. Du kan lägga till en post i externa DNS för federationstjänsten (t.ex. fs.contoso.com) som matchar DNS-etiketten för den externa belastningsutjämnaren (contosofs.westus.cloudapp.azure.com).

![Konfigurera en Internetuppkopplad belastningsutjämnare](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Konfigurera en Internetuppkopplad belastningsutjämnare (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3.    Konfigurera serverdelspoolen för den Internetuppkopplade (offentliga) belastningsutjämnaren** 

Följ samma steg som när du skapade den interna belastningsutjämnaren för att konfigurera serverdelspoolen för den Internetuppkopplade (offentliga) belastningsutjämnaren som tillgänglighetsuppsättningen för WAP-servrarna. Till exempel contosowapset.

![Konfigurera serverdelspoolen för den Internetuppkopplade belastningsutjämnaren](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)

**8.4.    Konfigurera avsökning**

Följ samma steg som när du konfigurerade den interna belastningsutjämnaren för att konfigurera avsökningen för serverdelspoolen för WAP-servrar.

![Konfigurera avsökningen för den Internetuppkopplade belastningsutjämnaren](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)

**8.5.    Skapa belastningsutjämningsregler**

Följ samma steg som när du konfigurerade den interna belastningsutjämnaren för att konfigurera belastningsutjämningsregeln för TCP 443.

![Konfigurera belastningsutjämningsregler för den Internetuppkopplade belastningsutjämnaren](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)

### <a name="9-securing-the-network"></a>9.    Skydda nätverket
**9.1.    Skydda det interna undernätet**

Du behöver följande regler för att effektivt skydda det interna undernätet (i den ordning som anges nedan)

| Regel | Beskrivning | Flöde |
|:--- |:--- |:---:|
| AllowHTTPSFromDMZ |Tillåt HTTPS-kommunikation från DMZ |Inkommande |
| DenyInternetOutbound |Ingen åtkomst till Internet |Utgående |

![INT-åtkomstregler (inkommande)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[kommentar]: <> (![INT-åtkomstregler (inkommande)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [kommentar]: <> (![INT-åtkomstregler (utgående)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))

**9.2.    Skydda DMZ-undernätet**

| Regel | Beskrivning | Flöde |
|:--- |:--- |:---:|
| AllowHTTPSFromInternet |Tillåt HTTPS från Internet till DMZ |Inkommande |
| DenyInternetOutbound |Allt utom HTTPS till Internet blockeras |Utgående |

![EXT-åtkomstregler (inkommande)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[kommentar]: <> (![EXT-åtkomstregler (inkommande)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [kommentar]: <> (![EXT-åtkomstregler (utgående)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

> [!NOTE]
> Om autentisering med klientanvändarcertifikat (clientTLS-autentisering med X509-användarcertifikat) krävs så kräver AD FS att TCP-port 49443 är aktiverad för inkommande åtkomst.
> 
> 

### <a name="10-test-the-ad-fs-signin"></a>10.    Testa AD FS-inloggningen
Det enklaste sättet är att testa AD FS med hjälp av sidan IdpInitiatedSignon.aspx. För att kunna göra det måste IdpInitiatedSignOn vara aktiverat i AD FS-egenskaperna. Kontrollera din AD FS-konfiguration genom att följa stegen nedan.

1. Kör cmdleten nedan på AD FS-servern med hjälp av PowerShell för att aktivera egenskapen.
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true 
2. Gå till https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx från valfri extern dator.  
3. Du bör se en AD FS-sida som den här:

![Testa inloggningssidan](./media/active-directory-aadconnect-azure-adfs/test1.png)

Om inloggningen lyckas visas ett meddelande som det nedan:

![Lyckat test](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Mall för att distribuera AD FS i Azure
Mallen distribuerar en konfiguration för 6 maskiner, 2 vardera för domänkontrollanter, AD FS och WAP.

[Distributionsmall för AD FS i Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Du kan använda ett befintligt virtuellt nätverk eller skapa ett nytt VNET när du distribuerar här mallen. De olika parametrarna för att anpassa distributionen listas nedan med beskrivning av parameterns användning vid distributionsprocessen. 

| Parameter | Beskrivning |
|:--- |:--- |
| Plats |Regionen att distribuera resurserna till, exempelvis USA, östra. |
| StorageAccountType |Typ av lagringskonto som skapas |
| VirtualNetworkUsage |Anger om ett nytt virtuellt nätverk ska skapas eller om ett befintligt ska användas |
| VirtualNetworkName |Namnet på det virtuella nätverket som ska skapas, obligatoriskt både vid användning av ett befintligt och nytt virtuellt nätverk |
| VirtualNetworkResourceGroupName |Anger namnet på resursgruppen där det befintliga virtuella nätverket finns. När du använder ett befintligt virtuellt nätverk blir det här en obligatorisk parameter så att distributionen kan hitta ID:t för det befintliga virtuella nätverket |
| VirtualNetworkAddressRange |Adressintervallet för det nya VNET, är obligatoriskt vid skapande av ett nytt virtuellt nätverk |
| InternalSubnetName |Namnet på det interna undernätet, obligatoriskt för bägge användningsalternativen för det virtuella nätverket (nytt eller befintligt) |
| InternalSubnetAddressRange |Adressintervallet för det interna undernätet, som innehåller domänkontrollanter och AD FS-servrar, obligatoriskt om du skapar ett nytt virtuellt nätverk. |
| DMZSubnetAddressRange |Adressintervallet för DMZ-undernätet, som innehåller Windows-programproxyservrar, obligatoriskt om du skapar ett nytt virtuellt nätverk. |
| DMZSubnetName |Namnet på det interna undernätet, obligatoriskt för bägge användningsalternativen för det virtuella nätverket (nytt eller befintligt). |
| ADDC01NICIPAddress |Den interna IP-adressen för den första domänkontrollanten, den här IP-adressen kommer att statiskt tilldelas till domänkontrollanten och måste vara en giltig IP-adress inom det interna undernätet |
| ADDC02NICIPAddress |Den interna IP-adressen för den andra domänkontrollanten, den här IP-adressen kommer att statiskt tilldelas till domänkontrollanten och måste vara en giltig IP-adress inom det interna undernätet |
| ADFS01NICIPAddress |Den interna IP-adressen för den första AD FS-servern, den här IP-adressen kommer att statiskt tilldelas till AD FS-servern och måste vara en giltig IP-adress inom det interna undernätet |
| ADFS02NICIPAddress |Den interna IP-adressen för den andra AD FS-servern, den här IP-adressen kommer att statiskt tilldelas till AD FS-servern och måste vara en giltig IP-adress inom det interna undernätet |
| WAP01NICIPAddress |Den interna IP-adressen för den första WAP-servern, den här IP-adressen kommer att statiskt tilldelas till WAP-servern och måste vara en giltig IP-adress inom DMZ-undernätet |
| WAP02NICIPAddress |Den interna IP-adressen för den andra WAP-servern, den här IP-adressen kommer att statiskt tilldelas till WAP-servern och måste vara en giltig IP-adress inom DMZ-undernätet |
| ADFSLoadBalancerPrivateIPAddress |Den interna IP-adressen för AD FS-belastningsutjämnaren, den här IP-adressen kommer att statiskt tilldelas till belastningsutjämnaren och måste vara en giltig IP-adress inom det interna undernätet |
| ADDCVMNamePrefix |Virtual Machine-namnprefixet för domänkontrollanter |
| ADFSVMNamePrefix |Virtual Machine-namnprefixet för AD FS-servrar |
| WAPVMNamePrefix |Virtual Machine-namnprefixet för WAP-servrar |
| ADDCVMSize |VM-storleken för domänkontrollanterna |
| ADFSVMSize |VM-storleken för AD FS-servrarna |
| WAPVMSize |VM-storleken för WAP-servrarna |
| AdminUserName |Namnet på den lokala administratören för de virtuella datorerna |
| AdminPassword |Lösenordet för det lokala administratörskontot för de virtuella datorerna |

## <a name="additional-resources"></a>Ytterligare resurser
* [Tillgänglighetsuppsättningar](https://aka.ms/Azure/Availability) 
* [Azure Load Balancer](https://aka.ms/Azure/ILB)
* [Interna belastningsutjämnare](https://aka.ms/Azure/ILB/Internal)
* [Internetuppkopplad belastningsutjämnare](https://aka.ms/Azure/ILB/Internet)
* [Lagringskonton](https://aka.ms/Azure/Storage)
* [Azure Virtual Networks](https://aka.ms/Azure/VNet)
* [AD FS och WAP-länkar (webbprogramproxy)](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Nästa steg
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
* [Konfigurera och hantera AD FS med Azure AD Connect](active-directory-aadconnectfed-whatis.md)
* [AD FS-distribution med hög tillgänglighet över geografiska områden i Azure med Azure Traffic Manager](active-directory-adfs-in-azure-with-azure-traffic-manager.md)




<!--HONumber=Nov16_HO2-->


