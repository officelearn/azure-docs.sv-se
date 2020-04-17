---
title: Microsoft Azure-datakryptering i vila | Microsoft-dokument
description: Den här artikeln innehåller en översikt över Microsoft Azure-datakryptering i vila, de övergripande funktionerna och allmänna överväganden.
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454687"
---
# <a name="azure-data-encryption-at-rest"></a>Azure Data Encryption-at-Rest

Microsoft Azure innehåller verktyg för att skydda data enligt företagets säkerhets- och efterlevnadsbehov. Detta dokument fokuserar på:

- Så här skyddas data i vila i Microsoft Azure
- Diskuterar de olika komponenter som deltar i genomförandet av uppgiftsskydd,
- Granskar för- och nackdelar med de olika viktiga hanteringsskyddsmetoderna.

Kryptering vid vila är ett vanligt säkerhetskrav. I Azure kan organisationer kryptera data i vila utan risk eller kostnad för en anpassad nyckelhanteringslösning. Organisationer har möjlighet att låta Azure hantera kryptering i vila helt. Dessutom har organisationer olika alternativ för att noggrant hantera krypterings- eller krypteringsnycklar.

## <a name="what-is-encryption-at-rest"></a>Vad är kryptering i vila?

Kryptering i vila är kodning (kryptering) av data när den sparas. Designen Kryptering på vila i Azure använder symmetrisk kryptering för att kryptera och dekryptera stora mängder data snabbt enligt en enkel konceptuell modell:

- En symmetrisk krypteringsnyckel används för att kryptera data som det skrivs till lagring.
- Samma krypteringsnyckel används för att dekryptera dessa data eftersom de är redo för användning i minnet.
- Data kan partitioneras och olika nycklar kan användas för varje partition.
- Nycklar måste lagras på en säker plats med identitetsbaserade åtkomstkontroll och granskningsprinciper. Datakrypteringsnycklar krypteras ofta med en nyckelkrypteringsnyckel i Azure Key Vault för att ytterligare begränsa åtkomsten.

I praktiken kräver viktiga hanterings- och kontrollscenarier, samt skal- och tillgänglighetssäkringar, ytterligare konstruktioner. Microsoft Azure-kryptering vid vila beskrivs nedan.

## <a name="the-purpose-of-encryption-at-rest"></a>Syftet med kryptering i vila

Kryptering i vila ger dataskydd för lagrade data (i vila). Attacker mot data i vila inkluderar försök att få fysisk åtkomst till maskinvaran som data lagras på och sedan komprometteras till inneslutna data. I en sådan attack kan en server hårddisk ha misskötts under underhåll så att en angripare att ta bort hårddisken. Senare angriparen skulle sätta hårddisken i en dator under deras kontroll för att försöka komma åt data.

Kryptering i vila är utformad för att förhindra att angriparen kommer åt okrypterade data genom att se till att data krypteras när de är på disken. Om en angripare skaffar en hårddisk med krypterade data men inte krypteringsnycklarna måste angriparen besegra krypteringen för att läsa data. Denna attack är mycket mer komplex och resurskrävande än att komma åt okrypterade data på en hårddisk. Därför rekommenderas kryptering i vila starkt och är ett högt prioriterat krav för många organisationer.

Kryptering i vila kan också krävas av en organisations behov av datastyrning och efterlevnadsinsatser. Bransch- och myndighetsregler som HIPAA, PCI och FedRAMP innehåller särskilda skyddsåtgärder när det gäller dataskydds- och krypteringskrav. Kryptering i vila är en obligatorisk åtgärd som krävs för att följa vissa av dessa regler. Mer information om Microsofts metod för FIPS 140-2-validering finns i [FIPS-publikationen (Federal Information Processing Standard) Publikation 140-2](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2). 

Förutom att uppfylla efterlevnads- och regelkrav ger kryptering i vila ett djupgående skydd. Microsoft Azure tillhandahåller en kompatibel plattform för tjänster, program och data. Det ger också omfattande lokal och fysisk säkerhet, dataåtkomstkontroll och granskning. Det är dock viktigt att tillhandahålla ytterligare "överlappande" säkerhetsåtgärder om en av de andra säkerhetsåtgärder misslyckas och kryptering i vila ger en sådan säkerhetsåtgärd.

