---
title: Vad är Azure API for FHIR? – Azure API för FHIR
description: 'Med Azure API för FHIR kan du snabbt utbyta data via FHIR-API: er. Mata in, hantera och behåll skyddad hälso information PHI med en hanterad moln tjänst.'
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: aca0d67326a5a0488d0108efa9acd0d01c7788cd
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "84820206"
---
# <a name="what-is-azure-api-for-fhirreg"></a>Vad är Azure API för FHIR &reg; ?

Med Azure API för FHIR kan du snabbt utbyta data genom att använda API: er för snabb sjukvård (FHIR®), som backas upp av ett hanterat PaaS-erbjudande (Platform-as-a Service) i molnet. Det gör det enklare för alla som arbetar med hälso data att mata in, hantera och bevara skyddad hälso information [Phi](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) i molnet: 

- Hanterad FHIR-tjänst, etablerad i molnet på några minuter 
- Företags klass, FHIR®-baserad slut punkt i Azure för data åtkomst och lagring i FHIR®-format
- Hög prestanda, låg latens
- Säker hantering av skyddade hälso data (PHI) i en kompatibel moln miljö
- SMART på FHIR för mobil-och webb implementeringar
- Kontrol lera dina egna data i skala med rollbaserad Access Control (RBAC)
- Gransknings logg spårning för åtkomst, skapande, ändring och läsningar i varje data lager

Med Azure API för FHIR kan du skapa och distribuera en FHIR-tjänst på bara några minuter för att utnyttja molnets elastiska skala.  Du betalar bara för det data flöde och den lagring du behöver. De Azure-tjänster som Power Azure API för FHIR är utformade för att få snabba prestanda oavsett vilken storlek data uppsättningar som du hanterar.

Med FHIR-API och kompatibelt data lager kan du på ett säkert sätt ansluta och interagera med alla system som använder FHIR-API: er.  Microsoft tar hand om drift-, underhålls-, uppdaterings-och efterlevnads kraven i PaaS-erbjudandet så att du kan frigöra dina egna operativa och utvecklings resurser. 

I följande video presenteras en översikt över Azure API för FHIR:

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Dra nytta av kraften hos dina data med FHIR

