---
title: Arkitekturer för att distribuera Oracle-appar på virtuella Azure-datorer | Microsoft-dokument
description: Programarkitekturer för att distribuera Oracle-appar, inklusive E-Business Suite, JD Edwards EnterpriseOne och PeopleSoft på virtuella Microsoft Azure-datorer med databaser i Azure eller Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: b183a4d4922c89f60ccb19b3e3e978216f33cc9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70100093"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Arkitekturer för att distribuera Oracle-program på Azure

Microsoft och Oracle har arbetat tillsammans för att göra det möjligt för kunder att distribuera Oracle-program som Oracle E-Business Suite, JD Edwards EnterpriseOne och PeopleSoft i molnet. I och med introduktionen av förhandsversionen av [den privata nätverkssammankopplingen](configure-azure-oci-networking.md) mellan Microsoft Azure och Oracle Cloud Infrastructure (OCI) kan Oracle-program nu distribueras på Azure med sina backend-databaser i Azure eller OCI. Oracle-program kan också integreras med Azure Active Directory, så att du kan konfigurera enkel inloggning så att användare kan logga in på Oracle-programmet med sina Azure Active Directory (Azure AD) autentiseringsuppgifter.

OCI erbjuder flera Oracle-databasalternativ för Oracle-program, inklusive DBaaS, Exadata Cloud Service, Oracle RAC och Infrastructure-as-a-Service (IaaS). För närvarande är autonom databas inte en backend som stöds för Oracle-program.

Det finns [flera alternativ](oracle-overview.md) för att distribuera Oracle-program i Azure, även på ett mycket tillgängligt och säkert sätt. Azure erbjuder också [avbildningar för virtuell Oracle-databas](oracle-vm-solutions.md) som du kan distribuera om du väljer att köra dina Oracle-program helt på Azure.

I följande avsnitt beskrivs arkitekturrekommendationer från både Microsoft och Oracle för att distribuera Oracle E-Business Suite, JD Edwards EnterpriseOne och PeopleSoft i en konfiguration över flera moln eller helt i Azure. Microsoft och Oracle har testat dessa program och bekräftat att prestanda uppfyller standarder som fastställts av Oracle för dessa program.

## <a name="architecture-considerations"></a>Hänsyn till arkitektur

Oracle-program består av flera tjänster, som kan finnas på samma eller flera virtuella datorer i Azure och eventuellt i OCI. 

Programinstanser kan ställas in med privata eller offentliga slutpunkter. Microsoft och Oracle rekommenderar att du konfigurerar en *virtuell värd för bastion* med en offentlig IP-adress i ett separat undernät för hantering av programmet. Tilldela sedan endast privata IP-adresser till de andra datorerna, inklusive databasnivån. 

När du konfigurerar ett program i en molnöverskridande arkitektur krävs planering för att säkerställa att IP-adressutrymmet i det virtuella Azure-nätverket inte överlappar det privata IP-adressutrymmet i OCI-nätverket för virtuella moln.

För ökad säkerhet ställer du in nätverkssäkerhetsgrupper på undernätsnivå för att säkerställa att endast trafik på specifika portar och IP-adresser tillåts. Datorer på mellannivån bör till exempel endast ta emot trafik inifrån det virtuella nätverket. Ingen extern trafik bör nå maskinerna på mellannivå direkt.

För hög tillgänglighet kan du ställa in redundanta instanser av de olika servrarna i samma tillgänglighetsuppsättning eller olika tillgänglighetszoner. Med tillgänglighetszoner kan du uppnå ett serviceavtal på 99,99 % drifttid, medan tillgänglighetsuppsättningar gör att du kan uppnå ett serviceavtal på 99,95 % i regionen. Exempelarkitekturer som visas i den här artikeln distribueras över två tillgänglighetszoner.

När du distribuerar ett program med sammankopplingen mellan moln kan du fortsätta att använda en befintlig ExpressRoute-krets för att ansluta din Azure-miljö till ditt lokala nätverk. Du behöver dock en separat ExpressRoute-krets för sammankopplingen till OCI än den som ansluter till ditt lokala nätverk.

## <a name="e-business-suite"></a>Svit E-Business

Oracle E-Business Suite (EBS) är en uppsättning program som Supply Chain Management (SCM) och Customer Relationship Management (CRM). För att dra nytta av OCI:s hanterade databasportfölj kan EBS distribueras med hjälp av sammankopplingen mellan flera moln mellan Microsoft Azure och OCI. I den här konfigurationen körs presentations- och programnivåerna i Azure och databasnivån i OCI, vilket illustreras i följande arkitekturdiagram (bild 1).

