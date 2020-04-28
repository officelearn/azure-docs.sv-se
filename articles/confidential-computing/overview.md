---
title: Översikt över Azure konfidentiella data behandling
description: Översikt över Azures konfidentiella data behandling (ACC)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: ae98325d98df1ac8a06e0c0bc950d89cc6b77eda
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192271"
---
# <a name="confidential-computing-on-azure"></a>Konfidentiell data behandling i Azure

Med Azures konfidentiella data behandling kan du isolera känsliga data medan de bearbetas i molnet. Många branscher använder konfidentiell data behandling för att skydda sina data. Följande arbets belastningar är:

- Skydda finansiella data
- Skydda patient information
- Köra Machine Learning-processer på känslig information
- Utföra algoritmer på krypterade data uppsättningar från flera källor


## <a name="overview"></a>Översikt
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Vi vet att det är viktigt att skydda dina moln data. Vi hör dina problem. Här är några frågor som våra kunder kan ha när de flyttar känsliga arbets belastningar till molnet: 

- Hur gör jag för att se till att Microsoft inte kan komma åt data som inte är krypterade?
- Hur gör jag för att att förhindra säkerhetshot från privilegierade administratörer i mitt företag?
- Vilka är fler sätt att förhindra att tredje part får åtkomst till känsliga kund data?

Microsoft Azure hjälper dig att minimera din attack yta så att du får bättre data skydd. Azure erbjuder redan många verktyg för att skydda [**data i vila**](../security/fundamentals/encryption-atrest.md) genom modeller som kryptering på klient sidan och kryptering på Server sidan. Dessutom erbjuder Azure mekanismer för att kryptera [**data under överföring**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) via säkra protokoll som TLS och https. På den här sidan introduceras en tredje del av data krypteringen – kryptering av **data som används**.


## <a name="introduction-to-confidential-computing"></a>Introduktion till konfidentiell dator användning<a id="intro to acc"></a>

