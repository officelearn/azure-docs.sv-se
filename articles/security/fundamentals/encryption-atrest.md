---
title: Microsoft Azure Data kryptering – på-rest | Microsoft Docs
description: Den här artikeln innehåller en översikt över Microsoft Azure Data kryptering i vila, de övergripande funktionerna och allmänna överväganden.
services: security
documentationcenter: na
author: msmbaldwin
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: mbaldwin
ms.openlocfilehash: 1e08e758fbba911d3391794f5bab31aaf6a5fc73
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81454687"
---
# <a name="azure-data-encryption-at-rest"></a>Azure Data Encryption – i vila

Microsoft Azure innehåller verktyg för att skydda data enligt företagets krav på säkerhet och efterlevnad. Det här dokumentet fokuserar på:

- Hur data skyddas i vila mellan Microsoft Azure
- Beskriver de olika komponenterna som ingår i implementeringen av data skydd.
- Granskar-och nack delar med olika metoder för nyckel hanterings skydd.

Kryptering i vila är ett vanligt säkerhets krav. I Azure kan organisationer kryptera data i vila utan risk eller kostnad för en anpassad nyckel hanterings lösning. Organisationer kan välja att låta Azure helt hantera kryptering i vila. Organisationer har dessutom olika alternativ för att noga hantera krypterings-eller krypterings nycklar.

## <a name="what-is-encryption-at-rest"></a>Vad är kryptering i vila?

Kryptering i vila är kodningen (kryptering) av data när den är beständig. Kryptering i rest-modeller i Azure använder symmetrisk kryptering för att kryptera och dekryptera stora mängder data snabbt enligt en enkel konceptuell modell:

- En symmetrisk krypterings nyckel används för att kryptera data när de skrivs till lagringen.
- Samma krypterings nyckel används för att dekryptera dessa data som de är lätta att använda i minnet.
- Data kan vara partitionerade och olika nycklar kan användas för varje partition.
- Nycklar måste lagras på en säker plats med identitetsbaserade åtkomst kontroll och gransknings principer. Data krypterings nycklar krypteras ofta med en nyckel krypterings nyckel i Azure Key Vault för att ytterligare begränsa åtkomsten.

I praktiken kräver nyckel hanterings-och kontroll scenarier, samt skalnings-och tillgänglighets garantier, ytterligare konstruktioner. Microsoft Azure kryptering i rest-koncept och-komponenter beskrivs nedan.

## <a name="the-purpose-of-encryption-at-rest"></a>Syftet med kryptering i vila

Kryptering i vila ger data skydd för lagrade data (i vila). Attacker mot data i vila inkluderar försök att få fysisk åtkomst till den maskin vara som data lagras på och sedan kompromettera de inneslutna data. I ett sådant angrepp kan en servers hård disk vara felhanterad under underhållet och göra det möjligt för en angripare att ta bort hård disken. Senare skulle angriparen lagra hård disken i en dator under sin kontroll för att försöka komma åt data.

Kryptering i vila är utformad för att förhindra att angriparen får åtkomst till okrypterade data genom att se till att data krypteras på disk. Om en angripare får en hård disk med krypterade data men inte krypterings nycklarna måste angriparen manipulera krypteringen för att läsa data. Det här angreppet är mycket mer komplicerat och Resursanvändning än att komma åt okrypterade data på en hård disk. Av den anledningen rekommenderas kryptering i vila starkt och är ett högt prioriterat krav för många organisationer.

Kryptering i vila kan också krävas av en organisations behov av data styrning och efterlevnad. Bransch-och myndighets bestämmelser som HIPAA, PCI och FedRAMP, utformar särskilda skydds nivåer för data skydd och krypterings krav. Kryptering i vila är ett obligatoriskt mått som krävs för att följa vissa av dessa regler. Mer information om Microsofts metod för FIPS 140-2-verifiering finns i [Federal Information Processing standard (FIPS) publikation 140-2](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2). 

Förutom att uppfylla kraven för efterlevnad och regler ger kryptering i rest skydd mot djupgående skydd. Microsoft Azure tillhandahåller en kompatibel plattform för tjänster, program och data. Den tillhandahåller även omfattande anläggningar och fysisk säkerhet, data åtkomst kontroll och granskning. Det är dock viktigt att tillhandahålla ytterligare "överlappande" säkerhets åtgärder om någon av de andra säkerhets åtgärderna Miss lyckas och kryptering i vila ger en sådan säkerhets åtgärd.

