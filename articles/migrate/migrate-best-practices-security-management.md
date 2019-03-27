---
title: Metodtips för att skydda och hantera arbetsbelastningar migreras till Azure | Microsoft Docs
description: När du har migrerat till Azure, hämta Metodtips för operativsystemet, hantera och skydda dina migrerade arbetsbelastningar.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: afcd180146bc349bda9375f10eb56f85f67ccb52
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498745"
---
# <a name="best-practices-for-securing-and-managing-workloads-migrated-to-azure"></a>Metodtips för att skydda och hantera arbetsbelastningar migreras till Azure

Som du planerar och utformar för migrering, förutom att tänka på migreringen, måste du överväga din modell för säkerhet och hantering i Azure efter migreringen. Den här artikeln beskrivs planering och bästa praxis för att skydda din Azure-distribution när du har migrerat och för pågående aktiviteter att hålla din distribution som körs på optimal nivå. 

> [!IMPORTANT]
> Bästa praxis och yttranden som beskrivs i den här artikeln baseras på Azure-plattformen och bearbeta funktioner som är tillgängliga vid tidpunkten för skrivning. Funktioner och möjligheter ändras med tiden.

## <a name="secure-migrated-workloads"></a>Skydda migrerade arbetsbelastningar

Efter migreringen är den mest kritiska uppgiften att skydda migrerade arbetsbelastningar från interna och externa hot. Dessa metodtips hjälper dig att göra det:

