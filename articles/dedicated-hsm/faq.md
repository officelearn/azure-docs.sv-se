---
title: Vanliga frågor och svar – Azure dedikerad HSM | Microsoft Docs
description: Vanliga frågor om olika ämnen på Azure dedikerad HSM
services: dedicated-hsm
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: barclayn
ms.openlocfilehash: 2c77f5abac69a1bade523d2a4b6793c2a9df8cf2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447871"
---
# <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

Hitta svar på vanliga frågor om Microsoft Azure dedikerad HSM.

## <a name="the-basics"></a>Grunderna

### <a name="q-what-is-a-hardware-security-module-hsm"></a>F: Vad är en maskinvarusäkerhetsmodul (HSM)?

En maskinvarusäkerhetsmodul (HSM) är en fysisk datorenhet som används för att skydda och hantera kryptografiska nycklar. Nycklar som är lagrade i HSM: er kan användas för kryptografiska åtgärder. Nyckelmaterial kvar på ett säkert sätt i manipulationsfri, säkerhetsförslutna maskinvara moduler. HSM kan bara autentiseras och auktoriseras programmen kan använda nycklarna. Nyckelmaterialet lämnar aldrig HSM:s skyddsgräns.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>F: Vad är Azure dedikerad HSM erbjudande?

Azure dedikerad HSM är en molnbaserad tjänst som tillhandahåller HSM: er i Azure-datacenter som är direkt anslutna till en kundens virtuella nätverk. Dessa HSM: er är dedikerad nätverksinstallationer (Gemalto's SafeNet nätverk HSM 7 modellen A790). De har distribuerats direkt till en kunders privata IP-adressutrymme och Microsoft har inte åtkomst till HSM: erna kryptografiska funktioner. Endast kunden har fullständig administrativ och kryptografiska kontroll över enheterna. Kunderna ansvarar för hanteringen av enheten och de kan få fullständig aktivitetsloggar direkt från sina enheter. Dedikerade HSM: er hjälpa kunder att uppfylla efterlevnad/regelkrav, till exempel FIPS 140-2 Level 3, HIPAA, PCI-DSS och eIDAS och mycket mer.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>F: Vilken maskinvara används för dedikerad HSM?

Microsoft samarbetar med Gemalto för leverans av Azure dedikerad HSM-tjänsten. Den specifika enheten som används är den [SafeNet Luna nätverk HSM 7 modellen A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Den här enheten inte bara innehåller FIPS 140-2 Level 3 verifieras av inbyggd programvara, men erbjuder också med låg latens, hög prestanda och hög kapacitet via 10 partitioner. 

### <a name="q-what-is-an-hsm-used-for"></a>F: Vad används HSM till?

HSM:er används för lagring av kryptografinycklar som används för kryptografifunktioner som SSL (Secure Socket Layer), kryptering av data, PKI (Public Key Infrastructure), DRM (Digital Rights Management) och signering av dokument.

### <a name="q-how-does-dedicated-hsm-work"></a>F: Hur fungerar Dedikerad HSM?

Kunder kan etablera HSM: er i vissa områden med PowerShell eller kommandoradsgränssnittet. Kunden anger vilka virtuellt nätverk HSM: erna kommer ansluten till och en gång etablerats HSM: erna blir tillgängliga i det angivna undernätet på den tilldelade IP-adresser i kundens privata IP-adressutrymme. Sedan kunder kan ansluta till HSM med hjälp av SSH för hantering av installation och administration, ställa in HSM-klientanslutningar kan initiera HSM: er, skapa partitioner, definiera och tilldela roller som partitionen officer, krypto officer och krypto-användare. Kunden kommer sedan använda Gemalto tillhandahålls HSM-verktyg/SDK/klientprogramvaran för att utföra kryptografiska åtgärder från sina program.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>F: Vilka programvaran tillhandahålls med dedikerad HSM-tjänst?

Gemalto tillhandahåller all programvara för HSM-enheten som en gång tillhandahålls av Microsoft. Programmet är tillgängligt på den [Gemalto kundportalen support](https://supportportal.gemalto.com/csm/). Kunder som använder dedikerade HSM-tjänsten krävs för att registreras för Gemalto stöder och har ett kund-ID som möjliggör åtkomst och hämtning av relevanta programvara. Stöds klientprogramvaran är version 7.2 som är kompatibel med FIPS 140-2 Level 3 verifieras version på inbyggd programvara 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>F: Erbjuder Azure dedikerad HSM lösenordsbaserad och PED-baserad autentisering?

För tillfället ger Azure dedikerad HSM bara HSM: er lösenordsbaserad autentisering.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>F: Arrangerar Azure dedikerad HSM min HSM: er för mig?

Microsoft erbjuder Gemalto SafeNet Luna nätverks-HSM via dedikerade HSM-tjänst endast och kan inte vara värd för alla enheter som tillhandahålls av kunden.

### <a name="q-does-azure-dedicated-hsm-support-pinetf-features-or-meet-pci-hsm-v1-or-v2-certification-requirements"></a>F: Azure dedikerad HSM stöder PIN-kod/ETF funktioner eller uppfylla kraven för PCI HSM v1 eller v2-certifiering?

Azure dedikerad HSM-tjänsten använder SafeNet Luna nätverk HSM 7 (modell A790)-enheter. Dessa enheter stöder inte betalning HSM funktioner (t.ex PIN-kod eller ETF) eller certifieringar. Om du vill ha Azure dedikerad HSM-tjänst för betalning HSM: er i framtiden, ge feedback nedan.

## <a name="interoperability"></a>Samverkan

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>F: Hur ansluter mitt program till en dedikerad HSM?

Du kan använda Gemalto tillhandahålls HSM-verktyg/SDK/klientprogramvaran för att utföra kryptografiska åtgärder från dina program. Programmet är tillgängligt på den [Gemalto kundportalen support](https://supportportal.gemalto.com/csm/). Kunder som använder dedikerade HSM-tjänsten krävs för att registreras för Gemalto stöder och har ett kund-ID som möjliggör åtkomst och hämtning av relevanta programvara.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>F: Kan ett program ansluta till dedikerade HSM från ett annat virtuellt nätverk i en eller flera regioner?

Ja, du behöver använda [VNET-peering](../virtual-network/virtual-network-peering-overview.md) inom en region för att upprätta en anslutning mellan virtuella nätverk. För över flera regioner anslutning, måste du använda [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>F: Kan jag synkronisera dedikerad HSM med en lokal HSM: er?

Ja, du kan synkronisera lokala HSM: er med dedikerad HSM. [Point-to-Point VPN eller punkt-till-plats](../vpn-gateway/vpn-gateway-about-vpngateways.md) anslutningen kan användas för att upprätta en anslutning med det lokala nätverket.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>F: Kan jag kryptera data som används av andra Azure-tjänster med hjälp av nycklar som lagras i dedikerade HSM?

Nej. Azure dedikerade HSM: er är endast tillgängliga från i ditt virtuella nätverk.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>F: Kan jag importera nycklar från en befintlig lokal HSM till dedikerade HSM?

Ja, om du har en lokal Gemalto SafeNet HSM: er. Det finns flera metoder. I Gemalto HSM-dokumentationen.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>F: Vilka operativsystem som stöds av dedikerad HSM-klientprogrammet?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtuella: VMware, Hyper-v, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>F: Hur konfigurerar jag mitt klientprogram för att skapa en konfiguration för hög tillgänglighet med flera partitioner från flera HSM: er?

Om du vill ha hög tillgänglighet, måste du konfigurera din programkonfiguration för HSM-klienten att använda partitioner från varje HSM. I dokumentationen för Gemalto HSM klienten programvara.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>F: Vilka autentiseringsmekanismer stöds av dedikerad HSM?

Azure dedikerad HSM använder SafeNet nätverk HSM 7-enheter (modellera A790) och de stöder lösenordsbaserad autentisering.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>F: Vilken SDK: er, API: er, klientprogramvara som är tillgängliga för användning med dedikerad HSM?

PKCS #11, Java (JCA/gemensamt styrda företaget), Microsoft CAPI och CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>F: Kan jag importera/migrera nycklar från Luna 5/6 HSM till Azure dedikerade HSM: er?

Ja. Se migreringsguiden Gemalto. 

## <a name="using-your-hsm"></a>Med hjälp av din HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>F: Hur jag för att avgöra om du vill använda Azure Key Vault eller Azure dedikerad HSM?

Azure dedikerad HSM är det bästa valet för företag som migrerar till Azure lokalt program som använder HSM: er. Dedikerade HSM: er visas ett alternativ för att migrera ett program med minimala ändringar. Om kryptografiska åtgärder utförs i programkod som körs i en virtuell Azure-dator eller Web App, kan de använda dedikerade HSM. I allmänhet form av krymp programvara som körs på IaaS (infrastruktur som en tjänst)-modeller som stöder HSM: er som en nyckellager använda dedikera HSM, till exempel Application gateway eller traffic manager för utan någon produktnyckel SSL, ADCS (Active Directory Certificate Services), eller liknande PKI-verktyg, verktyg/program-används för dokumentsignering, kodsignering eller en SQL-Server (IaaS) som konfigurerats med TDE (transparent databaskryptering) med huvudnyckeln i en HSM med hjälp av en EKM-providern (extensible key management). Azure Key Vault är lämpliga för ”föddes-i-moln” program eller för kryptering i rest-scenarier där kundernas data bearbetas av PaaS (plattform som en tjänst) eller SaaS (programvara som en tjänst)-scenarier, till exempel Office 365-kund nyckel, Azure Information Protection , Azure Disk Encryption, Azure Data Lake Store-kryptering med Kundhanterade, Azure Storage kryptering med kunden hanteras nyckeln och Azure SQL med kund-hanterad nyckel.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>F: Vilka Användningsscenarier som bäst passar Azure dedikerad HSM?

Azure dedikerad HSM är mest lämplig för Migreringsscenarier. Det innebär att om du migrerar lokala program till Azure som redan använder HSM: er. Detta ger en smidig möjlighet att migrera till Azure med minimala ändringar till programmet. Om kryptografiska åtgärder utförs i programkod som körs i Virtuella Azure- eller Webbapp, kan dedikerad HSM användas. I allmänhet form av krymp programvara som körs på IaaS (infrastruktur som en tjänst)-modeller som stöder HSM: er som en förvaringsplats för nycklar kan använda dedikera HSM, som:

* Application gateway eller traffic manager för utan någon produktnyckel SSL
* ADCS (Active Directory Certificate Services)
* Liknande PKI-verktyg
* Verktyg/program-används för dokumentsignering
* Kodsignering
* SQL Server (IaaS) som konfigurerats med TDE (transparent databaskryptering) med huvudnyckeln i en HSM med hjälp av en EKM-providern (extensible key management)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>F: Kan Dedikerad HSM användas med Kundnyckel i Office 365, Azure Information Protection, Azure Data Lake Store, Disk Encryption, kryptering för Azure Storage eller Azure SQL TDE?

Nej. Dedikerad HSM etableras direkt i en kunds privata IP-adressutrymme så att den gör inte kan nås av andra Azure- eller Microsoft-tjänster.

## <a name="administration-access-and-control"></a>Administration, åtkomst och kontroll

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>F: Får kunden fullständig exklusiv kontroll över HSM: er med dedikerade HSM: er?

Ja. Varje HSM-installation är helt dedikerad till en enda kund och ingen annan har administrativ kontroll en gång etablerats och administratörslösenord ändras.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>F: Vilken åtkomstnivå har Microsoft till min HSM?

Microsoft har inte några administrativa eller kryptografiska kontroll över HSM. Microsoft har Övervakare på åtkomst via seriell port-anslutning att hämta grundläggande telemetri, till exempel temperatur och komponenten hälsa. På så sätt kan Microsoft för att ge Proaktiva meddelanden om problem med hälsotillstånd. Om det behövs kan kan kunden inaktivera det här kontot.

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>F: Kan Microsoft eller någon på Microsoft åtkomstnycklar i mitt dedikerade HSM?

Nej. Microsoft har inte åtkomst till nycklar som lagras i kund som allokerats dedikerad HSM.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>F: Kan jag uppgradera programvara/inbyggda programvaran på HSM: er som allokerats till mig?

Om du vill få bästa support rekommenderar Microsoft inte för att uppgradera programvaran/inbyggda programvaran på HSM. Kunden har fullständig administrativ kontroll inklusive uppgradera programvara/inbyggd programvara om det krävs specifika funktioner från olika firmware-versioner. Innan du gör ändringar, måste förstå konsekvenserna eftersom detta kan, till exempel gälla FIPS verifieras status. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>F: Hur hanterar jag dedikerad HSM?

Du kan hantera dedikerade HSM: er genom att öppna dem med hjälp av SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>F: Hur hanterar partitionerna på dedikerad HSM?

Klientprogrammet Gemalto HSM används för att hantera HSM: er och partitioner.

### <a name="q-how-do-i-monitor-my-hsm"></a>F: Hur övervakar jag min HSM?

En kund har fullständig åtkomst till HSM-aktivitetsloggar via syslog- och SNMP. En kund behöver du konfigurera en syslog-servern eller SNMP-server tar emot loggar eller händelser från HSM: erna.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>F: Kan jag få fullständig åtkomstlogg över alla HSM åtgärder från dedikerad HSM?

Ja. Du kan skicka loggar från HSM-installation till en syslog-server

## <a name="high-availability"></a>Hög tillgänglighet

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>F: Är det möjligt att konfigurera hög tillgänglighet i samma region eller över flera regioner?

Ja. Konfiguration för hög tillgänglighet och installationen utförs i HSM-klientprogrammet som tillhandahålls av Gemalto. HSM: er från samma virtuella nätverk eller andra virtuella nätverk i samma region eller i olika regioner eller i lokala HSM: er som är ansluten till ett virtuellt nätverk med plats-till-plats eller point-to-point VPN kan läggas till samma konfiguration för hög tillgänglighet.

### <a name="can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Kan jag lägga till HSM: er från mitt lokala nätverk till en grupp för hög tillgänglighet med Azure dedikerad HSM?

Ja. De måste uppfylla kraven för hög tillgänglighet för SafeNet Luna nätverk HSM 7.

### <a name="can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Kan jag lägga till Luna 5/6 HSM: er från lokala nätverk till en grupp för hög tillgänglighet med Azure dedikerad HSM?

Nej.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>F: Hur många HSM: er kan jag lägga till samma konfiguration för hög tillgänglighet från ett enda program?

16.

## <a name="support"></a>Support

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>F: Vad är serviceavtalet för dedikerad HSM-tjänst?

Det finns inget serviceavtal för dedikerad HSM-tjänst för tillfället. Microsoft garanterar nätverksåtkomst till enheten och kan därför standard Azure networking serviceavtal gäller.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>F: Hur används HSM: erna i Azure dedikerad HSM-skyddade?

Azure-Datacenter har omfattande fysiska och procedurmässig säkerhetskontroller. Utöver det finns dedikerade HSM: er i en mer begränsad åtkomst som del av datacentret. Dessa områden har ytterligare fysiska åtkomstkontroller och videokamera övervakning för ökad säkerhet.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>F: Vad händer om det finns en säkerhetsbrist eller maskinvara manipulering händelsen?

Dedikerad HSM-tjänsten använder SafeNet nätverk HSM 7-enheter. Fysisk och logisk ändringsidentifiering stöd för dessa installationer. Om du har en förvanskningstålig händelse zeroized är automatiskt HSM: erna.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>F: Hur undviker jag att nycklar i mitt dedikerade HSM: er inte är förlorade på grund av fel eller en skadlig insider attack?

Vi rekommenderar starkt att använda en lokal HSM säkerhetskopieringsenhet för att utföra regelbundna regelbunden säkerhetskopiering av HSM: erna för katastrofåterställning. Du måste använda en peer-to-peer eller plats-till-plats VPN-anslutning till en lokal arbetsstation som är ansluten till en HSM-enhet för säkerhetskopiering.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>F: Hur får jag support för dedikerad HSM?

På samma sätt som du får support för alla andra Azure-tjänster. Azure-supportteam kommer Eskalera till Gemalto support vid behov beroende på fallet.

### <a name="q-how-do-i-get-access-to-dedicated-hsm-client-software-documentation-firmware-images"></a>F: Hur gör jag för att få åtkomst till dedikerad HSM-klientprogramvaran, dokumentation, avbildningar av inbyggd programvara?

Kunden måste arbeta direkt med Gemalto att få åtkomst till HSM-klientprogramvaran, dokumentation, avbildningar av operativsystem/inbyggd programvara.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>F: Om det finns en säkerhetsrisk som hittades och en korrigering har släppts av Gemalto som ansvarar för uppgradering/uppdatering operativsystem/inbyggd programvara?

Microsoft har inte möjlighet att ansluta till HSM: er som allokerats till kunder. Kunder måste uppgradera och korrigera sina HSM: er.

## <a name="cryptography-and-standards"></a>Kryptering och standarder

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>F: Är det säkert att lagra krypteringsnycklar för Mina viktigaste data i dedikerade HSM?

Ja, dedikerad HSM etablerar SafeNet nätverk HSM 7-enheter att använda FIPS 140-2 nivå 3 verifierade HSM: er. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>F: Vilka kryptografiska nycklar och algoritmer stöds av dedikerad HSM?

Dedikerad HSM-tjänsten tillhandahåller SafeNet nätverk HSM 7 installationer. De stöder en mängd olika kryptografiska nyckeltyper och algoritmer, inklusive: Fullständigt stöd för Suite B

* Asymmetrisk:
  * RSA
  * DSA
  * Diffie-Hellman
  * Elliptisk kurva
  * Kryptografi (ECDSA, ECDH, Ed25519, ECIES) med namngivna, användardefinierade och Brainpool kurvor, KCDSA
* Symmetrisk:
  * AES-GCM
  * Trippel-DES
  * DES
  * ARIA, DIRIGERING
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/Message Digest/HMAC: SHA-1, SHA-2, SM3
  * Viktiga härledning: SP800 108 räknare läge
  * Viktiga radbrytning: SP800-38F
  * Slumpmässig generering av nummer: FIPS 140-2 godkända DRBG (SP 800 – 90 CTR läge) uppfyller BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>F: Är dedikerad HSM FIPS 140-2 Level 3 verifierade?

Ja. Dedikerad HSM tjänsten etablerar SafeNet nätverk HSM 7-installationer som använder FIPS 140-2 nivå 3-verifierade HSM: er.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>F: Vad behöver jag göra för att kontrollera att jag fungerar dedikerad HSM i FIPS 140-2 Level 3 verifieras läge?

Dedikerad HSM-tjänst etablerar SafeNet Luna nätverk HSM 7-enheter. Dessa installationer Använd FIPS 140-2 nivå 3 verifierade HSM: er. Standardkonfigurationen distribueras, operativsystemet och inbyggd programvara är också FIPS-verifierade. Du behöver inte vidta några åtgärder för FIPS 140-2 Level 3-kompatibilitet.

### <a name="how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Hur ser en kund till att när en HSM datorn avetableras alla nyckelmaterial rensar?

Innan du begär avetablering, har en kund zeroized HSM med hjälp av Gemalto tillhandahålls HSM-klientverktyg.

## <a name="performance-and-scale"></a>Prestanda och skalning

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>F: Hur många kryptografiska åtgärder stöds per sekund med dedikerad HSM?

Dedikerad HSM tillhandahåller SafeNet nätverk HSM 7 installationer (modell A790). Här är en sammanfattning av maximala prestanda för vissa åtgärder: 

* RSA-2048: 10 000 transaktioner per sekund
* ECC P256: 20 000 transaktioner per sekund
* AES-GCM: 17,000 transaktioner per sekund

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>F: Hur många partitioner kan skapas i dedikerade HSM?

Baserat på specifika modell för HSM används, finns det 10 partitioner som är tillgängliga.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>F: Hur många nycklar som kan användas i dedikerade HSM?

Maximalt antal nycklar. Dessa siffror kan också användas för nyckelpar om du använder asymmetriska nycklar.

* RSA 2048 - 19,000
* ECC-P256 - 91,000
* AES-256 - 218,000

Kapacitet varierar beroende på specifika viktiga attribut som angetts i mallen för nyckelgenerering och antalet partitioner.

