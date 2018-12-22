---
title: Vad är Dedikerad HSM? – Dedikerad HSM i Azure| Microsoft Docs
description: Översikten över dedikerad HSM i Azure tillhandahåller funktioner för lagring av nycklar i Azure som uppfyller FIPS 140-2 Level 3-certifiering
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 1eeafa33c8c1cdbcd7d0e55e3860dda1b8d451fe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080086"
---
# <a name="what-is-azure-dedicated-hsm"></a>Vad är Dedikerad HSM i Azure?

Dedikerad HSM i Azure är en Azure-tjänst som tillhandahåller lagring av kryptografiska nycklar i Azure. Dedikerad HSM uppfyller de strängaste säkerhetskraven. Det är den perfekta lösningen för kunder som behöver FIPS 140-2 Level 3-verifierade enheter och fullständig och exklusiv kontroll över HSM-apparaten. HSM-enheter distribueras globalt i flera Azure-regioner och enkelt etableras som ett par av enheter och konfigureras för hög tillgänglighet. HSM:er kan även etableras över regioner för att säkerställa redundans på regionsnivå. Microsoft har levererat tjänsten Dedikerad HSM med hjälp av apparaten [SafeNet Luna Network HSM 7 (modell A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) från Gemalto. Den här enheten erbjuder prestanda och alternativ för kryptografisk integrering på högsta nivå. När HSM:er etableras ansluts de direkt till kundens virtuella nätverk och kan även användas av lokala program och hanteringsverktyg med en konfiguration av VPN-anslutning punkt-till-plats eller plats-till-plats. Kunder hämtar programvara och dokumentation för att konfigurera och hantera HSM-enheter från Gemaltos supportportal.

## <a name="why-use-azure-dedicated-hsm"></a>Varför bör du använda Dedikerad HSM i Azure?

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 Level 3-efterlevnad

Många organisationer lyder under stränga branschregler som kräver att lagring av kryptografiska nycklar uppfyller kraven för [FIPS 140-2 Level 3](https://csrc.nist.gov/publications/detail/fips/140/2/final). Microsofts Azure Key Vault-tjänst för flera klientorganisationer tillhandahåller för närvarande endast FIPS 140-2 Level 2-certifiering. Dedikerad HSM i Azure uppfyller ett verkligt behov hos finansbolag, myndigheter och andra som måste uppfylla kraven för FIPS 140-2 Level 3.

### <a name="single-tenant-devices"></a>Enheter för enskild klientorganisation

Många av våra kunder har ett krav för enskilt innehav av den kryptografiska lagringsenheten. Dedikerad HSM i Azure möjliggör etablering av en fysisk enhet från någon av Microsofts globalt distribuerade datacenter. När enheten har etablerats till en kund kan endast den kunden komma åt enheten.

### <a name="full-administrative-control"></a>Fullständig administrativ kontroll

Utöver enheter för enskild klientorganisation behöver många kunder fullständig administrativ kontroll och ensam åtkomst i administrativt syfte. När enheten har etablerats har bara den kunden åtkomst till enheten på administrativ nivå eller programnivå. Microsoft har ingen administrativ kontroll efter kundens första åtkomst, som kräver en ändring av lösenordet. Därefter är kunden en äkta enskild klientorganisation med fullständig administrativ kontroll och programhanteringsfunktion. Microsoft har viss åtkomst på övervakningsnivå (inte en administratörsroll) för telemetri via serieportsanslutning som omfattar maskinvarusensorer för saker som temperatur, strömförsörjningens hälsa och fläktars hälsa. Kunden kan inaktiverat detta vid behov, men får i så fall inte proaktiva hälsoaviseringar från Microsoft.

### <a name="high-performance"></a>Höga prestanda

Gemalto-enheten valdes ut för den här tjänsten tack vare dess breda utbud av stöd för krypteringsalgoritmer, det flertal av operativsystem som stöds samt omfattande stöd för API:er. Den särskilda modell som distribueras erbjuder utmärkta prestanda med 10 000 åtgärder per sekund för RSA-2048. Den har stöd för 10 partitioner som kan användas för unika programinstanser. Det här är en enhet med låg latens, hög kapacitet och högt dataflöde.

### <a name="unique-cloud-based-offering"></a>Unikt molnbaserat erbjudande

Microsoft identifierade ett specifikt behov hos en unik uppsättning kunder och är den enda molnleverantör som erbjuder nya kunder en dedikerad HSM-tjänst som är verifierad för FIPS 140-2 Level 3, och erbjuder omfattande molnbaserad och lokalt baserad programintegrering.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Är Dedikerad HSM i Azure rätt val för dig?

Dedikerad HSM i Azure är en specialiserad tjänst som uppfyller unika krav hos en specifik typ av storskalig organisation. Därför förväntas de flesta Azure-kunder inte passa profilen för användning av den här tjänsten. För många är Azure Key Vault-tjänsten ett lämpligare och mer kostnadseffektivt alternativ. Vi har identifierat följande kriterier för att hjälpa dig avgöra huruvida tjänsten passar dig.

### <a name="best-fit"></a>Passar bäst

Mest lämplig för ”lift and shift”-scenarier som kräver direkt och ensam åtkomst till HSM-enheter. Exempel:

- Migrera program från en lokal plats till virtuella Azure-datorer
- Migrera program från Amazon AWS EC2 till virtuella Azure-datorer som använder tjänsten AWS Cloud HSM Classic (Amazon erbjuder inte denna tjänst till nya kunder)
- Köra kommersiell programvara på virtuella Azure-datorer såsom Apache/Ngnix SSL Offload, Oracle TDE och ADCS

### <a name="not-a-fit"></a>Passar inte

Microsoft-molntjänster som stöder kryptering med kundhanterade nycklar (till exempel Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL och Office 365-kundnyckel) är inte integrerade med Dedikerad HSM i Azure.

### <a name="it-depends"></a>Det beror på

Många scenarier beror på en potentiellt komplex blandning av krav och vilka kompromisser som kan eller inte kan göras. Ett exempel är krav för FIPS 140-2 Level 3, som ofta är obligatoriskt och för vilket Dedikerad HSM därför är de enda alternativen.  Om dessa krav inte är relevanta står valet ofta mellan Azure Key Vault och Dedikerad HSM baserat på utvärdering av flera olika krav. Exempel:

- Ny kod som körs på en kunds virtuella Azure-dator
- SQL Server TDE på en virtuell Azure-dator
- Kryptering på klientsidan med Azure Storage
- SQL Server och Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Nästa steg

Med tanke på den här tjänstens kraftigt specialiserade profil rekommenderas fullständiga kunskaper om vissa av de viktiga begrepp som förekommer i den här dokumentationsuppsättningen samt om prissättning, support och serviceavtal. Det finns även en självstudie som underlättar etablering av HSM:er till en befintlig miljö med virtuella nätverk. [Gemalto-integreringsguider](https://safenet.gemalto.com/partners/microsoft/) och instruktionsguider för att välja distributionsarkitektur är också en bra resurs.

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
