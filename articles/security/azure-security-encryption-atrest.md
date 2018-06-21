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
ms.date: 06/20/2018
ms.author: barclayn
ms.openlocfilehash: 21438b107632166f3717c07b0fd01a56a2944f34
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294064"
---
# <a name="azure-data-encryption-at-rest"></a>Azure Data kryptering i vila

Microsoft Azure innehåller verktyg för att skydda data efter behov för säkerhet och efterlevnad av ditt företag. Det här dokumentet fokuserar på:

- Hur skyddas data i vila i Microsoft Azure
- Beskriver de olika komponenterna som deltar i data protection-implementering
- Granskar- och nackdelar med de olika nyckelhantering skydd metoderna. 

Kryptering i vila är vanliga säkerhetskrav. I Azure, kan organisationer uppnå kryptering i vila utan kostnaden för implementering och hantering och risken för en anpassad nyckel lösning. Organisationer har du möjlighet att låta Azure helt hantera kryptering i vila. Dessutom kan har organisationer olika alternativ för att hantera nära kryptering eller krypteringsnycklar.

## <a name="what-is-encryption-at-rest"></a>Vad är kryptering i vila?

Kryptering i vila är kodning (kryptering) av data om det sparas. Kryptering i vila Designer i Azure Använd symmetrisk kryptering för att kryptera och dekryptera stora mängder data snabbt enligt en enkel konceptuell modell:

- En symmetrisk kryptografisk nyckel används för att kryptera data när de skrivs till lagring. 
- Samma krypteringsnyckel används för att dekryptera data som den färdigställs för användning i minnet.
- Data kan vara partitionerade och olika nycklar kan användas för varje partition.
- Nycklar måste lagras på en säker plats med principer för åtkomstkontroll att begränsa åtkomsten till vissa identiteter och loggning nyckelanvändning. Datakrypteringsnycklar krypteras ofta med asymmetriska kryptering att ytterligare begränsa åtkomst.

I praktiken kräver viktiga scenarier för hantering och kontroll, samt tillgänglighet och skala garantier ytterligare konstruktioner. Microsoft Azure-kryptering i Rest-begrepp och komponenter som beskrivs nedan.

## <a name="the-purpose-of-encryption-at-rest"></a>Syftet med kryptering i vila

Kryptering i vila skyddar data lagrade data (vilande). Attacker mot data i vila omfattar försöker få tillgång till maskinvaran som data lagras och skada innehåller data. I sådana angrepp kan hårddisken för en server ha tagits mishandled under underhåll så att en angripare att ta bort hårddisken. Angriparen skulle senare försätta hårddisken i en dator under deras kontroll att försöka komma åt data. 

Kryptering i vila är utformat för att förhindra angripare från att komma åt den okrypterade data genom att säkerställa att data krypteras på disken. Om en angripare att få en hårddisk med sådana krypterade data och ingen åtkomst till krypteringsnycklarna, skulle angriparen inte äventyra data utan bra svårt. En angripare kan försöka attacker mot krypterade data, vilket är mycket mer komplexa och resursen förbrukar än åtkomst till okrypterade data på en hårddisk i ett sådant scenario. Därför kryptering i vila rekommenderas och är ett krav för hög prioritet för många organisationer. 

Kryptering i vila kan också krävas för en organisations behov av åtgärder för styrning och efterlevnad av data. Branschmässiga och statliga förordningar som HIPAA, PCI och FedRAMP, utformar skyddsåtgärder av kraven för skydd och kryptering av data. Kryptering i vila är ett obligatoriskt mått som krävs för kompatibilitet med vissa av dessa förordningar.

Förutom efterlevnad och regelkrav ska kryptering i vila uppfattas som en funktion för skydd på djupet plattform. Även om Microsoft tillhandahåller en kompatibel plattform för tjänster, program och data, omfattande anläggning och fysisk säkerhet data åtkomstkontroll och granskning, är det viktigt att ange ytterligare säkerhetsåtgärder för ”överlappande” om en av de andra säkerhetsmetoder misslyckas. Kryptering i vila innehåller sådana en ytterligare skydd.

Microsoft strävar efter att tillhandahålla kryptering i vila alternativ för molntjänster och för att ge kunderna lämplig hanterbarhet krypteringsnycklar och åtkomst till loggar som visar när krypteringsnycklar används. Dessutom samarbetar Microsoft mot mål för att göra alla kunddata krypterat i vila som standard.

## <a name="azure-encryption-at-rest-components"></a>Azure kryptering i vila komponenter

