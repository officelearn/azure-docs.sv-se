---
title: Vanliga frågor och svar - Azure Dedicated HSM | Microsoft-dokument
description: Vanliga frågor om olika ämnen på Azure Dedicated HSM
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: a0cb7957008308425d91abb3e0f828cc40301736
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064934"
---
# <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

Hitta svar på vanliga frågor om Microsoft Azure Dedicated HSM.

## <a name="the-basics"></a>Grunderna

### <a name="q-what-is-a-hardware-security-module-hsm"></a>F: Vad är en maskinvarusäkerhetsmodul (HSM)?

En maskinvarusäkerhetsmodul (HSM) är en fysisk datorenhet som används för att skydda och hantera kryptografiska nycklar. Nycklar som lagras i HSM kan användas för kryptografiska åtgärder. Nyckelmaterialet förblir säkert i manipuleringssäkra, manipuleringssäkra hårdvarumoduler. HSM tillåter endast autentiserade och auktoriserade program att använda nycklarna. Nyckelmaterialet lämnar aldrig HSM-skyddsgränsen.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>F: Vad är Azure Dedicated HSM-erbjudandet?

Azure Dedicated HSM är en molnbaserad tjänst som tillhandahåller HSM-datorer som finns i Azure-datacenter som är direkt anslutna till en kunds virtuella nätverk. Dessa HSMs är dedikerade nätverksenheter (Gemaltos SafeNet Network HSM 7 Modell A790). De distribueras direkt till en kunds privata IP-adressutrymme och Microsoft har ingen åtkomst till HSM:ernas kryptografiska funktionalitet. Endast kunden har fullständig administrativ och kryptografisk kontroll över dessa enheter. Kunderna är ansvariga för hanteringen av enheten och de kan få fullständiga aktivitetsloggar direkt från sina enheter. Dedikerade HSM-tillverkare hjälper kunder att uppfylla efterlevnads-/regelkrav som FIPS 140-2 Nivå 3, HIPAA, PCI-DSS och eIDAS och många andra.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>F: Vilken maskinvara används för dedikerad HSM?

