---
title: Azure Stack datacenter integration genomgången | Microsoft Docs
description: Läs om vad som händer på plats distributionen av Azure Stack i ditt datacenter.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: asganesh
ms.openlocfilehash: 554fd9c32c9831d45a40c62a871e3a8d5f8d7cb9
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191759"
---
# <a name="azure-stack-datacenter-integration"></a>Integrering med Azure Stack-datacenter

Den här artikeln beskriver kundupplevelsen för slutpunkt till slutpunkt i Azure Stack, från att köpa en integrerad lösning genom att lokala distributionen av en leverantör av lösningar. Använd informationen för att underlätta din resa och för att skapa förväntningar på det du, som Azure Stack-kund kan förvänta dig när de integrerar Azure Stack i ditt datacenter.

Azure Stack-kund, bör du förutse följande datacenter integration faser:

|     |Planeringsfasen|Process för|Före|Factory process|Maskinvara leverans|Distribution på plats|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Interagera med partner för att tillhandahålla stöd för före försäljning.|Förbered programvarulicensiering och kontrakt efter behov.|Ange nödvändiga verktyg för att samla in integreringskraven för datacenter och dokumentation till kund.|Ange de senaste versioner av baslinjen och verktyget uppdateringar på en månatlig takt.|Gäller inte|Microsoft support-tekniker hjälpa till med eventuella problem med distributionen.|
|**Partner**|Rekommendera lösningsalternativ baserat på kundernas krav.<br><br>Föreslå konceptbevis (POC) om det behövs.<br><br>Upprätta affärsrelation.<br><br>Besluta om supportnivå.|Förbered nödvändiga avtal med kunden.<br><br>Skapa inköpsorder för kunden.<br><br>Besluta om leverans tidslinje.<br><br>Anslut kunden med Microsoft om det behövs.|Nödvändiga utbildning för att möjliggöra tolkning av alla kraven för distribution och datacenter-integreringsalternativ ger kunden.<br><br>Hjälpa kunderna med verifiering av insamlade data så är korrekt och slutförd.|Tillämpa den senaste validerade baslinje-versionen.<br><br>Tillämpa krävs Microsoft deployment toolkit.|Leverera maskinvara till kunden.|Distribution som hanteras av en teknikern.<br><br>Rack och stack.<br><br>Distributionen av maskinvara livscykel värden (HLH).<br><br>Azure Stack-distribution.<br><br>Leverera till kund.|
|**Kunden**|Beskriver avsedda användningsfall och ange krav.|Fastställa faktureringsmodellen för att använda, granska och Godkänn kontrakt.|Slutför kalkylblad för distribution och se till att alla distributionskrav är uppfyllda och klar för distribution.|Gäller inte|Förbered datacenter genom att kontrollera att alla nödvändiga power och kylning, kantlinje anslutningar och andra nödvändiga datacenter integration krav är uppfyllda.|Vara tillgänglig under distributionen uppge autentiseringsuppgifter för prenumerationen och support om det finns frågor på dessa data.|
| | | | | | | |


## <a name="planning-phase"></a>Planeringsfasen
Planeringsfasen är när Microsoft eller lösningspartner Azure Stack, tillsammans med dig att utvärdera och förstå dina behov för att avgöra om Azure Stack är rätt lösning för dig:

De hjälper dig att fatta beslut om följande:

-   Är Azure Stack den rätta lösningen för din organisation?

-   Vad storlek-lösning behöver du?

-   Vilken typ av modell för fakturering och licensiering fungerar för din organisation?

-   Vad är power och kylning krav?

Att säkerställa att maskinvarulösningen bäst passar dina behov, den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) kan användas för att hjälpa Pre-Purchase som planerar att fastställa rätt kapacitet och konfiguration för Azure Stack-maskinvara lösning.

Kalkylbladet är *inte* avsedd att användas som ersättning för undersökning och analys av lösningar för maskinvara som bäst passar dina behov. När du planerar för distribution av Azure Stack, bör du även läsa den [Allmänt datacenter integration överväganden](azure-stack-datacenter-integration.md) integrerade system för Azure Stack.