Konfidentiell data behandling är en bransch term som definieras av kopia ( [konfidentiell Computing Consortium](https://confidentialcomputing.io/) ), en grund som är dedikerad för att definiera och påskynda införandet av konfidentiell dator användning. Konfidentiell data behandling är skydd av data som används när du utför beräkningar. Beräkningarna sker i en maskinvarubaserad betrodd körnings miljö (TEE).

En TEE är en miljö som tillämpar körning av enbart auktoriserad kod. Alla data i TEE kan inte läsas eller manipuleras av någon kod utanför miljön.

### <a name="enclaves-and-trusted-execution-environments"></a>Enclaves och betrodda körnings miljöer

I samband med konfidentiell data behandling kallas TEEs vanligt vis *enclaves* eller *säker enclaves*. Enclaves är säkra delar av en maskin varu processor och minne. Det finns inget sätt att visa data eller kod inuti enklaven, även med en fel sökare. Om ej betrott kod försök ändrar innehållet i enklaven-minnet inaktive ras miljön och åtgärderna nekas.

När du utvecklar program kan du använda- [program verktyg](#oe-sdk) för att avskärma delar av koden och data i enklaven. Dessa verktyg ser till att din kod och dina data inte kan visas eller ändras av någon utanför den betrodda miljön. 

Du kan till exempel tänka på en enklaven som en svart ruta. Du anger krypterad kod och data i rutan. Från utsidan av rutan kan du inte se något. Du ger enklaven en nyckel för att dekryptera data, och sedan bearbetas och krypteras data innan de skickas ut från enklaven.

### <a name="attestation"></a>Hälsoattestering

Du vill ha en kontroll och verifiera att din betrodda miljö är säker. Den här verifieringen är processen för attestering. 

Attestering gör det möjligt för en förlitande part att ha ökat förtroende för att deras program vara (1) körs i en enklaven och (2) att enklaven är uppdaterat och säkert. En enklaven ställer till exempel den underliggande maskin varan för att generera en autentiseringsuppgift som innehåller bevis på att enklaven finns på plattformen. Rapporten kan sedan ges till en andra enklaven som verifierar att rapporten genererades på samma plattform.

Attestering måste implementeras med hjälp av en säker attesterings tjänst som är kompatibel med systemprogram vara och kisel. [Intels attesterings-och etablerings tjänster](https://software.intel.com/sgx/attestation-services) är kompatibla med virtuella Azure-datorer med konfidentiella data behandling.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Använda Azure för molnbaserad konfidentiell data behandling<a id="cc-on-azure"></a>

Med Azures konfidentiella data behandling kan du utnyttja funktioner för konfidentiell användning i en virtualiserad miljö. Nu kan du använda verktyg, program och moln infrastruktur för att bygga ovanpå säker maskin vara. 

### <a name="virtual-machines"></a>Virtuella datorer

Azure är den första moln leverantören som erbjuder konfidentiell data behandling i en virtualiserad miljö. Vi har utvecklat virtuella datorer som fungerar som ett abstraktions lager mellan maskin varan och ditt program. Du kan köra arbets belastningar i skala och med alternativ för redundans och tillgänglighet.  

#### <a name="intel-sgx-enabled-virtual-machines"></a>Intel SGX-aktiverade Virtual Machines

I Azures konfidentiella virtuella datorer är en del av PROCESSORns maskin vara reserverad för en del av kod och data i ditt program. Den här begränsade delen är enklaven. 

![VM-modell](media/overview/hardware-backed-enclave.png)

Azures infrastruktur för konfidentiell behandling består för närvarande av en special-SKU av virtuella datorer (VM). De här virtuella datorerna körs på Intel-processorer med Software Guard-tillägget (Intel SGX). [Intel SGX](https://intel.com/sgx) är den komponent som möjliggör det ökade skyddet som vi har med konfidentiell dator användning. 

Idag erbjuder Azure [DCsv2-serien](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) som bygger på Intel SGX-teknik för maskinvarubaserad enklaven-skapande. Du kan skapa säkra enklaven-baserade program som ska köras i DCsv2-serien med virtuella datorer för att skydda dina program data och kod som används. 

Du kan [läsa mer](virtual-machine-solutions.md) om att distribuera virtuella datorer med Azure konfidentiell dator med maskinvarubaserad betrodda enclaves.

## <a name="application-development"></a>Program utveckling<a id="application-development"></a>

För att dra nytta av kraften i enclaves och isolerade miljöer måste du använda verktyg som stöder konfidentiell dator användning. Det finns olika verktyg som stöder enklaven program utveckling. Du kan till exempel använda de här ramverken med öppen källkod: 

- [Öppna enklaven Software Development Kit (SDK)](https://github.com/openenclave/openenclave)
- [CCF (konfidentiellt konsortiet Framework)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>Översikt

Ett program som skapats med enclaves är partitionerat på två sätt:
1. En "ej betrodd" komponent (värden)
1. En "betrodd" komponent (enklaven)

**Värden** är ditt enklaven-program som körs på en ej betrodd miljö. Koden i värden har inte åtkomst till koden som lästs in i enklaven. 

**Enklaven** är där kod och data körs i tee-implementeringen. Säkra beräkningar bör utföras i enklaven för att säkerställa att hemligheter och känsliga data förblir skyddade. 

När du börjar utveckla ett enklaven-program måste du bestämma vilken kod och vilka data som behöver skyddas. Den kod som du väljer att placera i den betrodda komponenten är isolerad från resten av ditt program. När enklaven har initierats och koden har lästs in i minnet, kan den koden inte läsas eller ändras från utanför skyddad miljö.

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Öppna enklaven Software Development Kit (OE SDK)<a id="oe-sdk"></a>

Använd ett bibliotek eller ramverk som stöds av din Provider om du vill skriva kod som körs i en enklaven. [Open ENKLAVEN SDK](https://github.com/openenclave/openenclave) (OE SDK) är en SDK med öppen källkod som tillåter abstraktion av olika konfidentiella data funktioner. 

OE SDK är konstruerat som ett enda abstraktions lager för all maskin vara på valfri KRYPTOGRAFIPROVIDER. Med OE SDK kan du använda virtuella datorer i Azure konfidentiella datorer för att skapa och köra program ovanpå enclaves.

## <a name="next-steps"></a>Nästa steg

Distribuera en virtuell dator med DCsv2-serien och installera OE SDK på den.

> [!div class="nextstepaction"]
> [Distribuera en konfidentiell data behandlings dator i Azure Marketplace](quick-create-marketplace.md)