Microsoft har åtagit sig att kryptering i vila alternativ över molntjänster och ge kunderna kontroll över krypteringsnycklar och loggar för nyckelanvändning. Dessutom arbetar Microsoft för att kryptera alla kunddata i vila som standard.

## <a name="azure-encryption-at-rest-components"></a>Azure-kryptering vid vila komponenter

Som tidigare beskrivits är målet med kryptering i vila att data som sparas på disken krypteras med en hemlig krypteringsnyckel. För att uppnå detta mål måste säker nyckel skapas, lagring, åtkomstkontroll och hantering av krypteringsnycklarna tillhandahållas. Även om informationen kan variera kan Azure-tjänster Kryptering vid vila-implementeringar beskrivas i termer som illustreras i följande diagram.

![Komponenter](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Krypteringsnycklarnas lagringsplats och åtkomstkontrollen till dessa nycklar är central för en krypteringsmodell. Nycklarna måste vara mycket säkrade men hanterbara av angivna användare och tillgängliga för specifika tjänster. För Azure-tjänster är Azure Key Vault den rekommenderade viktiga lagringslösningen och ger en gemensam hanteringsupplevelse för tjänster. Nycklar lagras och hanteras i nyckelvalv och åtkomst till ett nyckelvalv kan ges till användare eller tjänster. Azure Key Vault stöder kundskapande av nycklar eller import av kundnycklar för användning i kundhanterade krypteringsnyckelscenarier.

### <a name="azure-active-directory"></a>Azure Active Directory

Behörigheter för att använda nycklarna som lagras i Azure Key Vault, antingen för att hantera eller komma åt dem för kryptering i vila och dekryptering, kan ges till Azure Active Directory-konton.

### <a name="key-hierarchy"></a>Nyckelhierarki

Mer än en krypteringsnyckel används i en kryptering i vila implementering. Lagring av en krypteringsnyckel i Azure Key Vault säkerställer säker nyckelåtkomst och central hantering av nycklar. Tjänstens lokala åtkomst till krypteringsnycklar är dock effektivare för masskryptering och dekryptering än att interagera med Key Vault för varje dataåtgärd, vilket möjliggör starkare kryptering och bättre prestanda. Om du begränsar användningen av en enda krypteringsnyckel minskar risken för att nyckeln äventyras och kostnaden för återkryptering när en nyckel måste bytas ut. Azure-krypteringar i vila modeller använder en nyckelhierarki som består av följande typer av nycklar för att tillgodose alla dessa behov:

- **Data Encryption Key (DEK)** – En symmetrisk AES256-nyckel som används för att kryptera en partition eller ett datablock.  En enda resurs kan ha många partitioner och många datakrypteringsnycklar. Om du krypterar varje datablock med en annan nyckel blir kryptoanalysattacker svårare. Åtkomst till DEK:er krävs av resursprovidern eller programinstansen som krypterar och dekrypterar ett visst block. När en DEK ersätts med en ny nyckel måste endast data i det associerade blocket krypteras om med den nya nyckeln.
- **Nyckelkrypteringsnyckel (KEK)** – En krypteringsnyckel som används för att kryptera datakrypteringsnycklarna. Användning av en nyckelkrypteringsnyckel som aldrig lämnar Key Vault gör att själva datakrypteringsnycklarna kan krypteras och kontrolleras. Den enhet som har tillgång till KEK kan skilja sig från den enhet som kräver DEK. En enhet kan förmedla åtkomst till DEK för att begränsa åtkomsten för varje DEK till en viss partition. Eftersom KEK är skyldig att dekryptera DEK, är KEK effektivt en enda punkt genom vilken DEK effektivt kan tas bort genom radering av KEK.

Datakrypteringsnycklarna, krypterade med nyckelkrypteringsnycklarna, lagras separat och endast en entitet med åtkomst till nyckelkrypteringsnyckeln kan dekryptera dessa datakrypteringsnycklar. Olika modeller av nyckellagring stöds. Vi kommer att diskutera varje modell mer i detalj senare i nästa avsnitt.

## <a name="data-encryption-models"></a>Datakrypteringsmodeller

En förståelse för de olika krypteringsmodellerna och deras för- och nackdelar är avgörande för att förstå hur de olika resursleverantörerna i Azure implementerar kryptering i vila. Dessa definitioner delas mellan alla resursleverantörer i Azure för att säkerställa gemensamt språk och taxonomi.

Det finns tre scenarier för kryptering på serversidan:

- Kryptering på serversidan med tjänsthanterade nycklar
  - Azure Resource Providers utför krypterings- och dekrypteringsåtgärder
  - Microsoft hanterar nycklarna
  - Fullständig molnfunktionalitet

- Kryptering på serversidan med kundhanterade nycklar i Azure Key Vault
  - Azure Resource Providers utför krypterings- och dekrypteringsåtgärder
  - Kunden styr nycklar via Azure Key Vault
  - Fullständig molnfunktionalitet

- Kryptering på serversidan med kundhanterade nycklar på kundstyrd maskinvara
  - Azure Resource Providers utför krypterings- och dekrypteringsåtgärder
  - Kunden styr nycklar på kundstyrd maskinvara
  - Fullständig molnfunktionalitet

För kryptering på klientsidan bör du tänka på följande:

- Azure-tjänster kan inte se dekrypterade data
- Kunder hanterar och lagrar nycklar lokalt (eller i andra säkra butiker). Nycklar är inte tillgängliga för Azure-tjänster
- Minskad molnfunktionalitet

Krypteringsmodellerna som stöds i Azure delas upp i två huvudgrupper: "Klientkryptering" och "Server-side Encryption" som tidigare nämnts. Oberoende av kryptering på vila modell som används, Azure-tjänster rekommenderar alltid användning av en säker transport som TLS eller HTTPS. Kryptering inom transport bör därför hanteras av transportprotokollet och bör inte vara en viktig faktor för att avgöra vilken kryptering i vila modell som ska användas.

### <a name="client-encryption-model"></a>Klientkrypteringsmodell

Klientkrypteringsmodellen refererar till kryptering som utförs utanför resursleverantören eller Azure av tjänsten eller det anropande programmet. Krypteringen kan utföras av tjänstprogrammet i Azure eller av ett program som körs i kunddatacentret. I båda fallen, när du använder den här krypteringsmodellen, tar Azure Resource Provider emot en krypterad blob med data utan möjlighet att dekryptera data på något sätt eller ha tillgång till krypteringsnycklarna. I den här modellen utförs nyckelhanteringen av anropande tjänst/program och är ogenomskinlig för Azure-tjänsten.

![Client](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Krypteringsmodell på serversidan

Krypteringsmodeller på serversidan refererar till kryptering som utförs av Azure-tjänsten. I den modellen utför resursprovidern krypterings- och dekrypteringsåtgärderna. Azure Storage kan till exempel ta emot data i oformaterad text och utföra kryptering och dekryptering internt. Resursleverantören kan använda krypteringsnycklar som hanteras av Microsoft eller av kunden beroende på vilken konfiguration som anges.

![Server](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Hanteringsmodeller för krypteringsnyckel på serversidan

Var och en av server-side kryptering i vila modeller innebär utmärkande egenskaper nyckelhantering. Detta inkluderar var och hur krypteringsnycklar skapas och lagras samt åtkomstmodeller och nyckelrotationsprocedurer.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Kryptering på serversidan med tjänsthanterade nycklar

För många kunder är det viktigaste kravet att se till att data krypteras när de vilar. Server-side kryptering med hjälp av tjänsthanterade nycklar gör det möjligt för den här modellen genom att tillåta kunder att markera den specifika resursen (Storage Account, SQL DB, etc.) för kryptering och lämnar alla viktiga hanteringsaspekter såsom nyckelutgivning, rotation och säkerhetskopiering till Microsoft. De flesta Azure-tjänster som stöder kryptering i vila stöder vanligtvis den här modellen för avlastning av hanteringen av krypteringsnycklarna till Azure. Azure-resursprovidern skapar nycklarna, placerar dem i säker lagring och hämtar dem när det behövs. Det innebär att tjänsten har fullständig åtkomst till nycklarna och att tjänsten har full kontroll över livscykelhanteringen för autentiseringsuppgifter.

![Hanterade](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

Server-side kryptering med hjälp av tjänsthanterade nycklar därför snabbt adresser behovet av att ha kryptering i vila med låg overhead till kunden. När en kund är tillgänglig öppnar en vanligtvis Azure-portalen för målprenumerations- och resursprovidern och kontrollerar en ruta som anger, vill de att data ska krypteras. I vissa Resource Managers-kryptering på serversidan med tjänsthanterade nycklar är aktiverad som standard.

Server-side kryptering med Microsoft-hanterade nycklar innebär att tjänsten har full tillgång till lagra och hantera nycklarna. Medan vissa kunder kanske vill hantera nycklarna eftersom de känner att de får större säkerhet, bör kostnaden och risken i samband med en anpassad nyckellagringslösning beaktas när den här modellen utvärderas. I många fall kan en organisation fastställa att resursbegränsningar eller risker för en lokal lösning kan vara större än risken för molnhantering av krypteringen vid vilonycklar.  Den här modellen kanske inte är tillräcklig för organisationer som har krav för att styra skapandet eller livscykeln för krypteringsnycklarna eller att ha olika personal som hanterar en tjänsts krypteringsnycklar än de som hanterar tjänsten (det vill säga segregering av nyckelhantering från den övergripande hanteringsmodellen för tjänsten).

##### <a name="key-access"></a>Tillgång till nyckel

När kryptering på serversidan med tjänsthanterade nycklar används hanteras nyckelgenerering, lagring och tjänståtkomst av tjänsten. Vanligtvis lagrar de grundläggande Azure-resursleverantörerna datakrypteringsnycklarna i ett arkiv som ligger nära data och snabbt är tillgängliga och tillgängliga medan nyckelkrypteringsnycklarna lagras i ett säkert internt arkiv.

**Fördelar**

- Enkel installation
- Microsoft hanterar nyckelrotation, säkerhetskopiering och redundans
- Kunden har inte kostnaden i samband med implementering eller risken för ett anpassat nyckelhanteringssystem.

**Nackdelar**

- Ingen kundkontroll över krypteringsnycklarna (nyckelspecifikation, livscykel, återkallning, etc.)
- Ingen förmåga att segregera nyckelhantering från övergripande förvaltningsmodell för tjänsten

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kryptering på serversidan med kundhanterade nycklar i Azure Key Vault

För scenarier där kravet är att kryptera data i vila och styra krypteringsnycklarna kunder kan använda server-side kryptering med hjälp av kundhanterade nycklar i Key Vault. Vissa tjänster kan lagra endast rotnyckelkrypteringsnyckeln i Azure Key Vault och lagra den krypterade datakrypteringsnyckeln på en intern plats närmare data. I det scenariot kunder kan ta med sina egna nycklar till Key Vault (BYOK - Ta med din egen nyckel), eller generera nya, och använda dem för att kryptera önskade resurser. Medan resursprovidern utför krypterings- och dekrypteringsåtgärderna används den konfigurerade nyckelkrypteringsnyckeln som rotnyckel för alla krypteringsåtgärder.

Förlust av nyckelkrypteringsnycklar innebär förlust av data. Därför bör inte nycklar tas bort. Nycklar ska säkerhetskopieras när de skapas eller roteras. [Soft-Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) ska aktiveras på alla valv som lagrar nyckelkrypteringsnycklar. I stället för att ta bort en nyckel anger du att den är aktiverad för att göra det falskt eller ange utgångsdatumet.

##### <a name="key-access"></a>Tillgång till nyckel

Krypteringsmodellen på serversidan med kundhanterade nycklar i Azure Key Vault innebär att tjänsten kommer åt nycklarna för att kryptera och dekryptera efter behov. Kryptering vid vila nycklar görs tillgängliga för en tjänst via en åtkomstkontrollprincip. Den här principen ger tjänsten identitetsåtkomst för att ta emot nyckeln. En Azure-tjänst som körs för en associerad prenumeration kan konfigureras med en identitet i den prenumerationen. Tjänsten kan utföra Azure Active Directory-autentisering och ta emot en autentiseringstoken som identifierar sig som den tjänsten som agerar för prenumerationen. Den token kan sedan presenteras för Key Vault för att få en nyckel som den har fått åtkomst till.

För åtgärder som använder krypteringsnycklar kan en tjänstidentitet beviljas åtkomst till någon av följande åtgärder: dekryptera, kryptera, ta bort nyckel, wrapKey, verifiera, signera, hämta, lista, uppdatera, skapa, importera, ta bort, säkerhetskopiera och återställa.

Om du vill hämta en nyckel för att kryptera eller dekryptera data i vila visas tjänstidentiteten som Resource Manager-tjänstinstansen körs som måste ha UnwrapKey (för att hämta nyckeln för dekryptering) och WrapKey (för att infoga en nyckel i nyckelvalvet när du skapar en ny nyckel).

>[!NOTE]
>Mer information om Key Vault-auktorisering finns på den säkra sidan för nyckelvalvet i [Azure Key Vault-dokumentationen](../../key-vault/general/secure-your-key-vault.md).

**Fördelar**

- Full kontroll över de nycklar som används – krypteringsnycklar hanteras i kundens Key Vault under kundens kontroll.
- Möjlighet att kryptera flera tjänster till en huvudsida
- Kan segregera nyckelhantering från övergripande hanteringsmodell för tjänsten
- Kan definiera tjänst- och nyckelplats mellan regioner

**Nackdelar**

- Kunden har fullt ansvar för nyckelåtkomsthantering
- Kunden har fullt ansvar för nyckellivscykelhantering
- Ytterligare konfigurationsinställningar & konfigurationskostnaderna

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Kryptering på serversidan med kundhanterade nycklar i kundstyrd maskinvara

Vissa Azure-tjänster aktiverar hyok-nyckelhanteringsmodellen (Host Your Own Key Management Model). Det här hanteringsläget är användbart i scenarier där det finns ett behov av att kryptera data i vila och hantera nycklarna i en egen databas utanför Microsofts kontroll. I den här modellen måste tjänsten hämta nyckeln från en extern plats. Prestanda- och tillgänglighetsgarantier påverkas och konfigurationen är mer komplex. Eftersom tjänsten har åtkomst till DEK under krypterings- och dekrypteringsåtgärderna liknar dessutom de övergripande säkerhetsgarantierna för den här modellen när nycklarna hanteras av kunden i Azure Key Vault.  Därför är den här modellen inte lämplig för de flesta organisationer om de inte har specifika nyckelhanteringskrav. På grund av dessa begränsningar stöder de flesta Azure Services inte kryptering på serversidan med serverhanterade nycklar i kundstyrd maskinvara.

##### <a name="key-access"></a>Tillgång till nyckel

När kryptering på serversidan med hjälp av tjänsthanterade nycklar i kundstyrd maskinvara används underhålls nycklarna på ett system som konfigurerats av kunden. Azure-tjänster som stöder den här modellen är ett sätt att upprätta en säker anslutning till en kund som levereras nyckellagring.

**Fördelar**

- Fullständig kontroll över rotnyckeln som används – krypteringsnycklar hanteras av en kund som lagras
- Möjlighet att kryptera flera tjänster till en huvudsida
- Kan segregera nyckelhantering från övergripande hanteringsmodell för tjänsten
- Kan definiera tjänst- och nyckelplats mellan regioner

**Nackdelar**

- Fullt ansvar för nyckellagring, säkerhet, prestanda och tillgänglighet
- Fullt ansvar för hantering av nyckelåtkomst
- Fullt ansvar för nyckellivscykelhantering
- Betydande kostnader för installation, konfiguration och löpande underhåll
- Ökat beroende av nätverkstillgänglighet mellan kunddatadatadata och Azure-datacenter.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Kryptering i vila i Microsofts molntjänster

Microsoft Cloud-tjänster används i alla tre molnmodeller: IaaS, PaaS, SaaS. Nedan har du exempel på hur de passar på varje modell:

- Programvarutjänster, som kallas Programvara som en server eller SaaS, som har program som tillhandahålls av molnet, till exempel Office 365.
- Plattformstjänster som kunderna använder molnet i sina program, med hjälp av molnet för saker som lagring, analys och servicebussfunktioner.
- Infrastrukturtjänster eller Infrastruktur som en tjänst (IaaS) där kunden distribuerar operativsystem och program som finns i molnet och eventuellt utnyttjar andra molntjänster.

### <a name="encryption-at-rest-for-saas-customers"></a>Kryptering i vila för SaaS-kunder

SaaS-kunder (Software as a Service) har vanligtvis kryptering i vila aktiverat eller tillgängligt i varje tjänst. Office 365 har flera alternativ för kunder att verifiera eller aktivera kryptering i vila. Information om Office 365-tjänster finns [i Kryptering i Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Kryptering i vila för PaaS-kunder

PaaS-kundens data (Platform as a Service) finns vanligtvis i en lagringstjänst som Blob Storage men kan också cachelagras eller lagras i programkörningsmiljön, till exempel en virtuell dator. Om du vill se vilka krypteringsalternativ som är tillgängliga för dig undersöker du tabellen nedan för de lagrings- och programplattformar som du använder.

### <a name="encryption-at-rest-for-iaas-customers"></a>Kryptering i vila för IaaS-kunder

Infrastruktur som en tjänst (IaaS) kunder kan ha en mängd olika tjänster och program som används. IaaS-tjänster kan aktivera kryptering i vila i sina Azure-värdbaserade virtuella datorer och virtuella hårddiskar med Hjälp av Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Krypterad lagring

Precis som PaaS kan IaaS-lösningar utnyttja andra Azure-tjänster som lagrar data krypterade i vila. I dessa fall kan du aktivera supporten Kryptering i vila enligt varje förbrukad Azure-tjänst. Tabellen nedan räknar upp de viktigaste lagrings-, -tjänster- och programplattformarna och modellen för kryptering i vila stöds. 

#### <a name="encrypted-compute"></a>Krypterad beräkning

Alla hanterade diskar, ögonblicksbilder och avbildningar krypteras med lagringstjänstkryptering med hjälp av en tjänsthanterad nyckel. En mer komplett lösning för kryptering vid vila säkerställer att data aldrig sparas i okrypterad form. När data bearbetas på en virtuell dator kan data sparas i Windows-sidfilen eller Linux-växlingsfilen, en kraschdump eller till en programlogg. För att säkerställa att dessa data krypteras i vila kan IaaS-program använda Azure Disk Encryption på en virtuell Azure IaaS-dator (Windows eller Linux) och virtuell disk.

#### <a name="custom-encryption-at-rest"></a>Anpassad kryptering i vila

Vi rekommenderar att IaaS-program när det är möjligt utnyttjar Azure Disk Encryption and Encryption at Rest-alternativ som tillhandahålls av alla förbrukade Azure-tjänster. I vissa fall, till exempel oregelbundna krypteringskrav eller icke-Azure-baserad lagring, kan en utvecklare av ett IaaS-program behöva implementera kryptering i vila själva. Utvecklare av IaaS-lösningar kan bättre integreras med Azure-hantering och kundernas förväntningar genom att utnyttja vissa Azure-komponenter. Specifikt bör utvecklare använda Azure Key Vault-tjänsten för att tillhandahålla säker nyckellagring samt ge sina kunder konsekventa alternativ för nyckelhantering med de flesta Azure-plattformstjänster. Dessutom bör anpassade lösningar använda Azure-Managed Service Identities för att aktivera tjänstkonton för att komma åt krypteringsnycklar. Information om utvecklare om Azure Key Vault- och Managed Service-identiteter finns i deras respektive SDK:er.

## <a name="azure-resource-providers-encryption-model-support"></a>Stöd för krypteringsmodell för Azure-resursleverantörer

Microsoft Azure Services stöder var och en av krypteringsmodellerna i vila. För vissa tjänster kan det dock hända att en eller flera krypteringsmodeller inte är tillämpliga. För tjänster som stöder kundhanterade nyckelscenarier kan de endast stödja en delmängd av de nyckeltyper som Azure Key Vault stöder för nyckelkrypteringsnycklar. Dessutom kan tjänster frigöra stöd för dessa scenarier och nyckeltyper vid olika scheman. I det här avsnittet beskrivs krypteringen i vila när det skrivs för var och en av de större Azure-datalagringstjänsterna.

### <a name="azure-disk-encryption"></a>Kryptering av Azure-disk

Alla kunder som använder Azure Infrastructure as a Service (IaaS) funktioner kan uppnå kryptering i vila för sina IaaS virtuella datorer och diskar via Azure Disk Encryption. Mer information om Azure Disk-kryptering finns i [dokumentationen för Azure Disk Encryption](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Azure-lagring

Alla Azure Storage-tjänster (Blob-lagring, kölagring, Tabelllagring och Azure-filer) stöder kryptering på serversidan i vila. Vissa tjänster stöder dessutom kundhanterade nycklar och kryptering på klientsidan. 

- Serversidan: Alla Azure Storage Services aktiverar kryptering på serversidan som standard med hjälp av tjänsthanterade nycklar, vilket är transparent för programmet. Mer information finns i [Azure Storage Service Encryption for Data at Rest](../../storage/common/storage-service-encryption.md). Azure Blob storage och Azure Files stöder också RSA 2048-bitars kundhanterade nycklar i Azure Key Vault. Mer information finns i [Lagringstjänstkryptering med kundhanterade nycklar i Azure Key Vault](../../storage/common/storage-encryption-keys-portal.md).
- Klientsidan: Azure Blobbar, Tabeller och köer stöder kryptering på klientsidan. När du använder kryptering på klientsidan krypterar kunderna data och överför data som en krypterad blob. Nyckelhantering görs av kunden. Mer information finns i [Kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database stöder för närvarande kryptering i vila för Microsoft-hanterade tjänstsidan och krypteringsscenarier på klientsidan.

Stöd för serverkryptering tillhandahålls för närvarande via SQL-funktionen Transparent datakryptering. När en Azure SQL Database-kund aktiverar TDE-nyckel skapas och hanteras automatiskt för dem. Kryptering i vila kan aktiveras på databas- och servernivå. Från och med juni 2017 aktiveras [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) som standard i nyskapade databaser. Azure SQL Database stöder RSA 2048-bitars kundhanterade nycklar i Azure Key Vault. Mer information finns i [Transparent datakryptering med stöd för ta med egen nyckel för Azure SQL Database och Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Kryptering på klientsidan av Azure SQL Database-data stöds via funktionen [Alltid krypterad.](https://msdn.microsoft.com/library/mt163865.aspx) Alltid krypterad använder en nyckel som skapats och lagrats av klienten. Kunder kan lagra huvudnyckeln i ett Windows-certifikatarkiv, Azure Key Vault eller en lokal maskinvarusäkerhetsmodul. Med HJÄLP AV SQL Server Management Studio väljer SQL-användare vilken nyckel de vill använda för att kryptera vilken kolumn.

#### <a name="encryption-model-and-key-management-table"></a>Krypteringsmodell och nyckelhanteringstabell

|                                  |                    | **Krypteringsmodell och nyckelhantering** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Använda tjänsthanterad nyckel på serversidan**     | **Server-side med hjälp av kundhanterad nyckel**             | **Klientsidan med klienthanterad nyckel**      |
| **AI och maskininlärning**      |                    |                    |                    |
| Azure Cognitive Search           | Ja                | Ja                | -                  |
| Azure Cognitive Services         | Ja                | Ja                | -                  |
| Azure Machine Learning           | Ja                | Ja                | -                  |
| Azure Machine Learning Studio    | Ja                | Förhandsgranskning, RSA 2048-bitars | -               |
| Content Moderator                | Ja                | Ja                | -                  |
| Ansikte                             | Ja                | Ja                | -                  |
| Språkförståelse           | Ja                | Ja                | -                  |
| Personanpassning                     | Ja                | Ja                | -                  |
| QnA Maker                        | Ja                | Ja                | -                  |
| Speech Services                  | Ja                | Ja                | -                  |
| Translator Text                  | Ja                | Ja                | -                  |
| Power BI                         | Ja                | Förhandsgranskning, RSA 2048-bitars | -                  |
| **Analys**                    |                    |                    |                    |
| Azure Stream Analytics           | Ja                | Ej tillämpligt\*            | -                  |
| Event Hubs                       | Ja                | Ja, alla RSA längder. | -                  |
| Functions                        | Ja                | Ja, alla RSA längder. | -                  |
| Azure Analysis Services          | Ja                | -                  | -                  |
| Azure Data Catalog               | Ja                | -                  | -                  |
| Azure HDInsight                  | Ja                | Alla                | -                  |
| Insikter för Azure Monitor-program | Ja                | Ja                | -                  |
| Azure Monitor Log Analytics      | Ja                | Ja                | -                  |
| Azure-datautforskaren              | Ja                | Ja                | -                  |
| Azure Data Factory               | Ja                | Ja                | -                  |
| Azure Data Lake Store            | Ja                | Ja, RSA 2048-bitars  | -                  |
| **Containrar**                   |                    |                    |                    |
| Azure Kubernetes Service         | Ja                | Ja                | -                  |
| Container Instances              | Ja                | Ja                | -                  |
| Container Registry               | Ja                | Ja                | -                  |
| **Compute**                      |                    |                    |                    |
| Virtuella datorer                 | Ja                | Ja, RSA 2048-bitars  | -                  |
| Skaluppsättning för virtuell dator        | Ja                | Ja, RSA 2048-bitars  | -                  |
| SAP HANA                         | Ja                | Ja, RSA 2048-bitars  | -                  |
| App Service                      | Ja                | Ja\*\*            | -                  |
| Automation                       | Ja                | Ja\*\*            | -                  |
| Azure Functions                  | Ja                | Ja\*\*            | -                  |
| Azure Portal                     | Ja                | Ja\*\*            | -                  |
| Logic Apps                       | Ja                | Ja                | -                  |
| Azure Managed Applications       | Ja                | Ja\*\*            | -                  |
| Service Bus                      | Ja                | Ja                | -                  |
| Site Recovery                    | Ja                | Ja                | -                  |
| **Databaser**                    |                    |                    |                    |
| SQL Server på Virtual Machines   | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Azure SQL Database               | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Azure SQL-databas för MariaDB   | Ja                | -                  | -                  |
| Azure SQL-databas för MySQL     | Ja                | Ja                | -                  |
| Azure SQL-databas för PostgreSQL | Ja               | Ja                | -                  |
| Azure Synapse Analytics          | Ja                | Ja, RSA 2048-bitars  | -                  |
| SQL Server Stretch Database      | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Table Storage                    | Ja                | Ja                | Ja                |
| Azure Cosmos DB                  | Ja                | Ja                | -                  |
| Azure Databricks                 | Ja                | Ja                | -                  |
| Azure Database Migration Service | Ja                | Ej tillämpligt\*              | -                  |
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
| Etablera ioT-hubbenhet      | Ja                | Ja                | -                  |
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
| Premium Blob-lagring             | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Disklagring                     | Ja                | Ja                | -                  |
| Ultra Disk lagring               | Ja                | Ja                | -                  |
| Hanterad disklagring             | Ja                | Ja                | -                  |
| File Storage                     | Ja                | Ja, RSA 2048-bitars  | -                  |
| Lagring av filpremier             | Ja                | Ja, RSA 2048-bitars  | -                  |
| File Sync                         | Ja                | Ja, RSA 2048-bitars  | -                  |
| Queue Storage                    | Ja                | Ja                | Ja                |
| Avere vFXT                       | Ja                | -                  | -                  |
| Azure Cache for Redis            | Ja                | Ej tillämpligt\*              | -                  |
| Azure NetApp Files               | Ja                | Ja                | -                  |
| Arkivlagring                  | Ja                | Ja, RSA 2048-bitars  | -                  |
| StorSimple                       | Ja                | Ja, RSA 2048-bitars  | Ja                |
| Azure Backup                     | Ja                | Ja                | Ja                |
| Data Box                         | Ja                | -                  | Ja                |
| Data Box Edge                    | Ja                | Ja                | -                  |

\*Den här tjänsten bevarar inte data. Eventuella tillfälliga cacheminnen krypteras med en Microsoft-nyckel.

\*\*Den här tjänsten stöder lagring av data i ditt eget Key Vault, Storage Account eller annan databeständig tjänst som redan stöder kryptering på serversidan med kundhanterad nyckel.

## <a name="conclusion"></a>Slutsats

Skydd av kunddata som lagras i Azure Services är av största vikt för Microsoft. Alla Azure-värdbaserade tjänster har åtagit sig att tillhandahålla kryptering vid vila alternativ. Azure-tjänster stöder antingen tjänsthanterade nycklar, kundhanterade nycklar eller kryptering på klientsidan. Azure-tjänster förbättrar i stort sett kryptering i vila tillgänglighet och nya alternativ planeras för förhandsversion och allmän tillgänglighet under de kommande månaderna.
