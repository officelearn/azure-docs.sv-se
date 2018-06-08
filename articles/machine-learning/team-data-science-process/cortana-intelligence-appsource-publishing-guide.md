---
title: Cortana Intelligence AppSource publiceringsguide | Microsoft Docs
description: Här är alla steg som du behöver utföra för att publicera Cortana Intelligence-lösningen till AppSource som Microsoft-Partner.
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams
ms.openlocfilehash: 9f4e88be7b9b8e3ed7f6a2bbd299fd1e92f9c7af
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836627"
---
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Cortana Intelligence AppSource publiceringsguide

## <a name="overview"></a>Översikt
AppSource är den enda mål för beslutsfattare (BDMs) för att identifiera och sömlöst försök lösningar/företagsappar skapat av partners och utvärderas av Microsoft. Titta på [den här videon](https://youtu.be/hpq_Y9LuIB8) att lära dig hur AppSource fungerar. 

Som en Microsoft-Partner kan du verkligen utnyttja publicera på AppSource om du har:
- En intelligent lösning-app med hjälp av [Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/?cdn=disable).
- Din lösning eller app adresser specifika affärsproblem.
- Du har skapat moduler eller immateriella som dina kunder kan återanvända relativt snabbt förutsägbart.

Ta en titt på [Cortana Intelligence-lösningar](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1) som redan har publicerats på AppSource. 

Den här artikeln beskriver stegen för att hämta en partner Cortana Intelligence-lösningen som publicerats till AppSource

## <a name="getting-started"></a>Komma igång
1. I den [Partner Community fördelar guiden](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) (PDF), se sidan 9 för att hämta listats som en Advanced Analytics-partner.
1. Slutför den [skicka din app](https://appsource.microsoft.com/en-us/partners/list-an-app) formuläret.

    För frågan *”Välj de produkter som din app är utformat för*”, kontrollera den **andra** kryssrutan och listan ”Cortana Intelligence” i dialogrutan Redigera kontroll.
1. Innan en Cortana Intelligence-app kan hämta publicerats så att AppSource, måste hämta godkänd av Cortana Intelligence Partner lösning för teknik-teamet. Att hämta att påbörjades, se dela information om din app genom att fylla i undersökningsformuläret vid [Cortana Intelligence lösning utvärdering för AppSource publicering](https://aka.ms/cisappsrceval). Den här platsen är lösenordsskyddat och platsen innehåller instruktioner om hur du återställer lösenordet.

## <a name="solution-evaluation-criteria"></a>Kriterier för utvärdering av lösning
Här är en lista över villkor som appen måste uppfylla
1. Appen måste adressen specifika företag använda case problem inom ett angivet funktionsområde på ett repeterbara sätt med minimala konfigurationer för fördefinierade värderingsförslag implementable inom en kort tidsperiod.
1. Lösningen ska använda minst en av följande komponenter:

    - HDInsight
    - Machine Learning
    - Data Lake Analytics
    - Stream Analytics
    - Cognitive Services
    - Bot Framework
    - Analysis Services
    - Fristående Microsoft R Server
    - R-tjänster i SQL 2016 eller HDInsight Premium
1. Lösningen ska generera minst 1000 $ i månad per kunden med DPOR/CSP.
1. Lösningen använder i Azure Active Directory federerad enkel inloggning (AAD federerad enkel inloggning) med medgivande som aktiverats för autentisering och åtkomstkontroll för resursen. Du måste visa utvärdering-teamet att din lösning är AAD federerad enkel inloggning aktiverad innan appen kan publiceras till AppSource.

     Om du vill se vad det innebär att AAD federerad enkel inloggning aktiverad försöka placera 02:35 i [AppSource utvärderingsversionen igenom](https://aka.ms/trialexperienceforwebapps) video. Om din app inte är aktiverad med AAD federerad enkel inloggning ännu, här är några dokumentationen om den
   1. [Ett klick inloggning](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/).
   1. [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application).
     
1. Använd Power BI i din lösning: valfritt men rekommenderas eftersom det bevisas för att generera högre antal leads.

## <a name="devcenter-account-setup"></a>Konfiguration av DevCenter
Detta är processen för att registrera ditt företag för att bli en utgivare med Microsoft. Om du redan är en registrerad utgivare med ett befintligt konto DevCenter dela e-post-ID som är associerade med kontot DevCenter. När din ansökan har godkänts för publicering, du får åtkomst till hela dokumentationen [moln Portal hantera publisher profiler](https://cloudpartner.azure.com/#documentation/manage-publisher-profile)

Om du inte har någon, är viktiga steg för att konfigurera ett konto för DevCenter nedan.
1. Skapa ett Microsoft-konto [här](https://signup.live.com/signup.aspx).
1. Gå till Microsoft-DevCenter [registreringssidan](http://go.microsoft.com/fwlink/?LinkId=615100) och klicka på ”Logga”.
1. När du uppmanas att göra betalningen kan du använda koden som vi har lagt till dig. Om du inte har en kod, kontakta [ appsourcecissupport@microsoft.com ](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup).
1. Välj den [land/region](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) där du bor, eller där din verksamhet finns. **Du kan inte ändra detta senare.**
1. Välj din [developer kontotyp](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) för AppSource Välj **företagets**. För ett företag bör du granska dessa [riktlinjer](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account).
1. Ange den kontaktinformation som du vill använda för ditt utvecklarkonto.
Detaljerade steg-för-steg-instruktioner för hur du konfigurerar DevCenter konto, se anvisningarna [här](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration).

## <a name="provide-info-for-microsoft-sellers"></a>Ange information för Microsoft säljare
En av de viktiga värderingsförslag partners AppSource är för att kunna samarbeta med Microsoft Sellers placera partnerappar framför potentiella kunder.

Fylls [Partner lösning information för Microsoft Sellers](https://aka.ms/aapartnerappinfo) och skicka det till [ appsourcecissupport@microsoft.com ](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs). Detta är nödvändiga steg för en Cortana Intelligence-app som får godkännas för publicering.

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>Skapa en tvingande kunden genomgång på AppSource
Först, ta en titt på [Neal Analytics inventering optimering](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) på AppSource. Varje app posten i AppSource har rubrik, Sammanfattning (maximalt 100 tecken), beskrivning (1300 tecken max), bilder, videor (valfritt), PDF-dokument förutom posten pekar för en utvärderingsversionen. Partners bör utnyttja dessa för att skapa en tvingande kundupplevelse.

Partners bör tänka på det innehåll som de lägger på AppSource som en försäljning orchestration slutpunkt till slutpunkt. Kunder läsa rubrik och beskrivning för att förstå prisvärt och sedan gå igenom bilder & videor för att förstå vad lösningen om. Fallstudier kunderna potentiella se hur befintliga kunder får värdet. 

Alla detta bör se kunden du intresserad av och vill veta mer. Tänk på dessa som breddsteg däck baserat presentation en bra teknisk säljare skulle igenom nya kunder. Formatet föreslagna beskrivningen är att dela upp text i underavdelningar som baseras på värdet propositions, var och en med markerade med en underrubrik. 

I korthet vanligtvis besökare över fältet ”erbjuder sammanfattning” och underordnade rubriker för att hämta gist vilka appen adresser och varför bör de appen i bara en snabb överblick över. Därför är det viktigt att hämta användarens uppmärksamhet ge dem en anledning att läsa för att hämta information.

Se vad dessa tillverkare har gjort.
- [Neal Analytics inventering optimering](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Plexure Retail-anpassning](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint men tjänster](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

Den slutliga försäljning är att visa en demonstration av app/lösningen visar hur värdeförslag levereras. Det är exakt en utvärderingsversion kundupplevelse på AppSource är avsett för. En bra demonstration att göra följande:
- Nytt sammanfatta molntjänsters app i kort och lista ut personer inom företaget kund som kan interagera med lösningen
- Berätta för en artikel och anger kontexten om en exempel-kund behandlar specifika problem
- Förklara hur situationen kan vanligtvis förvärvats och påverka kunden (innan) jämfört med vad som skulle hända om lösningen används. Detta kan visas med PowerBI instrumentpaneler osv.
- Sammanfatta hur lösningen gör det ske med hjälp av en specifik maskininlärningsalgoritmer osv.

Innehåll som läggs till i AppSource ska vara av hög kvalitet och stitched in tillräckligt för att aktivera följande:
- En partner tekniska försäljning avdelningen bör använda den för sina försäljning orchestration. Din försäljning grupper med hjälp av det är ett bra tecken som du förväntar dig MS försäljning avdelningen även att kunna göra samma sak. Detta aktiverar kunden kontaktpunkt för att kunna vidarebefordra konsekvent samma artikel till deras team förarbiträden och högre ups för att få budget och godkännanden innan kan du göra ett inköp erbjudande.
- En kund som besöker webbplatsen ekologiskt kan gå igenom innehållet i alla ord och känslan taggade svara tillbaka till partner kommunikation att gå vidare med nästa steg.

Det är därför partners fundera de enkelt på AppSource som en försäljning orchestration slutpunkt till slutpunkt. Baserat på artikeln rad och funktioner som ska visas i utvärderingsversionen, kan typ av erbjudandet fastställas.

## <a name="publish-your-app-on-the-publishing-portal"></a>Publicera appen i publishing portal
När vi har utvärderats stegen ovan för programmet, du får åtkomst till publishing portal och kan se [hur du publicerar ett erbjudande Cortana Intelligence via molnet partnerportalen](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app) detaljerad information om nästa steg.

Om du behöver information om något av fälten e- <appsourcecissupport@microsoft.com>.
## <a name="my-app-is-published-on-appsource---now-what"></a>Min app har publicerats på AppSource - vad?
Det första publicerade Gratulerar komma din app.
Andelen returnerar dig från att publicera en app på AppSource kraftigt beror på hur du påverka målgrupp. Se [hackningsförsök Cortana Intelligence appen på AppSource tillväxt](http://aka.ms/aagrowthhackguide) för mer information om hur du kan utnyttja returnerar.

Om du har några frågor eller förslag, nå oss på <appsourcecissupport@microsoft.com>.

