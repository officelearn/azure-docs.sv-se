---
title: "Azure Notification Hubs: Vanliga frågor (FAQ) | Microsoft Docs"
description: "Vanliga frågor och svar om att designa/implementera lösningar på Notification Hubs"
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: "push-meddelande, push-meddelanden, push-meddelanden för iOS, android push-meddelanden, push ios, android push"
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
ms.openlocfilehash: f3bfda250b7f8ed679d05057682b95683d402535
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Push-meddelanden med Azure Notification Hubs: vanliga frågor och svar
## <a name="general"></a>Allmänt
### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Vad är resurs-strukturen för Meddelandehubbar?

Azure Notification Hub har två nivåer för resursen: NAV och namnområden. En hub är en enkel push-resurs som kan innehålla flera plattformar push-information för en app. En namnrymd är en samling av NAV i en region.

Rekommenderade mappning matchar ett namnområde med en app. Du kan ha en produktions-hubb som fungerar med din produktionsapp, en testnings-hubb som fungerar med din testning app och så vidare i ett namnområde.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Vad är Prismodell för Meddelandehubbar?
Senaste prisinformation finns på den [Notification Hub-priser] sidan. Notification Hubs faktureras på namnområdesnivån. (Definition av ett namnområde, finns i ”vad är resurs strukturen för Notification Hubs”?) Notification Hubs ger tre nivåer:

* **Ledigt**: den här nivån är en bra utgångspunkt för att utforska push-funktioner. Det rekommenderas inte för produktion appar. Du får 500 enheter och 1 miljon push-meddelanden med per namnområde per månad, med ingen garanti för tjänsten servicenivåavtal (SLA).
* **Grundläggande**: det här skiktet (eller standardnivån) rekommenderas för mindre produktion appar. Du får 200 000 enheter och 10 miljoner push-meddelanden med per namnområde per månad som utgångspunkt. Kvoten ingår tillväxt.
* **Standard**: den här nivån rekommenderas för medelstora till stora produktion appar. Du får 10 miljoner enheter och 10 miljoner push-meddelanden med per namnområde per månad som utgångspunkt. Kvoten öka alternativ och omfattande telemetri funktioner ingår.

Standardnivån funktioner:
* **Effektiv telemetri**: du kan använda Notification Hubs Per meddelande telemetri för att spåra alla push begäranden och Platform Notification System Feedback för felsökning.
* **Multitenancy**: du kan arbeta med Platform Notification System autentiseringsuppgifter på en nivå i namnområdet. Det här alternativet kan du enkelt dela hyresgäster i hubbar inom samma namnområde.
* **Schemalagda push**: du kan schemalägga meddelanden som ska skickas när som helst.

### <a name="what-is-the-notification-hubs-sla"></a>Vad är Notification Hubs SERVICENIVÅAVTAL?
För nivåerna Basic och Standard Notification Hubs, korrekt konfigurerade program skicka push-meddelanden eller utföra hanteringsåtgärder för registrering minst 99,9% av tiden. Om du vill veta mer om SLA, gå till den [Notification Hubs SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) sidan.

> [!NOTE]
> Eftersom push-meddelanden är beroende av tredje parts plattformsspecifika meddelandesystem (till exempel Apple APNS och Google FCM), finns det inga SLA-garantin för leverans av meddelanden. När Meddelandehubbar skickar batcharna till plattformsspecifika meddelandesystem (SLA garanteras), ansvarar plattformsspecifika meddelandesystem att leverera push-meddelanden (ingen SLA garanteras).

### <a name="which-customers-are-using-notification-hubs"></a>Som kunder använder Notification Hubs?
Många kunder använder Notification Hubs. Vissa viktiga som finns här:

* Sochi 2014: Hundratals intressegrupper, 3 + miljoner enheter och 150 + miljoner meddelanden som skickas i två veckor. [Fallstudie: Sochi]
* Skanska: [Fallstudie: Skanska]
* Seattle tidpunkter: [Fallstudie: Seattle gånger]
* Mural.LY: [Fallstudie: Mural.ly]
* 7Digital: [Fallstudie: 7Digital]
* Bing-appar: Tiotal miljoner enheter skicka 3 miljoner meddelanden per dag.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Hur uppgraderar jag eller nedgradera min nav eller -namnområde till ett annat skikt?
Gå till den  **[Azure-portalen]** > **Notification Hubs namnområden** eller **Meddelandehubbar**. Välj den resurs som du vill uppdatera och gå till **prisnivån**. Observera följande krav:

