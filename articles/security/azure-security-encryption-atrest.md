---
title: Microsoft Azure Data kryptering vid vila | Microsoft Docs
description: Den här artikeln innehåller en översikt över Microsoft Azure data kryptering i vila, övergripande funktionerna och allmänna överväganden.
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
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: 40c69d996721b664fbea5cd539f60ecc6a521d24
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457654"
---
# <a name="azure-data-encryption-at-rest"></a>Azure Data kryptering vid vila

Microsoft Azure innehåller verktyg för att skydda data i enlighet med företagets behov för säkerhet och efterlevnad. Det här dokumentet fokuserar på:

- Så här skyddas data i vila i Microsoft Azure
- Beskriver de olika komponenterna som deltar i data protection-implementering
- Granskar- och nackdelar med olika nyckelhantering protection metoder. 

Vilande kryptering är ett vanligt säkerhetskrav. I Azure, kan organisationer uppnå kryptering i vila utan kostnaden för implementering och hantering och risken för en anpassad nyckel hanteringslösning. Organisationer har alternativet för att låta Azure helt hantera kryptering i vila. Dessutom kan har organisationer olika alternativ för att hantera kryptering eller krypteringsnycklar.

## <a name="what-is-encryption-at-rest"></a>Vad är kryptering i vila?

Vilande kryptering är kodning (kryptering) av data när den sparas. Kryptering i Rest-Designer i Azure använder symmetrisk kryptering för att kryptera och dekryptera stora mängder data snabbt enligt en enkel konceptuell modell:

- En symmetrisk kryptografisk nyckel används för att kryptera data när de skrivs till lagring. 
- Du samma nyckel används för att dekryptera dessa data som den färdigställs för användning i minnet.
- Data kan partitioneras och olika nycklar kan användas för varje partition.
- Nycklar måste lagras på en säker plats med principer för åtkomstkontroll begränsa åtkomsten till vissa identiteter och loggning nyckelanvändning. Datakrypteringsnycklar krypteras ofta med asymmetrisk kryptering att ytterligare begränsa åtkomst.

I praktiken, viktiga scenarier för hantering och kontroll, samt skalbarhet och tillgänglighet säkerhet, kan du kräva ytterligare konstruktioner. Microsoft Azure-kryptering i Rest-begrepp och komponenter beskrivs nedan.

## <a name="the-purpose-of-encryption-at-rest"></a>Syftet med kryptering i vila

Kryptering i vila tillhandahåller dataskydd för lagrade data (i vila). Attacker mot data i vila omfattar försöker få tillgång till den maskinvara där data lagras och sedan äventyra inneslutna data. I sådana angrepp kan hårddisken för en server ha tagits mishandled under underhåll så att en angripare att ta bort hårddisken. Angriparen skulle senare lägger hårddisken i en dator under deras kontroll att försöka komma åt data. 

Vilande kryptering är utformad för att förhindra att angriparen från att komma åt den okrypterade data genom att kontrollera att data krypteras på disk. Om en angripare att erhålla en hårddisk med, till exempel krypterade data och ingen åtkomst till krypteringsnycklarna, skulle angriparen inte äventyra data utan bra svårt. I ett sådant scenario angriparen att försöka attacker mot krypterade data, vilket är mycket mer komplexa och resurs förbrukar än åtkomst till okrypterade data på en hårddisk. Därför kryptering i vila rekommenderar och är ett krav för hög prioritet för många organisationer. 

Kryptering i vila kan också krävas för en organisations behov av åtgärder för styrning och efterlevnad av data. Branscher och myndigheter förordningar som HIPAA, PCI och FedRAMP, utforma specifika skydd av data protection och kryptering kraven. Vilande kryptering är ett obligatoriskt mått som krävs för efterlevnad med några av dessa föreskrifter.

Förutom efterlevnad och regelkrav bör kryptering i vila uppfattas som en plattformsfunktion för skydd på djupet. Microsoft tillhandahåller en kompatibel plattform för tjänster, program, och data, omfattande anläggning och fysisk säkerhet, dataåtkomst kontroll och granskning, är det viktigt att tillhandahålla ytterligare ”överlappande” säkerhetsåtgärder om något av de andra säkerhetsmetoder misslyckas. Kryptering i vila innehåller sådana en ytterligare skydd.