Microsoft värnar om kryptering på rest-alternativ över moln tjänster och ger kunderna kontroll över krypterings nycklar och loggar av nyckel användning. Dessutom arbetar Microsoft för att kryptera all kund information på rest som standard.

## <a name="azure-encryption-at-rest-components"></a>Azure-kryptering i rest-komponenter

Som tidigare nämnts är målet för kryptering i vila att data som finns kvar på disken krypteras med en hemlig krypterings nyckel. För att uppnå detta måste du tillhandahålla ett mål för att skapa säkra nycklar, lagring, åtkomst kontroll och hantering av krypterings nycklarna. Även om informationen kan variera kan Azure Services-kryptering i rest-implementeringar beskrivas i enlighet med följande diagram.

![Komponenter](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Lagrings platsen för krypterings nycklarna och åtkomst kontrollen till dessa nycklar är central för en kryptering i rest-modellen. Nycklarna måste vara hög säkra men hanterbara av angivna användare och tillgängliga för specifika tjänster. För Azure-tjänster är Azure Key Vault den rekommenderade nyckel lagrings lösningen och ger en gemensam hanterings upplevelse över tjänster. Nycklar lagras och hanteras i nyckel valv, och åtkomst till ett nyckel valv kan ges till användare eller tjänster. Azure Key Vault stöder kund skapande av nycklar eller import av kund nycklar för användning i scenarier med Kundhanterade krypterings nycklar.

### <a name="azure-active-directory"></a>Azure Active Directory

Behörigheter för att använda nycklarna som lagras i Azure Key Vault, antingen för att hantera eller komma åt dem för kryptering i rest-kryptering och dekryptering, kan ges till Azure Active Directory-konton.

### <a name="key-hierarchy"></a>Nyckel-hierarki

Mer än en krypterings nyckel används i en kryptering vid rest-implementering. Lagring av en krypterings nyckel i Azure Key Vault garanterar säker nyckel åtkomst och central hantering av nycklar. Tjänst lokal åtkomst till krypterings nycklar är dock mer effektiv för Mass kryptering och dekryptering än att interagera med Key Vault för varje data åtgärd, vilket ger bättre kryptering och bättre prestanda. Att begränsa användningen av en enskild krypterings nyckel minskar risken för att nyckeln komprometteras och kostnaden för omkryptering när en nyckel måste bytas ut. Azure-kryptering i rest-modeller använder en nyckel-hierarki som består av följande typer av nycklar för att hantera alla dessa behov:

- **Data krypterings nyckel (DEK)** – en symmetrisk AES256-nyckel som används för att kryptera en partition eller data block.  En enskild resurs kan ha många partitioner och många data krypterings nycklar. Kryptering av varje data block med en annan nyckel gör det svårare att analysera krypteringen. Åtkomst till DEKs krävs av resurs leverantören eller program instansen som krypterar och dekrypterar ett särskilt block. När en DEK ersätts med en ny nyckel måste endast data i det associerade blocket krypteras igen med den nya nyckeln.
- **Nyckel krypterings nyckel (KEK)** – en krypterings nyckel som används för att kryptera data krypterings nycklarna. Användning av en nyckel krypterings nyckel som aldrig lämnar Key Vault tillåter att data krypterings nycklarna krypteras och kontrol leras. Entiteten som har åtkomst till KEK kan skilja sig från den entitet som kräver DEK. En entitet kan Broker-åtkomst till DEK för att begränsa åtkomsten för varje DEK till en speciell partition. Eftersom KEK krävs för att dekryptera DEKs är KEK en enda punkt med vilken DEKs kan tas bort effektivt genom borttagning av KEK.

Data krypterings nycklarna som krypteras med nyckel krypterings nycklarna lagras separat och endast en entitet med åtkomst till nyckel krypterings nyckeln kan dekryptera dessa data krypterings nycklar. Olika modeller av nyckel lagring stöds. Vi kommer att diskutera varje modell i detalj senare i nästa avsnitt.

## <a name="data-encryption-models"></a>Data krypterings modeller

Det är viktigt att förstå de olika krypterings modellerna och deras-och nack delar med att förstå hur olika resurs leverantörer i Azure implementerar kryptering i vila. Dessa definitioner delas mellan alla resurs leverantörer i Azure för att säkerställa vanliga språk och taxonomier.

Det finns tre scenarier för kryptering på Server sidan:

- Kryptering på Server sidan med tjänst-hanterade nycklar
  - Azure-resurs-providers utför krypterings-och dekrypterings åtgärder
  - Microsoft hanterar nycklarna
  - Fullständig moln funktion

- Kryptering på Server sidan med Kundhanterade nycklar i Azure Key Vault
  - Azure-resurs-providers utför krypterings-och dekrypterings åtgärder
  - Kund styr nycklar via Azure Key Vault
  - Fullständig moln funktion

- Kryptering på Server sidan med Kundhanterade nycklar på kundkontrollerad maskin vara
  - Azure-resurs-providers utför krypterings-och dekrypterings åtgärder
  - Kund styr nycklar på kundkontrollerad maskin vara
  - Fullständig moln funktion

För kryptering på klient sidan bör du tänka på följande:

- Azure-tjänster kan inte se dekrypterade data
- Kunder hanterar och lagrar nycklar lokalt (eller i andra säkra lager). Nycklar är inte tillgängliga för Azure-tjänster
- Minskad moln funktion

De krypterings modeller som stöds i Azure delas in i två huvud grupper: "klient kryptering" och "kryptering på Server sidan" som tidigare nämnts. Oberoende av vilken kryptering som används i rest-modellen rekommenderar Azure-tjänster alltid användningen av en säker transport, till exempel TLS eller HTTPS. Därför bör kryptering i transporten åtgärdas av transport protokollet och bör inte vara en viktig faktor för att fastställa vilken kryptering i rest-modellen som ska användas.

### <a name="client-encryption-model"></a>Klient krypterings modell

Klient krypterings modell syftar på kryptering som utförs utanför resurs leverantören eller Azure av tjänsten eller det anropande programmet. Krypteringen kan utföras av tjänst programmet i Azure eller av ett program som körs i kundens Data Center. I båda fallen tar Azure Resource providern emot en krypterad BLOB med data utan möjlighet att dekryptera data på något sätt eller ha åtkomst till krypterings nycklarna när du använder den här krypterings modellen. I den här modellen görs nyckel hanteringen av den anropande tjänsten/programmet och är ogenomskinlig för Azure-tjänsten.

![Klient](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Krypterings modell på Server Sidan

Krypterings modeller på Server sidan avser kryptering som utförs av Azure-tjänsten. I den modellen utför resurs leverantören åtgärderna kryptera och dekryptera. Azure Storage kan till exempel ta emot data i klartext och utföra kryptering och dekryptering internt. Resurs leverantören kan använda krypterings nycklar som hanteras av Microsoft eller av kunden beroende på den angivna konfigurationen.

![Server](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modeller för hantering av krypterings nyckel för Server Sidan

Var och en av krypteringen på Server sidan i rest-modeller innebär olika egenskaper för nyckel hantering. Detta inkluderar var och hur krypterings nycklar skapas och lagras samt åtkomst modeller och nyckel rotations procedurer.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Kryptering på Server sidan med tjänst-hanterade nycklar

För många kunder är det grundläggande kravet att se till att data krypteras när de är i vila. Kryptering på Server sidan med hjälp av hanterade nycklar aktiverar den här modellen genom att kunderna kan markera den angivna resursen (lagrings konto, SQL DB osv.) för kryptering och lämna alla viktiga hanterings aspekter som nyckel utfärdande, rotation och säkerhets kopiering till Microsoft. De flesta Azure-tjänster som har stöd för kryptering i vila stöder vanligt vis den här modellen för att avlasta hanteringen av krypterings nycklarna till Azure. Azure-adressresursen skapar nycklar, placerar dem i säkert lagrings utrymme och hämtar dem när de behövs. Det innebär att tjänsten har fullständig åtkomst till nycklarna och att tjänsten har fullständig kontroll över livs cykel hanteringen av autentiseringsuppgifter.

![leda](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

Kryptering på Server sidan med hjälp av service-hanterade nycklar tar därför snabbt itu över behovet av att ha kryptering i vila med låg kostnad för kunden. När det finns en kund som vanligt vis öppnar Azure Portal för mål prenumerationen och resurs leverantören och kontrollerar en ruta som visar att data ska krypteras. I vissa resurs hanterare kryptering på Server sidan med tjänst hanterade nycklar som standard.

Kryptering på Server sidan med Microsoft-hanterade nycklar innebär att tjänsten har fullständig åtkomst till lagring och hantering av nycklarna. Vissa kunder kanske vill hantera nycklarna eftersom de känner till att de får högre säkerhet, kostnaden och risken som är kopplade till en anpassad nyckel lagrings lösning bör övervägas när den här modellen utvärderas. I många fall kan en organisation avgöra om resurs begränsningar eller risker i en lokal lösning kan vara större än risken för moln hantering i rest-nycklar.  Den här modellen kanske inte räcker för organisationer som har krav på att kontrol lera skapandet eller livs cykeln för krypterings nycklarna eller ha olika personal som hanterar en tjänsts krypterings nycklar än de som hanterar tjänsten (det vill säga uppdelning av nyckel hantering från den övergripande hanterings modellen för tjänsten).

##### <a name="key-access"></a>Nyckel åtkomst

När Server sidans kryptering med hanterade nycklar används, hanteras nyckeln, lagringen och tjänst åtkomsten av tjänsten. Normalt lagras data krypterings nycklarna i en butik som ligger nära data och som är tillgängliga samtidigt som nyckel krypterings nycklarna lagras i ett säkert internt arkiv.

**Fördelar**

- Enkel installation
- Microsoft hanterar nyckel rotation, säkerhets kopiering och redundans
- Kunden har ingen kostnad som är kopplad till implementeringen eller risken för ett anpassat nyckel hanterings schema.

**Nackdelar**

- Ingen kund kontroll över krypterings nycklarna (nyckel specifikation, livs cykel, återkallning osv.)
- Det går inte att särskilja nyckel hantering från den övergripande hanterings modellen för tjänsten

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kryptering på Server sidan med Kundhanterade nycklar i Azure Key Vault

För scenarier där kravet är att kryptera data i vila och kontrol lera krypterings nycklarna kan kunder använda kryptering på Server sidan med Kundhanterade nycklar i Key Vault. Vissa tjänster kan bara lagra rot nyckelns krypterings nyckel i Azure Key Vault och lagra krypterings nyckeln för krypterade data på en intern plats närmare data. I det scenariot kan kunder ta med sina egna nycklar för att Key Vault (BYOK – Bring Your Own Key) eller generera nya och använda dem för att kryptera de önskade resurserna. Medan resurs leverantören utför krypterings-och dekrypterings åtgärder, använder den den konfigurerade nyckel krypterings nyckeln som rot nyckel för alla krypterings åtgärder.

Förlust av nyckel krypterings nycklar innebär förlust av data. Därför bör nycklarna inte tas bort. Nycklar ska säkerhets kopie ras när de skapas eller roteras. [Mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) ska vara aktiverat på alla valv som lagrar nyckel krypterings nycklar. I stället för att ta bort en nyckel ställer du in på falskt eller anger förfallo datum.

##### <a name="key-access"></a>Nyckel åtkomst

Krypterings modellen på Server sidan med Kundhanterade nycklar i Azure Key Vault innebär att tjänsten använder nycklarna för att kryptera och dekryptera efter behov. Kryptering i rest-nycklar görs tillgängligt för en tjänst via en princip för åtkomst kontroll. Den här principen ger åtkomst till tjänst identiteten för att ta emot nyckeln. En Azure-tjänst som körs på uppdrag av en associerad prenumeration kan konfigureras med en identitet i den prenumerationen. Tjänsten kan utföra Azure Active Directory autentisering och ta emot en autentiseringstoken som identifierar sig själv som den tjänst som agerar på uppdrag av prenumerationen. Denna token kan sedan visas för Key Vault för att få en nyckel som har getts åtkomst till.

För åtgärder som använder krypterings nycklar kan en tjänst identitet beviljas åtkomst till någon av följande åtgärder: dekryptera, kryptera, unwrapKey, wrapKey, verifiera, signera, Hämta, Visa, uppdatera, skapa, importera, ta bort, säkerhetskopiera och Återställ.

För att få en nyckel som används vid kryptering eller dekryptering av data i vila den tjänst identitet som Resource Manager-tjänstinstans ska köra som måste ha UnwrapKey (för att hämta nyckeln för dekryptering) och WrapKey (för att kunna infoga en nyckel i Key Vault när du skapar en ny nyckel).

>[!NOTE]
>Mer information om Key Vault auktorisering finns på sidan skydda ditt nyckel valv i Azure Key Vault- [dokumentationen](../../key-vault/general/secure-your-key-vault.md).

**Fördelar**

- Fullständig kontroll över de nycklar som används – krypterings nycklar hanteras i kundens Key Vault under kundens kontroll.
- Möjlighet att kryptera flera tjänster till en huvud server
- Kan åtskilja nyckel hantering från den övergripande hanterings modellen för tjänsten
- Kan definiera tjänst-och nyckel plats mellan regioner

**Nackdelar**

- Kunden har fullt ansvar för hantering av nyckel åtkomst
- Kunden har fullt ansvar för hantering av nyckel livs cykeln
- Ytterligare konfiguration & konfigurations omkostnader

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Kryptering på Server sidan med Kundhanterade nycklar i kundkontrollerad maskin vara

Vissa Azure-tjänster aktiverar nyckel hanterings modellen värd för din egen nyckel (HYOK). Det här hanterings läget är användbart i scenarier där det finns behov av att kryptera data i vila och hantera nycklarna i en egen lagrings plats utanför Microsofts kontroll. I den här modellen måste tjänsten hämta nyckeln från en extern plats. Prestanda-och tillgänglighets garantier påverkas och konfigurationen är mer komplex. Eftersom tjänsten har åtkomst till DEK under kryptering och dekryptering, liknar de övergripande säkerhets garantirna för den här modellen när nycklarna är kund hanterade i Azure Key Vault.  Därför är den här modellen inte lämplig för de flesta organisationer om de inte har särskilda krav på nyckel hantering. På grund av dessa begränsningar stöder de flesta Azure-tjänster inte kryptering på Server sidan med hjälp av Server-hanterade nycklar i kundkontrollerad maskin vara.

##### <a name="key-access"></a>Nyckel åtkomst

När Server sidans kryptering använder tjänst hanterade nycklar i kundkontrollerad maskin vara används nycklarna i ett system som kon figurer ATS av kunden. Azure-tjänster som stöder den här modellen ger ett sätt att upprätta en säker anslutning till en kund som har angett nyckel lager.

**Fördelar**

- Fullständig kontroll över den rot nyckel som används – krypterings nycklar hanteras av en kund som har tillhandahållit ett arkiv
- Möjlighet att kryptera flera tjänster till en huvud server
- Kan åtskilja nyckel hantering från den övergripande hanterings modellen för tjänsten
- Kan definiera tjänst-och nyckel plats mellan regioner

**Nackdelar**

- Fullständigt ansvar för nyckel lagring, säkerhet, prestanda och tillgänglighet
- Fullständigt ansvar för hantering av nyckel åtkomst
- Fullständigt ansvar för nyckel livs cykel hantering
- Viktiga kostnader för konfiguration, konfiguration och löpande underhåll
- Ökat beroende på nätverks tillgänglighet mellan kundens Data Center och Azure-datacenter.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Kryptering i vila i Microsofts moln tjänster

Microsoft Cloud tjänster används i alla tre moln modeller: IaaS, PaaS, SaaS. Nedan visas några exempel på hur de passar för varje modell:

- Program varu tjänster, som kallas program vara som en server eller SaaS, som har program som tillhandahålls av molnet, till exempel Office 365.
- Plattforms tjänster som kunder utnyttjar molnet i sina program, med hjälp av molnet för saker som lagring, analys och Service Bus-funktioner.
- Infrastruktur tjänster eller infrastruktur som en tjänst (IaaS) i vilken kunden distribuerar operativ system och program som finns i molnet och eventuellt använder andra moln tjänster.

### <a name="encryption-at-rest-for-saas-customers"></a>Kryptering i vila för SaaS-kunder

SaaS-kunder (Software as a Service) har vanligt vis kryptering på rest eller tillgänglig i varje tjänst. Office 365 har flera alternativ för kunderna att verifiera eller aktivera kryptering i vila. Information om Office 365-tjänster finns [i kryptering i Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Kryptering i vila för PaaS-kunder

PaaS (Platform as a Service)-kunder finns vanligt vis i en lagrings tjänst som Blob Storage men kan också cachelagras eller lagras i program körnings miljön, till exempel en virtuell dator. Om du vill se vilka krypterings alternativ som finns tillgängliga för dig, undersöker du tabellen nedan för de lagrings-och programplattformar som du använder.

### <a name="encryption-at-rest-for-iaas-customers"></a>Kryptering i vila för IaaS-kunder

IaaS-kunder (Infrastructure as a Service) kan ha en mängd olika tjänster och program som används. IaaS Services kan aktivera kryptering i vila på sina virtuella Azure-datorer och virtuella hård diskar med hjälp av Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Krypterad lagring

Som PaaS kan IaaS-lösningar utnyttja andra Azure-tjänster som lagrar data som är krypterade i vila. I dessa fall kan du aktivera kryptering vid rest-stöd som tillhandahålls av varje förbrukad Azure-tjänst. Tabellen nedan räknar upp de viktigaste lagrings enheterna, tjänsterna och programplattformarna och vilken modell av kryptering som stöds i rest. 

#### <a name="encrypted-compute"></a>Krypterad beräkning

Alla Managed Disks, ögonblicks bilder och avbildningar krypteras med hjälp av Kryptering för lagringstjänst med hjälp av en tjänst hanterad nyckel. En mer fullständig kryptering i rest-lösning säkerställer att data aldrig sparas i okrypterad form. När data bearbetas på en virtuell dator kan data vara kvar på Windows-växlingsfilen eller Linux-swap-filen, en kraschdump eller en program logg. För att säkerställa att dessa data är krypterade i vila kan IaaS-program använda Azure Disk Encryption på en virtuell Azure IaaS-dator (Windows eller Linux) och virtuell disk.

#### <a name="custom-encryption-at-rest"></a>Anpassad kryptering i vila

Vi rekommenderar att när det är möjligt utnyttjar IaaS-program Azure Disk Encryption och kryptering i rest-alternativ som tillhandahålls av alla förbrukade Azure-tjänster. I vissa fall, till exempel oregelbundet krypterings krav eller icke-Azure-baserad lagring, kan en utvecklare av ett IaaS-program behöva implementera kryptering i vila. Utvecklare av IaaS-lösningar kan bättre integrera med Azure-hantering och kund förväntningar genom att använda vissa Azure-komponenter. Mer specifikt bör utvecklare använda tjänsten Azure Key Vault för att tillhandahålla säker nyckel lagring samt förse sina kunder med enhetliga alternativ för nyckel hantering med det i de flesta Azure Platform-tjänster. Dessutom bör anpassade lösningar använda Azure-hanterade tjänst identiteter för att ge tjänst konton åtkomst till krypterings nycklar. Information om utvecklare om Azure Key Vault och hanterade tjänst identiteter finns i respektive SDK: er.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure Resource providers stöd för krypterings modell

Microsoft Azure Services har stöd för en eller flera av krypteringen i rest-modeller. För vissa tjänster är det dock inte säkert att en eller flera av krypterings modellerna är tillämpliga. För tjänster som stöder Kundhanterade nyckel scenarier kan de bara ha stöd för en delmängd av de nyckel typer som Azure Key Vault har stöd för nyckel krypterings nycklar. Dessutom kan tjänster släppa support för dessa scenarier och nyckel typer vid olika scheman. I det här avsnittet beskrivs kryptering vid rest-support när detta skrivs för var och en av de stora Azure Data Storage-tjänsterna.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Alla kunder som använder Azure Infrastructure as a Service (IaaS)-funktioner kan uppnå kryptering i vila för sina IaaS-VM: ar och diskar via Azure Disk Encryption. Mer information om Azure Disk Encryption finns i [Azure Disk Encryption-dokumentationen](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Azure-lagring

Alla Azure Storage tjänster (Blob Storage, Queue Storage, Table Storage och Azure Files) stöder kryptering på Server sidan i vila. vissa tjänster stöder även Kundhanterade nycklar och kryptering på klient sidan. 

- Server sidan: alla Azure Storage-tjänster aktiverar kryptering på Server sidan som standard med hjälp av tjänst nycklar, som är transparent för programmet. Mer information finns i [Azure Storage tjänst kryptering för vilande data](../../storage/common/storage-service-encryption.md). Azure Blob Storage och Azure Files stöder även RSA 2048-bitars Kundhanterade nycklar i Azure Key Vault. Mer information finns i [kryptering för lagringstjänst att använda Kundhanterade nycklar i Azure Key Vault](../../storage/common/storage-encryption-keys-portal.md).
- Klient sidan: Azure-blobar, tabeller och köer stöder kryptering på klient sidan. När du använder kryptering på klient sidan, krypterar kunder data och laddar upp data som en krypterad blob. Nyckel hanteringen görs av kunden. Mer information finns i [kryptering på klient sidan och Azure Key Vault för Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database stöder för närvarande kryptering i vila för Microsoft-hanterad tjänst sida och krypterings scenarier på klient sidan.

Stöd för server kryptering tillhandahålls för närvarande genom SQL-funktionen som kallas transparent datakryptering. När en Azure SQL Database-kund aktiverar TDE-nyckeln skapas och hanteras automatiskt. Kryptering i vila kan aktive ras på databas-och server nivå. Från och med juni 2017 är [Transparent datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) aktiverat som standard på nyligen skapade databaser. Azure SQL Database stöder RSA 2048-bitars Kundhanterade nycklar i Azure Key Vault. Mer information finns i [Transparent datakryptering med Bring Your Own Key stöd för Azure SQL Database och informations lager](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Kryptering på klient sidan av Azure SQL Database data stöds via funktionen [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) . Always Encrypted använder en nyckel som skapas och lagras av klienten. Kunder kan lagra huvud nyckeln i ett Windows-certifikat Arkiv, Azure Key Vault eller en lokal säkerhetsmodul för maskin vara. Med hjälp av SQL Server Management Studio väljer SQL-användare vilken nyckel de vill använda för att kryptera vilken kolumn som helst.

#### <a name="encryption-model-and-key-management-table"></a>Krypterings modell och nyckel hanterings tabell

|                                  |                    | **Krypterings modell och nyckel hantering** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Server sidan med tjänst hanterad nyckel**     | **Server sidan med kundhanterad nyckel**             | **Klient sidan med hjälp av klient hanterad nyckel**      |
| **AI och maskininlärning**      |                    |                    |                    |
| Azure Cognitive Search           | Ja                | Ja                | -                  |
| Azure Cognitive Services         | Ja                | Ja                | -                  |
| Azure Machine Learning           | Ja                | Ja                | -                  |
| Azure Machine Learning Studio    | Ja                | För hands version, RSA 2048-bitars | -               |
| Content Moderator                | Ja                | Ja                | -                  |
| Ansikte                             | Ja                | Ja                | -                  |
| Language Understanding           | Ja                | Ja                | -                  |
| Personanpassning                     | Ja                | Ja                | -                  |
| QnA Maker                        | Ja                | Ja                | -                  |
| Speech Services                  | Ja                | Ja                | -                  |
| Translator Text                  | Ja                | Ja                | -                  |
| Power BI                         | Ja                | För hands version, RSA 2048-bitars | -                  |
| **Analys**                    |                    |                    |                    |
| Azure Stream Analytics           | Ja                | Saknas\*            | -                  |
| Event Hubs                       | Ja                | Ja, alla RSA-längder. | -                  |
| Functions                        | Ja                | Ja, alla RSA-längder. | -                  |
| Azure Analysis Services          | Ja                | -                  | -                  |
| Azure Data Catalog               | Ja                | -                  | -                  |
| Azure HDInsight                  | Ja                | Alla                | -                  |
| Azure Monitor Application Insights | Ja                | Ja                | -                  |
| Azure Monitor Log Analytics      | Ja                | Ja                | -                  |
| Azure-datautforskaren              | Ja                | Ja                | -                  |
| Azure Data Factory               | Ja                | Ja                | -                  |
| Azure Data Lake Store            | Ja                | Ja, RSA 2048-bitars  | -                  |
| **Containrar**                   |                    |                    |                    |
| Azure Kubernetes Service         | Ja                | Ja                | -                  |
| Container Instances              | Ja                | Ja                | -                  |
| Container Registry               | Ja                | Ja                | -                  |
| **Compute**                      |                    |                    |                    |
| Virtual Machines                 | Ja                | Ja, RSA 2048-bitars  | -                  |
| Skalnings uppsättning för virtuell dator        | Ja                | Ja, RSA 2048-bitars  | -                  |
| SAP HANA                         | Ja                | Ja, RSA 2048-bitars  | -                  |
| App Service                      | Ja                | Ja\*\*            | -                  |
| Automation                       | Ja                | Ja\*\*            | -                  |
| Azure Functions                  | Ja                | Ja\*\*            | -                  |
| Azure-portalen                     | Ja                | Ja\*\*            | -                  |
| Logic Apps                       | Ja                | Ja                | -                  |
| Azure Managed Applications       | Ja                | Ja\*\*            | -                  |
| Service Bus                      | Ja                | Ja                | -                  |
| Site Recovery                    | Ja                | Ja                | -                  |
| **Databaser**                    |                    |                    |                    |
| SQL Server på virtuella datorer   | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Azure SQL Database               | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Azure SQL Database för MariaDB   | Ja                | -                  | -                  |
| Azure SQL Database för MySQL     | Ja                | Ja                | -                  |
| Azure SQL Database för PostgreSQL | Ja               | Ja                | -                  |
| Azure Synapse Analytics          | Ja                | Ja, RSA 2048-bitars  | -                  |
| SQL Server Stretch Database      | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Table Storage                    | Ja                | Ja                | Ja                |
| Azure Cosmos DB                  | Ja                | Ja                | -                  |
| Azure Databricks                 | Ja                | Ja                | -                  |
| Azure Database Migration Service | Ja                | Saknas\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Ja                | -                  | Ja                |
| Azure-lagringsplatser                      | Ja                | -                  | Ja                |
| **Identitet**                     |                    |                    |                    |
| Azure Active Directory           | Ja                | -                  | -                  |
| Azure Active Directory Domain Services | Ja          | Ja, RSA 2048-bitars  | -                  |
| **Integrering**                  |                    |                    |                    |
| Service Bus                      | Ja                | Ja                | Ja                |
| Event Grid                       | Ja                | -                  | -                  |
| API Management                   | Ja                | -                  | -                  |
| **IoT-tjänster**                 |                    |                    |                    |
| IoT Hub                          | Ja                | Ja                | Ja                |
| IoT Hub Device Provisioning      | Ja                | Ja                | -                  |
| **Hantering och styrning**    |                    |                    |                    |
| Azure Site Recovery              | Ja                | -                  | -                  |
| Azure Migrate                    | Ja                | Ja                | -                  |
| **Media**                        |                    |                    |                    |
| Media Services                   | Ja                | -                  | Ja                |
| **Säkerhet**                     |                    |                    |                    |
| Azure Security Center för IoT    | Ja                | Ja                | -                  |
| Azure Sentinel                   | Ja                | Ja                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Premium-Blob Storage             | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Disklagring                     | Ja                | Ja                | -                  |
| Ultra Disklagring               | Ja                | Ja                | -                  |
| Hanterade Disklagring             | Ja                | Ja                | -                  |
| File Storage                     | Ja                | Ja, RSA 2048-bitars  | -                  |
| Fil Premium Storage             | Ja                | Ja, RSA 2048-bitars  | -                  |
| File Sync                         | Ja                | Ja, RSA 2048-bitars  | -                  |
| Queue Storage                    | Ja                | Ja                | Ja                |
| Avere vFXT                       | Ja                | -                  | -                  |
| Azure Cache for Redis            | Ja                | Saknas\*              | -                  |
| Azure NetApp Files               | Ja                | Ja                | -                  |
| Arkivlagring                  | Ja                | Ja, RSA 2048-bitars  | -                  |
| StorSimple                       | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Azure Backup                     | Ja                | Ja                | Ja                |
| Data Box                         | Ja                | -                  | Ja                |
| Data Box Edge                    | Ja                | Ja                | -                  |

\*Den här tjänsten bevarar inte data. Tillfälliga cacheminnen, om det finns, krypteras med en Microsoft-nyckel.

\*\*Den här tjänsten stöder lagring av data i din egen Key Vault, lagrings konto eller annan data tjänst som redan stöder kryptering på Server sidan med kundhanterad nyckel.

## <a name="conclusion"></a>Slutsats

Skydd av kund information som lagras i Azure-tjänster är av största vikt för Microsoft. Alla Azure-värdbaserade tjänster allokeras för att tillhandahålla kryptering vid rest-alternativ. Azure-tjänster har stöd för antingen service-hanterade nycklar, Kundhanterade nycklar eller kryptering på klient sidan. Azure-tjänster ökar i stor mängd kryptering vid vila och nya alternativ planeras för för hands version och allmän tillgänglighet under kommande månader.
