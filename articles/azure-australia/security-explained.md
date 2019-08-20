---
title: Så här fungerar säkerhet i Azure Australien
description: Information som är mest efterfrågad om de australiska regionerna och uppfyller de särskilda kraven i den australiska regeringens politik, förordningar och lagstiftning.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 79f5773cf6f4906f93a5b95ce5c042da09c789fc
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575455"
---
# <a name="azure-australia-security-explained"></a>Så här fungerar säkerhet i Azure Australien

Den här artikeln handlar om några av de vanliga frågorna och intresse områdena för myndigheter i Australien som undersöker med, utformar och distribuerar till Microsoft Azure Australien.

## <a name="irap-and-certified-cloud-services-list-documents"></a>IRAP och certifierade Cloud Services List dokument

Det australiska cyberhot Security Centre (ACSC) tillhandahåller en brev med certifiering, en certifierings rapport och en konsument handbok för tjänsten när den läggs till i listan med certifierade Cloud Services (CCSL).

Microsoft finns för närvarande i CCSL för Azure, Office 365 och Dynamics 365 CRM.

Microsoft gör gransknings-, utvärderings-och ACSC-certifieringar tillgängliga för kunder och partner på en Australien-bestämd sida i [Microsoft Service Trust-portalen](https://aka.ms/au-irap).

## <a name="dissemination-limiting-markers-and-protected-certification"></a>Spridnings begränsning markörer och skyddad certifiering

Processen att ha system, inklusive moln tjänster, som godkänts för användning av myndighets organisationer definieras i [informations säkerhets hand boken (ISM)](https://acsc.gov.au/infosec/ism/) som produceras och publiceras av ACSC. ACSC är den entitet i den australiska Signals direktoratet (ASD) som ansvarar för cyberhot säkerhets-och moln certifiering.

Processen för godkännande är två steg:

1. **Säkerhets utvärdering (IRAP)** : En process där registrerade experter bedömer system, tjänster och lösningar mot de tekniska kontrollerna i ISM och utvärdera om kontrollerna har implementerats effektivt. Utvärderingen identifierar också eventuella enskilda risker för godkännande myndigheten att överväga innan ett godkännande för att genomföras (ATO) utfärdas.

1. **Godkännande för att hantera**: Den process där en erfaren tjänsteman hos ett myndighets organ formellt erkänner och godkänner den kvarvarande risken för ett system till den information som den bearbetar, lagrar och kommunicerar. En indatamängd av den här processen är säkerhets utvärderingen.

Utvärderingen av Azure-tjänster på den skyddade nivån anger att implementeringen av de säkerhets kontroller som krävs för lagring och bearbetning av skyddade och under data har bekräftats vara på plats och fungerar effektivt.

## <a name="australian-data-classification-changes"></a>Australiska data klassificerings ändringar

Den 1 oktober 2018 presenterade tjänstebolags avdelningen offentligt ändringar i skydds princip ramverket (PSPF), särskilt ett nytt [känsligt och klassificerat informations system](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Ändrade PSPF-klassificeringar](media/pspf-classifications.png)

Alla australiska myndigheter och organisationer som arbetar under PSPF påverkas av dessa ändringar. Den primära ändringen som påverkar våra aktuella IRAP/CCSL-certifieringar är att den aktuella spridningen av spridnings begränsningen (DLMs) drogs tillbaka. OFFICIELLA: Känslig märkning ersätter de olika DLMs som används för att skydda känslig information. Ändringen introducerade även tre informations hanterings markörer som kan tillämpas på all officiell information på alla nivåer av känslighet och klassificering. Den skyddade klassificeringen förblir oförändrad.

Termen "oklassificerad" tas bort från det nya systemet och termen "ej officiellt" tillämpas på icke-myndighets information, även om det inte krävs någon formell märkning.

## <a name="choose-an-azure-region-for-official-sensitive-and-protected-workloads"></a>Välj en Azure-region för officiellt: Känsliga och skyddade arbets belastningar

Azure-tjänsteman: Känsliga och skyddade certifierade tjänster distribueras till alla fyra australiska data Centres regioner: Australien, östra; Australien, östra, Australien, centrala och Australien, centrala 2. Certifierings rapporten som utfärdats av ACSC rekommenderar att skyddade data distribueras till Azure Government regioner i Canberra om en tjänst är tillgänglig där. Mer information om skyddade certifierade Azure-tjänster finns [på sidan Australien på service Trust-portalen](https://aka.ms/au-irap).

>[!NOTE]
>Microsoft rekommenderar att myndighets data för alla sensitivities och klassificeringar bör distribueras till regionerna Australien, centrala och Australien, centrala 2, eftersom de är utformade och som är avsedda att användas specifikt för myndighets behov.

Mer information om de särskilda egenskaperna hos de australiska Azure-regionerna finns i [Azure-regionerna Australien, centrala](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Hur Microsoft delar upp sekretessbelagda och officiella data

Microsoft driver Azure och Office 365 i Australien som om alla data är känsliga eller klassificerade, vilket höjer våra säkerhets kontroller till den höga stapeln.

Infrastrukturen som stöder Azure kan hantera data för flera klassificeringar. Eftersom vi förutsätter att kund informationen är klassificerad, är lämpliga kontroller på plats. Microsoft har antagit en grundläggande säkerhets position för våra tjänster som uppfyller PSPF-kraven för att lagra och bearbeta skyddad sekretessbelagd information.

För att se till att våra kunder som en klient i Azure inte är utsatta för andra klienter, implementerar Microsoft omfattande skydds kontroller.

Utöver de funktioner som implementeras inom Microsoft Azures plattformen kan ytterligare kund konfigurerbara kontroller, till exempel kryptering med Kundhanterade nycklar, kapslad virtualisering och just-in-Time administrativ åtkomst, minska risken ytterligare. Inom Azure Government australiska regionerna i Canberra är en process för formell vit listning endast australiska och nya Zeeland myndigheter och nationella kritiska infrastruktur organisationer. Det här community-molnet ger ytterligare garantier för organisationer som är känsliga för risker med klient organisationer.

Rapporten Microsoft Azure skyddad certifiering bekräftar att dessa kontroller är effektiva för lagring och bearbetning av skyddade, klassificerade data och deras isolering.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Relevans för IRAP/CCSL till statliga myndigheter och leverantörer av kritiska infrastrukturer

Många statliga myndigheter och leverantörer av kritiska infrastrukturer innefattar federala myndighets krav i säkerhets policyn och garanti ramverket. Dessa organisationer hanterar också officiella, officiella: Känsligt, och en mängd skyddade, klassificerade data, antingen från deras interaktion med federala myndigheter eller i sin egen rätt.

Den australiska regeringen fokuserar i allt högre ordning och lagstiftning om skydd av icke-myndighets data som påverkar säkerheten och den ekonomiska välgrunden för Australien. Därför är Azure-regionerna i Australien och CCSL-certifieringen relevanta för alla dessa branscher.

![Kritiska infrastruktur sektorer](media/nci-sectors.png)

Microsofts certifieringar visar att Azure-tjänster har genomgått en grundlig, rigorös och formell utvärdering av säkerhets skydd på plats och att de har godkänts för att hantera sådana mycket känsliga data.

## <a name="location-and-control-of-microsoft-data-centres"></a>Plats och kontroll över Microsofts Data Center

Det är ett obligatoriskt krav på myndighets och kritisk infrastruktur för att explicit känna till data centrets plats och ägarskap för moln tjänster som bearbetar sina data. Microsoft är unikt som en moln leverantör med hög skalbarhet för att ge omfattande information om dessa platser och ägarskap.

Microsoft Azure-regionerna i Australien (Australien, centrala och Australien, centrala 2) används i anläggningarna för CDC-Data Center. Ägarskapet till CDC-Data Center är australiskt med 48% ägande från samväldet Superannuation Corporation, 48% ägande från Infratil (en ny Zeelands, två australiska och Nya Zeelands fondbörs som visar långsiktiga infrastrukturer till gångs fond) och 4% australisk hantering. 

Hantering av CDC-Data Center har avtal på plats med den australiska regeringen som begränsar framtida överföring av ägande rätt och kontroll. Den här transparensen av försörjnings kedjan och ägande rätten via Microsofts partnerskap med CDC-Data Center är i linje med principerna för [hela den offentliga värd strategin](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) och definitionen av ett certifierat Server Center.

## <a name="azure-services-that-are-included-in-the-current-ccsl-certification"></a>Azure-tjänster som ingår i den aktuella CCSL-certifieringen

I juni 2017 är ACSC Certified 41 Azure-tjänster för lagring och bearbetning av data på oklassificerade: DLM-nivå. I april 2018 var 24 av dessa tjänster certifierade för skyddade, klassificerade data.

Tillgängligheten för ACSC Azure-tjänster i våra Azure-regioner i Australien är följande (tjänster som visas i fetstil är certifierade på den skyddade nivån).

|Azure-regioner i Australien, centrala|Icke-regionala tjänster och andra regioner|
|---|---|
|API Management, app Gateway, Programtjänster, **automatisering**, **Azure Portal**, **säkerhets kopiering**, **batch**, **Cloud Services**, Cosmos DB, Event Hubs, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, logg Analys, **Multi-Factor Authentication**, Redis cache, **Resource Manager**, **Service Bus**, **Service Fabric**, **Site Recovery**, **SQL Database**, **lagring**, Traffic Manager, **virtuell Datorer**, **Virtual Network** **VPN gateway**|**Azure Active Directory**, CDN, Data Catalog, **importera export**, **Information Protection**, **IOT Hub**, Machine Learning, Media Services, **Notification Hubs**, Power BI, **Scheduler**, **Security Center**, Sök Stream Analytics|
|

Microsoft publicerar översikten [över Microsoft Azure kompatibilitet](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) som visar alla omfattande tjänster för alla globala, myndigheter, branscher och regionala efterlevnads-och utvärderings processer som Azure går igenom, vilket omfattar IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-needed"></a>Azure-tjänsten är inte listad eller utvärderad på lägre nivå än vad som behövs

Tjänster som inte är certifierade eller som har certifierats vid officiella: Känslig, men inte den skyddade nivån, kan användas tillsammans eller som en del av en lösning som är värd för skyddade data förutsatt att tjänsterna är antingen:

- Inte lagring eller bearbetning av skyddade data okrypterade, eller
- Du har slutfört en riskbedömning och godkänt tjänsten för att lagra skyddade data själv.

Du kan använda en tjänst som inte ingår i CCSL för att lagra och bearbeta officiella data, men ISM kräver att du meddelar ACSC när du gör det innan du går in i eller förnyar ett kontrakt med en moln tjänst leverantör.

Alla tjänster som används av ett organ för skyddade arbets belastningar måste utvärderas och godkännas i enlighet med de processer som beskrivs i ISM-och IRAP-utvärderings processen för företag i den [DTA säkra moln strategin](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf).

![DTA certifierings process för säker moln strategi](media/certification.png)

Microsoft utvärderar kontinuerligt våra tjänster för att säkerställa att plattformen är säker och anpassad för användning för den australiska regeringen. Kontakta Microsoft om du behöver hjälp med en tjänst som inte finns på CCSL på den skyddade nivån.

Eftersom Microsoft har en mängd tjänster som är certifierade på CCSL både i oklassificerad DLM och skyddade klassificeringar, kräver ISM att vi genomför en IRAP utvärdering av våra tjänster minst vartannat år. Microsoft underbörjar en årlig utvärdering, vilket även är en möjlighet att inkludera ytterligare tjänster för övervägande.

## <a name="certified-protected-gateway-in-azure"></a>Certifierad skyddad gateway i Azure

Microsoft använder inte en myndighets-certifierad säker Internet-Gateway (SIG) på grund av begränsningar för antalet SIGs som tillåts under Gateway-konsoliderings programmet. Men de förväntade och nödvändiga funktionerna i en SIG kan konfigureras i Microsoft Azure.

Med den skyddade certifieringen av Azure-tjänster har ACSC vissa rekommendationer till myndigheter för att ansluta till Azure och när de implementerar nätverks segmentering mellan säkerhets domäner, till exempel mellan skyddade och Internet. Dessa rekommendationer omfattar användningen av nätverks säkerhets grupper, brand väggar och virtuella privata nätverk. ACSC rekommenderar att du använder en virtuell gateway-enhet. Det finns flera virtuella apparater som är tillgängliga i Azure och som har en fysisk motsvarighet i listan över ASD-produkter eller som har utvärderats mot de gemensamma kriterie-skydds profilerna och som visas på portalen med vanliga villkor. De här produkterna erkänns ömsesidigt av ASD som en undertecknare i överenskommelsen om gemensamt kriterium igenkänning.

Microsoft har skapat vägledning om hur du implementerar Azure-baserade funktioner som tillhandahåller de säkerhetsfunktioner som krävs för att skydda avgränsningen mellan olika säkerhets domäner, som, när de kombineras, utgör motsvarigheten till en certifierad SIG. Ett antal partner kan hjälpa till med utformning och implementering av dessa funktioner och ett antal partner lösningar är tillgängliga som gör samma sak.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Säkerhets fri klarande och medborgarskap hos Microsofts support personal

Microsoft driver våra tjänster globalt med övervakad och utbildad säkerhets personal. Personal som har unescorted fysisk åtkomst till anläggningar i Sydney och Melbourne har australiska myndigheters bas linje säkerhet. Personal i Australien Central-och Australien, centrala 2-regionerna har minst negativa först konsumentsajter 1-(NV1) fria utrymme (efter behov för hemliga data). Dessa krav på utrymme ger ytterligare garantier till kunder om att personal i data centra som använder Azure har hög tillförlitlighet.

Microsoft har ingen stående åtkomst princip med åtkomst som beviljats via ett system av just-in-Time och bara tillräckligt med administration baserat på rollbaserade åtkomst kontroller. I de flesta fall kräver våra administratörer inte åtkomst eller behörighet till kund information för att felsöka och underhålla tjänsten. Hög grad av automatisering och skript för aktiviteter för fjärrkörning, vilket gör att du inte behöver direkt åtkomst till kunddata.

Den allmänna advokaten har bekräftat att Microsofts personal säkerhets principer och procedurer i Azure överensstämmer med syftet med PSPF-åtkomsten till informations bestämmelserna i informations säkerhet – 9.

## <a name="store-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Lagra internationell trafik av vapen bestämmelser (ITAR) eller export av administrations bestämmelser (öron) data

Azures tekniska kontroller som hjälper kunder att uppfylla sina skyldigheter för export-styrda data är samma globalt i Azure. Det är viktigt att det inte finns några formella utvärderings-och certifierings ramverk för export-styrda data.

Vi har lagt till ytterligare avtals-och process åtgärder på plats för Azure Government och Office 365 amerikanska myndigheter för att stödja kunder som omfattas av export kontroller. De ytterligare avtals satserna och den garanterade amerikanska nationella supporten och administrationen av Azure-regioner finns inte på plats för Australien.

Det innebär inte att Azure i Australien inte kan användas för ITAR/öron, men du måste tydligt förstå de begränsningar som du har infört genom export licensen. Du måste också implementera ytterligare skydd för att uppfylla dessa skyldigheter innan du använder Azure för att lagra dessa data. Du kan till exempel behöva:

- Bygg nationalitet som ett attribut i Azure Active Directory.
- Använd Azure Information Protection för att genomdriva krypterings regler över data och begränsa det till endast USA och andra nationella bestämmelser som ingår i export licensen.
- Kryptera alla data lokalt innan du lagrar dem i Azure genom att använda en kund nyckel eller hålla din egen nyckel för ITAR-data.

Eftersom ITAR inte är en formell certifiering måste du förstå vilka begränsningar och begränsningar som är kopplade till export licensen. Sedan kan du arbeta via om det finns tillräckligt många kontroller i Azure för att uppfylla dessa krav. I det här fallet är ett av problemen att tänka på att det är till gång av våra tekniker som kanske inte är medborgare som godkänts på export licensen.

## <a name="next-steps"></a>Nästa steg

 För ISM-kompatibel konfiguration och implementering av VPN-anslutning till Azure Australien, se [Azure VPN gateway](vpn-gateway.md).