Microsoft strävar efter att tillhandahålla kryptering i rest-alternativ för molntjänster och för att ge kunderna lämplig hantering av krypteringsnycklar och åtkomst till loggar som visar när krypteringsnycklar används. Dessutom har arbetar Microsoft mot målet att göra alla kunddata krypterat i vila som standard.

## <a name="azure-encryption-at-rest-components"></a>Azure kryptering i Rest-komponenter

Som tidigare nämnts, är syftet med kryptering i vila att data som sparas på disken är krypterad med en hemlig krypteringsnyckel. För att uppnå det säkra nycklarna skapandet för målet, måste lagring, åtkomstkontroll och hantering av krypteringsnycklarna anges. Även om innehåll kan variera, kan Azure services kryptering på Rest-implementeringarna beskrivas i villkor som illustreras i följande diagram.

![Komponenter](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Lagringsplatsen för krypteringsnycklar och åtkomstkontroll till dessa nycklar är centrala för en kryptering i rest-modellen. Nycklarna måste vara mycket säker men börjar hanteras av angivna användarna och tillgängliga för specifika tjänster. För Azure-tjänster, Azure Key Vault är den rekommendera nyckellagring-lösningen och ger en gemensam hanteringsupplevelse för tjänster. Nycklar lagras och hanteras i nyckelvalv och kan få åtkomst till ett nyckelvalv till användare eller tjänster. Azure Key Vault stöder kunden skapandet av nycklar eller import av Kundnycklar för användning i kundhanterad kryptering viktiga scenarier.

### <a name="azure-active-directory"></a>Azure Active Directory

Behörighet att använda nycklar som lagras i Azure Key Vault, antingen för att hantera eller för att komma åt dem för kryptering på Rest-kryptering och dekryptering, kan ges till Azure Active Directory-konton. 

### <a name="key-hierarchy"></a>Hierarki

Mer än en krypteringsnyckel används i en kryptering i rest-implementering. Asymmetrisk kryptering är användbart för att upprätta förtroende och autentisering som behövs för viktiga åtkomst och hantering. Symmetrisk kryptering är effektivare för bulk-kryptering och dekryptering, vilket ger kraftfullare kryptering och bättre prestanda. Begränsa användningen av en enda krypteringsnyckel minskar risken att nyckeln ska äventyras och kostnaden för att kryptera om när en nyckel måste ersättas. Azure har konfigurerats på rest-modeller använder en hierarki består av följande typer av nycklar:

- **Data Datakrypteringsnyckeln (DEK)** – en symmetrisk AES256 nyckel som används för att kryptera en partition eller datablock.  En enskild resurs kan ha många partitioner och många datakrypteringsnycklar. Kryptera varje datablock med en annan nyckel gör krypto analysis attacker svårare. Åtkomst till DEKs krävs av den resource provider eller program-instans som kryptera och dekryptera ett visst block. När en DEK ersätts med en ny nyckel vara endast data i dess associerade block omkrypterade med den nya nyckeln.
- **Nyckeln kryptering nyckel (KEK)** – en asymmetrisk krypteringsnyckel som används för att kryptera krypteringsnycklarna Data. Användning av en krypteringsnyckel kan datakrypteringsnycklar själva krypteras och kontrolleras. Den entitet som har åtkomst till KEK kan skilja sig från den entitet som kräver DEK. En entitet kan mäkla åtkomst till DEK att begränsa åtkomst till varje DEK till en specifik partition. Eftersom KEK krävs för att dekryptera DEKs, är KEK effektivt en som DEKs kan raderas effektivt genom borttagning av KEK.

Krypteringsnycklarna Data krypterade med nycklar för kryptering lagras separat och endast en entitet med åtkomst till den krypteringsnyckel får krypteringsnycklar Data krypteras med nyckeln. Olika modeller av nyckelarkiv stöds. Vi kommer att diskutera varje modell i detalj senare i nästa avsnitt.

## <a name="data-encryption-models"></a>Kryptering för datamodeller

Förstå de olika modellerna för kryptering och deras- och nackdelar är viktigt för att förstå hur de olika resursprovidrar i Azure implementera kryptering i vila. Dessa definitioner är gemensamma för alla resursprovidrar i Azure för att säkerställa vanliga språk och taxonomi. 

Det finns tre scenarier för kryptering på serversidan:

- Server side encryption med Tjänsthanterade nycklar
    - Azure Resursprovidrar utför åtgärder för kryptering och dekryptering
    - Microsoft hanterar nycklarna
    - Fullständig molnfunktioner

- Server side encryption använder Kundhanterade nycklar i Azure Key Vault
    - Azure Resursprovidrar utför åtgärder för kryptering och dekryptering
    - Kunden styr nycklar via Azure Key Vault
    - Fullständig molnfunktioner

- Server side encryption med Kundhanterade nycklar på kund-kontrollerade maskinvara
    - Azure Resursprovidrar utför åtgärder för kryptering och dekryptering
    - Kunden styr nycklar på kund-kontrollerade maskinvara
    - Fullständig molnfunktioner

För kryptering av klientsidan, Tänk på följande:

- Azure-tjänster inte kan se dekrypterade data
- Kunder hantera och lagra nycklar på lokalt (eller skydda butiker i andra). Nycklarna är inte tillgängliga för Azure-tjänster
- Minskad molnfunktioner

Kryptering stöds-modeller i Azure som delas upp i två grupper: ”klient” och ”serversidan kryptering” som tidigare nämnts. Observera att, oberoende av kryptering i rest-modellen används, Azure services rekommenderar alltid användningen av en säker transport, till exempel TLS- eller HTTPS. Därför bör åtgärdas av transportprotokollet kryptering i transport och får inte vara en viktig faktor avgöra vilken kryptering i rest-modell ska använda.

### <a name="client-encryption-model"></a>Klientmodell för kryptering

Klientmodell för kryptering refererar till kryptering som utförs utanför Resource Provider eller Azure av den tjänsten eller det anropande programmet. Krypteringen kan utföras av tjänstprogram i Azure eller av ett program som körs i kundernas datacenter. I båda fallen när möjligheterna med den här modellen för kryptering, får Azure Resource Provider en krypterad blob av data utan möjlighet att dekryptera data på något sätt eller har åtkomst till krypteringsnycklarna. I den här modellen nyckelhantering som görs av det anropande tjänstprogrammet och är täckande för Azure-tjänsten.

![Client](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Kryptering på serversidan modell

Serversidan kryptering modeller avser kryptering som utförs av Azure-tjänsten. I den här modellen utför Resursprovidern kryptera och dekryptera åtgärder. Till exempel Azure Storage kan ta emot data i klartext åtgärder och utför kryptering och dekryptering internt. Resursprovidern kan använda krypteringsnycklar som hanteras av Microsoft eller av kunden beroende på den angivna konfigurationen. 

![Server](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Server side encryption nyckelhantering modeller

Var och en av serversidan kryptering i rest-modeller innebär olika egenskaper för nyckelhantering. Detta inkluderar var och hur krypteringsnycklar skapas och lagras samt Åtkomstmodeller och rotation av procedurerna. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Serversidan datakryptering med hjälp av tjänsten-hanterade nycklar

För många kunder är väsentligt krav att säkerställa att data krypteras när det är i vila. Serversidan datakryptering med hjälp av tjänsten hanterade nycklar kan den här modellen genom att så att kunder kan markera den specifika resursen (Storage-konto, SQL DB o.s.v.) för kryptering och låta alla nyckelhantering aspekter, till exempel viktiga utfärdande, rotering och säkerhetskopiering till Microsoft. De flesta Azure-tjänster som stöder kryptering i vila normalt stöd för den här modellen för att avlasta hantering av krypteringsnycklar till Azure. Azure-resursprovidern skapar nycklarna, placerar dem i säker lagring och hämtar dem efter behov. Det innebär att tjänsten har fullständig åtkomst till nycklarna och tjänsten har fullständig kontroll över Livscykelhantering för autentiseringsuppgifter.

![Hanterad](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Serversidan datakryptering med hjälp av tjänsthanterade nycklar därför snabbt löser behovet av att ha kryptering i vila med låg belastning för kunden. Om det finns en kund vanligtvis öppnas Azure-portalen för målprenumerationen och resursprovidern och kontrollerar en som anger vill de att data ska krypteras. I vissa resurshanterare är kryptering på serversidan med tjänst-hanterade nycklar aktiverad som standard.

Kryptering på serversidan med Microsoft-hanterade nycklar innebär att tjänsten har fullständig åtkomst till lagrar och hanterar nycklarna. Även om vissa kunder vill hantera nycklar eftersom de tycker de få större säkerhet, övervägas kostnaderna och riskerna med en anpassad nyckel lagringslösning vid utvärdering av den här modellen. I många fall kan organisationen bestämma att resurser eller riskerna med en lokal lösning kan större än risken för hantering av kryptering i rest-nycklar.  Den här modellen kan dock inte är tillräckligt för organisationer som har krav på att kontrollera att skapandet eller livscykeln för krypteringsnycklarna eller ha olika personer hantera en tjänst för krypteringsnycklar än de som hanterar tjänsten (d.v.s. uppdelning av nyckelhantering från den övergripande Hanteringsmodellen för tjänsten).

##### <a name="key-access"></a>Nyckelåtkomst

När kryptering på serversidan med tjänst-hanterade nycklar används, hanteras skapa nyckel, lagringen och tjänståtkomst alla av tjänsten. Normalt de grundläggande Azure resursprovidrar lagrar krypteringsnycklarna Data i en butik som ligger nära data och snabbt tillgängliga och kan nås när nyckeln krypteringsnycklarna lagras i en säker intern lagring.

**Fördelar**

- Enkel installation
- Microsoft hanterar nyckelrotation, säkerhetskopiering och redundans
- Kunden har inte den kostnad som hör till implementering eller risken för ett schema för anpassade nyckelhantering.

**Nackdelar**

- Ingen kund kontroll över krypteringsnycklarna (nyckelspecifikation, livscykel, återkallande, osv.)
- Ingen möjlighet att fördela ansvaret för nyckelhantering från övergripande Hanteringsmodellen för tjänsten

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Server side encryption med Kundhanterade nycklar i Azure Key Vault 

Kryptering nycklar kunder kan använda server side Encryption använder kund hanterade nycklar i Key Vault för scenarier där kravet är att kryptera data i rest och kontroll. Vissa tjänster kan lagra endast roten krypteringsnyckel i Azure Key Vault och lagra den krypterade krypteringsnyckeln för Tjänstdata i en intern plats närmare till data. I som scenario kunder ta med deras egna nycklar till Key Vault (BYOK – Bring Your Own Key), eller skapa nya och använda dem för att kryptera de önskade resurserna. När Resursprovidern utför kryptering och dekryptering åtgärder använder den konfigurera nyckeln som rotnyckeln för alla krypteringsåtgärder. 

##### <a name="key-access"></a>Nyckelåtkomst

Kryptering på serversidan modellen med Kundhanterade nycklar i Azure Key Vault innebär att tjänsten åtkomst till nycklar för att kryptera och dekryptera efter behov. Kryptering i rest-nycklar görs tillgängliga för en tjänst via en principer för åtkomstkontroll. Den här principen ger service identity-åtkomst för att ta emot nyckeln. En Azure-tjänst som körs åt ett kopplat prenumerations kan konfigureras med en identitet i den prenumerationen. Tjänsten kan utföra Azure Active Directory-autentisering och får en autentiseringstoken identifiera sig själv som den tjänst som agerar på uppdrag av prenumerationen. Denna token kan sedan presenteras för Key Vault för att få en nyckel som den har fått åtkomst till.

För åtgärder med hjälp av krypteringsnycklar, en tjänstidentitet kan beviljas åtkomst till någon av följande åtgärder: dekryptera, kryptera, unwrapKey, wrapKey, verifiera, signera, hämta, lista, uppdatera, skapa, importera, ta bort, säkerhetskopiera och återställa.

Om du vill hämta en nyckel för användning i krypterar eller dekrypterar data i vila tjänstidentiteten som använder Resource Manager-tjänstinstans måste ha UnwrapKey (för att hämta nyckeln för dekryptering) och WrapKey (för att infoga en nyckel till nyckelvalvet när du skapar en ny nyckel).


>[!NOTE] 
>Mer information om Key Vault auktorisering finns i den säkra sidan nyckelvalv i den [dokumentationen för Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Fördelar**

- Fullständig kontroll över nycklarna används – kryptering nycklar som hanteras i kundens Key Vault kundens kontrolleras.
- Möjlighet att kryptera flera tjänster till en huvudserver
- Kan fördela ansvaret för nyckelhantering från övergripande Hanteringsmodellen för tjänsten
- Definiera tjänsten och nyckelplats i flera regioner

**Nackdelar**

- Kunden har fullt ansvar för hantering av nycklar
- Kunden har fullt ansvar för hantering av klientnyckelns livscykel
- Ytterligare kostnader för installation och konfiguration

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Serversidan datakryptering med hjälp av tjänsten-hanterade nycklar i kund-kontrollerade maskinvara

Vissa Azure-tjänster kan värden Your Own Key (HYOK) nyckelhantering modellen. Den här hanteringsläge är användbart i scenarier där det finns ett behov av att kryptera vilande data och hantera nycklar i en skyddad databas utanför Microsofts kontroll. I den här modellen måste tjänsten hämta nyckeln från en extern webbplats. Prestanda och tillgänglighet garantier som påverkas och konfigurationen är mer komplexa. Dessutom eftersom tjänsten har åtkomst till DEK under åtgärder för kryptering och dekryptering liknar övergripande säkerhetsgarantier med den här modellen när nycklarna är i Azure Key Vault hanteras av kunden.  Den här modellen är därför inte lämplig för de flesta organisationer såvida de inte har specifika nyckelhantering krav. De flesta Azure-tjänster stöder inte serversidan datakryptering med hjälp av server-hanterade nycklar i kund-kontrollerade maskinvara på grund av dessa begränsningar.

##### <a name="key-access"></a>Nyckelåtkomst

När serversidan datakryptering med hjälp av tjänsten-hanterade nycklar i kundens kontrolleras maskinvara används bevaras nycklarna på ett system som konfigurerats av kunden. Azure-tjänster som stöder den här modellen ger avsikt att skapa en säker anslutning till en kund angivna nyckelarkivet.

**Fördelar**

- Fullständig kontroll över rotnyckeln används – kryptering nycklar som hanteras av en kunden tillhandahåller butik
- Möjlighet att kryptera flera tjänster till en huvudserver
- Kan fördela ansvaret för nyckelhantering från övergripande Hanteringsmodellen för tjänsten
- Definiera tjänsten och nyckelplats i flera regioner

**Nackdelar**

- Fullt ansvar för lagring av nycklar, säkerhet, prestanda och tillgänglighet
- Fullt ansvar för hantering av nycklar
- Fullt ansvar för hantering av klientnyckelns livscykel
- Betydande installation, konfiguration och löpande underhållskostnader
- Ökad beroende på nätverkets tillgänglighet mellan kunden datacentret och Azure-datacenter.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Kryptering i vila i Microsofts molntjänster

Microsoft Cloud-tjänsterna som används i alla tre modeller: IaaS, PaaS, SaaS. Nedan finner du exempel på hur de får plats på varje modell:

- Programvarutjänster som kallas programvara som en Server eller SaaS, som har program som tillhandahålls av molnet, till exempel Office 365.
- Plattformstjänster vilka kunder utnyttja molnet i sina program med hjälp av molnet för lagring, analys och service bus-funktioner.
- Infrastrukturtjänster eller infrastruktur som en tjänst (IaaS) i vilken kund distribuerar operativsystem och program som finns i molnet och eventuellt använder andra molntjänster.

### <a name="encryption-at-rest-for-saas-customers"></a>Kryptering i vila för SaaS-kunder

Programvara som en tjänst (SaaS)-kunder har vanligtvis kryptering i vila aktiverat eller tillgängliga i varje tjänst. Office 365 har flera alternativ för kunder att verifiera eller aktivera kryptering i vila. Information om Office 365-tjänster finns i Data krypteringstekniker för Office 365.

### <a name="encryption-at-rest-for-paas-customers"></a>Kryptering i vila för PaaS-kunder

Plattform som en tjänst (PaaS) kunddata finns vanligtvis i en miljö för körning av program och alla Azure Resource Providers som används för att lagra kunddata. Om du vill se kryptering i vila granska som finns tillgängliga i tabellen nedan för de plattformar för lagring och program som du använder. Om det stöds, finns länkar till anvisningar om hur du aktiverar kryptering i vila för varje resursprovider. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Kryptering i vila för IaaS-kunder

Infrastruktur som en tjänst (IaaS)-kunder kan ha en mängd olika tjänster och program som används. IaaS-tjänster kan aktivera kryptering i vila i sina Azure-värd för virtuella datorer och virtuella hårddiskar med Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Krypterad lagring

Som PaaS, kan IaaS-lösningar använda andra Azure-tjänster som lagrar data krypteras i vila. I dessa fall kan aktivera du kryptering på Rest-support som tillhandahålls av förbrukade Azure-tjänster. I tabellen nedan innehåller större lagringen, tjänster, och programplattformar och modell för kryptering i vila som stöds. Om det stöds, länkar till anvisningar om hur du aktiverar kryptering i vila. 

#### <a name="encrypted-compute"></a>Krypterade beräkning

En fullständig kryptering i Rest-lösningen kräver att data sparas aldrig i okrypterad form. Som används på en server som läser in data i minnet och sparas data lokalt på olika sätt, inklusive Windows växlingsfilen, en kraschdumpfil och eventuella loggning som programmet kan utföra. För att säkerställa att dessa data är krypterade i vila kan IaaS-program använda Azure Disk Encryption på en Azure IaaS-dator (Windows eller Linux) och en virtuell disk. 

#### <a name="custom-encryption-at-rest"></a>Anpassade kryptering i vila

Vi rekommenderar att om möjligt, IaaS program använder sig av Azure Disk Encryption och kryptering på Rest-alternativ som tillhandahålls av de förbrukade Azure-tjänsterna. I vissa fall, som oregelbunden krypteringskrav eller Azure-baserat lagringsutrymme, utvecklare av ett IaaS-program kan behöva implementera kryptering i helt själva. Utvecklare av IaaS-lösningar kan bättre integrera med Azure hantering och kundernas förväntningar genom att använda vissa Azure-komponenter. Utvecklare bör mer specifikt kan använda Azure Key Vault-tjänsten för att ge säker samt ge sina kunder med konsekvent nyckelhanteringsalternativen med de flesta Azure-plattformstjänster. Anpassade lösningar bör dessutom använda Azure-Managed tjänstidentiteter för att aktivera tjänstkonton för att få åtkomst till krypteringsnycklarna. Information för utvecklare om Azure Key Vault och hanterade tjänstidentiteter finns i deras respektive SDK: er.

## <a name="azure-resource-providers-encryption-model-support"></a>Stöd för Azure resource providers kryptering modell

Microsoft Azure-tjänster varje stöd för en eller flera av kryptering i rest-modeller. För vissa tjänster men kan en eller flera av kryptering modeller inte tillämpas. För tjänster som har stöd för Kundhanterade viktiga scenarier, kan de stöder endast en delmängd av nyckeltyper som har stöd för Azure Key Vault för viktiga krypteringsnycklar. Tjänster kan dessutom släppa stöd för dessa scenarier och nyckeltyper på olika scheman. Det här avsnittet beskrivs kryptering i rest-stöd när detta skrivs för varje större Azure-storage datatjänster.

### <a name="azure-disk-encryption"></a>Azure-diskkryptering

Alla kunder med Azure-infrastruktur som en tjänst (IaaS)-funktioner kan uppnå kryptering i vila för sina virtuella IaaS-datorer och diskar via Azure Disk Encryption. Mer information om Azure Disk encryption finns i den [dokumentation om Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Azure-lagring

Alla Azure Storage-tjänster (Blob storage, Queue storage, Table storage och Azure Files) stöder serversidan kryptering i vila, med vissa tjänster som stöder Kundhanterade nycklar och client side encryption.  

- Serversidan: Alla Azure-lagringstjänster Aktivera kryptering för serversidan som standard med hjälp av tjänsten-hanterade nycklar som är transparent för programmet. Mer information finns i [Azure Storage Service Encryption för vilande Data](https://docs.microsoft.com/azure/storage/storage-service-encryption). Azure Blob storage och Azure Files stöder även RSA 2048-bitars Kundhanterade nycklar i Azure Key Vault. Mer information finns i [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).
- Klientsidan: Azure Blobs, tabeller och köer stöder client side encryption. När du använder client side encryption kan kunder kryptera data och ladda upp data som en krypterad blob. Nyckelhantering görs av kunden. Mer information finns i [Client Side Encryption och Azure Key Vault för Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database stöder för närvarande vilande kryptering för serversidan hanteras av Microsoft och client side encryption scenarier.

Stöd för serverkryptering av är för närvarande tillgängligt via den SQL-funktion som kallas Transparent datakryptering. När en Azure SQL Database-kund kan skapas och hanteras för dem automatiskt TDE-nyckel. Du kan aktivera kryptering i vila på databasen och server. Från och med juni 2017 [Transparent datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) är aktiverad som standard för nya databaser. Azure SQL Database stöder RSA 2048-bitars Kundhanterade nycklar i Azure Key Vault. Mer information finns i [Transparent datakryptering med Bring Your Own Key-stöd för Azure SQL Database och Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Klientsidan kryptering av data som Azure SQL Database stöds via den [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) funktionen. Alltid använder krypterad en nyckel som skapas och lagras av klienten. Kunder kan lagra huvudnyckeln i ett Windows-certifikatarkiv, Azure Key Vault eller en lokal maskinvarusäkerhetsmodul. Med SQL Server Management Studio, välja SQL-användare vilken nyckel som de vill använda för att kryptera vilken kolumn.

|                                  |                    | **Modell för kryptering och nyckelhantering** |                   |                    |
|----------------------------------|--------------------|--------------------|--------------------|--------------------|
|                                  | **Med hjälp av Tjänsthanterad nyckel för serversidan**     | **Serversidan med Kundhanterade i Key Vault**             |  **Serversidan Kundhanterad lokal användning**                  | **Klienten med hjälp av hanteras med klientprogram**      |
| **Lagring och databaser**        |                    |                    |                    |                    |                    |
| Disk (IaaS)                      | -                  | Ja, RSA 2048-bitars  | Ja               | -                  |
| SQLServer (IaaS)                | Ja                | Ja, RSA 2048-bitars  | Ja                | Ja                |
| Azure SQL (databas/Data Warehouse) | Ja                | Ja, RSA 2048-bitars  | -                  | Ja                |
| Azure Storage (Block/Sidblob) | Ja                | Ja, RSA 2048-bitars  | -                  | Ja                |
| Azure Storage (filer)            | Ja                | Ja, RSA 2048-bitars  | -                  | -                  |
| Azure Storage (tabeller, köer)   | Ja                | -                  | -                  | Ja                |
| Cosmos DB (dokumentdatabas)          | Ja                | -                  | -                  | -                  |
| StorSimple                       | Ja                | -                  | -                  | Ja                |
| Backup                           | -                  | -                  | -                  | Ja                |
| **Intelligens och analys**   |                    |                    |                    |                    |
| Azure Data Factory               | Ja                | -                  | -                  | -                  |
| Azure Machine Learning           | -                  | Förhandsgranskning, RSA 2048-bitars | -                  | -                  |
| Azure Stream Analytics           | Ja                | -                  | -                  | -                  |
| HDInsight (Azure Blob Storage)   | Ja                | -                  | -                  | -                  |
| HDInsight (Data Lake Storage)    | Ja                | -                  | -                  | -                  |
| Azure Data Lake Store            | Ja                | Ja, RSA 2048-bitars  | -                  | -                  |
| Azure Data Catalog               | Ja                | -                  | -                  | -                  |
| Power BI                         | Ja                | -                  | -                  | -                  |
| **IoT-tjänster**                 |                    |                    |                    |                    |
| IoT Hub                          | -                  | -                  | -                  | Ja                |
| Service Bus                      | Ja                | -                  | -                  | Ja                |
| Event Hubs                       | Ja                | -                  | -                  | -                  |
| Event Grid                       | Ja                | -                  | -                  | -                  |


## <a name="conclusion"></a>Sammanfattning

Skyddet av kundinformation lagras i Azure-tjänster är av yttersta vikt till Microsoft. Alla Azure-värdtjänsten tjänster är allt för att ge kryptering på Rest-alternativ. Grundläggande tjänster, till exempel Azure Storage, Azure SQL Database och viktiga analytics och smarta tjänster du redan tillhandahåller kryptering på Rest-alternativ. Vissa av dessa tjänster stöder Kundnycklar kontrolleras och client side encryption samt service-hanterade nycklar och kryptering. Microsoft Azure-tjänster brett förbättrar kryptering vid tillgänglighet för Rest och nya alternativ planeras för förhandsversionen och allmän tillgänglighet under de kommande månaderna.