Som tidigare nämnts är målet för kryptering i vila att data som sparas på disk är krypterad med en kryptering med hemliga nyckel. För att uppnå att målet nycklarna skapas för säker måste lagring, åtkomstkontroll och hantering av krypteringsnycklarna anges. Även om information kan variera, kan Azure services kryptering på Rest-implementeringar beskrivas i termer som illustreras i följande diagram.

![Komponenter](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Lagringsplatsen för krypteringsnycklarna och åtkomstkontroll till dessa nycklar är centrala för en kryptering i vila modellen. Nycklarna måste vara mycket säkra men hanteras av angivna användare och tillgängliga för vissa tjänster. För Azure-tjänster, Azure Key Vault är den rekommenderade nyckellagring lösningen och ger en gemensam hanteringsupplevelse för tjänster. Nycklar lagras och hanteras i nyckelvalv och kan få åtkomst till en nyckelvalv till användare eller tjänster. Azure Key Vault stöder kunden skapandet av nycklar eller import av Kundnycklar för användning i kundhanterad kryptering viktiga scenarier.

### <a name="azure-active-directory"></a>Azure Active Directory

Behörighet att använda nycklarna lagras i Azure Key Vault vill hantera eller komma åt dem för kryptering i vila kryptering och dekryptering, kan ges till Azure Active Directory-konton. 

### <a name="key-hierarchy"></a>Viktiga hierarki

Mer än en krypteringsnyckel används i en kryptering i vila implementering. Asymmetrisk kryptering är användbart för att upprätta förtroende och autentisering som behövs för åtkomst och hantering. Symmetrisk kryptering är effektivare för bulk-kryptering och dekryptering, tillåter starkare kryptering och bättre prestanda. Begränsa användningen av en enda krypteringsnyckel minskar risken att nyckeln äventyras och kostnaden för att kryptera när en nyckel måste ersättas. Azure krypteringar på rest modeller med en nyckel hierarki består av följande typer av nycklar:

- **Datakrypteringsnyckeln (DEK)** – en symmetrisk AES256 nyckel som används för att kryptera en partition eller datablock.  En enskild resurs kan ha många partitioner och många datakrypteringsnycklar. Kryptera varje datablock med en annan nyckel gör kryptografi analys attacker svårare. Åtkomst till DEKs krävs av den resource provider eller program-instans som kryptering och dekryptering av ett visst block. När en DEK ersätts med en ny nyckel måste bara data i dess associerade block krypteras igen med den nya nyckeln.
- **Nyckeln kryptering nyckel (KEK)** – en asymmetrisk krypteringsnyckel som används för att kryptera krypteringsnycklarna Data. Med en nyckel krypteringsnyckel kan datakrypteringsnycklar sig själva till krypteras och kontrolleras. Den enhet som har åtkomst till KEK är annorlunda än den entitet som kräver DEK. En entitet kan broker åtkomst till DEK att begränsa åtkomsten till varje DEK till en specifik partition. Eftersom KEK krävs för att dekryptera DEKs, är KEK en enda åtkomstpunkt som DEKs kan raderas effektivt genom borttagning av KEK.

Krypteringsnycklarna Data krypteras med nyckeln krypteringsnycklarna lagras separat och endast en entitet med åtkomst till nyckeln krypteringsnyckeln får alla datakrypteringsnycklar krypteras med nyckeln. Olika modeller för lagring av nycklar stöds. Diskuteras varje modell i detalj senare i nästa avsnitt.

## <a name="data-encryption-models"></a>Kryptering datamodeller

Förstå de olika modellerna kryptering och deras- och nackdelar är viktigt för att förstå hur olika resursproviders i Azure implementera kryptering i vila. Dessa definitioner är gemensamma för alla providrar i Azure så vanliga språk och taxonomi. 

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

Serversidan kryptering med för tjänsten hanteras därför snabbt adresser behöver ha kryptering i vila med låg belastning till kunden. När det finns en kund vanligtvis öppnar Azure-portalen för målprenumerationen och resursprovidern och kontrollerar en som anger vill de att data ska vara krypterade. I vissa resurshanterare är serversidan kryptering med nycklar som hanteras av tjänsten aktiverad som standard.

Kryptering för serversidan med Microsoft-hanterad nycklar innebär att tjänsten har fullständig åtkomst till lagrar och hanterar nycklarna. Även om vissa kunder vill hantera nycklar eftersom de anser att de får bättre säkerhet, övervägas kostnad och risker som är associerade med en anpassad nyckel lagringslösning vid utvärdering av den här modellen. I många fall kan organisationen bestämma resursvillkor eller riskerna med en lokal lösning kan större än risken för molnhantering av kryptering i vila nycklar.  Den här modellen kan dock inte tillräckliga för organisationer som har krav styra skapandet eller livscykel krypteringsnycklarna eller har olika personer Hantera tjänstens krypteringsnycklar än de som hanterar tjänsten (d.v.s. uppdelning av hantering av nycklar från den övergripande Hanteringsmodellen för tjänsten).

##### <a name="key-access"></a>Nyckelåtkomst

När SSI-kryptering med Service-hanterade nycklar används hanteras nyckelgenerering, lagring och åtkomst till alla av tjänsten. Normalt grundläggande Azure resursleverantörer kommer att lagra krypteringsnycklarna Data i ett arkiv som ligger nära data och snabbt tillgänglig och kan nås när nyckeln krypteringsnycklarna lagras i en säker intern lagring.

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

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Serversidan kryptering med service-hanterade nycklar i kund-kontrollerade maskinvara

Vissa Azure-tjänster aktivera värden din egen nyckel HYOK-nyckelhantering modellen. Den här hanteringsläge är användbart i scenarier där det finns en behovet av att kryptera data i vila och hantera nycklar i en skyddad databas utanför Microsofts kontroll. I den här modellen hämta tjänsten nyckeln från en extern plats. Garantier för tillgänglighet och prestanda påverkas och konfigurationen är mer komplexa. Dessutom eftersom tjänsten har åtkomst till DEK under åtgärder för kryptering och dekryptering liknar övergripande säkerhetsgarantier med den här modellen när nycklarna är hanteras i Azure Key Vault-kund.  Den här modellen är därför inte lämplig för de flesta organisationer om de inte har särskilda nyckelhantering krav. De flesta Azure Services stöder inte kryptering för serversidan med hjälp av server-hanterade nycklar i kund-kontrollerade maskinvara på grund av dessa begränsningar.

##### <a name="key-access"></a>Nyckelåtkomst

När servern kryptering med service-hanterade nycklar i kundens kontrollerade maskinvara används bevaras nycklarna på ett system som konfigurerats av kunden. Azure-tjänster som stöder den här modellen ger ett sätt att upprätta en säker anslutning till en kund som angetts för KeyStore.

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
- Infrastrukturtjänster eller infrastruktur som en tjänst (IaaS) använder andra molntjänster i vilken kund distribuerar operativsystem och program som finns i molnet och eventuellt.

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

Vi rekommenderar att om möjligt IaaS program utnyttja Azure Disk Encryption och kryptering i vila alternativen i alla förbrukade Azure-tjänster. I vissa fall som oregelbundna krypteringskrav eller Azure-baserad lagring, en utvecklare av ett IaaS-program kan behöva implementera kryptering i vila sig själva. Utvecklare av IaaS-lösningar kan bättre integrera med Azure hantering och kundens förväntningar genom att använda vissa Azure komponenter. Mer specifikt kan med utvecklare Azure Key Vault-tjänsten att tillhandahålla säker lagring av nycklar samt ger sina kunder med alternativ för konsekvent nyckelhantering med som mest Azure-plattformstjänster. Anpassade lösningar bör dessutom använda Azure-Managed Tjänsteidentiteter för att aktivera tjänstekonton åtkomst till krypteringsnycklarna. Information för utvecklare på Azure Key Vault och hanteras Tjänsteidentiteter finns i deras respektive SDK: er.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure-resurs providers modellen krypteringsstöd

Microsoft Azure Services varje stöd för en eller flera av kryptering i vila modeller. För vissa tjänster, men kanske en eller flera av kryptering inte gäller. För tjänster som stöder kundhanterad viktiga scenarier, kan de stöder endast en delmängd av nyckeltyper som har stöd för Azure Key Vault för viktiga krypteringsnycklar. Tjänster kan dessutom släpp stöd för dessa scenarier och nyckeltyper på olika scheman. Det här avsnittet beskrivs kryptering i rest-stöd när detta skrivs för var och en av viktiga data för Azure storage-tjänster.

### <a name="azure-disk-encryption"></a>Azure disk encryption

En kund med Azure-infrastrukturen som en tjänst (IaaS)-funktioner kan uppnå kryptering i vila för sina virtuella IaaS-datorer och diskar via Azure Disk Encryption. Mer information om Azure Disk encryption finns i [dokumentation för Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Azure-lagring

Alla Azure Storage-tjänster (Blob storage, Queue storage, Table storage och Azure-filer) stöd för serversidan kryptering i vila, med vissa tjänster som stöder kundhanterad nycklar och kryptering på klientsidan.  

- Serversidan: Alla Azure-lagringstjänster aktiverar kryptering på serversidan som standard med hjälp av service-hanterade nycklar som är transparent för programmet. Mer information finns i [Azure Storage Service-kryptering för Data i vila](https://docs.microsoft.com/azure/storage/storage-service-encryption). Azure Blob storage och Azure-filer stöder även RSA 2048-bitars kundhanterad nycklar i Azure Key Vault. Mer information finns i [Lagringstjänstens kryptering med kundhanterad nycklar i Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).
- På klientsidan: Azure-Blobbar, tabeller och köer stöd för kryptering på klientsidan. När du använder klientsidan kryptering kunder kryptera data och ladda upp data som en krypterad blob. Nyckelhantering görs av kunden. Mer information finns i [kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database stöder för närvarande kryptering i vila för Microsoft-hanterad tjänstsidan och kryptering på klientsidan scenarier.

Stöd för krypteringen av server tillhandahålls för närvarande via funktionen SQL kallas Transparent datakryptering. När en kund med Azure SQL Database kan skapas och hanteras för dem automatiskt TDE nyckel. Du kan aktivera kryptering i vila på databas- och programnivå. Från och med juni 2017 [Transparent Data kryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) är aktiverad som standard för nya databaser. Azure SQL Database stöder RSA 2048-bitars kundhanterad nycklar i Azure Key Vault. Mer information finns i [Transparent datakryptering med Bring Your Own Key stöd för Azure SQL Database och datalagret](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Klientsidans kryptering av data i Azure SQL Database stöds via den [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) funktion. Krypterad använder alltid en nyckel som skapas och lagras av klienten. Kunder kan lagra huvudnyckeln i ett certifikatarkiv för Windows Azure Key Vault eller en lokal maskinvarusäkerhetsmodul. SQL-användare välja med SQL Server Management Studio, vilka nyckel som de vill använda för att kryptera vilken kolumn.

|                                  |                    | **Kryptering modellen och hantering av nycklar** |                   |                    |
|----------------------------------|--------------------|--------------------|--------------------|--------------------|
|                                  | **Med hjälp av Service-hanterad nyckel för serversidan**     | **Servern använder Kundhanterad i Nyckelvalvet**             |  **Serversidan med Kundhanterad lokalt**                  | **Klienten med klient-hanterade**      |
| **Lagring och databaser**        |                    |                    |                    |                    |                    |
| Disken (IaaS)                      | -                  | Ja, RSA 2048-bitars  | Ja               | -                  |
| SQLServer (IaaS)                | Ja                | Ja, RSA 2048-bitars  | Ja                | Ja                |
| Azure SQL-databas (PaaS)        | Ja                | Ja, RSA 2048-bitars  | -                  | Ja                |
| Azure Storage (blockera/Sidblobbar) | Ja                | Ja, RSA 2048-bitars  | -                  | Ja                |
| Azure Storage (filer)            | Ja                | Ja, RSA 2048-bitars  | -                  | -                  |
| Azure Storage (tabeller, köer)   | Ja                | -                  | -                  | Ja                |
| Cosmos DB (dokumentet DB)          | Ja                | -                  | -                  | -                  |
| StorSimple                       | Ja                | -                  | -                  | Ja                |
| Backup                           | -                  | -                  | -                  | Ja                |
| **Tillgångsinformation och analys**   |                    |                    |                    |                    |
| Azure Data Factory               | Ja                | -                  | -                  | -                  |
| Azure Machine Learning           | -                  | Förhandsgranskning, RSA 2048-bitars | -                  | -                  |
| Azure Stream Analytics           | Ja                | -                  | -                  | -                  |
| HDInsight (Azure Blob Storage)   | Ja                | -                  | -                  | -                  |
| HDInsight (Data Lake lagring)    | Ja                | -                  | -                  | -                  |
| Azure Data Lake Store            | Ja                | Ja, RSA 2048-bitars  | -                  | -                  |
| Azure Data Catalog               | Ja                | -                  | -                  | -                  |
| Power BI                         | Ja                | -                  | -                  | -                  |
| **IoT-tjänster**                 |                    |                    |                    |                    |
| IoT Hub                          | -                  | -                  | -                  | Ja                |
| Service Bus                      | Ja                | -                  | -                  | Ja                |
| Event Hubs                       | Ja                | -                  | -                  | -                  |


## <a name="conclusion"></a>Sammanfattning

Skydd av kundinformation som lagras i Azure-tjänster är av avgörande betydelse till Microsoft. Alla Azure-värdtjänsten tjänster avser att ge kryptering på övriga alternativ. Grundläggande tjänster som Azure Storage, Azure SQL Database och analytics nyckel och intelligence ge redan kryptering Rest-alternativ. Vissa av dessa tjänster stöder kontrollerade Kundnycklar och kryptering på klientsidan som hanteras av tjänsten nycklar och kryptering. Microsoft Azure-tjänster brett förbättra kryptering vid Rest tillgänglighet och nya alternativ planeras för förhandsversionen och allmän tillgänglighet under de kommande månaderna.
