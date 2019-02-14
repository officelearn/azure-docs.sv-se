---
title: Vad är Dedikerad HSM? – Dedikerad HSM i Azure| Microsoft Docs
description: Översikten över dedikerad HSM i Azure tillhandahåller funktioner för lagring av nycklar i Azure som uppfyller FIPS 140-2 Level 3-certifiering
services: dedicated-hsm
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 447518d2189a3b2723edc7c7db74ccc8435b71b7
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110378"
---
# <a name="what-is-azure-dedicated-hsm"></a>Vad är Dedikerad HSM i Azure?

Dedikerad HSM i Azure är en Azure-tjänst som tillhandahåller lagring av kryptografiska nycklar i Azure. Dedikerad HSM uppfyller de strängaste säkerhetskraven. Det är den perfekta lösningen för kunder som behöver FIPS 140-2 Level 3-validerade enheter och fullständig och exklusiv kontroll över HSM-apparaten. 

 HSM enheter distribueras globalt över flera Azure-regioner. De kan enkelt etableras som ett par av enheter och konfigureras för hög tillgänglighet. HSM-enheter kan även etableras över regioner för att säkerställa redundans på regionsnivå. Microsoft levererar den dedikerade HSM-tjänsten med hjälp av apparaten [SafeNet Luna Network HSM 7 (modell A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) från Gemalto. Den här enheten erbjuder prestanda och alternativ för kryptografisk integrering på högsta nivå. 

När de är etablerade så är HSM-enheter direkt anslutna till en kunds virtuella nätverk. De kan även användas av lokala program och hanteringsverktyg när du konfigurerar en VPN-anslutning från punkt-till-plats eller plats-till-plats. Kunder hämtar programvaran och dokumentationen för att konfigurera och hantera HSM-enheter från Gemaltos supportportal.

## <a name="why-use-azure-dedicated-hsm"></a>Varför bör du använda Azure Dedikerad HSM?

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 Level-3-efterlevnad

Många organisationer lyder under stränga branschregler som kräver att lagring av kryptografiska nycklar uppfyller kraven för [FIPS 140-2 Level-3](https://csrc.nist.gov/publications/detail/fips/140/2/final). Microsofts Azure Key Vault-tjänst för flera klientorganisationer erbjuder för närvarande endast FIPS 140-2 Level-2-certifiering. Azure Dedikerad HSM uppfyller ett verkligt behov hos finansbolag, industribolag, myndigheter och andra som måste uppfylla kraven för FIPS 140-2 Level-3.

### <a name="single-tenant-devices"></a>Enheter för en enda klient

Många av våra kunder har ett krav för enskilt innehav av den kryptografiska lagringsenheten. Azure Dedikerad HSM-tjänsten låter dem etablera en fysisk enhet från någon av Microsofts globalt distribuerade datacenter. När enheten har etablerats till en kund kan endast den kunden komma åt enheten.

### <a name="full-administrative-control"></a>Fullständig administrativ kontroll

Många kunder behöver fullständig administrativ kontroll och enskild åtkomst till sin enhet i administrativt syfte. Efter att en enhet har etablerats så har bara den kunden åtkomst till enheten på administrativ eller programnivå.

 Microsoft har ingen administrativ kontroll efter att kunden anslutit till enheten för första gången och ändrar lösenordet. Därefter är kunden en äkta enskild klient med fullständig administrativ kontroll och programhanteringsfunktion. Microsoft har kvar behörighet på övervakningsnivå (inte en administratörsroll) för telemetri via den seriella portanslutningen. Den här åtkomsten omfattar maskinvaruövervakning som temperatur, hälsotillstånd för strömförsörjning och fläkt. 
 
 Det står kunden fritt att inaktivera den här övervakningen vid behov. Om de inaktiverar den så kommer de dock inte att få proaktiva hälsovarningar från Microsoft.

### <a name="high-performance"></a>Höga prestanda

Gemalto-enheten har valts för den här tjänsten av många olika skäl. Den har ett brett stöd för krypteringsalgoritmer, en mängd olika operativsystem och ett brett API-stöd. Den specifika modell som distribueras erbjuder utmärkta prestanda med 10 000 åtgärder per sekund för RSA-2048. Den har stöd för 10 partitioner som kan användas för unika programinstanser. Det här är en enhet med låg svarstid, hög kapacitet och högt dataflöde.

### <a name="unique-cloud-based-offering"></a>Unikt molnbaserat erbjudande

Microsoft identifierade ett specifikt behov för en unik uppsättning kunder. Det är den enda molnleverantör som erbjuder nya kunder en dedikerad HSM-tjänst som är validerad för FIPS 140-2 Level-3 och erbjuder en så omfattande molnbaserad och lokal programintegrering.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Är Dedikerad HSM i Azure rätt val för dig?

Azure dedikerad HSM är en specialiserad tjänst som uppfyller unika krav hos en specifik typ av storskaliga organisationer. Därför förväntas de flesta Azure-kunder inte passa profilen för användning av den här tjänsten. För många är Azure Key Vault-tjänsten ett lämpligare och mer kostnadseffektivt alternativ. Vi har identifierat följande kriterier för att hjälpa dig avgöra huruvida tjänsten passar dig.

### <a name="best-fit"></a>Passar bäst

Azure dedikerad HSM lämpar sig bäst för ”lift and shift”-scenarier som kräver direkt och enskild åtkomst till HSM-enheter. Exempel:

- Migrera program från en lokal plats till virtuella Azure-datorer
- Migrera program från Amazon AWS EC2 till virtuella datorer som använder tjänsten AWS Cloud HSM Classic (Amazon erbjuder inte denna tjänst för nya kunder)
- Köra kommersiell programvara på virtuella Azure-datorer såsom Apache/Ngnix SSL Offload, Oracle TDE och ADCS 

### <a name="not-a-fit"></a>Passar inte

Azure dedikerad HSM lämpar sig inte för följande typ av scenario: Microsoft-molntjänster som stöder kryptering med kundhanterade nycklar (till exempel Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database och kundnycklar för Office 365) som inte är integrerade med Azure dedikerad HSM.

### <a name="it-depends"></a>Det beror på

Om Azure dedikerad HSM fungerar för dig beror på en potentiellt komplex blandning av krav och kompromisser som du kan eller inte kan göra. Ett exempel är FIPS 140-2 Level-3-kravet. Det här kravet är vanligt och för tillfället är dedikerad HSM det enda alternativet för att uppfylla det. Om de här föreskrivna kraven inte är relevanta så står valet ofta mellan Azure Key Vault och dedikerad HSM. Utvärdera dina krav innan du fattar ett beslut.

Situationer där du måste väga dina alternativ är: 

- Ny kod som körs på en kunds virtuella Azure-dator
- SQL Server TDE på en virtuell Azure-dator
- Kryptering på klientsidan med Azure Storage
- SQL Server och Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Nästa steg

Det här är en höggradigt specialiserad tjänst. Därför rekommenderar vi att du förstår nyckelbegreppen i den här dokumentationen, inklusive prissättning, support och servicenivåavtal. 

[Gemalto-integreringsguiderna](https://safenet.gemalto.com/partners/microsoft/) hjälper dig att underlätta etablering av HSM:er i en befintlig virtuell nätverksmiljö. Det finns också instruktionsguider som hjälper dig att avgöra hur du ställer in din distributionsarkitektur.

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
