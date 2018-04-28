---
title: Microsoft Azure Data kryptering i vila | Microsoft Docs
description: Den här artikeln innehåller en översikt över Microsoft Azure data kryptering i vila, övergripande funktioner och allmänna överväganden.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: c0bc3c8774b68b49be95d5df86319a2e0463e6ae
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-data-encryption-at-rest"></a>Azure Data kryptering i vila
Det finns flera verktyg i Microsoft Azure för att skydda data efter behov för säkerhet och efterlevnad av ditt företag. Det här dokumentet fokuserar på:
- Hur skyddas data i vila i Microsoft Azure
- Beskriver de olika komponenterna som deltar i data protection-implementering
- Granskar- och nackdelar med de olika nyckelhantering skydd metoderna. 

Kryptering i vila är vanliga säkerhetskrav. En fördel med Microsoft Azure är att organisationer kan uppnå kryptering i vila utan kostnaden för implementering och hantering och risken för en anpassad nyckel lösning. Organisationer har du möjlighet att låta Azure helt hantera kryptering i vila. Dessutom kan har organisationer olika alternativ för att hantera nära kryptering eller krypteringsnycklar.

## <a name="what-is-encryption-at-rest"></a>Vad är kryptering i vila?
Kryptering i vila avser kryptografiska kodning (kryptering) av data om det sparas. Kryptering i vila Designer i Azure Använd symmetrisk kryptering för att kryptera och dekryptera stora mängder data snabbt enligt en enkel konceptuell modell:

- En symmetrisk krypteringsnyckel som används för att kryptera data när det sparas 
- Samma krypteringsnyckel som används för att dekryptera data som den färdigställs för användning i minnet
- Data kan vara partitionerade och olika nycklar kan användas för varje partition
- Nycklar måste lagras på en säker plats med principer för åtkomstkontroll att begränsa åtkomsten till vissa identiteter och loggning nyckelanvändning. Datakrypteringsnycklar krypteras ofta med asymmetriska kryptering att ytterligare begränsa åtkomst (beskrivs i den *nyckel hierarkin*senare i den här artikeln)

Ovanstående beskrivs vanliga element finns på hög nivå av kryptering i vila. I praktiken kräver viktiga scenarier för hantering och kontroll, samt tillgänglighet och skala garantier ytterligare konstruktioner. Microsoft Azure-kryptering i Rest-begrepp och komponenter som beskrivs nedan.

## <a name="the-purpose-of-encryption-at-rest"></a>Syftet med kryptering i vila
Kryptering i vila är avsett att ge dataskydd för data i vila (enligt beskrivningen ovan.) Attacker mot data i vila omfattar försöker få tillgång till maskinvaran som data lagras och skada innehåller data. I sådana angrepp kan hårddisken för en server ha tagits mishandled under underhåll så att en angripare att ta bort hårddisken. Angriparen skulle senare försätta hårddisken i en dator under deras kontroll att försöka komma åt data. 

Kryptering i vila är utformat för att förhindra angripare från att komma åt den okrypterade data genom att säkerställa att data krypteras på disken. Om en angripare att få en hårddisk med sådana krypterade data och ingen åtkomst till krypteringsnycklarna, skulle angriparen inte äventyra data utan bra svårt. En angripare kan försöka attacker mot krypterade data som är mycket mer komplexa och resursen förbrukar än åtkomst till okrypterade data på en hårddisk i ett sådant scenario. Därför kryptering i vila rekommenderas och är ett krav för hög prioritet för många organisationer. 

I vissa fall krävs också kryptering i vila av en organisations behov av åtgärder för styrning och efterlevnad av data. Branschmässiga och statliga förordningar som HIPAA, PCI och FedRAMP, utformar skyddsåtgärder av kraven för skydd och kryptering av data. För många av dessa förordningar är kryptering i vila en obligatorisk åtgärd som krävs för kompatibla hantering och skydd. 

Förutom efterlevnad och regelkrav ska kryptering i vila uppfattas som en funktion för skydd på djupet plattform. Även om Microsoft tillhandahåller en kompatibel plattform för tjänster, program och data, omfattande anläggning och fysisk säkerhet data åtkomstkontroll och granskning, är det viktigt att ange ytterligare säkerhetsåtgärder för ”överlappande” om en av de andra säkerhetsmetoder misslyckas. Kryptering i vila innehåller sådana en ytterligare skydd.