## <a name="order-process-phase"></a>Ordning processen fas
I det här skedet skulle många frågor för sannolikheten har besvarats. Nu när du är redo att bekräfta att köpa Azure Stack och när du har registrerat alla nödvändiga kontrakt och inköpsorder blir du ombedd att tillhandahålla integration krav data till leverantören.

## <a name="pre-deployment-phase"></a>Före fas
Under den här fasen måste du bestämma hur du vill integrera Azure Stack i ditt datacenter. För att underlätta den här processen kan sätta Microsoft, i samarbete med lösningsleverantörer, ihop en mall för krav för att hjälpa dig att samla in nödvändig information för att planera för distribution av integrerade system i din miljö.

Den [Allmänt datacenter integration överväganden](azure-stack-datacenter-integration.md) artikeln innehåller information som hjälper till att du slutför mallen kallas kalkylblad för distribution. 

> [!IMPORTANT]
> Under det här steget är det viktigt att all information som krävs är undersöks och valt innan ordning lösningen. Tänk på att det här steget tar tid och kräver samordning och samla in data från flera områden inom din organisation. 

I fasen före distributionen måste du fatta beslut om följande:

- **Azure Stack-modellen och identitet anslutningsprovidern**. Du kan välja att distribuera Azure Stack antingen [som är anslutna till internet (och till Azure) eller frånkopplad](azure-stack-connection-models.md). För att få utnyttja från Azure Stack, inklusive hybridscenarier, kan du distribuera är ansluten till Azure. Att välja Active Directory Federation Services (AD FS) eller Azure Active Directory (Azure AD) är ett enstaka beslut som du måste göra vid tidpunkten för distribution. **Du kan inte ändra detta senare utan att omdistribuera hela systemet**.

- **Licensieringsmodell**. Licensieringsalternativ modell där du kan välja från beror på vilken typ av distribution du har. Valfri identity-providern har inte någon bäring på virtuella datorer eller identitetssystem och konton som de använder.
    - Kunder som tillhör en [frånkopplad distribution](azure-stack-disconnected-deployment.md) har bara ett alternativ: kapacitetsbaserad fakturering.

    - Kunder som tillhör en [anslutna distribution](azure-stack-connected-deployment.md) kan välja mellan kapacitetsbaserad fakturering och betalning som du-användning. Kapacitetsbaserad fakturering kräver en Azure-prenumeration för Enterprise Agreement (EA) för registrering. Detta krävs för registrering, vilket ger för tillgängligheten för objekt i Marketplace via en Azure-prenumeration.

- **Nätverksintegrering**. [Nätverksintegrering](azure-stack-network.md) är avgörande för distributionen, drift och hantering av Azure Stack-system. Det finns flera saker som går in säkerställer Azure Stack-lösningen är flexibel och har en högtillgänglig fysisk infrastruktur att stödja driften.

- **Brandväggen integration**. Vi rekommenderar att du [använder en brandvägg](azure-stack-firewall.md) för att säkra Azure Stack. Brandväggar kan förhindra DDOS-attacker, intrångsidentifiering och innehållsgranskning. Det bör dock noteras att det blir en flaskhals för dataflöde för Azure storage-tjänster.


- **Certifikatkraven**. Det är viktigt som alla [certifikat som krävs](azure-stack-pki-certs.md) finns *tidigare* till en teknikern anländer till datacentret för distribution.

När all information som krävs har samlats in via kalkylblad för distribution, startar lösningsleverantören processen factory baserat på data som samlas in för att säkerställa en lyckad integrering av Azure Stack i ditt datacenter.

## <a name="hardware-delivery-phase"></a>Maskinvara leverans fas
Leverantören fungerar med dig på schemaläggning när lösningen kommer att komma fram till din anläggning. När tas emot och upprättat, måste du schemalägga tidpunkten med lösningsleverantören för att en supporttekniker kommer på plats för att utföra Azure Stack-distributioner.

