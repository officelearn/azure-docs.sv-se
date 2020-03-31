---
title: Vanliga frågor och svar om azure-meddelandehubbar | Microsoft-dokument
description: Vanliga frågor om hur du utformar och implementerar lösningar på Azure Notification Hubs.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
editor: jwargo
keywords: push anmälan, push-meddelanden, iOS push-meddelanden, Android push-meddelanden, ios push, android push
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 11/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 3212520f37d33a2d8fb1b071506f688b9f75f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263837"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Push-meddelanden med Azure Notification Hubs: Vanliga frågor och svar

## <a name="general"></a>Allmänt

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Vad är resursstrukturen för Meddelandehubbar?

Azure Notification Hubs har två resursnivåer: hubbar och namnområden. Ett nav är en enda push-resurs som kan innehålla push-informationen över flera plattformar för en app. Ett namnområde är en samling nav i en region. Rekommenderad mappning matchar ett namnområde med en app. Inom ett namnområde kan du ha ett produktionsnav som fungerar med din produktionsapp, ett testnav som fungerar med din testapp och så vidare.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Vad är prismodellen för Meddelandehubbar?

Den senaste prisinformationen finns på sidan [Prissättning av meddelandehubbar.] Meddelandehubbar faktureras på namnområdesnivå. (För definitionen av ett namnområde finns i "Vad är resursstrukturen för meddelandehubbar?") Notification Hubs erbjuder tre nivåer:

* **Gratis:** Den här nivån är en bra utgångspunkt för att utforska push-funktioner. Det rekommenderas inte för produktionsappar. Du får 500 enheter och 1 miljon pushs ingår per namnområde per månad, utan servicenivåavtal (SLA) garanti.
* **Grundläggande:** Den här nivån (eller standardnivån) rekommenderas för mindre produktionsappar. Du får 200 000 enheter och 10 miljoner push-enheter som ingår per namnområde och månad som baslinje.
* **Standard**: Den här nivån rekommenderas för medelstora till stora produktionsappar. Du får 10 miljoner enheter och 10 miljoner pushes ingår per namnområde per månad som en baslinje. Innehåller omfattande telemetri (ytterligare data om push-status som tillhandahålls).

Standardnivåfunktioner:

* **Rich telemetri:** Du kan använda Meddelandehubbar per meddelandetelemetri för att spåra eventuella push-begäranden och feedback från plattformsmeddelanden för felsökning.
* **Flera innehavare:** Du kan arbeta med autentiseringsuppgifter för plattformsmeddelandesystem på namnområdesnivå. Med det här alternativet kan du enkelt dela upp klienter i hubbar inom samma namnområde.
* **Schemalagd push:** Du kan schemalägga meddelanden som ska skickas ut när som helst.
* **Massåtgärder**: Aktiverar funktioner för export/import av registreringar enligt beskrivningen i dokumentet [Exportera/importera registreringar.]

### <a name="what-is-the-notification-hubs-sla"></a>Vad är serviceavtalet för meddelandehubbar?

