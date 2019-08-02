---
title: Så här fungerar säkerhet i Azure Australien
description: Information som är mest efterfrågad om de australiska regionerna och uppfyller de särskilda kraven i den australiska regeringens politik, förordningar och lagstiftning.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 212d14e31c152e50c216f2f34fb225c29fe054b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571437"
---
# <a name="azure-australia-security-explained"></a>Så här fungerar säkerhet i Azure Australien

Den här artikeln handlar om några vanliga frågor och intresse områden för myndigheter som undersöker, utformar och distribuerar till Microsoft Azure Australien.

## <a name="irap-and-certified-cloud-services-list-ccsl-documents"></a>IRAP och CCSL-dokument (Certified Cloud Services List)

Det australiska cyberhot Security Centre (ACSC) tillhandahåller en certifierings brev, en certifierings rapport och en konsument guide för tjänsten när den läggs till i CCSL.

Microsoft finns för närvarande i CCSL för Azure, Office 365 och Dynamics 365 CRM.

Microsoft gör gransknings-, utvärderings-och ACSC-certifieringar tillgängliga för kunder och partner på en Australien-bestämd sida i [Microsoft Service Trust-portalen](https://aka.ms/au-irap).

## <a name="dissemination-limiting-markers-dlm-and-protected-certification"></a>Spridnings begränsnings markörer (DLM) och skyddad certifiering

Processen för att ha system, inklusive moln tjänster, som godkänts för användning av myndighets organisationer definieras i [informations säkerhets hand boken (ISM)](https://acsc.gov.au/infosec/ism/) som produceras och publiceras av australisk cyberhot Security Centre (ACSC). Det australiska cyberhot Security Centre (ACSC) är den entitet i ASD som ansvarar för cyberhot-säkerhet och moln certifiering.

Processen för godkännande är två steg:

1. Säkerhets utvärdering (IRAP) – en process där registrerade experter bedömer system, tjänster och lösningar mot de tekniska kontrollerna i ISM och utvärdera om kontrollerna har implementerats effektivt. Utvärderingen identifierar också eventuella enskilda risker för godkännande myndigheten att överväga innan ett godkännande för att genomföras (ATO) utfärdas.

1. Godkännande för att genomföra – processen där en erfaren tjänsteman hos ett myndighets organ formellt erkänner och godkänner den kvarvarande risken för ett system till den information som den bearbetar, lagrar och kommunicerar.  En indatamängd av den här processen är säkerhets utvärderingen.

Utvärderingen av Azure-tjänster på nivån skyddad anger att implementeringen av de säkerhets kontroller som krävs för lagring och bearbetning av skyddade och under data har bekräftats vara på plats och fungerar effektivt.

## <a name="australian-data-classification-changes"></a>Australiska data klassificerings ändringar

Den 1 oktober 2018 bevisade tjänstebolags avdelningen offentligt ändringar i säkerhets princip ramverket (PSPF), särskilt ett nytt [känsligt och klassificerat informations system](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Ändrade PSPF-klassificeringar](media/pspf-classifications.png)

Alla australiska myndigheter och organisationer som arbetar under PSPF påverkas av dessa ändringar. Den primära ändringen som påverkar våra aktuella IRAP/CCSL-certifieringar är den aktuella spridnings begränsningen (DLM) har dragits tillbaka. Märknings **officiella: Känsligt** ersätter de olika DLMs som används för att skydda känslig information. Ändringen introducerade även tre informations hanterings markörer som kan tillämpas på all officiell information på alla nivåer av känslighet och klassificering. Den **skyddade** klassificeringen förblir oförändrad.

Termen "oklassificerad" tas bort från det nya systemet och den icke-officiella termen tillämpas på icke-myndighets information, trots att det inte krävs någon formell märkning.

## <a name="choosing-an-azure-region-for-my-official-sensitive-and-protected-workloads"></a>Välja en Azure-region för min officiella: Känsliga och skyddade arbets belastningar

Azure **-tjänsteman: Känsliga** och **skyddade** certifierade tjänster distribueras till alla fyra australiensiska Data Center regioner (Australien, östra, Australien, östra, Australien, centrala och Australien, centrala), men certifierings rapporten som utfärdats av ACSC rekommenderar att **skyddade** data distribueras till Azure Government regioner i Canberra om en tjänst är tillgänglig där. Mer detaljerad information om **skyddade** certifierade Azure-tjänster finns [på sidan Australien på STP](https://aka.ms/au-irap).

>[!NOTE]
>Microsoft rekommenderar myndighets data för alla sensitivities och klassificeringar distribueras till regionerna Australien, centrala och Australien, centrala 2, så som de är utformade och drivs specifikt för myndighets behov.

Mer information om den särskilda beskaffenheten hos Azure australiska-regionerna finns på [Azure-regioner i Australien, centrala](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Hur Microsoft delar upp sekretessbelagda och officiella data

Microsoft driver Azure och Office 365 i Australien som om alla data är känsliga och/eller klassificerade, vilket höjer våra säkerhets kontroller till denna höga stapel.

Infrastrukturen som stöder Azure kan hantera data för flera klassificeringar.  Men eftersom vårt antagande är att kunddata klassificeras, är lämpliga kontroller på plats. Microsoft har antagit en grundläggande säkerhets position för våra tjänster som uppfyller PSPF-kraven för att lagra och bearbeta **skyddad** sekretessbelagd information.

För att se till att våra kunder som en klient i Azure inte är utsatta för andra klienter, implementerar Microsoft omfattande skydds kontroll.

Utöver de funktioner som implementeras inom Microsoft Azures plattformen kan ytterligare kund konfigurerbara kontroller, till exempel kryptering med Kundhanterade nycklar, kapslad virtualisering och just-in-Time administrativ åtkomst ytterligare minska risken. Inom Azure Government australiska regionerna i Canberra är en process för formell vit listning endast australiska & Nya Zeeland myndigheter och nationella kritiska infrastruktur organisationer är på plats. Det här community-molnet ger ytterligare garantier för organisationer som är känsliga för risker med klient organisationer.

Rapporten Microsoft Azure **skyddad** certifiering bekräftar att dessa kontroller är effektiva för lagring och bearbetning av **skyddade** , klassificerade data och deras isolering.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Relevans för IRAP/CCSL till statliga myndigheter och leverantörer av kritiska infrastrukturer

Många statliga myndigheter och leverantörer av kritiska infrastrukturer innefattar federala myndighets krav i säkerhets policyn och garanti ramverket. Dessa organisationer hanterar också **officiella**, **officiella: Känslig** och viss mängd **skyddade** data, antingen från deras interaktion med federala myndigheter eller i sin egen rätt.

Den australiska regeringen fokuserar på att fokusera principer och lagstiftning om skydd av icke-myndighets data som påverkar säkerheten och den ekonomiska framgång för Australien. Dessa Azure-regioner i Australien och CCSL-certifiering är relevanta för alla dessa branscher.

![Kritiska infrastruktur sektorer](media/nci-sectors.png)

Microsofts certifieringar visar att Azure-tjänster har genomgått en grundlig, rigorös och formell utvärdering av Säkerhetsskydden på plats och att de har godkänts för att hantera sådana mycket känsliga data.

## <a name="location-and-control-of-microsoft-data-centres"></a>Plats och kontroll över Microsofts Data Center

Det är ett obligatoriskt krav på myndighets och kritisk infrastruktur för att explicit känna till data centrets plats och ägarskap för moln tjänster som bearbetar sina data.  Microsoft är unikt som en moln leverantör med hög skalbarhet för att ge omfattande information om dessa platser och ägarskap.

Microsoft Azure-regionerna i Australien (Australien, centrala och Australien, centrala 2) drivs i anläggningarna för CDC-Data Center.  Ägarskapet till CDC-Data Center är australiskt med 48% ägande från samväldet Superannuation Corporation, 48% ägande från Infratil (ett NZ-baserat, dubbelt australiskt och nya Zeeland börs kurs som visar långsiktig infrastruktur till gångs fond) och 4% australisk hantering.  

Hantering av CDC-Data Center har avtals enliga garantier på plats med den australiska regeringen som begränsar framtida överföring av ägande rätt och kontroll. Den här transparensen av försörjnings kedjan och ägande rätten via Microsofts partnerskap med CDC Data Center, är i linje med principerna för [hela myndighets värd strategin](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) och definitionen av ett **certifierat suveränt datacenter**.

## <a name="the-azure-services-that-are-included-in-the-current-ccsl-certification"></a>De Azure-tjänster som ingår i den aktuella CCSL-certifieringen

I juni 2017 är ACSC Certified 41 Azure-tjänster för lagring och bearbetning av data på **oklassificerade: DLM** -nivå. I april 2018 var 24 av dessa tjänster certifierade för **skyddade** , klassificerade data.

Tillgängligheten för ACSC Certified Azure-tjänster i våra Azure-regioner i Australien är följande (de i fet stil är certifierade vid **skyddade**):

|Azure-regioner i Australien, centrala|Icke-regionala tjänster och andra regioner|
|---|---|
|API Management, app Gateway, Programtjänster, **automatisering, Azure Portal, säkerhets kopiering, batch, Cloud Services**, Cosmos DB, Event Hubs, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, Log Analytics, **Multi-Factor Autentisering**, Redis cache, **Resource Manager, Service Bus, Service Fabric, Site Recovery, SQL Database, lagring**, Traffic Manager, **Virtual Machines, Virtual Network, VPN gateway**|**Azure Active Directory**, CDN, Data Catalog, **importera export, information Protection, IOT Hub**, Machine Learning, Media Services, **Notification Hubs**, Power BI, **Security Center, Scheduler**, search, Stream Analytics|
|

Microsoft publicerar en [Översikt över Microsoft Azure kompatibilitet](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) som visar alla omfattande tjänster för all global, myndighets, bransch och regional efterlevnad och utvärdering av Azure går igenom, inklusive IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-i-need"></a>Azure-tjänsten är inte listad eller utvärderad på en lägre nivå än jag behöver

Tjänster som inte är certifierade eller som har certifierats vid **officiella: Känslig** men inte **skyddad**, kan användas tillsammans med eller som en del av en lösning som är värd för **skyddade** data förutsatt att tjänsterna är antingen:
1. inte lagring eller bearbetning av **skyddade** data okrypterade, eller
1. du har slutfört en riskbedömning och godkänt tjänsten för att lagra **skyddade** data själv.

Om du vill använda en tjänst som inte finns med på CCSL för att lagra och bearbeta **officiella** data, kan du, men ISM-funktionen kräver att du meddelar ACSC när du gör det, innan du går in i eller förnyar ett kontrakt med en moln tjänst leverantör.

En tjänst som används av ett organ för **skyddade** arbets belastningar måste fortfarande vara säkerhet som utvärderas och godkännas i enlighet med de processer som beskrivs i ISM-och IRAP-utvärderings processen i [DTA i säker moln strategi](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf).

![DTA certifierings process för säker moln strategi](media/certification.png)

Microsoft utvärderar kontinuerligt våra tjänster för att säkerställa att plattformen är säker och anpassad för användning för den australiska regeringen, så du kan kontakta Microsoft om du behöver hjälp med en tjänst som inte finns på CCSL vid **skyddad**.

Eftersom Microsoft har en mängd tjänster som är certifierade på CCSL både i **oklassificerad DLM** och **skyddade** klassificeringar, kräver ISM att vi genomför en IRAP utvärdering av våra tjänster minst vartannat år. Microsoft underbörjar en årlig utvärdering, vilket även är möjligheten att inkludera ytterligare tjänster för övervägande.

## <a name="certified-protected-gateway-in-azure"></a>Certifierad skyddad gateway i Azure

Microsoft använder inte en myndighets Certified Secure Internet-Gateway (SIG) på grund av begränsningar för antalet SIGs som tillåts under Gateway-konsoliderings programmet.  Men de förväntade och nödvändiga funktionerna i en SIG kan konfigureras i Microsoft Azure.

Med hjälp av den **skyddade** certifieringen av Azure-tjänster har ACSC vissa rekommendationer till myndigheter för att ansluta till Azure och när de implementerar nätverks segmentering mellan säkerhets domäner, till exempel mellan **skyddade** och E. Dessa rekommendationer omfattar användningen av nätverks säkerhets grupper, brand väggar och virtuella privata nätverk.  ACSC rekommenderar att du använder en virtuell gateway-enhet. Det finns flera virtuella apparater som är tillgängliga i Azure och som har en fysisk motsvarighet i listan över ASD-produkter eller som har utvärderats mot de gemensamma kriterie-skydds profilerna och som visas på portalen med vanliga villkor. Dessa produkter erkänns ömsesidigt av ASD som en undertecknare i den gemensamma kriterie igenkännings ordningen (CCRA).

Microsoft har fått vägledning om hur du implementerar Azure-baserade funktioner som tillhandahåller de säkerhetsfunktioner som krävs för att skydda avgränsningen mellan olika säkerhets domäner, som i kombination utgör motsvarigheten till ett certifierat SIG. Det finns ett antal partner som kan hjälpa till med utformning och implementering av dessa funktioner samt ett antal tillgängliga partner lösningar som gör samma sak.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Säkerhets fri klarande och medborgarskap hos Microsofts support personal

Microsoft driver våra tjänster globalt med övervakad och utbildad säkerhets personal.  Personal som har unescorted fysisk åtkomst till anläggningar i Sydney och Melbourne har australiska myndigheters bas linje säkerhet. Personal i Australien Central-och Australien, centrala 2-regionerna har minst negativa först konsumentsajter 1-(NV1) fria utrymme (efter behov för **hemliga** data). Detta ger ytterligare garantier till kunder som arbetar i data centra som använder Azure med hög tillförlitlighet.

Microsoft har ingen stående åtkomst princip med åtkomst som beviljats via ett system av just-in-Time och bara tillräckligt med administration baserat på rollbaserade åtkomst kontroller. I de flesta fall kräver våra administratörer inte åtkomst eller behörighet till kund information för att felsöka och underhålla tjänsten.  Hög grad av automatisering och skript för aktiviteter för fjärrkörning, vilket gör att du inte behöver direkt åtkomst till kunddata.

Jurist-General-avdelningen har bekräftat att Microsofts personal säkerhets principer och procedurer i Azure är konsekventa med avsikten att PSPF åtkomst till informations bestämmelser i informations säkerhet – 9.

## <a name="storing-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Lagring av internationella trafik för vapen bestämmelser (ITAR) eller export av administrations bestämmelser (öron) data

Azures tekniska kontroller som hjälper kunder att uppfylla sina skyldigheter för export-styrda data är samma globalt i Azure. Det är viktigt att det inte finns någon "ticker ITAR/öron box" eftersom det inte finns någon formell utvärderings-och certifierings ramverk för export-styrda data.

För Azure Government och Office 365 amerikanska myndigheter är det viktigt att vi har lagt till ytterligare avtals-och process åtgärder på plats för att stödja kunder som omfattas av export kontroller. De ytterligare avtals satserna och den garanterade amerikanska nationella supporten och administrationen av Azure-regionerna är inte på plats för Australien.

Det innebär inte att Azure i Australien kan användas för ITAR/öron, men du måste tydligt förstå de begränsningar som gäller för dig via export licensen och du måste implementera ytterligare skydd för att uppfylla dessa skyldigheter innan du använder Azure för att lagra dessa data. Du kan till exempel behöva bygga nationalitet som ett attribut i Azure Active Directory, använda Azure Information Protection för att genomdriva krypterings regler över data och begränsa det till endast oss och andra nationella krav ingår i export licensen. kryptera alla data lokalt innan du lagrar dem i Azure, med hjälp av kund nyckeln eller innehåller din egen nyckel för ITAR-data och listan går vidare......

Eftersom ITAR inte är en formell certifiering måste du förstå vad de begränsningar och begränsningar som är kopplade till export licensen och sedan gå igenom om det finns tillräckligt med kontroller i Azure för att uppfylla dessa krav. I det här fallet är ett av problemen att tänka på att det är till gång av våra tekniker som inte är medborgaren som godkänts på export licensen.

## <a name="next-steps"></a>Nästa steg

Läs artikeln om [azure VPN gateway](vpn-gateway.md) for ISM-kompatibel konfiguration och implementering av VPN-anslutning till Azure Australien