Microsoft strävar efter att tillhandahålla kryptering i vila alternativ för molntjänster och för att ge kunderna lämplig hanterbarhet krypteringsnycklar och åtkomst till loggar som visar när krypteringsnycklar används. Dessutom samarbetar Microsoft mot mål för att göra alla kunddata krypterat i vila som standard.

## <a name="azure-encryption-at-rest-components"></a>Azure kryptering i vila komponenter

Som tidigare nämnts är målet för kryptering i vila att data som sparas på disk är krypterad med en kryptering med hemliga nyckel. För att uppnå den målet säker nyckelgenerering, lagring, måste åtkomstkontroll och hantering av krypteringsnycklarna anges. Även om information kan variera, Azure services kryptering på Rest-implementeringar kan beskrivas i den nedan begrepp som sedan illustreras i följande diagram.

![Komponenter](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Lagringsplatsen för krypteringsnycklarna och åtkomstkontroll till dessa nycklar är centrala för en kryptering i vila modellen. Nycklarna måste vara mycket säkra men hanteras av angivna användare och tillgängliga för vissa tjänster. För Azure-tjänster, Azure Key Vault är den rekommenderade nyckellagring lösningen och ger en gemensam hanteringsupplevelse för tjänster. Nycklar lagras och hanteras i nyckelvalv och kan få åtkomst till en nyckelvalv till användare eller tjänster. Azure Key Vault stöder kunden skapandet av nycklar eller import av Kundnycklar för användning i kundhanterad kryptering viktiga scenarier.

### <a name="azure-active-directory"></a>Azure Active Directory

Behörighet att använda nycklarna lagras i Azure Key Vault vill hantera eller komma åt dem för kryptering i vila kryptering och dekryptering, kan ges till Azure Active Directory-konton. 

### <a name="key-hierarchy"></a>Viktiga hierarki

Mer än en krypteringsnyckel används i en kryptering i vila implementering. Asymmetrisk kryptering är användbart för att upprätta förtroende och autentisering som behövs för åtkomst och hantering. Symmetrisk kryptering är effektivare för bulk-kryptering och dekryptering, tillåter starkare kryptering och bättre prestanda. Dessutom kan minskar begränsa användningen av en enda krypteringsnyckel risken att nyckeln äventyras och kostnaden för att kryptera när en nyckel måste ersättas. För att utnyttja fördelarna med asymmetriska och symmetrisk kryptering och begränsa användning och exponering av en enskild nyckel, används Azure krypteringar på rest modeller en nyckel hierarki består av följande typer av nycklar:

- **Datakrypteringsnyckeln (DEK)** – en symmetrisk AES256 nyckel som används för att kryptera en partition eller datablock.  En enskild resurs kan ha många partitioner och många datakrypteringsnycklar. Kryptera varje datablock med en annan nyckel gör kryptografi analys attacker svårare. Åtkomst till DEKs krävs av den resource provider eller program-instans som kryptering och dekryptering av ett visst block. När en DEK ersätts med en ny nyckel måste bara data i dess associerade block krypteras igen med den nya nyckeln.
- **Nyckeln kryptering nyckel (KEK)** – en asymmetrisk krypteringsnyckel som används för att kryptera krypteringsnycklarna Data. Med en nyckel krypteringsnyckel kan datakrypteringsnycklar sig själva till krypteras och kontrolleras. Den enhet som har åtkomst till KEK är annorlunda än den entitet som kräver DEK. På så sätt kan en enhet kan broker åtkomst till DEK för att säkerställa begränsad åtkomst för varje DEK till specifika partition. Eftersom KEK krävs för att dekryptera DEKs, är KEK en enda åtkomstpunkt som DEKs kan raderas effektivt genom borttagning av KEK.

Krypteringsnycklarna Data krypteras med nyckeln krypteringsnycklarna lagras separat och endast en entitet med åtkomst till nyckeln krypteringsnyckeln får alla datakrypteringsnycklar krypteras med nyckeln. Olika modeller för lagring av nycklar stöds. Diskuteras varje modell i detalj senare i nästa avsnitt.

## <a name="data-encryption-models"></a>Kryptering datamodeller

Det är viktigt för att förstå hur olika resursproviders i Azure implementera kryptering i vila att förstå de olika modellerna i kryptering och deras- och nackdelar. Dessa definitioner är gemensamma för alla providrar i Azure så vanliga språk och taxonomi. 

Det finns tre scenarier för kryptering på serversidan:

- Kryptering för serversidan med tjänsten hanterade nycklar
    - Azure Resursproviders utför åtgärder för kryptering och dekryptering
    - Microsoft hanterar nycklarna
    - Funktioner för fullständig moln

- Kryptering för serversidan med kundhanterad nycklar i Azure Key Vault
    - Azure Resursproviders utför åtgärder för kryptering och dekryptering
    - Kunden styr nycklar via Azure Key Vault
    - Funktioner för fullständig moln

- Kryptering för serversidan med kundhanterad nycklar på kund-kontrollerade maskinvara
    - Azure Resursproviders utför åtgärder för kryptering och dekryptering
    - Kunden styr nycklar på kund-kontrollerade maskinvara
    - Funktioner för fullständig moln

För kryptering på klientsidan, Tänk på följande:

- Azure-tjänster inte kan se dekrypterade data
- Kunder hantera och lagra nycklar på lokalt (eller i andra säkra lagrar). Nycklarna är inte tillgänglig för Azure-tjänster
- Minskad molnet funktioner

Stöds kryptering modeller i Azure som delas upp i två grupper: ”klienten kryptering” och ”serversidan kryptering” som tidigare nämnts. Observera att, oberoende av kryptering i vila modellen används, Azure services rekommenderar alltid användning av en säker transport, till exempel TLS- eller HTTPS. Därför bör åtgärdas av transportprotokollet kryptering i transport och får inte vara en viktig faktor för att fastställa vilka kryptering i vila modell som ska användas.

### <a name="client-encryption-model"></a>Modell med klientdator kryptering

Modell med klientdator kryptering refererar till kryptering som utförs utanför Resursprovidern eller Azure av tjänsten eller det anropande programmet. Kryptering kan utföras av tjänstprogrammet i Azure eller av ett program som körs i kundens datacenter. I båda fallen när möjligheterna med den här modellen för kryptering, får en krypterad blob av data utan att dekryptera data på något sätt eller har åtkomst till krypteringsnycklarna i Azure Resource Provider. I den här modellen nyckelhantering som görs genom att anropa tjänstprogrammet och är täckande till Azure-tjänsten.

![Client](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Kryptering för serversidan modellen

Serversidan kryptering modeller refererar till kryptering som utförs av Azure-tjänsten. I den här modellen utför Resursprovidern kryptera och dekryptera åtgärder. Azure Storage kan ta emot data i klartext åtgärder och utför kryptering och dekryptering internt. Resursprovidern kan använda krypteringsnycklar som hanteras av Microsoft eller kunden beroende på den angivna konfigurationen. 

![Server](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Kryptering för serversidan nyckelhantering modeller

Var och en av serversidan kryptering i vila modeller innebär olika egenskaper för hantering av nycklar. Detta inkluderar var och hur krypteringsnycklar skapas och lagras samt modeller för åtkomst och viktiga rotation procedurer. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Kryptering för serversidan med hjälp av service-hanterade nycklar

För många kunder är väsentligt krav att säkerställa att informationen är krypterad när det är i vila. Kryptering för serversidan med tjänsten hanterade nycklar kan den här modellen genom att kunderna kan markera specifika resursen (Storage-konto, SQL DB o.s.v.) för kryptering och låta alla nyckelhantering aspekter som nyckel utfärdande, rotation och säkerhetskopiering till Microsoft. De flesta Azure-tjänster som stöder kryptering i vila vanligtvis stöd för den här modellen för hantering av krypteringsnycklar till Azure-avlastning. Azure-resursprovider skapar nycklarna, placerar dem i säker lagring och hämtar dem när de behövs. Detta innebär att tjänsten har fullständig åtkomst till nycklarna och tjänsten har fullständig kontroll över livscykelhantering autentiseringsuppgifter.

![Hanteras](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Serversidan kryptering med för tjänsten hanteras därför snabbt adresser behöver ha kryptering i vila med låg belastning till kunden. När det är tillgängligt öppnas Azure-portalen för målprenumerationen och resursprovidern vanligtvis en kund och kontrollerar en som anger de vill att data ska vara krypterade. I vissa resurshanterare är serversidan kryptering med nycklar som hanteras av tjänsten aktiverad som standard. 

Kryptering för serversidan med Microsoft-hanterad nycklar innebär att tjänsten har fullständig åtkomst till lagrar och hanterar nycklarna. Även om vissa kunder vill hantera nycklar eftersom de anser att de kan säkerställa bättre säkerhet, övervägas kostnad och risker som är associerade med en anpassad nyckel lagringslösning vid utvärdering av den här modellen. I många fall kan en organisation bestämma att resursen begränsningar eller riskerna med en lokal lösning kan större än risken för molnhantering av kryptering i vila nycklar.  Den här modellen kan dock inte tillräckliga för organisationer som har krav styra skapandet eller livscykel krypteringsnycklarna eller har olika personer Hantera tjänstens krypteringsnycklar än de som hanterar tjänsten (d.v.s. uppdelning av hantering av nycklar från den övergripande Hanteringsmodellen för tjänsten).

##### <a name="key-access"></a>Nyckelåtkomst

När SSI-kryptering med tjänsten hanterade nycklar används, nyckelgenerering, lagring och åtkomst till tjänsten är alla hanteras av tjänsten. Normalt grundläggande Azure resursleverantörer kommer att lagra krypteringsnycklarna Data i ett arkiv som ligger nära data och snabbt tillgänglig och kan nås när nyckeln krypteringsnycklarna lagras i en säker intern lagring.

**Fördelar**

- Enkel installation
- Microsoft hanterar viktiga rotation, säkerhetskopiering och redundans
- Kunden har kostnaden för implementering och risken för ett schema för anpassade nyckelhantering.

**Nackdelar**

- Ingen kund kontroll över krypteringsnycklarna (nyckelspecifikation, livscykel, återkallade, etc.)
- Ingen möjlighet att särskilja nyckelhantering från övergripande Hanteringsmodellen för tjänsten

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kryptering för serversidan med hjälp av kunden hanterade nycklar i Azure Key Vault 

Kryptering nycklar kunder kan använda SSI-kryptering med kunden hanterade nycklar i Key Vault för scenarier där kravet är att kryptera data i vila och kontroll. Vissa tjänster kan lagra endast roten nyckeln krypteringsnyckeln i Azure Key Vault och lagra krypteringsnyckeln krypterade Data i en intern plats närmare till data. I som scenariot kunder få sina egna nycklar till Key Vault (BYOK – ta med din egen nyckel), eller generera nya och använda dem för att kryptera önskade resurser. Medan Resursprovidern utför åtgärderna kryptering och dekryptering använder den konfigurerade nyckeln som rotnyckeln för alla krypteringsåtgärder för. 

##### <a name="key-access"></a>Nyckelåtkomst

Kryptering för serversidan modellen med hanterade Kundnycklar i Azure Key Vault innebär att tjänsten åtkomst till nycklar för att kryptera och dekryptera efter behov. Kryptering i vila nycklar görs tillgänglig för en tjänst genom en princip för åtkomstkontroll. Den här principen ger service identitet åtkomst för att ta emot nyckeln. Kör ett kopplat prenumerations en Azure-tjänst kan konfigureras med en identitet i den prenumerationen. Tjänsten kan utföra Azure Active Directory-autentisering och ta emot en token för autentisering som identifierar sig själv som den tjänst som agerar på uppdrag av prenumerationen. Denna token kan sedan presenteras för Key Vault hämta en nyckel som den har fått åtkomst till.

För åtgärder med hjälp av krypteringsnycklar, en tjänstidentitet kan beviljas åtkomst till någon av följande åtgärder: dekryptera, kryptera, unwrapKey, wrapKey, verifiera, loggar, hämta, visa, uppdatera, skapa, importera, ta bort, säkerhetskopiera och återställa.

Om du vill hämta en nyckel för att kryptera eller dekryptera data i vila tjänstidentiteten instansen för Resource Manager-tjänsten körs som måste ha UnwrapKey (för att hämta nyckel för dekryptering) och WrapKey (för att infoga en nyckel i nyckelvalvet när du skapar en ny nyckel).


>[!NOTE] 
>Mer information om Key Vault auktorisering finns i den säkra sidan nyckelvalvet i den [Azure Key Vault dokumentationen](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Fördelar**

- Fullständig kontroll över nycklar används – kryptering nycklar hanteras i kundens Key Vault under kundens kontroll.
- Möjlighet att kryptera flera tjänster till en överordnad
- Kan särskilja nyckelhantering från övergripande Hanteringsmodellen för tjänsten
- Definiera tjänsten och nyckelplats över regioner

**Nackdelar**

- Kunden har fullt ansvar för hantering av nycklar
- Kunden har fullt ansvar för hantering av livscykel
- Ytterligare kostnader för installation och konfiguration

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Kryptering för serversidan via tjänsten hanterade nycklar i kundens kontrollerade maskinvara

För scenarier där kravet är att kryptera data i vila och hantera nycklar i en skyddad databas utanför Microsofts kontroll, aktivera vissa Azure-tjänster värden din egen nyckel HYOK-nyckelhantering modellen. I den här modellen tjänsten måste hämta nyckeln från en extern plats och därför garantier för tillgänglighet och prestanda påverkas och konfigurationen är mer komplexa. Dessutom eftersom tjänsten har åtkomst till DEK under åtgärder för kryptering och dekryptering liknar övergripande säkerhetsgarantier med den här modellen när nycklarna är hanteras i Azure Key Vault-kund.  Den här modellen är därför inte lämplig för de flesta organisationer om de inte har särskilda nyckelhantering krav vilket kräver den. De flesta Azure-tjänster stöder inte kryptering för serversidan med hjälp av server-hanterade nycklar i kundens kontrollerade maskinvara på grund av dessa begränsningar.

##### <a name="key-access"></a>Nyckelåtkomst

När servern kryptering med för tjänsten som hanteras i kundens kontrollerade maskinvara används bevaras nycklarna på ett system som konfigurerats av kunden. Azure-tjänster som stöder den här modellen ger ett sätt att upprätta en säker anslutning till en kund som angetts för KeyStore.

**Fördelar**

- Fullständig kontroll över rotnyckeln används – kryptering nycklar som hanteras av en kund som store
- Möjlighet att kryptera flera tjänster till en överordnad
- Kan särskilja nyckelhantering från övergripande Hanteringsmodellen för tjänsten
- Definiera tjänsten och nyckelplats över regioner

**Nackdelar**

- Fullt ansvar för nyckellagring, säkerhet, prestanda och tillgänglighet
- Fullt ansvar för hantering av nycklar
- Fullt ansvar för hantering av livscykel
- Betydande installation, konfiguration och löpande underhållskostnaderna
- Ökad beroende på nätverkets tillgänglighet mellan kunden datacenter och Azure-datacenter.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Kryptering i vila i Microsoft-molntjänster

Microsoft Cloud services används i alla tre modeller: IaaS, PaaS, SaaS. Nedan finner du exempel på hur de får plats på varje modell:

- Programtjänster, kallad programvara som en Server eller SaaS som har program som tillhandahålls av molntjänster som Office 365.
- Tjänster-plattformen som kunder utnyttja molnet i sina program med hjälp av molnet för till exempel lagring, analyser och service bus-funktioner.
- Infrastrukturtjänster eller infrastruktur som en tjänst (IaaS) i vilken kund distribuera operativsystem och program som finns i molnet och eventuellt använder andra molntjänster.

### <a name="encryption-at-rest-for-saas-customers"></a>Kryptering i vila för SaaS-kunder

Programvara som en tjänst (SaaS)-kunder har vanligtvis kryptering i vila aktiverat eller tillgängliga i varje tjänst. Office 365 har flera alternativ för kunder att verifiera eller aktivera kryptering i vila. Information om Office 365-tjänster finns i Data krypteringstekniker för Office 365.

### <a name="encryption-at-rest-for-paas-customers"></a>Kryptering i vila för PaaS-kunder

Plattform som en tjänst (PaaS) kundinformation finns vanligtvis i en miljö för körning av program och alla Azure-Resursproviders som används för att lagra kundens data. Om du vill se kryptering i vila granska alternativ som är tillgängliga i tabellen nedan för lagrings- och plattformar som du använder. Där de stöds, finns länkar till instruktioner om hur du aktiverar kryptering i vila för varje resursprovider. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Kryptering i vila för IaaS-kunder

Infrastruktur som en tjänst (IaaS)-kunder kan ha flera olika tjänster och program används. IaaS-tjänster kan aktivera kryptering i vila i sina Azure värd för virtuella datorer och virtuella hårddiskar med hjälp av Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Krypterade lagring

Som PaaS, kan IaaS-lösningar utnyttja andra Azure-tjänster som lagrar data krypterat i vila. I dessa fall måste aktivera du kryptering i vila stöd som tillhandahålls av varje förbrukade Azure-tjänsten. I tabellen nedan räknar större lagring, tjänster och program plattformar och modell för kryptering i vila som stöds. Där de stöds, finns länkar till instruktioner om hur du aktiverar kryptering i vila. 

#### <a name="encrypted-compute"></a>Krypterade beräkning

En fullständig kryptering i vila lösning kräver att data sparas aldrig i okrypterat format. Som används på en server som läser in data i minnet, sparas data lokalt på olika sätt, inklusive växlingsfilen, en krasch-dump och eventuella loggning som programmet kan utföra. För att säkerställa att informationen är krypterad vilande kan IaaS-program använda Azure Disk Encryption på en virtuell Azure IaaS (Windows eller Linux) och virtuell disk. 

#### <a name="custom-encryption-at-rest"></a>Anpassade kryptering i vila

Vi rekommenderar att om möjligt IaaS program utnyttja Azure Disk Encryption och kryptering i vila alternativen i alla förbrukade Azure-tjänster. I vissa fall som oregelbundna krypteringskrav eller Azure-baserad lagring, en utvecklare av ett IaaS-program kan behöva implementera kryptering i vila sig själva. Utvecklare av IaaS-lösningar kan bättre integrera med Azure hantering och kundens förväntningar genom att använda vissa Azure komponenter. Mer specifikt kan med utvecklare Azure Key Vault-tjänsten att tillhandahålla säker lagring av nycklar samt ger sina kunder med alternativ för konsekvent nyckelhantering med som mest Azure-plattformstjänster. Anpassade lösningar bör dessutom använda Azure hanteras Tjänsteidentiteter för att aktivera tjänstekonton åtkomst till krypteringsnycklarna. Information för utvecklare på Azure Key Vault och hanteras Tjänsteidentiteter finns i deras respektive SDK: er.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure-resurs providers modellen krypteringsstöd

Microsoft Azure Services varje stöd för en eller flera av kryptering i vila modeller. För vissa tjänster, men kanske en eller flera av kryptering inte gäller. Tjänster kan dessutom släpp stöd för dessa scenarier vid olika scheman. Det här avsnittet beskrivs kryptering i rest-stöd när detta skrivs för var och en av viktiga data för Azure storage-tjänster.

### <a name="azure-disk-encryption"></a>Azure disk encryption

En kund med Azure-infrastrukturen som en tjänst (IaaS)-funktioner kan uppnå kryptering i vila för sina virtuella IaaS-datorer och diskar via Azure Disk Encryption. Mer information om Azure Disk encryption finns i [dokumentation för Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Azure-lagring

Azure Blob och filen har stöd för kryptering i vila för serversidan krypterade scenarier samt kundens krypterade data (kryptering på klientsidan).

- Serversidan: kunder som använder Azure blob storage kan aktivera kryptering i vila på varje resurs Azure storage-konto. En gång aktiverad serversidan datakryptering görs transparent till programmet. Se [Azure Storage Service-kryptering för Data i vila](https://docs.microsoft.com/azure/storage/storage-service-encryption) för mer information.
- På klientsidan: kryptering på klientsidan av Azure BLOB stöds. När med hjälp av klientsidan kryptering kunder kryptera data och ladda upp data som en krypterad blob. Nyckelhantering görs av kunden. Se [kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) för mer information.


#### <a name="sql-azure"></a>SQL Azure

SQL Azure stöder för närvarande kryptering i vila för Microsoft-hanterad tjänstsidan och kryptering på klientsidan scenarier.

Stöd för krypteringen av server tillhandahålls för närvarande via funktionen SQL kallas Transparent datakryptering. När en SQL Azure-kund kan skapas och hanteras för dem automatiskt TDE nyckel. Du kan aktivera kryptering i vila på databas- och programnivå. Från och med juni 2017 [Transparent Data kryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) aktiveras som standard för nya databaser.

Klientsidan kryptering av data i SQL Azure stöds via den [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) funktion. Krypterad använder alltid en nyckel som skapas och lagras av klienten. Kunder kan lagra huvudnyckeln i ett certifikatarkiv för Windows Azure Key Vault eller en lokal maskinvarusäkerhetsmodul. SQL-användare välja med SQL Server Management Studio, vilka nyckel som de vill använda för att kryptera vilken kolumn.

|                                  |                |                     | **Kryptering modellen**             |                              |        |
|----------------------------------|----------------|---------------------|------------------------------|------------------------------|--------|
|                                  |                |                     |                              |                              | **Klienten** |
|                                  | **Hantering av nycklar** | **Tjänsten hanteras nyckel** | **Kunden hanteras i Nyckelvalvet** | **Kunden hanteras lokala** |        |
| **Lagring och databaser**            |                |                     |                              |                              |        |
| Disken (IaaS)                      |                | -                   | Ja                          | Ja*                         | -      |
| SQLServer (IaaS)                |                | Ja                 | Ja                          | Ja                          | Ja    |
| Azure SQL (PaaS)                 |                | Ja                 | Ja                          | -                            | Ja    |
| Azure Storage (blockera/Sidblobbar) |                | Ja                 | Ja                          | -                            | Ja    |
| Azure Storage (filer)            |                | Ja                 | -                            | -                            | -      |
| Azure Storage (tabeller, köer)   |                | -                   | -                            | -                            | Ja    |
| Cosmos DB (dokumentet DB)          |                | Ja                 | -                            | -                            | -      |
| StorSimple                       |                | Ja                 | -                            | -                            | Ja    |
| Backup                           |                | -                   | -                            | -                            | Ja    |
| **Tillgångsinformation och analys**       |                |                     |                              |                              |        |
| Azure Data Factory               |                | Ja                 | -                            | -                            | -      |
| Azure Machine Learning           |                | -                   | Förhandsversion                      | -                            | -      |
| Azure Stream Analytics           |                | Ja                 | -                            | -                            | -      |
| HDInsights (Azure Blob Storage)  |                | Ja                 | -                            | -                            | -      |
| HDInsights (Data Lake lagring)   |                | Ja                 | -                            | -                            | -      |
| Azure Data Lake Store            |                | Ja                 | Ja                          | -                            | -      |
| Azure Data Catalog               |                | Ja                 | -                            | -                            | -      |
| Power BI                         |                | Ja                 | -                            | -                            | -      |
| **IoT-tjänster**                     |                |                     |                              |                              |        |
| IoT Hub                          |                | -                   | -                            | -                            | Ja    |
| Service Bus                      |                | Ja              | -                            | -                            | Ja    |
| Händelsehubbar                       |                | Ja             | -                            | -                            | -      |


## <a name="conclusion"></a>Sammanfattning

Skydd av kundinformation som lagras i Azure-tjänster är av avgörande betydelse till Microsoft. Alla Azure-värdtjänsten tjänster avser att ge kryptering på övriga alternativ. Grundläggande tjänster som Azure Storage, SQL Azure och viktiga analytics och intelligence ge redan kryptering Rest-alternativ. Vissa av dessa tjänster kontrollerade Kundnycklar och kryptering på klientsidan som tjänsten hanterade nycklar och kryptering. Microsoft Azure-tjänster brett förbättra kryptering vid Rest tillgänglighet och nya alternativ planeras för förhandsversionen och allmän tillgänglighet under de kommande månaderna.