För basic- och Standard Notification Hubs-nivåer kan korrekt konfigurerade program skicka push-meddelanden eller utföra registreringshanteringsåtgärder minst 99,9 procent av tiden. Mer information om serviceavtalet finns på sidan [SLA för meddelandehubbar.](https://azure.microsoft.com/support/legal/sla/notification-hubs/)

> [!NOTE]
> Eftersom push-meddelanden är beroende av tredjepartsplattformsmeddelandesystem som Apples API:er (Push Notification Service) och Googles Firebase Cloud Messaging (FCM), finns det ingen SLA-garanti för leverans av dessa meddelanden. När Meddelandehubbar skickar batcharna till Platform Notification Systems (SLA guaranteed) är det plattformsmeddelandesystemens ansvar att leverera pushes (ingen SLA garanteras).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Hur uppgraderar eller nedgraderar jag navet eller namnområdet till en annan nivå?

Gå till **[Namnområdena]** > för Azure Portal**Notification Hubs** eller **Notification Hubs**. Välj den resurs som du vill uppdatera och gå till **Prisnivå**. Observera följande krav:

* Den uppdaterade prisnivån gäller för *alla* hubbar i namnområdet som du arbetar med.
* Om antalet enheter överskrider gränsen för den nivå som du nedgraderar till måste du ta bort enheter innan du nedgraderar.

## <a name="design-and-development"></a>Design och utveckling

### <a name="which-server-side-platforms-do-you-support"></a>Vilka serverplattformar stöder ni?

Server-SDK:er är tillgängliga för .NET, Java, Node.js, PHP och Python. API:er för meddelandehubbar baseras på REST-gränssnitt, så du kan arbeta direkt med REST-API:er om du använder olika plattformar eller inte vill ha extra beroende. Mer information finns på sidan [REST API:er för meddelandehubbar.]

### <a name="which-client-platforms-do-you-support"></a>Vilka kundplattformar stöder du?

Push-meddelanden stöds för [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), Android Kina [(via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) och Android och [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Mer information finns på sidan [Komma igång med meddelandehubbar.]

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Stöder du sms-, e-post- eller webbaviseringar?

Meddelandehubbar skickar meddelanden till enheter som kör mobilappar. Det ger inte e-post eller sms-funktioner. Meddelandehubbar tillhandahåller inte heller en push-meddelandetjänst i webbläsaren. Kunder kan implementera den här funktionen med SignalR ovanpå de serverplattformar som stöds. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Hur många enheter kan jag ha stöd för om jag skickar push-meddelanden via Meddelandehubbar?

Mer information om antalet enheter som stöds finns på sidan Priser för [meddelandehubbar.]

Om du behöver stöd för mer än 10 miljoner registrerade enheter måste du partitionera dina enheter över flera hubbar.

### <a name="how-many-push-notifications-can-i-send-out"></a>Hur många push-meddelanden kan jag skicka ut?

Beroende på den valda nivån skalas Azure Notification Hubs automatiskt upp baserat på antalet meddelanden som flödar genom systemet.

> [!NOTE]
> Den totala användningskostnaden kan öka baserat på antalet push-meddelanden som skickas. Se till att du är medveten om de nivågränser som beskrivs på sidan [Prissättning av meddelandehubbar.]

Våra kunder använder Meddelandehubbar för att skicka miljontals push-meddelanden dagligen. Du behöver inte göra något speciellt för att skala räckvidden för dina push-meddelanden så länge du använder Azure Notification Hubs.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Hur lång tid tar det för skickade push-meddelanden att nå min enhet?

I ett scenario med normal användning, där den inkommande belastningen är konsekvent och jämn, kan Azure Notification Hubs bearbeta minst *1 miljon push-meddelanden skickar en minut*. Den här hastigheten kan variera beroende på antalet taggar, typen av inkommande skickar och andra externa faktorer.

Under den beräknade leveranstiden beräknar tjänsten målen per plattform och dirigerar meddelanden till Push Notification Service (PNS) baserat på registrerade taggar eller tagguttryck. Det är PNS ansvar att skicka meddelanden till enheten.

PNS garanterar inte något serviceavtal för att leverera meddelanden. De flesta push-meddelanden levereras dock till målenheter inom några minuter (vanligtvis inom 10 minuter) från den tidpunkt då de skickas till Meddelandehubbar. Några aviseringar kan ta längre tid.

> [!NOTE]
> Azure Notification Hubs har en princip för att släppa alla push-meddelanden som inte levereras till PNS inom 30 minuter. Den här fördröjningen kan inträffa av flera skäl, men oftast eftersom PNS begränsar ditt program.

### <a name="is-there-any-latency-guarantee"></a>Finns det någon latens garanti?

På grund av push-meddelandenas karaktär (de levereras av en extern, plattformsspecifik PNS) finns det ingen latensgaranti. Vanligtvis levereras de flesta push-meddelanden inom några minuter.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Vad behöver jag tänka på när jag utformar en lösning med namnområden och meddelandehubbar?

#### <a name="mobile-appenvironment"></a>Mobilapp/miljö

* Använd en meddelandehubb per mobilapp, per miljö.
* I ett scenario med flera innehavare bör varje klient ha en separat hubb.
* Dela aldrig samma meddelandehubb för produktions- och testmiljöer. Den här metoden kan orsaka problem när du skickar meddelanden. (Apple erbjuder slutpunkter för Sandbox och Production Push, var och en med separata autentiseringsuppgifter.)
* Som standard kan du skicka testmeddelanden till dina registrerade enheter via Azure-portalen eller den Azure-integrerade komponenten i Visual Studio. Tröskelvärdet är inställt på 10 enheter som väljs slumpmässigt från registreringspoolen.

> [!NOTE]
> Om hubben ursprungligen konfigurerades med ett Apple-sandlådecertifikat och sedan konfigurerades om för att använda ett Apple-produktionscertifikat är de ursprungliga enhetstoken ogiltiga. Ogiltiga tokens orsakar att pushes misslyckas. Separera dina produktions- och testmiljöer och använd olika nav för olika miljöer.

#### <a name="pns-credentials"></a>PNS-autentiseringsuppgifter

När en mobilapp registreras hos en plattforms utvecklarportal (till exempel Apple eller Google) skickas en appidentifierare och säkerhetstoken. Appens servering tillhandahåller dessa token till plattformens PNS så att push-meddelanden kan skickas till enheter. Säkerhetstoken kan vara i form av certifikat (till exempel Apple iOS eller Windows Phone) eller säkerhetsnycklar (till exempel Google Android eller Windows). De måste konfigureras i meddelandehubbar. Konfigurationen görs vanligtvis på meddelande-hubvsnivå, men det kan också göras på namnområdesnivå i ett scenario med flera innehavare.

#### <a name="namespaces"></a>Namnområden

Namnområden kan användas för distributionsgruppering. De kan också användas för att representera alla meddelandehubbar för alla klienter i samma app i ett scenario med flera innehavare.

#### <a name="geo-distribution"></a>Geodistribution

Geodistribution är inte alltid avgörande i push-meddelandescenarier. Olika PNSes (till exempel APNs eller FCM) som levererar push-meddelanden till enheter distribueras inte jämnt.

Om du har ett program som används globalt kan du skapa hubbar i olika namnområden med hjälp av tjänsten Notification Hubs i olika Azure-regioner runt om i världen.

> [!NOTE]
> Vi rekommenderar inte detta arrangemang eftersom det ökar din hanteringskostnad, särskilt för registreringar. Det bör endast göras om det finns ett uttryckligt behov.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Ska jag göra registreringar från appens backend eller direkt via klientenheter?

Registreringar från appens backend är användbara när du måste autentisera klienter innan du skapar registreringen. De är också användbara när du har taggar som måste skapas eller ändras av appens backend baserat på applogik. Mer information finns på vägledningen för [backend-registrering] och [vägledning för backend-registrering 2] sidor.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Vad är säkerhetsmodellen för push-meddelandeleverans?

Azure Notification Hubs använder en signaturbaserad säkerhetsmodell med [delad åtkomst.](../storage/common/storage-dotnet-shared-access-signature-part-1.md) Du kan använda signaturtoken för delad åtkomst på rotnamnområdesnivå eller på den detaljerade meddelandehubben. Signaturtoken för delad åtkomst kan ställas in så att de följer olika auktoriseringsregler, till exempel för att skicka meddelandebehörigheter eller för att lyssna efter meddelandebehörigheter. Mer information finns i [säkerhetsmodelldokumentet för meddelandehubbar.]

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Hur ska jag hantera känslig nyttolast i push-meddelanden?

Alla meddelanden levereras till målenheter av plattformens PNS. När ett meddelande skickas till Azure Notification Hubs bearbetas det och skickas till respektive PNS.

Alla anslutningar, från avsändaren till Azure Notification Hubs till PNS, använder HTTPS.

> [!NOTE]
> Azure Notification Hubs loggar inte nyttolasten för meddelanden.

Om du vill skicka känsliga nyttolaster rekommenderar vi att du använder ett säkert push-mönster. Avsändaren levererar ett ping-meddelande med en meddelandeidentifierare till enheten utan den känsliga nyttolasten. När appen på enheten tar emot nyttolasten anropar appen ett säkert API direkt för att hämta meddelandeinformationen. En guide om hur du implementerar det här mönstret finns på [självstudiesidan För Notification Hubs Secure Push.]

## <a name="operations"></a>Åtgärder

### <a name="what-support-is-provided-for-disaster-recovery"></a>Vilket stöd ges för katastrofåterställning?

Vi tillhandahåller metadata katastrofåterställning täckning på vår sida (Meddelande Hubs namn, anslutningssträngen och annan viktig information). När ett katastrofåterställningsscenario utlöses är registreringsdata det *enda segmentet* i Notification Hubs-infrastrukturen som går förlorad. Du måste implementera en lösning för att återbefolka dessa data i ditt nya nav efter återställningen:

1. Skapa en sekundär meddelandehubb i ett annat datacenter. Vi rekommenderar att du skapar en från början för att skydda dig från en katastrofåterställningshändelse som kan påverka dina hanteringsfunktioner. Du kan också skapa en vid tidpunkten för haveriberedskapshändelsen.

2. Fyll i den sekundära meddelandehubben med registreringarna från din primära meddelandehubb. Vi rekommenderar inte att du försöker underhålla registreringar på båda hubbar och hålla dem synkroniserade när registreringar kommer in. Denna praxis fungerar inte bra på grund av den inneboende tendensen av registreringar att löpa ut på PNS sidan. Meddelandehubbar rensar upp dem när de får PNS-feedback om utgångna eller ogiltiga registreringar.  

Vi har två rekommendationer för app backends:

* Använd en app-backend som behåller en viss uppsättning registreringar i slutet. Den kan sedan utföra en massinsats i den sekundära meddelandehubben.
* Använd en app-backend som får en vanlig dump av registreringar från den primära meddelandehubben som en säkerhetskopia. Den kan sedan utföra en massinsats i den sekundära meddelandehubben.

> [!NOTE]
> Funktioner för export/import av registreringar som är tillgängliga på standardnivån beskrivs i dokumentet [Exportera/importera registreringar.]

Om du inte har en backend, när appen startar på målenheter, utför de en ny registrering i den sekundära meddelandehubben. Så småningom kommer den sekundära meddelandehubben att ha alla aktiva enheter registrerade.

Det kommer att finnas en tidsperiod när enheter med oöppnade appar inte får aviseringar.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Lagras alla mina data i krypterad form?

Azure Notification Hubs krypterar alla kunddata i vila med undantag för registreringstaggar. Därför bör du inte lagra personliga eller konfidentiella data med hjälp av taggar.

### <a name="is-there-audit-log-capability"></a>Finns det granskningsloggkapacitet?

Ja. Alla hanteringsåtgärder för meddelandehubbar uppdaterar den Azure-aktivitetslogg som visas i [Azure-portalen]. Azure Activity Log ger insikter om de åtgärder som utförs på resurser i dina prenumerationer. Med aktivitetsloggen kan du bestämma vad, vem och när för alla skrivåtgärder (PUT, POST, DELETE) som gjorts för resurserna i prenumerationen. Du kan också förstå status för åtgärderna och andra relevanta egenskaper. Emellertid. Aktivitetsloggen innehåller inte läsåtgärd (GET).

## <a name="monitoring-and-troubleshooting"></a>Övervakning och felsökning

### <a name="what-troubleshooting-capabilities-are-available"></a>Vilka felsökningsfunktioner är tillgängliga?

Azure Notification Hubs innehåller flera funktioner för felsökning, särskilt för det vanligaste scenariot med ignorerade meddelanden. Mer information finns i felsökningsvitboken [för meddelandehubbar.]

### <a name="what-telemetry-features-are-available"></a>Vilka telemetrifunktioner är tillgängliga?

Azure Notification Hubs gör det möjligt att visa telemetridata i [Azure-portalen]. Information om måtten finns på sidan [Mått för meddelandehubbar.]

Du kan också programmatiskt komma åt mått. Mer information finns i följande artiklar:

- [Hämta Azure Monitor-mått med .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). I det här exemplet används användarnamn och lösenord. Om du vill använda ett certifikat överbelastar du metoden FromServicePrincipal för att ange ett certifikat som visas i [det här exemplet](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Hämta mått och aktivitetsloggar för en resurs](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Azure Övervakning REST API genomgång](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> Lyckade meddelanden innebär helt enkelt att push-meddelanden har levererats till externa PNS (till exempel APN för iOS och macOS eller FCM för Android-enheter). Det är PNS ansvar att leverera meddelanden till målenheter. Vanligtvis exponerar PNS inte leveransmått för tredje part.  

[Azure-portal]: https://portal.azure.com
[Prissättning av meddelandehubbar]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[REST-API:er för meddelandehubbar]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Självstudier för kom igång-start i meddelandehubben]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Vägledning för säkerhetskopieringsregistrering]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Vägledning för backend-registrering 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Säkerhetsmodell för meddelandehubbar]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Självstudiekurs för Notification Hubs Secure Push]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Felsökning av meddelandehubbar]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Mått för meddelandehubbar]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Registreringar Exportera/Importera]: https://docs.microsoft.com/azure/notification-hubs/export-modify-registrations-bulk
[Azure-portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