Sjukvårds branschen transformerar snabbt hälso data till den nya standarden av [FHIR &reg; ](https://hl7.org/fhir) (snabba hälso-och sjukvårds resurser). FHIR möjliggör en robust, utöknings bar data modell med standardiserade semantik och data utbyte som gör det möjligt för alla system som använder FHIR att samar beta.  Genom att omvandla dina data till FHIR kan du snabbt ansluta befintliga data källor, till exempel elektroniska hälso poster eller forsknings databaser. FHIR möjliggör även snabbt utbyte av data i moderna implementeringar av mobil-och webb utveckling. Det viktigaste är att FHIR fören klar data inhämtningen och påskyndar utvecklingen med verktyg för analys och maskin inlärning.  

### <a name="securely-manage-health-data-in-the-cloud"></a>Hantera hälso data på ett säkert sätt i molnet

Azure API för FHIR möjliggör data utbyte via konsekventa, RESTful, FHIR-API: er baserade på HL7 FHIR-specifikationen. Det finns även en skalbar och säker miljö för hantering och lagring av skyddade hälso informations data (PHI) i det interna FHIR-formatet, med ett hanterat PaaS-erbjudande i Azure.  

### <a name="free-up-your-resources-to-innovate"></a>Frigör dina resurser för att förnya

Du kan investera resurser genom att skapa och köra din egen FHIR-tjänst, men med Azure-API: t för FHIR, använder Microsoft arbets belastningen för åtgärder, underhåll, uppdateringar och efterlevnad, så att du kan frigöra dina egna operativa och utvecklings resurser.

### <a name="enable-interoperability-with-fhir"></a>Aktivera samverkan med FHIR

Med hjälp av Azure API för FHIR kan du ansluta till alla system som utnyttjar FHIR-API: er för läsning, skrivning, sökning och andra funktioner.  Det kan användas som ett kraftfullt verktyg för att konsolidera, normalisera och tillämpa maskin inlärning med kliniska data från elektroniska hälso poster, Clinician och patient instrument paneler, program för fjärrövervakning eller med databaser utanför systemet som har FHIR-API: er.

### <a name="control-data-access-at-scale"></a>Kontrol lera data åtkomst i skala

Du styr dina data. Med rollbaserad Access Control (RBAC) kan du hantera hur dina data lagras och nås.  Genom att tillhandahålla ökad säkerhet och minska den administrativa belastningen kan du bestämma vem som har åtkomst till de data uppsättningar som du skapar, baserat på de roll definitioner som du skapar för din miljö.  

### <a name="audit-logs-and-tracking"></a>Gransknings loggar och spårning 

Spåra snabbt var dina data kommer med inbyggda gransknings loggar. Spåra åtkomst, skapande, ändring och läsningar i varje data lager.

### <a name="secure-your-data"></a>Skydda dina data

Skydda ditt PHI med oöverträffad säkerhets information.  Dina data är isolerade till en unik databas per API-instans och skyddas med redundans i flera regioner. Azure API för FHIR implementerar ett skiktat, djupgående försvar och Avancerat skydd för dina data.  

## <a name="applications-for-a-fhir-service"></a>Program för en FHIR-tjänst

FHIR-servrar är nyckel verktyg för att kunna samverka med hälso data.  Azure API för FHIR är utformat som ett API och en tjänst som du kan skapa, distribuera och börja använda snabbt.  I takt med att FHIR-standarden ökar i sjukvården kommer användnings fall att fortsätta att växa, men vissa inledande kund program där Azure API för FHIR är användbara är nedan: 

- **Start/IoT och utveckling av appar:**  Kunder som utvecklar en patient-eller provider-inriktad app (mobil eller webb) kan utnyttja Azure API för FHIR som en fullständigt hanterad server dels tjänst. Azure API för FHIR ger en värdefull resurs i som kunderna kan använda för att hantera data och utbyta data i en säker moln miljö som är utformad för hälso tillstånd, använda smarta lösningar för implementering av FHIR och göra det möjligt för deras teknik att användas av alla leverantörs system (till exempel de flesta EHRs har aktiverat FHIR Read API: er).   
- **Hälso-eko system:**  Även om EHRs finns som primär "källa för sanningen" i många kliniska inställningar är det inte ovanligt att providers har flera databaser som inte är anslutna till varandra eller lagrar data i olika format.  Genom att använda Azure API för FHIR som en tjänst som finns ovanpå dessa system kan du standardisera data i FHIR-format.  Detta hjälper till att aktivera data utbyte över flera system med ett konsekvent data format. 

- **Forskning:** Sjukvårds forskare kommer att hitta FHIR-standarden i allmänhet och Azure-API: et för FHIR användbar eftersom det normaliserar data runt en vanlig FHIR-datamodell och minskar arbets belastningen för maskin inlärning och data delning.
Data utbyte via Azure API för FHIR innehåller gransknings loggar och åtkomst kontroller som hjälper dig att styra data flödet och vem som har åtkomst till vilka data typer. 

## <a name="fhir-from-microsoft"></a>FHIR från Microsoft

FHIR-funktioner från Microsoft finns i två konfigurationer:

* Azure API för FHIR – ett PaaS-erbjudande i Azure, som är enkelt att etablera i Azure Portal och hanteras av Microsoft.
* FHIR Server för Azure – ett projekt med öppen källkod som kan distribueras till din Azure-prenumeration, som finns på GitHub på https://github.com/Microsoft/fhir-server .

För användnings fall som kräver utökning eller anpassning av FHIR-servern eller kräver åtkomst till underliggande tjänster, t. ex. databasen, utan att gå via FHIR-API: erna, ska utvecklare välja FHIR-server med öppen källkod för Azure.   För implementering av en FHIR-API-och backend-tjänst med konstant nyckel och en backend-tjänst där beständiga data bara bör nås via FHIR-API: t bör utvecklare välja Azure API för FHIR

## <a name="next-steps"></a>Efterföljande moment

Om du vill börja arbeta med Azure-API: et för FHIR följer du snabb starten på 5 minuter för att distribuera Azure-API: t för FHIR.

>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-portal-quickstart.md)

FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.
