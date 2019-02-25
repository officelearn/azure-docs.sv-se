---
title: 'Azure Notification Hubs: Vanliga frågor (FAQ) | Microsoft Docs'
description: Vanliga frågor och svar på Designa/implementera lösningar på Meddelandehubbar
services: notification-hubs
documentationcenter: mobile
author: jwargo
manager: patniko
editor: spelluru
keywords: push-meddelande, push-meddelanden, push-meddelanden för iOS, android push-meddelanden, push för ios, android-push
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/24/2019
ms.author: jowargo
ms.openlocfilehash: f2f11abcb3290e1d9459c26167706abc79c1b7fa
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670340"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Push-meddelanden med Azure Notification Hubs: Vanliga frågor och svar

## <a name="general"></a>Allmänt

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Vad är resource strukturen för Meddelandehubbar?

Azure Notification Hubs har två resursnivåer: hubs och namnområden. En hubb är en enkel push-resurs som kan innehålla plattformsoberoende push-information för en app. Ett namnområde är en samling med hubbar i en region.

Rekommenderade mappning matchar ett namnområde med en app. Du kan ha en hubb för produktion som fungerar med din produktionsapp, ett testning nav som fungerar med din app och så vidare inom namnområdet.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Vad är Prismodell för Meddelandehubbar?

Senaste prisinformationen hittar du på den [Prisinformation om Notification hub] sidan. Notification Hub faktureras på namnområdesnivå. (Definition av ett namnområde, finns i ”vad är resource strukturen för Meddelandehubbar”?) Meddelandehubbar erbjuder tre nivåer:

* **Kostnadsfria**: Den här nivån är en bra utgångspunkt för att utforska push-funktioner. Det rekommenderas inte för produktionsappar. Du får 500 enheter och 1 miljon push-meddelanden ingår per namnområde per månad, med ingen garanti för tjänsten servicenivåavtal (SLA).
* **Basic**: Den här nivån (eller Standard-nivån) rekommenderas för mindre produktionsappar. Du får 200 000 enheter och 10 miljoner push-meddelanden ingår per namnområde per månad som utgångspunkt.
* **Standard**: Den här nivån rekommenderas för medelstora till stora produktionsappar. Du får 10 miljoner enheter och 10 miljoner push-meddelanden ingår per namnområde per månad som utgångspunkt. Innehåller omfattande telemetri (ytterligare data om push-status som).

Standard-nivån funktioner:

* **Omfattande telemetri**: Du kan använda Notification Hubs telemetri Per meddelande för att spåra alla push-begäranden och feedback från plattformsspecifikt System för felsökning.
* **Multitenancy**: Du kan arbeta med plattformsspecifika meddelandesystem autentiseringsuppgifter i en namnområdesnivå. Det här alternativet kan du enkelt dela klienter i hubs inom samma namnområde.
* **Schemalagda push**: Du kan schemalägga meddelanden skickas ut när som helst.
* **Massregistrera operations**: Aktivera registreringar Export/Import-funktioner som beskrivs i den [registreringar Export/Import] dokumentet.

### <a name="what-is-the-notification-hubs-sla"></a>Vad är Notification Hubs SLA?

För nivåerna Basic och Standard Meddelandehubbar kan korrekt konfigurerade program skicka push-meddelanden eller utföra registreringshanteringsåtgärder minst 99,9 procent av tiden. Mer information om serviceavtalet går du till den [Notification Hubs SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) sidan.

> [!NOTE]
> Eftersom push-meddelanden är beroende av tredje parts plattformsspecifika meddelandesystem (till exempel Apple APNS och Google FCM), finns det ingen garanti för serviceavtal (SLA) för leverans av dessa meddelanden. När Meddelandehubbar skickar batcharna till plattformsspecifika meddelandesystem (SLA garanterad), är det ansvar plattformsspecifika meddelandesystem att leverera push-meddelanden (inget serviceavtal som garanterar).

### <a name="which-customers-are-using-notification-hubs"></a>Vilka kunder använder Notification Hubs?

Många kunder använder Notification Hubs. Vissa viktiga som finns här:

* Sochi 2014: Hundratals intressegrupper, 3 + miljoner enheter och 150 + miljoner meddelanden som skickas inom två veckor. [Fallstudie: Sochi]
* Skanska: [Fallstudie: Skanska]
* Seattle gånger: [Fallstudie: Seattle gånger]
* Mural.LY: [Fallstudie: Mural.LY]
* 7Digital: [Fallstudie: 7Digital]
* Bing-appar: Tiotals miljoner enheter skicka 3 miljoner meddelanden per dag.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Hur jag för att uppgradera eller nedgradera min hubb eller namnområdet till en annan nivå?