- [Arbeta med Azure Security Center](#best-practice-follow-azure-security-center-recommendations): Lär dig hur du arbetar med övervakning, utvärderingar och rekommendationer som tillhandahålls av Azure Security Center
- [Kryptera dina data](#best-practice-encrypt-data): Få metodtips för att kryptera dina data i Azure.
- [Konfigurera program mot skadlig kod](#best-practice-protect-vms-with-antimalware): Skydda dina virtuella datorer mot skadlig kod och skadliga attacker.
- [Skydda webbappar](#best-practice-secure-web-apps): Skydda känslig information i migrerade web apps.
- [Granska prenumerationer](#best-practice-review-subscriptions-and-resource-permissions): Kontrollera vem som kan komma åt din Azure-prenumerationer och resurser efter migreringen.
- [Arbeta med loggar](#best-practice-review-audit-and-security-logs): Granska dina Azure gransknings- och loggar regelbundet.
- [Granska andra säkerhetsfunktioner](#best-practice-evaluate-other-security-features): Förstå och utvärdera avancerade säkerhetsfunktioner som Azure erbjuder.

## <a name="best-practice-follow-azure-security-center-recommendations"></a>Rekommendation: Följ Azure Security Center-rekommendationer

Microsoft arbetar hårt för att säkerställa att Azure innehavaradministratörer har information som behövs för att aktivera funktioner för att skyddar arbetsbelastningar från attacker.  Azure Security Center erbjuder enhetlig säkerhetshantering. I Security Center kan du tillämpa säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Security Center analyserar resurser och konfigurationer över Azure-klienter och gör säkerhetsrekommendationer, inklusive:

- **Centraliserad principhantering** – Säkerställ att företagets eller reglerade säkerhetskrav följer standarden genom att hantera principer centralt i alla dina arbetsbelastningar i hybridmoln.
- **Kontinuerlig säkerhetsbedömning** – Övervaka säkerheten i datorer, nätverk, lagring, datatjänster och program så att du kan identifiera potentiella säkerhetsproblem.
- **Rekommendationer som kan åtgärdas** – Åtgärda säkerhetsproblem innan de kan utnyttjas av angripare med hjälp av rangordnade säkerhetsrekommendationer.
- **Rangordnade aviseringar och incidenter** – Fokusera på de mest kritiska hoten först med rangordnade säkerhetsaviseringar och incidenter.

Förutom bedömningar och rekommendationerna tillhandahåller i Security Center ett antal andra säkerhetsfunktioner som kan aktiveras för specifika resurser.

- **Precis i tid JIT-åtkomst**: Minska dina nätverkskontakter med just-in-time, kontrollerad åtkomst till hanteringsportar på virtuella Azure-datorer.
    - Med VM RDP-porten 3389 öppen på internet visar virtuella datorer till kontinuerliga obehörig aktivitet. Azure IP-adresser är välkänd och hackare avsökning kontinuerligt dem för attacker på Öppna 3389 portar. 
    - Just-in-time använder nätverkssäkerhet grupper (NSG) och inkommande regler som gräns för hur lång tid som en specifik port är öppen.
    - Med just-in-aktiveringen, kontrollerar Security Center att en användare har rollbaserad åtkomstkontroll (RBAC) skrivåtkomst åtkomstbehörigheter för en virtuell dator. Dessutom kan ange regler för hur användare kan ansluta till virtuella datorer. Om behörigheterna är OK, en åtkomstbegäran har godkänts och konfigurerar Security Center NSG: er för att tillåta inkommande trafik till de markerade portarna för den tid anger du. NSG: er kan gå tillbaka till föregående tillstånd när tiden upphör att gälla.
- **Anpassningsbara programkontroller**: Håll programvara och skadlig kod av virtuella datorer genom att styra vilka appar som körs på dem. med hjälp av dynamisk app listan över tillåtna program.
    - Anpassningsbara programkontroller kan du lista för tillåten appar och förhindra att obehöriga användare eller administratörer från att installera icke-godkända eller veta appar på dina virtuella datorer.
    - Du kan blockera eller avisering försöker köra skadliga program, undvika oönskade eller skadliga appar och säkerställa efterlevnad med din organisations säkerhetsprincip för appen.
- **Övervakning av filintegritet**: Kontrollera integriteten för filer som körs på virtuella datorer.
    - Du behöver inte installera programvara för att göra med virtuella datorer.  Ändra en fil kan även orsaka försämring av VM-fel eller prestanda.  Filen integritet övervakning undersöker systemfiler och registerinställningar för ändringar och meddelar dig om det har uppdaterats.
    - Security Center rekommenderar som du bör övervaka.


**Lära sig mer:**

- [Läs mer](https://docs.microsoft.com/azure/security-center/security-center-intro) om Azure Security Center.
- [Läs mer](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) om just-in-time-åtkomst till virtuell dator.
- [Lär dig mer om](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) tillämpa anpassningsbara programkontroller.
- [Kom igång](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring) med Filintegritetsövervakning.


## <a name="best-practice-encrypt-data"></a>Rekommendation: Kryptera data 

Kryptering är en viktig del av Azure säkerhetsrutiner. Säkerställer att kryptering är aktiverat på alla nivåer förhindrar att obehöriga personer får åtkomst till känsliga data, inklusive data under överföring och i vila. 

### <a name="encryption-for-iaas"></a>Kryptering för IaaS

- **Virtuella datorer**: Du kan använda Azure Disk Encryption för virtuella datorer för att kryptera din Windows- och Linux IaaS VM-diskar.
    - Diskkryptering använder BitLocker för Windows och DM-Crypt i Linux för volymkryptering för operativsystem och datadiskar.
    - Du kan använda en krypteringsnyckel som skapats av Azure eller ange egna krypteringsnycklar, skyddas i Azure Key Vault. 
    - Med Disk Encryption, IaaS VM-data är skyddade i vila (på disk) och under VM start. 
    - Azure Security Center varnar dig om du har virtuella datorer som inte är krypterade.
- **Storage**: Skydda vilande data som lagras i Azure storage.
    - Data som lagras i Azure storage-konton kan krypteras med Microsoft-genererade AES-nycklar som är FIPS 140-2-kompatibel eller du kan använda dina egna nycklar.
    - Kryptering av lagringstjänst är aktiverat för alla nya och befintliga lagringskonton och kan inte inaktiveras.


### <a name="encryption-for-paas"></a>Kryptering för PaaS

Till skillnad från IaaS där du hanterar dina egna virtuella datorer och infrastruktur, i en PaaS hanteras modellen plattform och infrastruktur av providern så att du kan fokusera på appen kärnlogik och funktioner. Med så många olika typer av PaaS-tjänster, kommer varje tjänst att utvärderas individuellt av säkerhetsskäl. Exempelvis kan du nu ska vi se hur vi kan aktivera kryptering för Azure SQL Database.

- **Alltid krypterad**: Använd alltid krypterad guiden i SQL Server Management Studio för att skydda data i vila.
    - Du skapar alltid krypterad nyckel för att kryptera enskilda kolumndata.
    - Alltid krypterad nycklar kan lagras krypterade i databasmetadata eller lagras i betrodda viktiga butiker, till exempel Azure Key Vault.
    - Appändringar krävs förmodligen för att använda den här funktionen.
- **Transparent datakryptering (TDE)**: Skydda Azure SQL Database med i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande.
    - TDE kan krypteringsaktiviteter ska kunna utföras utan att göra ändringar i appnivån.
    - TDE kan använda krypteringsnycklar som tillhandahålls av Microsoft eller du kan ange dina egna nycklar med stöd för Bring Your Own Key.


**Lära sig mer:**
- [Lär dig mer om](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) Azure Disk Encryption för virtuella IaaS-datorer.
- [Aktivera](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-windows) kryptering för IaaS Windows-datorer.
- [Lär dig mer om](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) Azure Storage Service Encryption för vilande data.
- [Läs](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) en översikt över Always Encrypted.
- [Läs mer om](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-2017) transparent Datakryptering för Azure SQL Database.
- [Lär dig mer om](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql) transparent Datakryptering med Bring Your Own Key.

## <a name="best-practice-protect-vms-with-antimalware"></a>Rekommendation: Skydda virtuella datorer med program mot skadlig kod

I synnerhet kanske äldre Azure migrerade virtuella datorer inte har en lämplig nivå av program mot skadlig kod installerats.  Azure tillhandahåller en kostnadsfri lösning som hjälper dig att skydda virtuella datorer från virus, spionprogram och annan skadlig kod.
- Microsoft Antimalware för Azure genererar varningar när känd skadlig eller oönskad programvara försöker installera sig själv.
- Det är en enskild agent-lösning som körs i bakgrunden utan mänsklig inblandning.
- I Azure Security Center kan du enkelt identifiera virtuella datorer som inte har slutpunktsskydd körs och installera Microsoft Antimalware efter behov.

![Program mot skadlig kod för virtuella datorer](./media/migrate-best-practices-security-management/antimalware.png)
*program mot skadlig kod för virtuella datorer*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/security/azure-security-antimalware) Microsoft program mot skadlig kod.

## <a name="best-practice-secure-web-apps"></a>Rekommendation: Skydda webbappar

Migrerade webbappar står inför ett antal problem:

- De flesta äldre webbprogram brukar ha känslig information i konfigurationsfiler.  Filer som innehåller sådan information kan uppvisa säkerhetsproblem när appar säkerhetskopieras eller när kod kontrolleras till eller från källkontroll.
- Dessutom när du migrerar webbappar som finns i en virtuell dator måste flyttar du förmodligen den datorn från ett lokalt nätverk och brandvägg-skyddade miljö till en miljö som riktas mot internet. Du måste se till att du ställer in en lösning som fungerar på samma sätt som din lokala resurser för skydd.


Azure tillhandahåller ett antal lösningar:

- **Azure Key Vault**: Idag tar appen webbutvecklare åtgärder för att säkerställa att känslig information inte sprids från dessa filer. En metod för att skydda information är att extrahera från filer och placerar den i ett Azure Key Vault.
    - Du kan använda Key Vault för att centralisera lagring av apphemligheter och styra spridningen. Så sätt undviker du att behöva lagra säkerhetsinformation i appfiler.
    - Appar kan åtkomst säkerhetsinformation i valvet med hjälp av URI: er, utan att behöva anpassad kod.
    - Azure Key Vault kan du låsa åtkomst via Azures säkerhetskontroller och smidigt implementera ”nycklar”. Microsoft varken ser eller extraherar dina data.
- **App Service Environment**: Om en app som du migrerar behöver extra skydd kan överväga du att lägga till en App Service-miljö och en brandvägg för webbaserade program för att skydda appens resurser.
    - Azure App Service Environment är en helt isolerad och dedikerad miljö där att köra App Service-appar som Windows och Linux-webbappar, Docker-behållare, mobila appar och funktioner.
    - Det är användbart för appar som är mycket hög skala, kräver isolering och säkert nätverk komma åt eller har hög minnesanvändning
- **Brandvägg för webbaserade program**: En funktion i Application Gateway som ger ett centraliserat skydd för web apps.
    - Det skyddar webbappar utan att kodändringar i serverdelen.
    - Det skyddar flera webbprogram samtidigt bakom en Programgateway.
    - Brandvägg för webbaserade program kan övervakas med Azure Monitor och är integrerat i Azure Security Center.



![Skydda webbappar](./media/migrate-best-practices-security-management/web-apps.png)
*Azure Key Vault*

**Lära sig mer:**

- [Få en översikt](https://docs.microsoft.com/azure/key-vault/key-vault-overview) av Azure Key Vault.
- [Lär dig mer om](https://docs.microsoft.com/azure/application-gateway/waf-overview) Brandvägg för webbaserade program.
- [Få en introduktion](https://docs.microsoft.com/azure/app-service/environment/intro) till App Service-miljöer.
- [Lär dig hur du](https://docs.microsoft.com/azure/key-vault/tutorial-web-application-keyvault) konfigurera en webbapp för att läsa hemligheter från Nyckelvalvet.
- [Lär dig mer om](https://docs.microsoft.com/azure/application-gateway/waf-overview) Brandvägg för webbaserade program

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>Rekommendation: Granska prenumerationer och resursbehörigheter

När du migrerar dina arbetsbelastningar och kör dem i Azure, personal tillgång arbetsbelastning flyttas runt. Ditt säkerhetsteam bör granska åtkomst till din Azure-klient och resursgrupper med jämna mellanrum. Azure tillhandahåller ett antal erbjudanden för Identitetshantering och säkerhet för åtkomstkontroll, inklusive rollbaserad åtkomstkontroll (RBAC) för att ge behörighet att komma åt Azure-resurser.

- RBAC tilldelar åtkomstbehörighet för säkerhetsobjekt. Säkerhetsobjekt representerar användare, grupper (en uppsättning användare), tjänsthuvudnamn (identitet som används av appar och tjänster) och hanterade identiteter (en Azure Active Directory-identitet hanteras automatiskt av Azure).
- RBAC kan tilldela roller till säkerhetsprinciper, till exempel ägare, deltagare och läsare och rollen definitioner (en samling av behörigheter) som definierar vilka åtgärder som kan utföras av rollerna.
- RBAC kan också ange omfång som anger gränsen för en roll. Omfånget kan anges på flera nivåer, inklusive en hanteringsgrupp, prenumeration, resursgrupp eller resurs
- Se till att administratörer med Azure åtkomst endast kan komma åt resurser som du vill tillåta.  Om de fördefinierade rollerna i Azure inte är tillräckligt detaljerad kan skapa du anpassade roller för att avgränsa och begränsa åtkomstbehörighet.

![Åtkomstkontroll](./media/migrate-best-practices-security-management/subscription.png)
*åtkomstkontroll - IAM*

**Lära sig mer:**

- [Om](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC.
- [Lär dig](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) att hantera åtkomst med RBAC och Azure-portalen.
- [Lär dig mer om](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) anpassade roller.

## <a name="best-practice-review-audit-and-security-logs"></a>Rekommendation: Granska loggarna för gransknings- och säkerhet

Azure Active Directory (AD) innehåller aktivitetsloggar som visas i Azure Monitor. Loggarna avbilda de åtgärder som utförs i Azure innehavare, när de inträffar och vem som utfört dem. 

- Granskningsloggar visar historiken för uppgifter i klienten. Inloggningsaktivitet loggar show som utfört uppgifterna. 
- Åtkomst till säkerhetsrapporter beror på din Azure AD-licens. På den kostnadsfria och grundläggande kan du hämta en lista över riskfyllda användare och inloggningar. I Premium 1 och 2 för Premium-versionerna får du underliggande händelseinformation.
- Du kan dirigera aktivitetsloggar till ett antal slutpunkter för långsiktig kvarhållning och insikter från data.
- Är det vanligt att granska loggarna eller integrera din säkerhet och händelsehantering (SIEM) hanteringsverktyg för att granska automatiskt avvikelser.  Om du inte använder Premium 1 eller 2, kommer du behöva göra mycket analys dig själv eller med din SIEM-system.  Analysis innehåller söker riskfyllda inloggningar och händelser och andra användare angreppsmönster.


![Användare och grupper](./media/migrate-best-practices-security-management/azure-ad.png)
*Azure AD-användare och grupper*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) Azure AD-aktivitetsloggar i Azure Monitor.
- [Lär dig hur du](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs) granska aktivitetsrapporter i Azure AD-portalen.

## <a name="best-practice-evaluate-other-security-features"></a>Rekommendation: Utvärdera andra säkerhetsfunktioner

Azure tillhandahåller ett antal andra säkerhetsfunktioner som tillhandahåller alternativ för avancerad säkerhet. Vissa av dessa bästa metoder kräver premium-alternativ och tilläggslicenser.

- **Implementera Azure AD administrativa enheter (AU)**: Delegera administrativa uppgifter för supportpersonal kan vara svårt med bara grundläggande Azure access control.  Ge stöd för personal tillgång till att administrera alla grupper i Azure AD kanske inte är det perfekta sättet för företagssäkerhet.  Med hjälp av automatiska uppdateringar kan du särskilja Azure-resurser i behållare på ett liknande sätt till lokala organisationsenheter (OU).  Om du vill använda Australien måste AU-administratören ha en Azure AD premium-licens. [Läs mer](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).
- **Använd multifaktorautentisering (MFA)**: Om du har en premium Azure AD-licens kan du aktivera och använda MFA på din administratörskonton. Nätfiske är det vanligaste sättet att konton autentiseringsuppgifter har komprometterats.  När någon obehörig har administratörsautentiseringsuppgifter för kontot, finns det ingen stoppar dem från omvälvande åtgärder, till exempel ta bort alla resursgrupper. Du kan konfigurera MFA på flera olika sätt, t.ex. med e-post, authenticator-appen och phone textmeddelanden. Som administratör kan du välja alternativet minst påträngande. MFA kan integreras med hotanalys och principer för villkorlig åtkomst till slumpmässigt kräver en MFA-kontrollen svara. Läs mer om [säkerhetsvägledning](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices), och [hur du ställer in](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) MFA.
- **Implementera villkorlig åtkomst**: I de flesta små och medelstora organisationer finns förmodligen Azure administratörer och supporten i ett enda geografiskt område. I det här fallet kommer de flesta inloggningar från samma områden. Om det är ganska statiska IP-adresserna för de här platserna kan det vara bra att du inte nog administratör inloggningar från utanför dessa områden. Även i en händelse som en fjärransluten obehörig komprometterar autentiseringsuppgifter som en administratör, kan du implementera säkerhetsfunktioner, t.ex. villkorlig åtkomst i kombination med MFA för att förhindra att inloggningen från fjärranslutna platser eller från falska platser från slumpmässiga IP-adresser. [Läs mer](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) om villkorlig åtkomst och [metodtips](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices) för villkorlig åtkomst i Azure AD.
- **Granska företagsprogram behörigheter**: Framöver kommer administratörer klickar du på Microsoft och tredje parts länkar utan att känna till deras inverkan på organisationen. Länkar kan ge samtycke skärmar som tilldela behörigheter till Azure-appar och kan tillåta åtkomst till läsa Azure AD-data, eller till och med fullständig åtkomst till att hantera hela Azure-prenumerationen. Du bör regelbundet läsa de appar som dina administratörer och användare har åtkomst till Azure-resurser. Du bör se till att de här apparna har de behörigheter som krävs. Dessutom kvartalsvis eller årligen semikolonavgränsade kan e-användare med en länk till appen sidor så att de är medvetna om de appar som de har åtkomst till sina organisationsdata. [Läs mer](https://docs.microsoft.com/azure/active-directory/manage-apps/application-types) om programtyper, och [kontrollerar](https://docs.microsoft.com/azure/active-directory/manage-apps/remove-user-or-group-access-portal) apptilldelningar i Azure AD.



## <a name="managed-migrated-workloads"></a>Hanterade migrerade arbetsbelastningar

I det här avsnittet rekommenderar vi några Metodtips för hantering av Azure, inklusive:

- [Hantera resurser](#best-practice-name-resource-groups): Metodtips för Azure-resursgrupper och resurser, inklusive smarta namngivning, förhindrar oavsiktlig borttagning, hantera resursbehörighet och effektiva resurstaggning.
- [Använda skisser](#best-practice-implement-blueprints): Få en snabb översikt över med skisser för att skapa och hantera dina distributionsmiljöer.
- [Granska arkitekturer](#best-practice-review-azure-reference-architectures): Granska exempel på Azure-arkitekturer att lära sig från när du skapar distributionen efter migreringen.
- [Konfigurera hanteringsgrupper](#best-practice-manage-resources-with-management-groups): Om du har flera prenumerationer kan du samla in dem i hanteringsgrupper och tillämpa inställningar för styrning i dessa grupper.
- [Konfigurera åtkomstprinciper](#best-practice-deploy-azure-policy): Tillämpa efterlevnadsprinciper för dina Azure-resurser.
- [Implementera en BCDR-strategi](#best-practice-implement-a-bcdr-strategy): Sätta ihop en disaster recovery (BCDR) strategi för affärskontinuitet och att data skyddas, din miljö som är flexibel och resurser och körs vid avbrott.
- [Hantera virtuella datorer](#best-practice-use-managed-disks-and-availability-sets): Gruppera virtuella datorer i Tillgänglighetsgrupper för ökad flexibilitet och hög tillgänglighet. Använda hanterade diskar för att underlätta disk och hanteringen av virtuella datorer.
- [Övervaka Resursanvändning](#best-practice-monitor-resource-usage-and-performance): Aktivera Diagnostisk loggning för Azure-resurser, skapa aviseringar och spelböcker för proaktiv felsökning och använda Azure-instrumentpanelen för en enhetlig vy över din distribution hälsa och status.
- [Hantera support och uppdateringar](#best-practice-manage-updates): Förstå din Azure-supportplan och hur du implementerar den, få metodtips för att hålla datorer uppdaterade och put processer för ändringshantering.


## <a name="best-practice-name-resource-groups"></a>Rekommendation: Namn på resursgrupper

Att säkerställa att dina resursgrupper har meningsfulla namn som administratörer och supportteamets medlemmar kan enkelt identifiera och navigera förbättrar drastiskt produktiviteten och effektiviteten.
- Vi rekommenderar att du följer namngivningskonventionerna Azure.
- Om du synkroniserar din lokala AD DS till Azure AD med hjälp av AD Connect du överväga att matcha namnen på security grupper mellan lokala och namnen på resursgrupper i Azure.

![Namnge](./media/migrate-best-practices-security-management/naming.png)
*namngivning av resurs-grupp*


**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) namngivningskonventioner

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>Rekommendation: Implementera ta bort lås för resursgrupper

Det är det sista du behöver för en resursgrupp att försvinna eftersom den togs bort av misstag. Vi rekommenderar att du implementera ta bort lås så att det inte inträffar.


![Ta bort lås](./media/migrate-best-practices-security-management/locks.png)
*ta bort lås*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) låsa resurser för att förhindra oväntade ändringar.


## <a name="best-practice-understand-resource-access-permissions"></a>Rekommendation: Förstå resource åtkomstbehörigheter 

En Prenumerationens ägare har åtkomst till alla resursgrupper och resurser i din prenumeration.
- Lägg till personer sparsamt i den här värdefulla tilldelningen. Det är viktigt i att skydda din miljö, säkra och stabila att förstå konsekvenserna av dessa typer av behörigheter.
- Kontrollera att du placera resurser i lämpliga resurser för grupper:
    - Matcha resurser med liknande livscykel tillsammans. Vi rekommenderar behöver du inte flytta en resurs när du vill ta bort en hela resursgruppen.
    - Resurser som har stöd för en funktion eller en arbetsbelastning ska placeras tillsammans för förenklad hantering.

**Lära sig mer:**

- [Lär dig mer om](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/) ordna prenumerationer och resursgrupper.

## <a name="best-practice-tag-resources-effectively"></a>Rekommendation: Tagga resurser effektivt

Ofta, finns med bara ett Resursgruppsnamn rör resurser inte tillräckligt med metadata för effektiv implementering av mekanismer som t.ex interna fakturering eller hantering inom en prenumeration.
- Som bästa praxis bör du använda Azure taggar för att lägga till användbara metadata som kan efterfrågas och rapporteras. 
- Taggar är ett sätt att organisera resurser med egenskaper som du definierar logiskt.  Taggar kan användas till resursgrupper eller resurser direkt.
- Taggar kan tillämpas på en resursgrupp eller på enskilda resurser. Gruppen resurstaggar överförs inte av resurser i gruppen.
- Du kan automatisera taggning med hjälp av Powershell eller Azure Automation, eller tagg enskilda grupper och resurser. -taggning metod eller en självbetjäning.  Om du har en begäran och ändra nuvarande hanteringssystemet, kan du enkelt använda informationen i förfrågan om att fylla i din företagsspecifika resurstaggar.


![Tagga](./media/migrate-best-practices-security-management/tagging.png)
*taggning*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) taggning och tagga begränsningar.
- [Granska](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#powershell) PowerShell och CLI-exempel att ställa in taggar och för att lägga till taggar från en resursgrupp på dess resurser.
- [Läs](https://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices/) Azure taggning bästa praxis.


## <a name="best-practice-implement-blueprints"></a>Rekommendation: Implementera skisser

Precis som när skissen tillåter ingenjörer och arkitekter att skiss ett projekts design parametrar, aktivera Azure skisser molnarkitekter och centrala IT-grupper att definiera en upprepningsbara uppsättning Azure-resurser som implementerar och tillämpar en organisations standarder mönster och krav. Med hjälp av Azure skisser utvecklingsteam kan snabbt skapa och skapa nya miljöer som uppfyller företagets efterlevnadskrav, och som har en uppsättning inbyggda komponenter, till exempel nätverk, för att påskynda utvecklingen och leverans.

- Använd skisser för att dirigera distribution av resursgrupper, Azure Resource Manager-mallar och principer och rollen tilldelningar.
- Azure skisser lagras i ett globalt distribuerade Azure Cosmos DB. Skissobjekt replikeras till flera Azure-regioner. Replikering ger mindre fördröjning, hög tillgänglighet och konsekvent åtkomst till skiss, oavsett den region som distribuerar en skiss resurser.

**Lära sig mer:**

- [Läs](https://docs.microsoft.com/azure/governance/blueprints/overview) om skisser.
- [Granska](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare/) en skiss exempel som används för att påskynda AI inom hälsovården.

## <a name="best-practice-review-azure-reference-architectures"></a>Rekommendation: Granska Azure-referensarkitekturer

Att skapa säkra, skalbara och hanterbara arbetsbelastningar i Azure kan kännas avskräckande.  Med kontinuerliga ändringar kan det vara svårt att hålla jämna steg med olika funktioner för en optimal miljö. En referens till Lär dig från kan vara till hjälp när du utformar och migrera dina arbetsbelastningar.  Azure och Azure partner har skapat flera exempel referensarkitekturer för olika typer av miljöer. De här exemplen är utformad att ge förslag som du kan läsa från och bygger på. 

Referensarkitekturer ordnas efter scenario. De innehåller rekommenderad praxis och råd om hantering, tillgänglighet, skalbarhet och säkerhet.
Azure App Service Environment ger en helt isolerad och dedikerad miljö där du kan köra App Service-appar, inklusive Windows och Linux-webbappar, Docker-behållare, mobilappar och funktioner. App Service lägger till kraften hos Azure i ditt program med säkerhet, belastningsutjämning, automatisk skalning och automatiserad hantering. Du kan också dra nytta av dess DevOps-funktioner, till exempel kontinuerlig distribution från Azure DevOps och GitHub, pakethantering, miljöer, anpassad domän och SSL-certifikat. App Service är användbart för appar som behöver isolering och säker nätverksåtkomst och de som använder hög mängder minne och andra resurser som behöver skala.
**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/architecture/reference-architectures/) Azure-referensarkitekturer.
- [Granska](https://docs.microsoft.com/azure/architecture/example-scenario/) exempel på Azure-scenarier.

## <a name="best-practice-manage-resources-with-management-groups"></a>Rekommendation: Hantera resurser med Hanteringsgrupper

Om din organisation har flera prenumerationer, som du behöver hantera åtkomst, principer och efterlevnad för dessa. Med Azures hanteringsgrupper får du en hanteringsnivå över prenumerationsnivån.

- Du organiserar prenumerationer i behållare som kallas hanteringsgrupper och tillämpa styrningsåtgärder villkor.
- Villkor för hantering av grupp ärver automatiskt i alla prenumerationer i en hanteringsgrupp.
- Hanteringsgrupper ger hantering av företagsklass i stor skala, oavsett vilken typ av prenumerationer som du har.
- Du kan till exempel använda en princip för hantering av grupp som begränsar de regioner där virtuella datorer kan skapas. Den här principen tillämpas sedan på alla hanteringsgrupper, prenumerationer och resurser under den hanteringsgruppen.
- Du kan skapa en flexibel struktur för hanteringsgrupper och prenumerationer, för att organisera dina resurser i en hierarki för enhetlig princip- och åtkomsthantering.

Följande diagram visar ett exempel på att skapa en hierarki för styrning med hjälp av hanteringsgrupper.

![Hanteringsgrupper](./media/migrate-best-practices-security-management/management-groups.png)
*hanteringsgrupper*

**Lära sig mer:**
- [Läs mer](https://docs.microsoft.com/azure/governance/management-groups/index) om att ordna resurser till hanteringsgrupper.

## <a name="best-practice-deploy-azure-policy"></a>Rekommendation: Distribuera Azure Policy

Azure Policy är en tjänst i Azure som används för att skapa, tilldela och hantera principer.

- Principer tillämpar olika regler och effekterna på resurserna, så att resurserna följer företagets standarder och serviceavtal.
- Azure Policy utvärderar dina resurser, söker efter sådana som inte är kompatibla med företagets säkerhetsprinciper.
- Du kan till exempel skapa en princip som tillåter bara en viss SKU-storlek för virtuella datorer i din miljö. Azure Policy kommer att utvärdera den här inställningen när du skapar och uppdaterar resurser, och när du skannar befintliga resurser. 
- Azure erbjuder några inbyggda principer som du kan tilldela eller skapa egna.


![Azure Policy](./media/migrate-best-practices-security-management/policy.png)
*Azure Policy*

**Lära sig mer:**
- [Få en översikt](https://docs.microsoft.com/azure/governance/policy/overview) över Azure Policy.
- [Lär dig mer om](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) skapa och hantera principer för att tvinga kompatibilitet.


## <a name="best-practice-implement-a-bcdr-strategy"></a>Rekommendation: Implementera en BCDR-strategi

Planera för affärskontinuitet och haveriberedskap (BCDR) är en kritisk Övning som du måste utföra när du planerar för migrering till Azure. I juridiska villkor innehåller en force majeure-instruktion som orsak skyldigheterna gentemot en större kraft, till exempel orkaner eller jordbävningar i ditt avtal. Du kan dock också skyldigheter runt en möjlighet att se till att tjänster fortsätter att köra och återställa vid behov om haveriberedskap bankerna. Du kan göra detta kan se eller dela ditt företags framtid.

Brett, din BCDR-strategi måste tänka på:
- **Säkerhetskopiering av data**: Hur du skyddar dina data så att du kan återställa den enkelt om avbrott inträffar.
- **Haveriberedskap**: Så här att hålla dina appar, flexibla och tillgängliga om avbrott inträffar. 

### <a name="azure-resiliency-features"></a>Återhämtningsfunktioner i Azure
Azure-plattformen innehåller ett antal återhämtningsfunktioner.

- **Regionen parkoppling**: Azure-par regioner för att skydda regionala inom gränserna för datahemvist av data. Azure säkerställer fysisk isolering mellan regionpar, prioriterar återställning av en av regionerna i paret vid avbrott i, distribuerar systemuppdateringar separat i varje region och gör att funktioner, till exempel Azure geo-redundant lagring att replikeras till den regionala par.
- **Tillgänglighetszoner**: Tillgänglighetszoner skydd mot fel på ett helt Azure datacenter genom att etablera fysiska separata zoner med en Azure-region. Varje zon har ett tydligt programvarusystem strömkälla, nätverkets infrastruktur och kylning mekanism.
- **Tillgänglighetsuppsättningar**: Tillgänglighetsuppsättningar skydd mot fel på ett datacenter. Du kan gruppera virtuella datorer i tillgänglighetsuppsättningar för att hålla dem med hög tillgänglighet. I varje tillgänglighetsuppsättning implementerar Azure flera feldomäner gruppen tillsammans underliggande maskinvara med en gemensam strömkälla och nätverksswitch uppdateringsdomäner som grupperar underliggande maskinvara som kan underhållas eller startas om, på samma gång. Till exempel när en arbetsbelastning fördelas mellan virtuella Azure-datorer kan du placera två eller flera virtuella datorer för varje app-nivå i en uppsättning. Du kan till exempel placera klientdelens virtuella datorer i en uppsättning och datanivå virtuella datorer i en annan. Eftersom endast en uppdateringsdomän är varje startas om samtidigt i en mängd och Azure ser till att virtuella datorer i en uppsättning fördelade över feldomäner kan du se till att inte alla virtuella datorer i en uppsättning ansluts inte på samma gång.

### <a name="set-up-bcdr"></a>Konfigurera BCDR

När du migrerar till Azure, är det viktigt att veta att du behöver skapa din Azure-distribution att dra nytta av Azures funktioner och tjänster som ger hög tillgänglighet, även om Azure-plattformen ger dessa funktioner för inbyggd återhämtning, haveriberedskap och säkerhetskopiering.

- Din BCDR-lösning beror dina mål för företag och påverkas av din strategi för Azure-distribution. Infrastruktur som en tjänst (IaaS) och plattform som en tjänst (PaaS)-distributioner finns olika utmaningar för BCDR.
- En gång på plats, din BCDR-lösningar bör testas regelbundet kontrollera att din strategi för ska vara användbara.


## <a name="best-practice-back-up-your-data"></a>Rekommendation: Säkerhetskopiera data

I de flesta fall en lokal arbetsbelastning har dragits tillbaka efter migreringen och din lokala strategi för säkerhetskopiering av data måste förlängas eller ersatts. Om du migrerar hela ditt datacenter till Azure måste du utforma och implementera en lösning för fullständig säkerhetskopiering med hjälp av Azure-tekniker eller tredje parts integrerade lösningar. 


### <a name="back-up-an-iaas-deployment"></a>Säkerhetskopiera en IaaS-distribution

Överväg att dessa säkerhetskopieringslösningar för arbetsbelastningar som körs på virtuella Azure IaaS-datorer:

- **Azure Backup**: Innehåller programkonsekventa säkerhetskopior för Azure Windows och Linux-datorer.
- **Ögonblicksbilder av lagring**: Ta ögonblicksbilder av blob-lagring.

#### <a name="azure-backup"></a>Azure Backup

Azure Backup säkerhetskopierar skapar återställningspunkter för data som lagras i Azure storage. Azure Backup kan säkerhetskopiera Virtuella Azure-diskar och Azure Files (förhandsversion). Azure Files tillhandahåller filresurser i molnet, tillgängliga via SMB.
   
Du kan använda Azure Backup för att säkerhetskopiera virtuella datorer i ett par olika sätt.

- **Direkt säkerhetskopiering från inställningar för virtuell dator**: Du kan säkerhetskopiera virtuella datorer med Azure Backup direkt från VM-alternativ i Azure-portalen. Du kan säkerhetskopiera den virtuella datorn en gång och dag och återställa den Virtuella datordisken efter behov. Azure Backup har tagit app-medvetna dataögonblicksbilder (VSS), ingen agent är installerad på den virtuella datorn.
- **Direkt säkerhetskopiering i Recovery Services-valvet**: Du kan säkerhetskopiera dina virtuella IaaS-datorer genom att distribuera ett Azure Backup Recovery Services-valv. Detta ger en enda plats för att spåra och hantera säkerhetskopior och innehåller detaljerade alternativ för säkerhetskopiering och återställning. Säkerhetskopiering har upp till tre gånger per dag, fil/mapp-nivå. Det är inte medveten om app och Linux stöds inte. Du måste installera Microsoft Azure Recovery Services MARS-agenten på varje virtuell dator som du vill säkerhetskopiera.
- **Azure Backup Server: Skydda den virtuella datorn till Azure Backup Server**: Azure Backup Server tillhandahålls kostnadsfritt med Azure Backup. Den virtuella datorn har säkerhetskopierats till lokal lagring i Azure Backup Server. Du sedan säkerhetskopiera Azure Backup Server till Azure i ett valv. Backup är app-medveten, med fullständig granularitet över ofta säkerhetskopiering och kvarhållning. Du kan säkerhetskopiera på appnivå. Till exempel genom att säkerhetskopiera SQL Server eller SharePoint.

För säkerhet, Azure Backup krypterar data pågående med hjälp av AES-256 och skickar dem via HTTPS till Azure. Säkerhetskopierade data i vila i Azure krypteras med [Storage Service Encryption (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), och data för överföring och lagring.


![Azure Backup](./media/migrate-best-practices-security-management/iaas-backup.png)
*Azure Backup*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) olika typer av säkerhetskopior.
- [Planera en infrastruktur för säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) för virtuella Azure-datorer.

#### <a name="storage-snapshots"></a>Ögonblicksbilder av lagring

Virtuella Azure-datorer lagras som sidblobar i Azure Storage. 

- Ögonblicksbilder avbilda blob-tillstånd vid en viss tidpunkt.
- Som en alternativ metod för säkerhetskopiering för Virtuella Azure-diskar, kan du ta en ögonblicksbild av storage-blobbar och kopiera dem till ett annat lagringskonto. 
- Du kan kopiera en hel blob eller använda en inkrementell ögonblicksbild kopia kopiera endast deltaändringar och minska lagringsutrymmet.
- Du kan aktivera mjuk borttagning för blob storage-konton som en extra försiktighetsåtgärd. Den här funktionen är aktiverad är en blob tas bort markerats för borttagning men inte omedelbart rensas. Under övergångsperioden kan blob återställas.

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Azure blob-lagring.
- [Lär dig hur du](https://docs.microsoft.com/azure/storage/blobs/storage-blob-snapshots) skapa en blobögonblicksbild.
- [Granska ett exempelscenario](https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup) för blob storage-säkerhetskopiering.
- [Läs mer om](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) mjuk borttagning.
- [Haveriberedskap och forcerad redundans (förhandsversion) i Azure Storage](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

#### <a name="third-party-backup"></a>Säkerhetskopiering från tredje part

Du kan dessutom använda lösningar från tredje part för att säkerhetskopiera virtuella Azure-datorer och storage-behållare till lokal lagring eller andra molnleverantörer. [Läs mer](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1) om lösningar för säkerhetskopiering i Azure marketplace. 


### <a name="back-up-a-paas-deployment"></a>Säkerhetskopiera en PaaS-distribution


Till skillnad från IaaS där du hanterar dina egna virtuella datorer och infrastruktur, i en PaaS hanteras modellen plattform och infrastruktur av providern så att du kan fokusera på appen kärnlogik och funktioner. Med så många olika typer av PaaS-tjänster utvärderas varje tjänst individuellt för säkerhetskopieringar. Vi ska titta på två vanliga Azure PaaS-tjänster – Azure SQL Database och Azure Functions.

#### <a name="back-up-azure-sql-database"></a>Säkerhetskopiera Azure SQL Database

Azure SQL Database är en fullständigt hanterad PaaS-databasmotorn. Den innehåller flera av funktioner för affärskontinuitet, inklusive automatisera säkerhetskopiering.

- SQL Database utför automatiskt veckovisa fullständiga databassäkerhetskopieringar och differentiella säkerhetskopieringar var 12: e timme. Säkerhetskopieringar av transaktionsloggen kommer alla fem till tio minuter till att skydda databasen mot dataförlust.
- Säkerhetskopior är transparent och leder inte till extra kostnad.
- Säkerhetskopior lagras i RA-GRS-lagring för geo-redundans, och replikeras till den länkade geografiska regionen.
- Kvarhållning av säkerhetskopior är beroende av inköpsmodellen. DTU-baserade tjänstnivåer gå från sju dagar för Basic-nivån till 35 dagar för andra nivåer.
- Du kan återställa en databas till point-in-time inom kvarhållningsperioden. Du kan även återställa en borttagen databas, Återställ till en annan geografisk region eller Återställ från en långsiktig säkerhetskopia om databasen har en principen för långsiktig kvarhållning av säkerhetskopior (LTR).


![Azure SQL-säkerhetskopiering](./media/migrate-best-practices-security-management/sql-backup.png)
*Azure SQL-säkerhetskopiering*

**Lära sig mer:**
- [Automatiska säkerhetskopior](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) för SQL-databas.
- [Återställa en databas](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) med hjälp av automatisk säkerhetskopiering.

 
#### <a name="back-up-azure-functions"></a>Säkerhetskopiera Azure Functions

Eftersom Azure Functions fungerar mer eller mindre som kod, bör du säkerhetskopiera dem på samma sätt som du använder för att skydda kod som till exempel källkodskontroll i GitHub eller Azure DevOps-tjänsterna

**Lära sig mer:**

- [Dataskydd](/azure/devops/organizations/security/data-protection) för Azure DevOps.

## <a name="best-practice-set-up-disaster-recovery"></a>Rekommendation: Konfigurera haveriberedskap 

Utöver att skydda data, betrakta BCDR planera så att appar och arbetsbelastningar som är tillgängliga i händelse av katastrof. 

### <a name="set-up-disaster-recovery-for-iaas-apps"></a>Konfigurera haveriberedskap för IaaS-appar

Överväg att dessa lösningar för arbetsbelastningar som körs på virtuella Azure IaaS-datorer och Azure storage:

- **Azure Site Recovery**: Arrangerar replikeringen av virtuella Azure-datorer i en primär till en sekundär region. Vid avbrott så växlar du över från den primära regionen till sekundärt och användarna kan fortsätta att komma åt appar. När saker är tillbaka till normal kan växla du tillbaka till den primära regionen.
- **Azure storage**: Azure tillhandahåller i build ökad flexibilitet och hög tillgänglighet för olika typer av lagring:

#### <a name="azure-site-recovery"></a>Azure Site Recovery 

Azure Site Recovery är den primära Azure-tjänst för att säkerställa som virtuella Azure-datorer kan anslutas och VM appar som har gjorts tillgängliga vid avbrott. Site Recovery replikerar virtuella datorer från en primär till sekundär Azure-region. När en katastrof inträffar du redundansväxlar virtuella datorer från den primära regionen och fortsatt åtkomst till dem som vanligt i den sekundära regionen. När operations återgår till normal kan du växla tillbaka virtuella datorer till den primära regionen.


![Azure Site Recovery](./media/migrate-best-practices-security-management/site-recovery.png)
*Site Recovery*

**Lära sig mer:**
- [Granska](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-disaster-recovery-guidance) scenarier för haveriberedskap för virtuella Azure-datorer.
- [Lär dig hur du](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-replicate-after-migration) konfigurera haveriberedskap för en Azure virtuell dator efter migreringen.

#### <a name="azure-storage"></a>Azure-lagring

Azure-lagringsutrymmet replikeras för inbyggda ökad flexibilitet och hög tillgänglighet.

-   **GEO-redundant lagring (GRS)**: Skyddar mot avbrott i ett regionomfattande, med minst 99,99999999999999% (16 9) objektshållbarhet under ett givet år.
    - Storage-data replikeras till den sekundära regionen som din primära region paras ihop.
    - Om den primära regionen slutar fungera och Microsoft initierar en växling till den sekundära regionen, kommer du ha läsbehörighet till dina data.
- **Geo-redundant lagring med läsbehörighet (RA-GRS)**: Skyddar mot avbrott i ett inom hela regionen.
    - Storage-data replikeras till den sekundära regionen.
    - Du har säkert läsåtkomst till replikerade data i den andra regionen, oavsett om huruvida Microsoft initierar en redundansväxling. där data som minst två datacenter i samma region kan ha problem än dina data finns kvar i en annan region.
-   **Zonredundant lagring (ZRS)**:  Skyddar mot datacenterfel.
    - ZRS replikerar data synkront i tre lagringskluster i en enda region. Kluster och fysiskt avgränsade och var och en finns i sin egen tillgänglighetszon.
    - Om en katastrof inträffar, kan din lagring fortfarande tillgängliga. ZRS ska vara det lägsta målet för verksamhetskritiska arbetsbelastningar.



**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/storage/common/storage-redundancy) Azure storage-replikering.


### <a name="set-up-disaster-recovery-for-paas-workloads"></a>Konfigurera haveriberedskap för arbetsbelastningar för PaaS

Anta att du haveriberedskap återställningsalternativ för våra exempel för PaaS-arbetsbelastning.

#### <a name="disaster-recovery-of-azure-sql-server"></a>Haveriberedskap för Azure SQL-Server

Det finns ett antal olika alternativ, påverkar var och en förlust av data, tiden för återställning och kostnad.

Du kan använda redundansgrupper och aktiv geo-replikering för att ge elasticitet mot regionala avbrott och kritiska fel

- **Aktiv geo-replikering**: Distribuera aktiv geo-replikering för snabb katastrofåterställning om ett datacenter-avbrott inträffar, eller om det går inte att upprätta en anslutning till en primär databas.
    - GEO-replikering skapar kontinuerligt läsbara repliker av databasen i upp till fyra sekundära databaser i samma eller olika regioner.
    - I ett avbrott växla över till en av de sekundära regionerna och ta databasen online igen.
- **Automatisk redundans grupper**: Automatisk redundans grupper utöka aktiv geo-replikering med transparent redundans för flera databaser.
    - En automatisk redundans-gruppen ger en kraftfull abstraktion av aktiv geo-replikering med en databas på replikering och automatisk redundans.
    - Du kan skapa en redundansgrupp som innehåller en primär server som är värd för en eller flera primära, en sekundär server som är värd för skrivskyddade repliker av de primära databaserna, lyssnare som pekar på varje server och en princip för automatisk redundans.
    - De angivna lyssnaren slutpunkterna ta bort behovet av att ändra SQL-anslutningssträng efter en redundansväxling.
- **GEO-återställning**: 
    -   GEO-återställning kan du återställa databasen till en annan region. Automatisk säkerhetskopiering för alla Azure-databaser kommer att replikeras till en sekundär region i bakgrunden. Det kommer alltid att återställa databasen från kopian av säkerhetskopierade filer som lagras i den sekundära regionen.
-   **Zonredundanta databaser** ger inbyggt stöd för tillgänglighetszoner i Azure.
    - Zonredundanta databaser används för att förbättra tillgängligheten för Azure SQL Server vid data center fel.
    - Med redundans, kan du placera redundant databasrepliker i olika tillgänglighetszoner i en region. 



![GEO-replikering](./media/migrate-best-practices-security-management/geo-replication.png)
*Geo-replikering*

**Lära sig mer:**
- [Lär dig mer om](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability) hög tillgänglighet för Azure SQL Server.
- [Läs](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/) Azure SQL-databaser 101 för katastrofåterställning.
- [Få en översikt](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) i grupperna active geo-replikering och redundans.
- [Lär dig mer om](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery) utformning för katastrofåterställning.
- [Få metodtips](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) för redundansgrupper.
- [Få metodtips](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-security-config) för säkerhet när geo-återställning eller redundans.
- [Lär dig mer om](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability#zone-redundant-configuration) zon redundans
- [Lär dig hur du](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-drills) utföra ett programåterställningstest för SQL-databas.

### <a name="disaster-recovery-for-azure-functions"></a>Haveriberedskap för Azure Functions

Om det inte går att datainfrastruktur i Azure, kan Azure Functions-app bli otillgänglig.

- För att minimera risken för sådana avbrott, använder du två funktionsappar som distribueras till olika regioner.
- Med Azure Traffic Manager kan konfigureras för att identifiera problem med primära funktionsappen och automatiskt dirigera trafik till funktionsappen i den sekundära regionen
- Traffic Manager med geo-redundant lagring kan du ha samma funktion i flera regioner vid regionala fel


![Traffic Manager](./media/migrate-best-practices-security-management/traffic-manager.png)
*Traffic Manager*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) haveriberedskap för Azure-appar.
- [Lär dig mer om](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution) haveriberedskap och geo-replikering för varaktiga funktioner i Azure.


## <a name="best-practice-use-managed-disks-and-availability-sets"></a>Rekommendation: Använda hanterade diskar och tillgänglighetsuppsättningar

Azure använder tillgänglighetsuppsättningar att gruppera virtuella datorer och för att isolera virtuella datorer i en mängd från andra resurser. Virtuella datorer i en tillgänglighetsuppsättning är fördelade på flera feldomäner med olika delsystem som skydd mot lokala fel, och även är fördelade på flera uppdateringsdomäner så att inte alla virtuella datorer i en uppsättning startas om samtidigt.

Azure-hanterade diskar förenklar Diskhantering för virtuella Azure IaaS-datorer genom att hantera de lagringskonton som är associerade med de Virtuella diskarna.

- Vi rekommenderar att du använder hanterade diskar där det är möjligt. Du behöver bara ange typ av lagring som du vill använda och storleken på disken du behöver, och skapar hanterar Azure disken åt dig, i bakgrunden.
- Du kan konvertera befintliga diskar som hanteras.
- Du bör skapa virtuella datorer i tillgänglighetsuppsättningar för hög flexibilitet och tillgänglighet. När planerade eller oplanerade driftstopp inträffar, tillgänglighetsuppsättningarna ser du till att minst en av dina virtuella datorer i uppsättningen fortsätter att vara tillgängligt.


![Hanterade diskar](./media/migrate-best-practices-security-management/managed-disks.png)
*hanterade diskar*

**Lära sig mer:**
- [Få en översikt](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) av hanterade diskar.
- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks) konvertera diskar som hanteras.
- [Lär dig hur du](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) hantera tillgängligheten av Windows-datorer i Azure.


## <a name="best-practice-monitor-resource-usage-and-performance"></a>Rekommendation: Övervaka användning och prestanda 

Du kanske har flyttat dina arbetsbelastningar till Azure för stora skalning. Flytta din arbetsbelastning betyder inte dock att Azure automatiskt implementerar skalning utan kommentarer. Som exempel:

- Om organisationen marknadsföring skickar en ny TV-annons som driver 300% mer trafik, kan det leda till problem med tillgängligheten för platsen. Din nyligen migrerade arbetsbelastning kan orsaka tilldelade gränser och krascher.
- Ett annat exempel kanske en distribuerad denial of service (DDoS)-attack på din migrerade arbetsbelastning. I det här fallet kanske du inte vill att skala, men för att förhindra att källan för attackerna från att nå dina resurser.

Båda fallen har olika lösningar, men båda du behöver du en överblick över vad som händer med användnings- och prestandaövervakning.

- Azure Monitor kan lyfta fram de här måtten och ge svar med aviseringar, automatisk skalning, händelsehubbar, logic apps och mycket mer.
- Du kan integrera din SIEM tredjepartsprogram att övervaka Azure loggarna för gransknings- och prestanda händelser utöver Azure-övervakning.


![Azure Monitor](./media/migrate-best-practices-security-management/monitor.png)
*Azure Monitor*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/azure-monitor/overview) Azure Monitor.
- [Få metodtips](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) för övervakning och diagnostik.
- [Lär dig mer om](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) automatisk skalning.
- [Lär dig hur du](https://docs.microsoft.com/azure/security-center/security-center-export-data-to-siem) dirigera data från Azure till ett SIEM-verktyg.

## <a name="best-practice-enable-diagnostic-logging"></a>Rekommendation: Aktivera diagnostikloggning

Azure-resurser genererar ett verkligt antal loggningsdata mått och telemetri.

- Som standard har de flesta typer av resurser inte Diagnostisk loggning är aktiverat.
- Du kan fråga efter loggningsdata och skapa aviseringar och spelböcker baserat på den genom att aktivera Diagnostisk loggning för dina resurser.
- När du aktiverar diagnostikloggning har varje resurs en specifik uppsättning kategorier. Du kan välja en eller flera loggningskategorier och en plats för loggdata. Loggar kan skickas till ett lagringskonto, event hub eller Azure Monitor-loggar. 


![Diagnostisk loggning](./media/migrate-best-practices-security-management/diagnostics.png)
*Diagnostisk loggning*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) samla in och använda loggdata.
- [Lär dig vad som stöds](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema) för Diagnostisk loggning.


## <a name="best-practice-set-up-alerts-and-playbooks"></a>Rekommendation: Konfigurera varningar och spelböcker

Med diagnostisk loggning har aktiverats för Azure-resurser, kan du börja använda loggningsdata för att skapa anpassade varningar.

- Aviseringar meddela proaktivt dig när villkor finns i dina övervakningsdata. Du kan sedan åtgärda problem innan systemanvändare uppmärksamma dem. Du kan meddela på måttvärden, loggsökningsfrågor, händelser i aktivitetsloggen, plattform hälsotillstånd och tillgänglighet för webbplatsen.
- Du kan köra en Logic App Spelbok när aviseringarna utlöses. En spelbok hjälper dig att automatisera och samordna ett svar på en specifik avisering. Spelböcker baseras på Azure Logic Apps. Du kan använda Logic App-mallar för att skapa spelböcker eller skapa egna.
- Som ett enkelt exempel kan du skapa en avisering utlöses när en port-genomsökning utnyttjas mot en grupp.  Du kan ställa in en spelbok som körs och låser IP-adressen för genomsökning ursprunget.
- Ett annat exempel kanske en app med en minnesläcka.  När minnesanvändningen hämtar till en viss punkt, kan en spelbok återanvända processen.


![Aviseringar](./media/migrate-best-practices-security-management/alerts.png)
*aviseringar*

**Lära sig mer:**
- [Lär dig mer om](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts) aviseringar.
- [Lär dig mer om](https://docs.microsoft.com/azure/security-center/security-center-playbooks) strategiböcker för säkerhet som svarar på aviseringar i Security Center.

## <a name="best-practice-use-the-azure-dashboard"></a>Rekommendation: Använd instrumentpanelen för Azure

Azure-portalen är en webbaserad enhetlig konsol som hjälper dig att skapa, hantera och övervaka allt från enkla webbappar till komplexa molnprogram. Den innehåller en anpassningsbar instrumentpanel och hjälpmedelsalternativ.
- Du kan skapa flera instrumentpaneler och dela dem med andra som har åtkomst till dina Azure-prenumerationer.
- Med den här delade modell har ditt team insyn i Azure-miljön, så att de kan vara proaktiv när du hanterar system i molnet.


![Azure-instrumentpanelen](./media/migrate-best-practices-security-management/dashboard.png)
*Azure-instrumentpanelen*

**Lära sig mer:**

- [Lär dig hur du](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) skapa en instrumentpanel.
- [Lär dig mer om](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-structure) instrumentpanelen struktur.


## <a name="best-practice-understand-support-plans"></a>Rekommendation: Förstå supportavtal

Någon gång behöver du samarbeta med supportpersonalen eller Microsoft supportpersonal. Det är viktigt att ha en uppsättning principer och procedurer för support under scenarier, till exempel haveriberedskap. Dessutom bör personalen administratörer och support utbildas på att implementera dessa principer.

- Administratörer bör veta hur du skickar in ett supportärende till Microsoft på det mest lämpliga och effektiva sättet det osannolika att ett problem med Azure-tjänst påverkar din arbetsbelastning.
- Bekanta dig med de olika supportplanerna erbjuds för Azure. De röra sig om svarstider som är dedikerad till Developer-instanser till Premier support med en svarstid på mindre än 15 minuter.


![Supportavtal](./media/migrate-best-practices-security-management/support.png)
*supportavtal*

**Lära sig mer:**
- [Få en översikt](https://azure.microsoft.com/support/options/) av Azure-supportavtal.
- [Lär dig mer om](https://azure.microsoft.com/support/legal/sla/) serviceavtal (SLA).

## <a name="best-practice-manage-updates"></a>Rekommendation: Hantera uppdateringar

Att hålla virtuella Azure-datorer uppdaterade med det senaste operativsystemet och programuppdateringar är en omfattande börda. Möjligheten att ansluta alla virtuella datorer om du vill ta reda på vilka uppdateringar som de behöver och att skicka den automatiskt dessa uppdateringar är mycket användbart.

- Du kan använda uppdateringshantering i Azure Automation för att hantera uppdateringar av operativsystemet för datorer som kör Windows och Linux-datorer som distribueras i Azure, lokalt och i andra molnleverantörer. 
- Använda uppdateringshantering för att snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera installationen av uppdateringen.
- Du kan aktivera uppdateringshantering för virtuella datorer direkt från ett Azure Automation-konto. Du kan också uppdatera en enskild virtuell dator från sidan virtuell dator i Azure-portalen.
- Dessutom kan virtuella Azure-datorer som kan registreras med System Center Configuration Manager. Du kan sedan migrera Configuration Manager-arbetsbelastningar till Azure och göra uppdateringar för rapporterings- och programvara från ett enda webbgränssnitt.


![VM-uppdateringar](./media/migrate-best-practices-security-management/updates.png)
*uppdateringar*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/automation/automation-update-management) uppdateringshantering i Azure.
- [Lär dig hur du](https://docs.microsoft.com/azure/automation/oms-solution-updatemgmt-sccmintegration) integrera Configuration Manager med hantering av uppdateringar.
- [Vanliga frågor och svar](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure) om Configuration Manager i Azure.


## <a name="implement-a-change-management-process"></a>Implementera en process för ändringshantering

Precis som med alla produktionssystemet kan det påverka din miljö att göra alla typer av ändringar. En process för ändringshantering som kräver begäranden skickas för att kunna göra ändringar i produktionssystem är en värdefull i miljön migrerade.

- Du kan skapa den bästa praxis ramverk för ändringshantering för att öka medvetenheten i administratörer och supportpersonal.
- Du kan använda Azure Automation för att hjälpa till med konfigurationshantering och spårning av ändringar för dina migrerade arbetsflöden.
- När tvingande av processen för ändringshantering, du kan använda granskningsloggar för att länka Azure ändra loggar i antas vara (eller inte) befintliga ändringsbegäranden. Så att du kan undersöka vad som gick fel i processen om du ser en ändring som gjorts utan en motsvarande ändringsbegäran.

Azure har en lösning för ändringsspårning i Azure automation:

- Lösningen spårar ändringar i Windows och Linux-programvara och filer, registernycklar för Windows, Windows-tjänster och Linux-daemon.
- Ändringar på övervakade servrar skickas till Azure Monitor-tjänsten i molnet för bearbetning.
- Logiken tillämpas på den mottagna data och Molntjänsten innehåller data.
- På instrumentpanelen för ändringsspårning kan du enkelt se ändringar som gjorts i din serverinfrastruktur.


![Ändringshantering](./media/migrate-best-practices-security-management/change.png)
*ändringshantering*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/automation/automation-change-tracking) ändringsspårning.
- [Lär dig mer om](https://docs.microsoft.com/azure/automation/automation-intro) Azure Automation-funktioner.




## <a name="next-steps"></a>Nästa steg 

Granska andra metodtips:


- [Bästa praxis](migrate-best-practices-networking.md) för nätverk efter migreringen.
- [Bästa praxis](migrate-best-practices-costs.md) för kostnadshantering efter migreringen.