* Den uppdaterade prisnivån gäller *alla* hubbar i namnområdet som du arbetar med.
* Om din enhet antal överskrider gränsen på den nivå som du Nedgradera till, måste du ta bort enheter innan du Nedgradera.


## <a name="design-and-development"></a>Design och utveckling
### <a name="which-server-side-platforms-do-you-support"></a>Vilka plattformar som servern stöder?
Server-SDK: er är tillgängliga för .NET, Java, Node.js, PHP och Python. Notification Hubs API: er baseras på REST-gränssnitt, så att du kan arbeta direkt med REST API: er om du använder olika plattformar eller inte vill att extra beroende. Mer information finns i [Notification Hub REST API: er] sidan.

### <a name="which-client-platforms-do-you-support"></a>Vilka klientplattformar stöd?
Push-meddelanden stöds för [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android Kina (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) och [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome-appar](notification-hubs-chrome-push-notifications-get-started.md), och [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Mer information finns i [Notification Hubs Kom igång-självstudierna] sidan.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Har du stöd för SMS, e-post eller webb-meddelanden?
Notification Hubs är främst att skicka meddelanden till mobila appar. Den ger inte e-post eller text-funktioner. Dock tredjeparts-plattformar som innehåller dessa funktioner kan integreras med Notification Hubs för att skicka native push-meddelanden med hjälp av [Mobilappar].

Notification Hubs ger också inte en tjänst i webbläsaren push notification leveransen direkt. Kunder kan implementera den här funktionen med SignalR på serversidan plattformar som stöds. Om du vill skicka meddelanden till webbläsarappar i sandlådan Chrome finns i [Chrome-appar kursen].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Hur är Mobile Apps och Azure Notification Hubs relaterade och när används de?
Om du har en befintlig mobila app tillbaka slutet och du vill lägga till endast möjligheten att skicka push-meddelanden kan du använda Azure Notification Hubs. Om du vill konfigurera din mobila app tillbaka slutet från början, Överväg att använda funktionen Mobilappar i Azure App Service. En mobil app etablerar en meddelandehubb automatiskt så att du enkelt skicka push-meddelanden från serverdelen för mobilappen. Priser för Mobile Apps innehåller grundläggande avgifterna för en meddelandehubb. Du betalar bara när du överskrider de inkluderade push-meddelanden. Mer information om kostnaderna för att gå till den [priser för Apptjänst] sidan.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Hur många enheter kan jag använda om jag skicka push-meddelanden via Notification Hubs?
Referera till den [Notification Hub-priser] ha information om hur många enheter som stöds.

Om du behöver stöd för fler än 10 miljoner registrerade enheter, [kontaktar du oss](https://azure.microsoft.com/overview/contact-us/) direkt och vi hjälper dig att skala din lösning.

### <a name="how-many-push-notifications-can-i-send-out"></a>Hur många push-meddelanden kan skickas?
Beroende på den valda nivån skalas Azure Notification Hubs automatiskt baserat på antalet meddelanden som passerar genom systemet.

> [!NOTE]
> Den totala användning kostnaden kan öka baserat på antalet push-meddelanden som hanteras. Kontrollera att du är medveten om gränserna nivån på den [Notification Hub-priser] sidan.
> 
> 

Våra kunder använder Notification Hubs för att skicka miljontals push-meddelanden varje dag. Du behöver inte göra något särskilt att skala räckvidden för push-meddelanden så länge som du använder Azure Notification Hubs.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Hur lång tid det tar för att skicka push-meddelanden till min enhet?
I ett scenario med normal användning, där den inkommande belastningen är konsekvent och även, Azure Notification Hubs kan bearbeta minst *1 miljon push-meddelanden skickas en minut*. Hastigheten kan variera beroende på antalet taggar, typ av inkommande skickar och andra yttre faktorer.

Under den uppskattade leveranstiden tjänsten beräknar per plattform och vägar till Push Notification Service (PNS) baserat på den registrerade taggar eller Tagguttryck. Den ansvarar pns-systemet att skicka meddelanden till enheten.

Pns-systemet garanterar inte någon SLA för att leverera meddelanden. Men levereras de flesta push-meddelanden till målenheter inom några minuter (normalt inom 10 minuter) från den tidpunkt som de skickas till Notification Hubs. Några meddelanden kan ta längre tid.

> [!NOTE]
> Azure Notification Hub har en princip för att ta bort alla push-meddelanden som inte levereras till pns-systemet inom 30 minuter. Den här fördröjningen kan bero på ett antal orsaker, men de flesta ofta eftersom pns-systemet begränsning är ditt program.
> 
> 

### <a name="is-there-any-latency-guarantee"></a>Finns det några garanti latens?
På grund av uppbyggnad push-meddelanden (de levereras med en extern, plattformsspecifika PNS), finns det ingen garanti för fördröjning. Merparten av push-meddelanden skickas vanligtvis inom några minuter.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Vad behöver jag att tänka på när du skapar en lösning med namnområden och notification hubs?
#### <a name="mobile-appenvironment"></a>Mobila appmiljön
* Använda en meddelandehubb per mobilappen per miljö.
* Varje klient ska ha en separat NAV i ett scenario med flera innehavare.
* Aldrig delar samma meddelandehubben för produktion och testmiljöer. Den här övningen kan orsaka problem när du skickar meddelanden. (Apple erbjuder Sandbox och produktion Push slutpunkter med särskilda referenser.)
* Du kan skicka testmeddelanden till din registrerade enheter via Azure-portalen eller Azure integrerad komponenten i Visual Studio som standard. Tröskelvärdet har angetts till 10 enheter som väljs slumpmässigt från poolen registrering.

> [!NOTE]
> Om din hubb ursprungligen konfigurerades med ett certifikat för Apple sandbox och sedan var konfigureras om för att använda ett certifikat för Apple produktion, är de ursprungliga enhetstoken ogiltiga. Ogiltig token orsaka push-meddelanden att misslyckas. Separata produktions- och testmiljöer och använda olika NAV för olika miljöer.
> 
> 

#### <a name="pns-credentials"></a>PNS-autentiseringsuppgifter
När en mobil app har registrerats med en plattform developer-portalen (till exempel Apple eller Google), skickas ett app-ID och säkerhetstoken. Serverdelen app ger dessa token till plattformens PNS så att push-meddelanden kan skickas till enheter. Säkerhetstoken kan vara i form av certifikat (till exempel Apple iOS eller Windows Phone) eller säkerhetsnycklar (till exempel Google Android eller Windows). De måste konfigureras i meddelandehubbar. Konfigurationen görs vanligtvis på meddelandehubben nivå, men kan även utföras på namnområdesnivån i ett scenario med flera innehavare.

#### <a name="namespaces"></a>namnområden
Namnområden kan användas för distribution av gruppering. De kan också användas för att representera alla meddelandehubbar för alla klienter i samma app i ett scenario med flera innehavare.

#### <a name="geo-distribution"></a>GEO-distribution
GEO-distribution är inte alltid viktigt i scenarier för push-meddelande. Olika PNSes (till exempel APN eller GCM) som levererar push-meddelanden till enheter som inte är jämnt.

Om du har ett program som används för globalt kan skapa du hubbar i olika namnområden med hjälp av tjänsten Meddelandehubbar i olika Azure-regioner runtom i världen.

> [!NOTE]
> Vi rekommenderar inte den här ordningen eftersom det ökar din management kostnader, särskilt för registreringar. Utfärdad endast om det behövs en explicit.
> 
> 

### <a name="should-i-do-registrations-from-the-app-back-end-or-directly-through-client-devices"></a>Bör jag göra registreringar från serverdelen app eller direkt via klienten enheter?
Registreringar från serverdelen app är användbara när du vill autentisera klienter innan du skapar registreringen. De kan också användas när du har taggar som måste skapas eller ändras av app-serverdel som baseras på logiken i appen. Mer information finns i [Backend registrering vägledning] och [Backend registrering vägledning 2] sidor.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Vad är push notification leveransmodell säkerhet?
Azure Notification Hubs använder en [signatur för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md)-baserade säkerhetsmodell. Du kan använda signatur för delad åtkomsttoken på rotnivå för namnområdet eller detaljerade notification hub. Delad åtkomstsignatur token kan ställas in att följa olika auktoriseringsregler, till exempel, att skicka meddelandet behörigheter eller att lyssna efter meddelande behörigheter. Mer information finns i [Meddelandehubbar säkerhetsmodell] dokumentet.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Hur får jag för att hantera känslig nyttolast i push-meddelanden?
Alla meddelanden levereras till målenheter av plattformens PNS. När ett meddelande skickas till Azure Notification Hubs, bearbetas och skickas till respektive pns-systemet.

Alla anslutningar från avsändaren med Azure Notification Hubs för att pns-systemet, använder HTTPS.

> [!NOTE]
> Azure Notification Hub loggar inte nyttolasten för meddelanden på något sätt.
> 
> 

Om du vill skicka känslig nyttolaster bör du använda en säker Push-mönster. Avsändaren ger ett ping-meddelande med ett meddelande-ID till enheten utan känsliga nyttolasten. När appen på enheten tar emot nyttolasten, anropar en säker API: et direkt för att hämta informationen i appen. En guide för hur du implementerar det här mönstret går du till den [Notification Hubs Secure Push kursen] sidan.

## <a name="operations"></a>Åtgärder
### <a name="what-support-is-provided-for-disaster-recovery"></a>Vilka support tillhandahålls för katastrofåterställning?
Vi ger metadata disaster recovery täckning hos oss (Notification Hub-namn, anslutningssträngen och annan viktig information). När en katastrofåterställning utlöses registreringsdata är den *endast segmentera* Meddelandehubbar infrastrukturens förlorade. Du behöver att implementera en lösning för igen dessa data till din nya hubb efter återställning:

1. Skapa en sekundär meddelandehubben i olika datacenter. Vi rekommenderar att du skapar en från första början för att skärma av du från disaster recovery händelser som kan påverka dina hanteringsfunktioner. Du kan också skapa en vid tidpunkten för disaster recovery-händelse.

2. Fyll i sekundära notification hub med registreringar från din primära meddelandehubb. Vi rekommenderar inte att du försöker hantera registreringar på båda NAV och hålla dem synkroniserade som registreringar finns i. Den här metoden fungerar inte bra på grund av inbyggd tendens registreringar att gälla på PNS-sida. Notification Hubs rensar dem som den tar emot PNS feedback om registreringar har upphört att gälla eller är ogiltig.  

Vi har två rekommendationer för app-servrar:

* Använda en app-serverdel som upprätthåller en given uppsättning registreringar med dess slut. Den kan utföra en massinfogning till sekundär notification hub.

* Använda en app-serverdel som hämtar en vanlig dump registreringar från den primära meddelandehubben som en säkerhetskopia. Den kan utföra en massinfogning till sekundär notification hub.

> [!NOTE]
> Registreringar Export/Import-funktioner som är tillgängliga i standardnivån beskrivs i den [registreringar Export/Import] dokumentet.
> 
> 

Om du inte har en serverdel när appen startar på målenheterna kan utföra de en ny registrering i sekundära notification hub. Slutligen har sekundära notification hub alla aktiva enheter som har registrerats.

Det blir en tidsperiod när enheter med oöppnad appar inte ta emot meddelanden.

### <a name="is-there-audit-log-capability"></a>Granska loggen kapaciteten finns?
Alla Meddelandehubbar hanteringsåtgärder gå till åtgärden loggarna, vilket visas i den [klassiska Azure-portalen].

## <a name="monitoring-and-troubleshooting"></a>Övervakning och felsökning
### <a name="what-troubleshooting-capabilities-are-available"></a>Vilka funktioner för felsökning är tillgängliga?
Azure Notification Hub innehåller flera funktioner för felsökning, särskilt för det vanligaste scenariot för utelämnade aviseringar. Mer information finns i [Meddelandehubbar felsökning] vitboken.

### <a name="what-telemetry-features-are-available"></a>Vilka telemetri funktioner är tillgängliga?
Azure Notification Hubs kan visa telemetridata i den [klassiska Azure-portalen]. Information om mätvärdena som är tillgängliga på den [Notification Hubs mått] sidan.

> [!NOTE]
> Lyckade meddelanden innebär helt enkelt att push-meddelanden har levererats till externa pns-systemet (till exempel APNS för Apple) eller GCM för Google. Ansvarar för att leverera meddelanden till målenheterna pns-systemet. Pns-systemet visar vanligtvis inte leverans mått till tredje part.  
> 
> 

Vi ger också möjlighet att exportera telemetridata programmässigt (i standardnivån). Mer information finns i [Notification Hubs mått exempel].

[klassiska Azure-portalen]: https://manage.windowsazure.com
[Notification Hub-priser]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Fallstudie: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Fallstudie: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Fallstudie: Seattle gånger]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Fallstudie: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Fallstudie: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[Notification Hub REST API: er]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Notification Hubs Kom igång-självstudierna]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome-appar kursen]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Backend registrering vägledning]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Backend registrering vägledning 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Meddelandehubbar säkerhetsmodell]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Notification Hubs Secure Push kursen]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Meddelandehubbar felsökning]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Notification Hubs mått]: https://msdn.microsoft.com/library/dn458822.aspx
[Notification Hubs mått exempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[registreringar Export/Import]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure-portalen]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobilappar]: https://azure.microsoft.com/services/app-service/mobile/
[priser för Apptjänst]: https://azure.microsoft.com/pricing/details/app-service/