![E-Business Suite-arkitektur över moln](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Bild 1: E-Business Suite-arkitektur över moln* 

I den här arkitekturen är det virtuella nätverket i Azure anslutet till ett virtuellt molnnätverk i OCI med hjälp av sammanlänkningen mellan moln. Programnivån har konfigurerats i Azure, medan databasen har konfigurerats i OCI. Vi rekommenderar att du distribuerar varje komponent till sitt eget undernät med nätverkssäkerhetsgrupper så att trafik endast tillåts från specifika undernät på specifika portar.

Arkitekturen kan också anpassas för distribution helt på Azure med högtillgängliga Oracle-databaser som konfigurerats med Oracle Data Guard i två tillgänglighetszoner i en region. Följande diagram (bild 2) är ett exempel på detta arkitektoniska mönster:

![Azure-arkitektur för E-Business Suite](media/oracle-oci-applications/ebs-arch-azure.png)

*Bild 2: Azure-arkitektur för E-Business Suite*

I följande avsnitt beskrivs de olika komponenterna på en hög nivå.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Programnivå (mitten)

Programnivån är isolerad i sitt eget undernät. Det finns flera virtuella datorer som ställts in för feltolerans och enkel patchhantering. Dessa virtuella datorer kan backas upp av delad lagring, som erbjuds av Azure NetApp-filer och Ultra SSD.These VMs can be backed by shared storage, which is offered by Azure NetApp Files and Ultra SSDs. Den här konfigurationen möjliggör enklare distribution av korrigeringsfiler utan driftstopp. Datorerna på programnivån bör frontas av en offentlig belastningsutjämnare så att begäranden till EBS-programnivån bearbetas även om en dator på nivån är offline på grund av ett fel.

### <a name="load-balancer"></a>Lastbalanserare

Med en Azure-belastningsutjämning kan du distribuera trafik över flera instanser av din arbetsbelastning för att säkerställa hög tillgänglighet. I det här fallet ställs en offentlig belastningsutjämnare in, eftersom användare har åtkomst till EBS-programmet via webben. Belastningsutjämnaren distribuerar belastningen till båda datorerna på mellannivån. För ökad säkerhet, tillåt endast trafik från användare som ansluter till systemet från företagets nätverk med hjälp av en plats-till-plats VPN eller ExpressRoute och nätverkssäkerhetsgrupper.

### <a name="database-tier"></a>Databasnivå

Den här nivån är värd för Oracle-databasen och är uppdelad i sitt eget undernät. Vi rekommenderar att du lägger till nätverksskyddsgrupper som endast tillåter trafik från programnivån till databasnivån på Oracle-specifik databasport 1521.

Microsoft och Oracle rekommenderar en konfiguration med hög tillgänglighet. Hög tillgänglighet i Azure kan uppnås genom att konfigurera två Oracle-databaser i två tillgänglighetszoner med Oracle Data Guard, eller genom att använda Oracle Database Exadata Cloud Service i OCI. När du använder Oracle Database Exadata Cloud Service distribueras databasen i två undernät. Du kan också konfigurera Oracle Database i virtuella datorer i OCI i två tillgänglighetsdomäner med Oracle Data Guard.


### <a name="identity-tier"></a>Identitetsnivå

Identitetsnivån innehåller den virtuella EBS Asserter-datorn. MED EBS Asserter kan du synkronisera identiteter från Oracle Identity Cloud Service (IDCS) och Azure AD. EBS Asserter behövs eftersom EBS inte stöder enskilda inloggningsprotokoll som SAML 2.0 eller OpenID Connect. EBS Asserter förbrukar OpenID-anslutningstoken (genererad av IDCS), validerar den och skapar sedan en session för användaren i EBS. 

Även om den här arkitekturen visar IDCS-integrering kan Azure AD-enhetlig åtkomst och enkel inloggning också aktiveras med Oracle Access Manager med Oracle Internet Directory eller Oracle Unified Directory. Mer information finns i faktablad om [distribution av Oracle EBS med IDCS-integrering](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) eller [distribution av Oracle EBS med OAM-integrering](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

För hög tillgänglighet rekommenderar vi att du distribuerar redundanta servrar för EBS Asserter över flera tillgänglighetszoner med en belastningsutjämnare framför den.

När infrastrukturen har konfigurerats kan E-Business Suite installeras genom att följa installationsguiden från Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracles JD Edwards EnterpriseOne är en integrerad programsvit med omfattande programvara för resursplanering av företag. Det är ett program med flera nivåer som kan konfigureras med antingen en Oracle- eller SQL Server-databasdel. I det här avsnittet beskrivs information om hur du distribuerar JD Edwards EnterpriseOne med en Oracle-databas backend antingen i OCI eller i Azure.

I följande rekommenderade arkitektur (figur 3) distribueras administrations-, presentations- och mellannivåerna till det virtuella nätverket i Azure. Databasen distribueras i ett virtuellt molnnätverk i OCI.

Precis som med E-Business Suite kan du ställa in en valfri bastionnivå för säkra administrativa ändamål. Använd värdvärden för skyddsdatorn som en hoppserver för att komma åt program- och databasinstanserna.

![JD Edwards EnterpriseOne arkitektur över moln](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Bild 3: JD Edwards EnterpriseOne arkitektur över moln*

I den här arkitekturen är det virtuella nätverket i Azure anslutet till det virtuella molnnätverket i OCI med hjälp av sammanlänkningen mellan moln. Programnivån har konfigurerats i Azure, medan databasen har konfigurerats i OCI. Vi rekommenderar att du distribuerar varje komponent till sitt eget undernät med nätverkssäkerhetsgrupper så att trafik endast tillåts från specifika undernät på specifika portar.

Arkitekturen kan också anpassas för distribution helt på Azure med högtillgängliga Oracle-databaser som konfigurerats med Oracle Data Guard i två tillgänglighetszoner i en region. Följande diagram (bild 4) är ett exempel på detta arkitektoniska mönster:

![JD Edwards EnterpriseOne Azure-arkitektur](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Bild 4: JD Edwards EnterpriseOne Azure-arkitektur*

I följande avsnitt beskrivs de olika komponenterna på en hög nivå.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Administrativ nivå

Som namnet antyder används den här nivån för administrativa uppgifter. Du kan skära ut ett separat undernät för den administrativa nivån. Tjänsterna och servrarna på den här nivån används främst för installation och administration av programmet. Därför är enstaka instanser av dessa servrar tillräckliga. Redundanta instanser krävs inte för att programmet ska vara mycket.

Komponenterna på den här nivån är följande:
    
 - **Etableringsserver** - Den här servern används för distribution från till på information om de olika komponenterna i programmet. Den kommunicerar med instanser på de andra nivåerna, inklusive instanserna på databasnivån, över port 22. Den är värd för Server Manager Console för JD Edwards EnterpriseOne.
 - **Distributionsserver** - Den här servern krävs främst för installation av JD Edwards EnterpriseOne. Under installationsprocessen fungerar den här servern som den centrala databasen för nödvändiga filer och installationspaket. Programvaran distribueras eller distribueras till andra servrar och klienter från den här servern.
 - **Utvecklingsklient** - Den här servern innehåller komponenter som körs i en webbläsare samt inbyggda program.

### <a name="presentation-tier"></a>Presentationsnivå

Den här nivån innehåller olika komponenter som Application Interface Services (AIS), Application Development Framework (ADF) och Java Application Servers (JAS). Servrarna på den här nivån kommunicerar med servrarna på mellannivån. De frontas av en belastningsutjämnare som dirigerar trafik till den nödvändiga servern baserat på portnumret och URL:en som trafiken tas emot på. Vi rekommenderar att du distribuerar flera instanser av varje servertyp för hög tillgänglighet.

Följande är komponenterna på den här nivån:
    
- **Application Interface Services (AIS)** - AIS-servern tillhandahåller kommunikationsgränssnittet mellan JD Edwards EnterpriseOne mobila företagsprogram och JD Edwards EnterpriseOne.
- **Java Application Server (JAS)** - JAS tar emot begäranden från belastningsutjämnaren och skickar den till den mellersta nivån för att utföra komplicerade uppgifter. JAS har förmågan att utföra enkel affärslogik.
- **BI Publisher Server (BIP)** – Den här servern presenterar rapporter baserat på de data som samlas in av JD Edwards EnterpriseOne-programmet. Du kan utforma och styra hur rapporten presenterar data baserat på olika mallar.
- **Business Services Server (BSS)** – BSS möjliggör informationsutbyte och interoperabilitet med andra Oracle-program.
- **Real-Time Events Server (RTE)** - Med RTE-servern kan du ställa in meddelanden till externa system om transaktioner som sker i JDE EnterpriseOne-systemet. Den använder en prenumerantmodell och tillåter tredjepartssystem att prenumerera på evenemang. Om du vill läsa in fördelningsbegäranden till båda RTE-servrarna kontrollerar du att servrarna finns i ett kluster.
- **Application Development Framework (ADF) Server** - ADF-servern används för att köra JD Edwards EnterpriseOne-program som utvecklats med Oracle ADF. Detta distribueras på en Oracle WebLogic-server med ADF-körning.

### <a name="middle-tier"></a>Mellannivå

Den mellersta nivån innehåller logikservern och batchservern. I det här fallet installeras båda servrarna på samma virtuella dator. För produktionsscenarier rekommenderar vi dock att du distribuerar logikserver och batchserver på separata servrar. Flera servrar distribueras på mellannivå över två tillgänglighetszoner för högre tillgänglighet. En Azure belastningsutjämnare bör skapas och dessa servrar bör placeras i sin servergrupp för att säkerställa att båda servrarna är aktiva och bearbetar begäranden.

Servrarna på mellannivå får begäranden från servrarna på presentationsnivån och endast den offentliga belastningsutjämnaren. Regler för nätverkssäkerhetsgrupp måste ställas in för att neka trafik från någon annan adress än undernätet för presentationsnivå och belastningsutjämnaren. En NSG-regel kan också ställas in för att tillåta trafik på port 22 från skyddsvärden för hantering. Du kanske kan använda den offentliga belastningsutjämnaren för att läsa in balansbegäranden mellan de virtuella datorerna på mellannivå.

Följande två komponenter finns på mellannivån:

- **Logikserver** - Innehåller affärslogiken eller affärsfunktionerna.
- **Batchserver** - Används för batchbearbetning

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>Peoplesoft

Oracles PeopleSoft-programsvit innehåller programvara för mänskliga resurser och ekonomisk förvaltning. Programpaketet är flerskiktat och program inkluderar HRMS (Human Resource Management Systems), CUSTOMER Relationship Management (CRM), ekonomi och supply chain management (FSCM) och Enterprise Performance Management (EPM).

Vi rekommenderar att varje nivå i programsviten distribueras i ett eget undernät. En Oracle-databas eller Microsoft SQL Server krävs som serverningsdatabas för programmet. I det här avsnittet beskrivs information om hur du distribuerar PeopleSoft med en Oracle-databasdel.

Följande är en kanonisk arkitektur för distribution av PeopleSoft-programsviten i en molnöverskridande arkitektur (bild 5).

![PeopleSoft-arkitektur över moln](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Bild 5: PeopleSoft-arkitektur över moln*

I den här exempelarkitekturen är det virtuella nätverket i Azure anslutet till det virtuella molnnätverket i OCI med hjälp av sammankopplingen mellan moln. Programnivån har konfigurerats i Azure, medan databasen har konfigurerats i OCI. Vi rekommenderar att du distribuerar varje komponent till sitt eget undernät med nätverkssäkerhetsgrupper så att trafik endast tillåts från specifika undernät på specifika portar.

Arkitekturen kan också anpassas för distribution helt på Azure med högtillgängliga Oracle-databaser som konfigurerats med Oracle Data Guard i två tillgänglighetszoner i en region. Följande diagram (bild 6) är ett exempel på detta arkitektoniska mönster:

![PeopleSoft Azure-arkitektur](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Bild 6: Endast PeopleSoft Azure-arkitektur*

I följande avsnitt beskrivs de olika komponenterna på en hög nivå.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Programnivå

Programnivån innehåller instanser av PeopleSoft-applikationsservrarna, PeopleSofts webbservrar, elastisk sökning och PeopleSoft Process Scheduler. En Azure-belastningsutjämnare har konfigurerats för att acceptera begäranden från användare som dirigeras till rätt server på programnivån.

För hög tillgänglighet bör du överväga att konfigurera redundanta instanser av varje server i programnivån i olika tillgänglighetszoner. Azure-belastningsutjämnaren kan konfigureras med flera backend-pooler för att dirigera varje begäran till rätt server.

### <a name="peopletools-client"></a>PeopleTools-klient

PeopleTools-klienten används för att utföra administrationsaktiviteter, till exempel utveckling, migrering och uppgradering. Eftersom PeopleTools-klienten inte krävs för att uppnå hög tillgänglighet för ditt program behövs inte redundanta servrar för PeopleTools Client.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Nästa steg

Använd [Terraform-skript](https://github.com/microsoft/azure-oracle) för att konfigurera Oracle-appar i Azure och upprätta anslutning mellan moln med OCI.

Mer information och faktablad om OCI finns i [Oracle Cloud-dokumentationen.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