Gå till den  **[Azure Portal]** > **Notification Hubs-Namnrymder** eller **Meddelandehubbar**. Välj den resurs som du vill uppdatera och gå till **prisnivå**. Observera följande krav:

* Uppdaterade prisnivån gäller *alla* hubs i namnområdet som du arbetar med.
* Om din enhetsantal överskrider gränsen på den nivå som du nedgradering till, måste du ta bort enheterna innan du börjar Nedgradera.

## <a name="design-and-development"></a>Design och utveckling

### <a name="which-server-side-platforms-do-you-support"></a>Vilka plattformar som serversidan stöder ni?

Server-SDK: er är tillgängliga för .NET, Java, Node.js, PHP och Python. Notification Hubs API: er baseras på REST-gränssnitt, så att du kan arbeta direkt med REST API: er om du använder olika plattformar eller inte vill att extra beroende. Mer information går du till den [Notification Hub REST API: er] sidan.

### <a name="which-client-platforms-do-you-support"></a>Vilka klientplattformar stöder ni?

Push-meddelanden stöds för [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android Kina (Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) och Android, [Chrome-appar](notification-hubs-chrome-push-notifications-get-started.md), och [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Mer information går du till den [Notification Hubs komma igång-Självstudier] sidan.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Har du stöd för SMS, e-post eller webb-meddelanden?

Meddelandehubbar är primärt utformat för att skicka meddelanden till mobila appar. Det ger inte e-post eller SMS-funktioner. Men från tredje part-plattformar som innehåller dessa funktioner kan integreras med Notification Hubs för att skicka systemspecifika push-meddelanden med hjälp av [Mobile Apps].

Meddelandehubbar ger också inte en i webbläsaren push leverans meddelandetjänst direkt ur lådan. Kunder kan implementera den här funktionen med SignalR ovanpå plattformarna som stöds från serversidan. Om du vill skicka meddelanden till webbläsarbaserade appar i sandlådan Chrome finns i den [Självstudier för Chrome].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Hur är Mobile Apps och Azure Notification Hubs relaterade och när ska jag använda dem?

Om du har en befintlig serverdel för mobilappen och du vill lägga till bara möjligheten att skicka push-meddelanden, kan du använda Azure Notification Hubs. Överväg att använda funktionen Mobile Apps i Azure App Service om du vill konfigurera mobilappsserverdelen från grunden. En mobil app etablerar automatiskt en notification hub så att du enkelt kan skicka push-meddelanden från serverdelen för mobilappen. Priser för Mobile Apps innehåller basavgifterna för en meddelandehubb. Du betalar endast när du överskrider de inkluderade push-meddelandena. Mer information om kostnaderna går du till den [prissättning för App Service] sidan.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Hur stöder många enheter kan jag om jag skicka push-meddelanden via Notification Hubs?

Referera till den [Prisinformation om Notification hub] för information om hur många enheter som stöds.

Om du behöver stöd för fler än 10 miljoner registrerade enheter, [Kontakta oss](https://azure.microsoft.com/overview/contact-us/) direkt och vi hjälpa dig att skala din lösning.

### <a name="how-many-push-notifications-can-i-send-out"></a>Hur många push-meddelanden kan jag skicka ut?

Beroende på den valda nivån skalar Azure Notification Hubs automatiskt upp baserat på antalet meddelanden som passerar genom systemet.

> [!NOTE]
> Den övergripande förbrukningskostnad kan öka baserat på antalet push-meddelanden som hanteras. Se till att du är medveten om gränserna för nivån som beskrivs i den [Prisinformation om Notification hub] sidan.

Våra kunder använder Notification Hubs för att skicka miljontals push-meddelanden per dag. Du behöver inte göra något speciellt för att skala räckvidden för push-meddelanden så länge du använder Azure Notification Hubs.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Hur lång tid tar det för skickas push-meddelanden till min enhet?

I ett scenario för normal användning, där den inkommande belastningen är konsekvent och även, Azure Notification Hubs kan bearbeta minst *1 miljon push-meddelande skickar en minut*. Priset kan variera beroende på antalet taggar, typ av inkommande skickar och andra externa faktorer.

Tjänsten beräknar per plattform och vägar till Push Notification Service (PNS) baserat på den registrerade taggar eller Tagguttryck under uppskattad leveranstid. Det är ansvar för pns-systemet att skicka meddelanden till enheten.

Pns-systemet garanterar inte servicenivåavtal (SLA) för att leverera meddelanden. Men levereras de flesta push-meddelanden till målenheter inom ett par minuter (vanligtvis inom 10 minuter) från tidpunkten då de skickas till Notification Hubs. Några meddelanden kan ta längre tid.

> [!NOTE]
> Azure Notification Hubs har en princip för att släppa alla push-meddelanden som inte levereras till PNS inom 30 minuter. Den här fördröjningen kan bero på ett antal orsaker, men de flesta ofta eftersom PNS begränsar ditt program.

### <a name="is-there-any-latency-guarantee"></a>Finns det ingen garanti för fördröjning?

På grund av push-meddelanden (de levereras med en extern, plattformsspecifika PNS), finns det ingen garanti för svarstid. Flesta av push-meddelanden levereras normalt inom några minuter.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Vad behöver jag tänka på när du utformar en lösning med namnområden och notification hubs?

#### <a name="mobile-appenvironment"></a>Mobila app-miljö

* Använda en meddelandehubb per mobilappen per miljö.
* Varje klient ska ha en separat hub i ett scenario med flera innehavare.
* Aldrig delar samma meddelandehubben för produktion- och testmiljöer. Den här metoden kan orsaka problem när du skickar meddelanden. (Apple erbjuder sandbox-miljön och Push-produktion slutpunkter, var och en med separata autentiseringsuppgifter.)
* Som standard kan du skicka testmeddelanden till dina registrerade enheter via Azure portal eller Azure integrerad komponenten i Visual Studio. Tröskelvärdet har angetts till 10 enheter som väljs slumpmässigt från poolen registrering.

> [!NOTE]
> Om din hubb ursprungligen konfigurerades med ett certifikat för Apple-sandbox-miljön och sedan har konfigurerats för att använda ett Apple-certifikat för produktion, är de ursprungliga enhetstoken ogiltiga. Ogiltig token orsaka push-meddelanden misslyckades. Separera din produktions- och testmiljöer och använda olika hubs för olika miljöer.

#### <a name="pns-credentials"></a>PNS-autentiseringsuppgifter

När en mobil app registreras med en plattform developer-portalen (till exempel Apple eller Google), skickas en apptoken för identifierare och säkerhet. Appserverdelen ger dessa token till plattformens PNS så att push-meddelanden kan skickas till enheter. Säkerhetstoken kan vara i form av certifikat (till exempel Apple iOS eller Windows Phone) eller security-nycklar (till exempel Google Android eller Windows). De måste konfigureras i meddelandehubbar. Konfigurationen görs normalt på meddelandehubb nivå, men det kan också göras på namnområdesnivå i ett scenario med flera innehavare.

#### <a name="namespaces"></a>Namnområden

Namnområden kan användas för distribution av gruppering. De kan också användas för att representera alla meddelandehubbar för alla klienter i samma app i ett scenario med flera innehavare.

#### <a name="geo-distribution"></a>GEO-replikering

GEO-replikering är inte alltid viktiga i scenarier för push-meddelande. Olika PNSes (till exempel APNS eller FCM) som levererar push-meddelanden till enheter som inte är jämnt.

Om du har ett program som används för globalt kan skapa du hubbar i olika namnområden med hjälp av Notification Hubs-tjänsten i olika Azure-regioner runtom i världen.

> [!NOTE]
> Vi rekommenderar inte detta arrangemang eftersom det ökar dina kostnader för hantering, särskilt för registreringar. Det görs bara om det finns ett explicit behov.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Ska jag göra registreringar från appserverdelen eller direkt via klienten enheter?

Registreringar från appserverdelen är användbara när du har att autentisera klienter innan du skapar registreringen. Det är också användbart när du har taggar som måste skapas eller ändras av appserverdelen baserat på applogik. Mer information går du till den [Vägledning för backend-registrering] och [Registrering av backend-vägledning 2] sidor.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Vad är push-meddelande leveransmodell säkerhet?

Azure Notification Hubs använder en [signatur för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md)-baserade säkerhetsmodell. Du kan använda token som signatur för delad åtkomst på rotnivå för namnområde eller på detaljerad notification hub. Signaturen för delad åtkomst token kan ställas in att följa olika regler, till exempel, att skicka meddelandet behörigheter eller att lyssna efter meddelanden behörigheter. Mer information finns i den [Säkerhetsmodell för Notification Hubs] dokumentet.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Hur hanterar jag bör känsliga nyttolast i push-meddelanden?

Alla meddelanden levereras till målenheter av plattformens PNS. När ett meddelande skickas till Azure Notification Hubs, bearbetas och skickas till respektive pns-systemet.

Alla anslutningar från avsändaren med Azure Notification Hubs för att pns-systemet, använder HTTPS.

> [!NOTE]
> Azure Meddelandehubbar loggar inte nyttolasten för meddelanden på något sätt.

För att skicka känslig nyttolaster, bör du använda en säker Push-mönster. Avsändaren levererar ett ping-meddelande med en identifierare för meddelandet till enheten utan känsliga nyttolasten. När appen på enheten får nyttolasten, anropar appen en säker API: et direkt för att hämta informationen. En guide för hur du implementerar det här mönstret går du till den [Handledning för Hubs säker Push-meddelande] sidan.

## <a name="operations"></a>Åtgärder

### <a name="what-support-is-provided-for-disaster-recovery"></a>Vilken support tillhandahålls för haveriberedskap?

Vi tillhandahåller metadata disaster recovery täckning på vår sida (Notification Hub-namn, anslutningssträngen och annan viktig information). När en katastrofåterställning utlöses registreringsdata är den *endast segmentera* av Notification Hubs-infrastruktur som går förlorad. Du behöver att implementera en lösning för att åter fylla dessa data till din nya hub efter återställning:

1. Skapa en sekundär meddelandehubben i ett annat datacenter. Vi rekommenderar att du skapar en från första början för att skydda dig från disaster recovery händelser som kan påverka dina hanteringsfunktioner. Du kan också skapa en vid tidpunkten för disaster recovery-händelse.

2. Fyll i den sekundära meddelandehubben med registreringen från din primära notification hub. Vi rekommenderar inte att underhålla registreringar på båda hubs och hålla dem synkroniserade när registreringar kommer in. Den här metoden fungerar inte bra brukar inneboende registreringar att gälla på PNS-sida. Meddelandehubbar rensar dem som den tar emot PNS feedback om registreringar har upphört att gälla eller är ogiltig.  

Vi har två rekommendationer för serverdelar:

* Använd en appserverdel som upprätthåller en given uppsättning registreringar på dess slut. Den kan sedan utföra en massinfogning till sekundära meddelandehubben.
* Använd en appserverdel som hämtar en vanlig dump registreringar från den primära meddelandehubben som en säkerhetskopia. Den kan sedan utföra en massinfogning till sekundära meddelandehubben.

> [!NOTE]
> Registreringar Export/Import-funktioner som är tillgängliga i Standard-nivån beskrivs i den [registreringar Export/Import] dokumentet.

Om du inte har en serverdel när appen startar på målenheter kan utföra de en ny registrering i sekundära meddelandehubben. Så småningom har sekundära meddelandehubben alla aktiva enheter som har registrerats.

Det blir en tidsperiod när enheter med oöppnad appar inte ta emot meddelanden.

### <a name="is-there-audit-log-capability"></a>Finns det audit log funktionen?

Ja. Hantering av åtgärder av alla Meddelandehubbar-uppdatering som Azure-aktivitetsloggen som exponeras i den [Azure Portal]. Azure-aktivitetsloggen ger insikt i de åtgärder som utförts på resurser i dina prenumerationer. Med aktivitetsloggen kan du bestämma vad, vem, och när för alla skrivåtgärder (PUT, POST, ta bort) för resurser i din prenumeration. Du kan också förstå statusen för åtgärder och andra relevanta egenskaper. Men. Aktivitetsloggen inkluderar inte läsåtgärder (GET) igen.

## <a name="monitoring-and-troubleshooting"></a>Övervakning och felsökning

### <a name="what-troubleshooting-capabilities-are-available"></a>Vilka funktioner för felsökning är tillgängliga?

Azure Meddelandehubbar ger flera funktioner för felsökning, särskilt för det vanligaste scenariot för utelämnade meddelanden. Mer information finns i [Meddelandehubbar felsökning] white paper om.

### <a name="what-telemetry-features-are-available"></a>Vilka telemetrifunktioner är tillgängliga?

Azure Notification Hubs kan visa dessa data i den [Azure Portal]. Information om mått som är tillgängliga på den [Notification Hubs mått] sidan.

> [!NOTE]
> Lyckade meddelanden innebär helt enkelt att push-meddelanden har levererats till externa PNS (till exempel APNS för Apple) eller FCM för Google. Ansvarar för att leverera meddelanden till målenheterna pns-systemet. Normalt exponerar PNS inte mått för leverans till tredje part.  

[Azure Portal]: https://portal.azure.com
[Prisinformation om Notification hub]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Fallstudie: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Fallstudie: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Fallstudie: Seattle gånger]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Fallstudie: Mural.LY]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Fallstudie: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[Notification Hub REST API: er]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Notification Hubs komma igång-Självstudier]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Självstudier för Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Vägledning för backend-registrering]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Registrering av backend-vägledning 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Säkerhetsmodell för Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Handledning för Hubs säker Push-meddelande]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Meddelandehubbar felsökning]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Notification Hubs mått]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Registreringar Export/Import]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[Prissättning för App Service]: https://azure.microsoft.com/pricing/details/app-service/