Det är **avgörande** att alla nödvändiga data är låst och kan *innan den på platsen anländer tekniker för att distribuera lösningen*.

-   Alla certifikat måste köpas och är redo.

-   Domännamnet måste vara valt.

-   Alla nätverksparametrar integration slutförs och överensstämmer med vad du har delat med leverantören.

> [!TIP]
> Om någon av den här informationen har ändrats, se till att kommunicera ändringen med lösningsleverantören innan du schemalägger verklig distribution.

## <a name="onsite-deployment-phase"></a>Distributionsfasen för på plats
För att distribuera Azure Stack, måste tillgång till tekniker från maskinvaruleverantören finnas för att sätta igång distributionen. Se till att all information som anges via distribution kalkylbladet inte har ändrats för att säkerställa en lyckad distribution. 

Följande är vad du bör förvänta sig teknikern under distributionen av:

- Kontrollera alla kablar och kantlinje anslutningen för att säkerställa att lösningen är korrekt uppbyggd och uppfyller dina krav
- Konfigurera lösningen HLH (maskinvara livscykel Host)
- Kontrollera att alla BMC, BIOS och nätverket är korrekta.
- Kontrollera att den inbyggda programvaran för alla komponenter är den senaste godkända versionen av lösningen
- Starta distributionen

> [!NOTE]
> En procedur för distribution av teknikern kan ta ungefär en vecka för företag för att slutföra.

## <a name="post-integration-phase"></a>Efter integrering fas
Flera steg måste utföras av partnern innan lösningen skickas till kunden i fasen efter integrering. I den här fasen är verifiering viktigt att se till att systemet har distribuerats och fungerar korrekt. 

Åtgärder som vidtas av OEM-Partner är:

-   [Kör test-azurestack](azure-stack-diagnostic-test.md#run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix)

-   [Registrering med Azure](azure-stack-registration.md)

-   [Marketplace-syndikering](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

-   Konfigurationsfiler för säkerhetskopiering växel

-   Ta bort DVM

-   Förbereda en sammanfattning av kund för distribution

-   [Kontrollera uppdateringar att kontrollera att lösningen-klientprogramvara uppdateras till den senaste versionen](azure-stack-updates.md)

Det finns flera steg som är obligatoriska eller valfria beroende på installationstypen av.

-   Om distributionen slutfördes med [AD FS](azure-stack-integrate-identity.md), sedan Azure Stack stämpel måste integreras med kundens användarens egna AD FS.

  > [!NOTE]
  > Det här steget är ansvar för kunden, även om partnern kan välja att erbjuda tjänster för att göra detta.

-   Integrering med en befintlig övervakningssystemet från respektive partner.

    -   [System Center Operations Manager Integration](azure-stack-integrate-monitor.md) stöder också funktioner för hantering av vagnpark.

    -   [Nagios-integrering](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>Övergripande tidslinje

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Support
Azure Stack kan en Azure-konsekventa, integrerad supportupplevelse som täcker systemets hela livscykel. Fullständigt stöd för integrerade Azure Stack-system kan måste kunder två supportkontrakt; en med Microsoft (eller deras leverantörer av Molnlösningar) för Azure-tjänster och en med-maskinvaruprovidern för systemsupport. Integrerad supportupplevelse ger samordnad eskalering och problemlösning, så att kunder får en enhetlig supportupplevelse oavsett vem de anropa först. För kunder som redan har Premier, Azure - Standard / ProDirect eller Partner support med Microsoft support för Azure Stack-programvara ingår.

Integrerad supportupplevelse gör användning av ett ärende Exchange mekanism för dubbelriktad överföring av supportärenden och användningsfall uppdateringar mellan Microsoft och partner för maskinvara. Microsoft Azure Stack följer den [moderna livscykelpolicy](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Nästa steg
Läs mer om [Allmänt datacenter integration överväganden](azure-stack-datacenter-integration.md).
