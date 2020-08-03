---
title: Vad är Azure API for FHIR? – Azure API för FHIR
description: 'Med Azure API för FHIR kan du snabbt utbyta data via FHIR-API: er. Mata in, hantera och behåll skyddad hälso information PHI med en hanterad moln tjänst.'
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 07/30/2019
ms.author: mihansen
ms.openlocfilehash: b083231ee4c302643b4ea5c361901ab07c3d068d
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496669"
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

## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector för FHIR (för hands version)

Azure IoT Connector för FHIR (för hands version) är en valfri funktion i Azure API för FHIR som ger möjlighet att mata in data från-enheter (Internet of medicin Tings). Internet av medicinska saker är en kategori av IoT-enheter som fångar och utbyter hälso & Wellness data med andra hälso vårds IT-system över nätverket. Några exempel på IoMT-enheter är lämplighet och klinisk wearables, övervakning av sensorer, aktivitets Spårare, skötsel av karriär platser eller till och med en smart Pill. Med funktionen Azure IoT Connector för FHIR (för hands version) kan du snabbt konfigurera en tjänst för att mata in IoMT-data i Azure API för FHIR på ett skalbart, säkert och kompatibelt sätt.

Azure IoT Connector för FHIR (för hands version) kan acceptera eventuella JSON-baserade meddelanden som skickas ut av en IoMT-enhet. Dessa data omvandlas först till lämpliga FHIR [observations](https://www.hl7.org/fhir/observation.html) resurser och sparas sedan i Azure API för FHIR. Data omvandlings logiken definieras genom ett par mappnings-mallar som du konfigurerar baserat på ditt meddelande schema och FHIR krav. Enhets data kan push-överföras direkt till Azure IoT Connector för FHIR (för hands version) eller smidigt används tillsammans med andra Azure IoT-lösningar ([azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) och [Azure IoT Central](https://docs.microsoft.com/azure/iot-central/)). Azure IoT Connector för FHIR (för hands version) tillhandahåller en säker datapipeline medan Azure IoT-lösningar tillåts hantera etablering och underhåll av de fysiska enheterna.

### <a name="applications-of-azure-iot-connector-for-fhir-preview"></a>Program för Azure IoT Connector för FHIR (för hands version)

Användning av IoMT-enheter kan snabbt expanderas i sjukvården och Azure IoT Connector för FHIR (för hands version) är utformat för att överbrygga luckan i att samla flera enhets data med säkerhet och efterlevnad i Azure API för FHIR. Att hämta IoMT-data till en FHIR-Server möjliggör holistiska data insikter och innovativa kliniska arbets flöden. Några vanliga scenarier för Azure IoT Connector för FHIR (för hands version) är:
- **Fjärrstyrd patient övervakning/telehälsa:** Fjärrövervakning av patienter ger möjlighet att samla in patient hälso data utanför traditionella sjukvårds inställningar. Hälso vårds institutioner kan använda Azure IoT Connector för FHIR (för hands version) för att överföra hälso data som genereras av fjärranslutna enheter till Azure API för FHIR. Dessa data kan användas för att noggrant spåra patienternas hälso status, övervaka patienterna i behandlings planen och tillhandahålla personlig vård.
- **Forskning och Life-vetenskap:** Kliniska försök är att snabbt införa IoMT-enheter som bio sensorer, wearables, mobilappar för att samla in utvärderings data. Dessa tester kan dra nytta av Azure IoT Connector för FHIR (för hands version) för att överföra enhets data till Azure API för FHIR på ett säkert, effektivt och effektivt sätt. En gång i Azure API för FHIR kan utvärderings data användas för att köra real tids analys av utvärderings data.
- **Avancerad analys:** IoMT-enheter kan ge stora mängder data och en hög hastighet, vilket gör att de passar bra för att betjäna utbildning och testa data för dina maskin inlärnings modeller. Azure IoT Connector för FHIR (för hands version) har skapats för att fungera med en mängd olika data frekvenser, flexibelt data schema och moln skalning med låg latens. Dessa attribut gör Azure IoT Connector för FHIR (för hands version) ett utmärkt alternativ för att samla in enhets data för dina avancerade analys behov.
- **Smart sjukhus/kliniker:** Idag är smart sjukhus och kliniker att skapa en infrastruktur för sammankopplade digitala till gångar. Azure IoT Connector för FHIR (för hands version) kan användas för att avbilda och integrera data från dessa anslutna komponenter. Användbara insikter från sådan data uppsättning möjliggör bättre patienter och drifts effektivitet.

## <a name="next-steps"></a>Efterföljande moment

Om du vill börja arbeta med Azure-API: et för FHIR följer du snabb starten på 5 minuter för att distribuera Azure-API: t för FHIR.

>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-portal-quickstart.md)

Du kan prova funktionen Azure IoT Connector för FHIR (för hands version) genom att titta på snabb starten för att distribuera IoT Connector med Azure Portal.

>[!div class="nextstepaction"]
>[Distribuera Azure IoT Connector för FHIR (för hands version)](iot-fhir-portal-quickstart.md)

I Azure Portal kallas Azure IoT-anslutaren för FHIR (för hands version) IoT Connector (för hands version).

FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.