Microsoft samarbetar med Gemalto för att leverera Azure Dedicated HSM-tjänsten. Den specifika enhet som används är [SafeNet Luna Network HSM 7 Model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Den här enheten ger inte bara FIPS 140-2 Nivå 3 validerad firmware, men erbjuder också låg latens, hög prestanda och hög kapacitet via 10 partitioner. 

### <a name="q-what-is-an-hsm-used-for"></a>F: Vad används en HSM för?

HSM används för att lagra kryptografiska nycklar som används för kryptografiska funktioner som SSL (säkert socketlager), kryptera data, PKI (infrastruktur för offentliga nycklar), DRM (hantering av digitala rättigheter) och signeringsdokument.

### <a name="q-how-does-dedicated-hsm-work"></a>F: Hur fungerar dedikerad HSM?

Kunder kan etablera HSM:er i specifika regioner med PowerShell eller kommandoradsgränssnitt. Kunden anger vilket virtuellt nätverk HSM:erna ska anslutas till och när de etablerat hsm:erna kommer att vara tillgängliga i det angivna undernätet på tilldelade IP-adresser i kundens privata IP-adressutrymme. Sedan kan kunder ansluta till HSM:erna med hjälp av SSH för hantering och administration av apparater, ställa in HSM-klientanslutningar, initiera HSM-moduler, skapa partitioner, definiera och tilldela roller som partitionsansvarig, kryptoofficer och kryptoanvändare. Då kunden kommer att använda Gemalto som HSM klientverktyg / SDK / programvara för att utföra kryptografiska åtgärder från sina applikationer.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>F: Vilken programvara medföljer dedikerad HSM-tjänsten?

Gemalto levererar all programvara för HSM-enheten när den har etablerats av Microsoft. Programvaran finns på [Gemalto kundsupport portal](https://supportportal.gemalto.com/csm/). Kunder som använder den dedikerade HSM-tjänsten måste vara registrerade för Gemalto-support och ha ett kund-ID som möjliggör åtkomst och nedladdning av relevant programvara. Klientprogramvaran som stöds är version 7.2, som är kompatibel med FIPS 140-2 Nivå 3-validerad firmware version 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>F: Erbjuder Azure Dedicated HSM lösenordsbaserad och PED-baserad autentisering?

För närvarande ger Azure Dedicated HSM endast HSM lösenordsbaserad autentisering.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>F: Kommer Azure Dedicated HSM att vara värd för mina HSM:er åt mig?

Microsoft erbjuder endast Gemalto SafeNet Luna Network HSM via den dedikerade HSM-tjänsten och kan inte vara värd för några enheter som tillhandahålls av kunden.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>F: Stöder Azure Dedicated HSM pin-/EFT-funktioner (Dedikerad HSM).F: Does Azure Dedicated HSM support payment (PIN/EFT) features?

Azure Dedikerad HSM-tjänsten använder SafeNet Luna Network HSM 7 -enheter (modell A790). Dessa enheter stöder inte hsm-specifika betalningsfunktioner (till exempel PIN- eller EFT) eller certifieringar. Om du vill att Azure Dedicated HSM-tjänsten ska stödja betalnings-HSM:er i framtiden skickar du feedbacken till din Microsoft-kontorepresentant.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>F: Vilka Azure-regioner är dedikerad HSM tillgänglig i?

Från och med slutet av mars 2019 finns de särskilda HSM i de 14 regioner som anges nedan. Ytterligare regioner planeras och kan diskuteras via din Microsoft-kontorepresentant.

* USA, östra
* USA, östra 2
* USA, västra
* USA, södra centrala
* Sydostasien
* Asien, östra
* Indien, centrala
* Södra Indien
* Japan, östra
* Japan, västra
* Europa, norra
* Europa, västra
* Storbritannien, södra
* Storbritannien, västra
* Kanada, centrala
* Kanada, östra
* Australien, östra
* Australien, sydöstra

## <a name="interoperability"></a>Samverkan

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>F: Hur ansluter mitt program till en dedikerad HSM?

Du använder Gemalto som tillhandahålls HSM-klientverktyg/SDK/programvara för att utföra kryptografiska åtgärder från dina program. Programvaran finns på [Gemalto kundsupport portal](https://supportportal.gemalto.com/csm/). Kunder som använder den dedikerade HSM-tjänsten måste vara registrerade för Gemalto-support och ha ett kund-ID som möjliggör åtkomst och nedladdning av relevant programvara.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>F: Kan ett program ansluta till dedikerad HSM från ett annat virtuella nätverk i eller mellan regioner?

Ja, du måste använda [VNET-peering](../virtual-network/virtual-network-peering-overview.md) inom en region för att upprätta anslutning över virtuella nätverk. För anslutning mellan regioner måste du använda [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>F: Kan jag synkronisera dedikerad HSM med lokala HSM:er?

Ja, du kan synkronisera lokala HSM:er med dedikerad HSM. [Punkt-till-punkt-VPN- eller point-to-site-anslutning](../vpn-gateway/vpn-gateway-about-vpngateways.md) kan användas för att upprätta anslutning till ditt lokala nätverk.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>F: Kan jag kryptera data som används av andra Azure-tjänster med hjälp av nycklar som lagras i Dedikerad HSM?

Nej. Azure Dedikerade HSM-tillverkare är endast tillgängliga inifrån det virtuella nätverket.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>F: Kan jag importera nycklar från en befintlig lokal HSM till dedikerad HSM?

Ja, om du har lokala Gemalto SafeNet HSM.Yes, if you have on-premises Gemalto SafeNet HSMs. Det finns flera metoder. Se Gemalto HSM-dokumentationen.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>F: Vilka operativsystem stöds av dedikerad HSM-klientprogramvara?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtuellt: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>F: Hur konfigurerar jag mitt klientprogram för att skapa en konfiguration med hög tillgänglighet med flera partitioner från flera HSM-tillverkare?

Om du vill ha hög tillgänglighet måste du konfigurera hsm-klientprogramkonfigurationen så att partitioner från varje HSM används. Se Gemalto HSM-klientprogramvaran.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>F: Vilka autentiseringsmekanismer stöds av Dedikerad HSM?

Azure Dedicated HSM använder SafeNet Network HSM 7-enheter (modell A790) och de stöder lösenordsbaserad autentisering.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>F: Vilka SDK:er, API:er, klientprogramvara är tillgängliga att använda med dedikerad HSM?

PKCS#11, Java (JCA/JCE), Microsoft CAPI och CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>F: Kan jag importera/migrera nycklar från Luna 5/6 HSM till Azure Dedikerada HSM:er?

Ja. Se gemaltomigreringsguiden. 

## <a name="using-your-hsm"></a>Använda din HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>F: Hur bestämmer jag om jag ska använda Azure Key Vault eller Azure Dedicated HSM?

Azure Dedikerad HSM är det lämpliga valet för företag som migrerar till Azure lokala program som använder HSM.Azure Dedicated HSM is the appropriate choice for enterprises migration to Azure on-premises applications that use HSMs. Dedikerade HSM:er presenterar ett alternativ för att migrera ett program med minimala ändringar. Om kryptografiska åtgärder utförs i programmets kod som körs i en Azure VM eller Web App kan de använda dedikerad HSM. I allmänhet kan krymplindad programvara som körs i IaaS (infrastruktur som tjänst) modeller, som stöder HSM som ett nyckelarkiv kan använda Dedikera HSM, till exempel Application gateway eller traffic manager för nyckellösa SSL, ADCS (Active Directory Certificate Services) eller liknande PKI-verktyg, verktyg/program som används för dokumentsignering, kodsignering eller en SQL Server (IaaS) konfigurerad med TDE (transparent databaskryptering) med huvudnyckel i en HSM med hjälp av en EKM-leverantör (utökningsbar nyckelhantering). Azure Key Vault är lämplig för "born-in-cloud"-program eller för kryptering i vila scenarier där kunddata bearbetas av PaaS (plattform som en tjänst) eller SaaS (Software as a service) scenarier såsom Office 365 Kundnyckel, Azure Information Protection , Azure Disk Encryption, Azure Data Lake Store-kryptering med kundhanterad nyckel, Azure Storage-kryptering med kundhanterad nyckel och Azure SQL med kundhanterad nyckel.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>F: Vilka användningsscenarier passar bäst Azure Dedicated HSM?

Azure Dedicated HSM är mest lämplig för migreringsscenarier. Det innebär att om du migrerar lokala program till Azure som redan använder HSM-moduler. Detta ger ett alternativ med låg friktion för att migrera till Azure med minimala ändringar i programmet. Om kryptografiska åtgärder utförs i programmets kod som körs i Azure VM eller Web App kan dedikerad HSM användas. I allmänhet kan krympinslagna program som körs i IaaS-modeller (infrastruktur som en tjänst) som stöder HSM som en nyckelbutik använda Dedikera HSM, till exempel:

* Programgateway eller traffic manager för keyless SSL
* ADCS (Active Directory-certifikattjänster)
* Liknande PKI-verktyg
* Verktyg/program som används för dokumentsignering
* Signering av kod
* SQL Server (IaaS) konfigurerad med TDE (transparent databaskryptering) med huvudnyckel i en HSM med hjälp av en EKM-provider (utökningsbar nyckelhantering)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>F: Kan dedikerad HSM användas med Office 365 Customer Key, Azure Information Protection, Azure Data Lake Store, Diskkryptering, Azure Storage-kryptering, Azure SQL TDE?

Nej. Dedikerad HSM etableras direkt i en kunds privata IP-adressutrymme så att den inte är tillgänglig för andra Azure- eller Microsoft-tjänster.

## <a name="administration-access-and-control"></a>Administration, åtkomst och kontroll

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>F: Får kunden full exklusiv kontroll över HSM:erna med dedikerade HSM:er?

Ja. Varje HSM-apparat är helt dedikerad till en enskild kund och ingen annan har administrativ kontroll när den har etablerats och administratörslösenordet har ändrats.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>F: Vilken åtkomstnivå har Microsoft till min HSM?

Microsoft har ingen administrativ eller kryptografisk kontroll över HSM. Microsoft har åtkomst på bildskärmsnivå via seriell portanslutning för att hämta grundläggande telemetri, till exempel temperatur och komponenthälsa. På så sätt kan Microsoft tillhandahålla proaktiva meddelanden om hälsoproblem. Om det behövs kan kunden inaktivera det här kontot.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>F: Vad använder "tenantadmin"-kontot som Microsoft använder, jag är van vid att administratörsanvändaren är "admin" på SafeNet-HSM:er?

HSM-enheten levereras med en standardanvändare av admin med sitt vanliga standardlösenord. Microsoft ville inte ha standardlösenord som används medan en enhet är i en pool som väntar på att etableras av kunder. Detta skulle inte uppfylla våra stränga säkerhetskrav. Därför anger vi ett starkt lösenord som kasseras vid etableringstillfället. Dessutom, vid etablering tid skapar vi en ny användare i admin roll som kallas "tenantadmin". Den här användaren har standardlösenordet och kunderna ändrar detta som den första åtgärden när du först loggar in på den nyligen etablerade enheten. Denna process säkerställer hög grad av säkerhet och upprätthåller vårt löfte om ensam administrativ kontroll för våra kunder. Det bör noteras att "tenantadmin"-användaren kan användas för att återställa administratörsanvändarlösenordet om en kund föredrar att använda det kontot. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>F: Kan Microsoft eller någon på Microsoft komma åt nycklar i min dedikerade HSM?

Nej. Microsoft har ingen åtkomst till nycklarna som lagras i kundallokerade Dedikerade HSM.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>F: Kan jag uppgradera programvara/inbyggd programvara på HSM:er som tilldelats mig?

För att få bästa möjliga support rekommenderar Microsoft starkt att inte uppgradera programvara/inbyggd programvara på HSM. Kunden har dock full administrativ kontroll, inklusive uppgradering av programvara/inbyggd programvara om särskilda funktioner krävs från olika firmware-versioner. Innan du gör ändringar måste konsekvenserna förstås eftersom detta till exempel kan påverka FIPS-validerad status. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>F: Hur hanterar jag dedikerad HSM?

Du kan hantera dedikerade HSM:er genom att komma åt dem med SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>F: Hur hanterar jag partitioner på dedikerad HSM?

Gemalto HSM-klientprogramvaran används för att hantera HSM:er och partitioner.

### <a name="q-how-do-i-monitor-my-hsm"></a>F: Hur övervakar jag min HSM?

En kund har full tillgång till HSM aktivitetsloggar via syslog och SNMP. En kund måste konfigurera en syslog-server eller SNMP-server för att ta emot loggar eller händelser från HSM:erna.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>F: Kan jag få full åtkomstlogg över alla HSM-åtgärder från Dedikerad HSM?

Ja. Du kan skicka loggar från HSM-apparaten till en syslog-server

## <a name="high-availability"></a>Hög tillgänglighet

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>F: Är det möjligt att konfigurera hög tillgänglighet i samma region eller i flera regioner?

Ja. Konfiguration och installation av hög tillgänglighet utförs i HSM-klientprogramvaran som tillhandahålls av Gemalto. HSM:er från samma virtuella nätverk eller andra virtuella nätverk i samma region eller mellan regioner, eller på lokala HSM-moduler som är anslutna till ett virtuella nätverk med plats-till-plats- eller punkt-till-punkt-VPN, kan läggas till i samma konfiguration med hög tillgänglighet. Det bör noteras att detta synkroniserar nyckelmaterial endast och inte specifika konfigurationsobjekt som roller.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>F: Kan jag lägga till HSM:er från mitt lokala nätverk i en grupp med hög tillgänglighet med Azure Dedicated HSM?

Ja. De måste uppfylla kraven för hög tillgänglighet för SafeNet Luna Network HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>F: Kan jag lägga till Luna 5/6 HSM från lokala nätverk i en grupp med hög tillgänglighet med Azure Dedicated HSM?

Nej.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>F: Hur många HSM-tillverkare kan jag lägga till i samma konfiguration med hög tillgänglighet från ett enda program?

16 medlemmar i en HA-grupp har genomgått fullgastester med utmärkta resultat.

## <a name="support"></a>Support

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>F: Vad är SLA för dedikerad HSM-tjänst?

Det finns ingen specifik drifttidsgaranti för dedikerad HSM-tjänst. Microsoft kommer att säkerställa åtkomst på nätverksnivå till enheten och därför gäller standardsa-system för Azure-nätverk.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>F: Hur skyddas HSM:erna i Azure Dedicated HSM?

Azure-datacenter har omfattande fysiska och processuella säkerhetskontroller. Förutom det dedikerade HSM finns i ett ytterligare begränsat åtkomstområde i datacentret. Dessa områden har ytterligare fysiska åtkomstkontroller och videokameraövervakning för ökad säkerhet.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>F: Vad händer om det finns en säkerhetsöverträdelse eller maskinvarumanipuleringshändelse?

Dedikerad HSM-tjänst använder SafeNet Network HSM 7-apparater. Dessa apparater stöder fysisk och logisk manipuleringsdetektering. Om det någonsin finns en manipulationshändelse nollställs HSM:erna automatiskt.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>F: Hur ser jag till att nycklar i mina dedikerade HSM:er inte går förlorade på grund av fel eller en skadlig insiderattack?

Vi rekommenderar starkt att du använder en lokal HSM-säkerhetskopieringsenhet för att utföra regelbunden periodisk säkerhetskopiering av HSM:erna för haveriberedskap. Du måste använda en peer-to-peer- eller plats-till-plats-VPN-anslutning till en lokal arbetsstation som är ansluten till en HSM-säkerhetskopieringsenhet.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>F: Hur får jag stöd för dedikerad HSM?

Support tillhandahålls av både Microsoft och Gemalto.  Om du har problem med maskinvaran eller nätverksåtkomsten kan du ta upp en supportbegäran med Microsoft och om du har problem med HSM-konfiguration, programvara och programutveckling kan du skapa en supportbegäran med Gemalto. Om du har ett obestämt problem kan du ta upp en supportbegäran med Microsoft och sedan kan Gemalto aktiveras efter behov. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>F: Hur får jag klientprogramvara, dokumentation och tillgång till integrationsvägledning för SafeNet Luna 7 HSM?

Efter registrering för tjänsten kommer ett Gemalto kund-ID att tillhandahållas som möjliggör registrering i Gemaltos kundsupportportal. Detta kommer att möjliggöra tillgång till all programvara och dokumentation samt aktivera supportförfrågningar direkt med Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>F: Om det finns ett säkerhetsproblem hittades och en patch släpps av Gemalto, vem är ansvarig för uppgradering / patching OS / Firmware?

Microsoft har inte möjlighet att ansluta till HSM-tillverkare som allokerats till kunder. Kunder måste uppgradera och korrigera sina HSM:er.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>F: Vad händer om jag behöver starta om min HSM?

HSM har en kommandorad omstart alternativ, men vi upplever omstart hänga frågor periodvis och av denna anledning rekommenderas för den säkraste omstarten som du tar upp en supportbegäran med Microsoft för att få enheten fysiskt startas om. 

## <a name="cryptography-and-standards"></a>Kryptografi och standarder

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>F: Är det säkert att lagra krypteringsnycklar för mina viktigaste data i Dedikerad HSM?

Ja, dedikerade HSM-bestämmelser SafeNet Network HSM 7-apparater som använder FIPS 140-2 Nivå 3 validerade HSM:er. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>F: Vilka kryptografiska nycklar och algoritmer stöds av Dedikerad HSM?

Dedikerade HSM-servicebestämmelser SafeNet Network HSM 7-apparater. De stöder ett brett utbud av kryptografiska nyckeltyper och algoritmer, inklusive: Support för Full Suite B

* Asymmetriska:
  * RSA
  * Dsa
  * Diffie-Hellman
  * Elliptisk kurva
  * Kryptografi (ECDSA, ECDH, Ed25519, ECIES) med namngivna, användardefinierade och Brainpool-kurvor, KCDSA
* Symmetrisk:
  * AES-GCM
  * Trippel DES
  * DES
  * ARIA, SEED
  * RC2 (på andra)
  * RC4 (på andra)
  * RC5 (på andra)
  * CAST
  * Hash/Meddelande Digest/HMAC: SHA-1, SHA-2, SM3
  * Nyckelhärledning: SP800-108 motläge
  * Nyckel omslag: SP800-38F
  * Slumpmässigt tal: FIPS 140-2 godkänd DRBG (SP 800-90 CTR-läge), som uppfyller BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>F: Valideras dedikerad HSM FIPS 140-2 Nivå 3?

Ja. Dedikerade HSM-tjänstbestämmelser SafeNet Network HSM 7-apparater som använder FIPS 140-2 Nivå 3-validerade HSM:er.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>F: Vad behöver jag göra för att se till att jag använder dedikerad HSM i FIPS 140-2 Nivå 3 validerat läge?

Dedikerade HSM-servicebestämmelserna SafeNet Luna Network HSM 7-apparater. Dessa apparater använder FIPS 140-2 Nivå 3 validerade HSM.These appliances use FIPS 140-2 Level 3 validated HSMs. Standardut distribuerad konfiguration, operativsystem och inbyggd programvara är också FIPS validerade. Du behöver inte vidta några åtgärder för FIPS 140-2 Nivå 3-efterlevnad.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>F: Hur ser en kund till att när en HSM avetableras raderas allt nyckelmaterial ut?

Innan du begär avetablering måste en kund ha nollställt HSM med hjälp av Gemalto-klientverktygen.

## <a name="performance-and-scale"></a>Prestanda och skalning

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>F: Hur många kryptografiska åtgärder stöds per sekund med dedikerad HSM?

Dedikerade HSM-bestämmelser SafeNet Network HSM 7-apparater (modell A790). Här är en sammanfattning av maximal prestanda för vissa åtgärder: 

* RSA-2048: 10 000 transaktioner per sekund
* ECC P256: 20 000 transaktioner per sekund
* AES-GCM: 17 000 transaktioner per sekund

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>F: Hur många partitioner kan skapas i Dedikerad HSM?

SafeNet Luna HSM 7 modell A790 används innehåller en licens för 10 partitioner i kostnaden för tjänsten. Enheten har en gräns på 100 partitioner och lägga till partitioner upp till den här gränsen skulle medföra extra licenskostnader och kräver installation av en ny licensfil på enheten.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>F: Hur många nycklar kan stödjas i Dedikerad HSM?

Det maximala antalet nycklar är en funktion av det tillgängliga minnet. SafeNet Luna 7-modellen A790 som används har 32 MB minne. Följande nummer är också tillämpliga på nyckelpar om du använder asymmetriska nycklar.

* RSA-2048 - 19 000
* ECC-P256 - 91 000

Kapaciteten varierar beroende på specifika nyckelattribut som anges i nyckelgenereringsmallen och antalet partitioner.
