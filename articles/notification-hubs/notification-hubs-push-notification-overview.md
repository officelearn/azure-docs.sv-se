<properties
    pageTitle="Azure Notification Hubs"
    description="Lär dig använda push-meddelanden i Azure. Kodexempel som skrivits i C# med hjälp av .NET-API."
    authors="wesmc7777"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="08/25/2016"
    ms.author="wesmc"/>


#Azure Notification Hubs

##Översikt

Azure Notification Hubs innehåller en lättanvänd och utskalad push-infrastruktur som kan användas för en rad olika plattformar. Med hjälp av dessa kan du skicka mobila push-meddelanden från alla serverdelar (i molnet eller lokalt) till alla mobila plattformar.

Med Notification Hubs kan du enkelt skicka plattformsoberoende, skräddarsydda push-meddelanden genom att abstrahera information från olika plattformsspecifika meddelandesystem (PNS). Med ett enda API-anrop kan du rikta in dig på enskilda användare eller hela målgruppssegment som innehåller miljontals användare, oavsett vad de använder för enheter.

Du kan använda Notification Hubs för både företags- och konsumentscenarier. Några exempel:

- Skicka meddelanden med de senaste nyheterna till miljoner användare med kort svarstid (Notification Hubs ger stöd för Bing-appar som finns förinstallerade i alla Windows-operativsystem och i Windows Phone-enheter).
- Skicka platsbaserade kuponger till användarsegment.
- Skicka händelsemeddelanden till användare eller grupper med sport-/ekonomi-/spelappar.
- Meddela användare om företagshändelser, till exempel nya meddelanden/mejl och säljleads.
- Skicka engångslösenord som krävs för Multi-Factor Authentication.



##Vad är push-meddelanden?

Smartmobiler och surfplattor kan ”avisera” användare när en händelse inträffar. Dessa meddelanden förekommer i en rad olika former.

I Windows Store- och Windows Phone-appar kan aviseringar göras via ett _popup-meddelande_: ett lägeslöst fönster visas, med ljud, för att signalera att ett nytt meddelande tagits emot. Andra aviseringstyper som kan användas är meddelanden av typerna _ikon_, _rådata_ och _aktivitetsikon_. Se [Paneler, aktivitetsikoner och meddelanden](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx) för mer information om vilka typer av aviseringar som stöds av Windows-enheterna.

På Apple iOS-enheter görs aviseringar med push-funktionen på ett liknande sätt via en dialogruta där användaren uppmanas öppna eller stänga meddelandet. Om du klickar på **Visa** öppnas den app som tar emot meddelandet. Se [iOS-meddelanden](http://go.microsoft.com/fwlink/?LinkId=615245) för mer information om aviseringar i iOS.

Push-meddelanden kan användas av mobila enheter för att visa uppdaterad information samtidigt som energi sparas. Meddelanden kan skickas av serverdelssystem för mobila enheter även om apparna i fråga inte är aktiva på enheterna. Push-meddelanden är en mycket viktig komponent för konsumentappar, där de används för att göra användarna mer aktiva och öka användningen av apparna. Meddelanden är också användbara för företag eftersom uppdaterad information ökar medarbetarnas svarsförmåga vad gäller affärshändelser.

Några exempel på specifika scenarier med Mobile Engagement är:

1.  En panel i Windows 8 eller på Windows Phone uppdateras med aktuell ekonomisk information.
2.  En användare meddelas via ett popup-meddelande att han eller hon har tilldelats vissa arbetsobjekt i en arbetsflödesbaserad företagsapp.
3.  En aktivitetsikon med det aktuella antalet säljleads i en CRM-app (till exempel Microsoft Dynamics CRM) visas.

##Så här fungerar push-meddelandena

Push-meddelanden levereras via plattformsspecifika infrastrukturer som kallas för _plattformsspecifika meddelandesystem_ (Platform Notification Systems, PNS). Ett PNS-system har barebones-funktioner (det vill säga, det har inget stöd för sändning eller anpassning) och det har inget gemensamt gränssnitt. Om du som exempel vill skicka ett meddelande till en Windows Store-app, måste en utvecklare kontakta WNS (Windows Notification Service). Om du vill skicka ett meddelande till en iOS-enhet, måste samma utvecklare kontakta APNS (Apple Push Notification Service) och skicka meddelandet en andra gång. Azure Notification Hubs gör processen enklare genom att tillhandahålla ett gemensamt gränssnitt, tillsammans med andra funktioner som stöder push-meddelanden för en rad olika plattformar.

Men på en hög nivå följer alla plattformsspecifika meddelandesystem samma mönster:

1.  Klientappen kontaktar PNS-systemet för att hämta dess _handtag_. Handtagstypen varierar beroende på vilket system som används. För WNS är det en URI, eller ”meddelandekanal”. För APNS är det en token.
2.  Klientappen lagrar detta handtag i appens _serverdel_ för senare användning. För WNS är serverdelen normalt en tjänst i molnet. För Apple kallas systemet för en _provider_.
3.  För att skicka ett push-meddelande kontaktar appens serverdel PNS-systemet genom att använda handtaget för att rikta in sig på en specifik klientappinstans.
4.  PNS-systemet vidarebefordrar meddelandet till den enhet som anges av handtaget.

![][0]

##Utmaningarna med push-meddelanden

Även om dessa system är mycket kraftfulla krävs det ändå mycket arbete av apputvecklaren för att implementera även de mest grundläggande scenarierna för push-meddelanden, som att sända eller skicka ut push-meddelanden till segment av användare.

Push-meddelanden är en av de mest efterfrågade funktionerna i molntjänster för mobila appar. Anledningen är att den infrastruktur som krävs för att göra så att de fungerar är ganska komplex och till stor del saknar samband med den huvudsakliga affärslogiken i appen. Några av utmaningarna med att bygga en push-infrastruktur av typen på begäran är:

- **Plattformsberoende.** För att kunna skicka meddelanden till enheter på olika plattformar, måste flera gränssnitt kodas in i serverdelen. Det är inte bara lågnivåinformationen som skiljer sig åt, utan även presentationen av aviseringarna (panel, popup-meddelande eller aktivitetsikon) är plattformsberoende. Dessa skillnader kan leda till att man får en komplex serverdelskod som är svår att underhålla.

- **Skalning.** Skalningen av denna infrastruktur består av två delar:
    + Enligt PNS-riktlinjerna måste enhetstoken uppdateras varje gång appen startas. Detta leder till att det krävs en stor mängd trafik (och databasåtkomst till följd av detta) bara för att hålla dessa enhetstoken uppdaterade. När antalet enheter växer och blir fler (det kan röra sig om miljontals), blir kostnaden för att skapa och hantera den här infrastrukturen långt ifrån försumbar.

    + De flesta PNS-system kan inte användas för att sända meddelanden till flera enheter. Detta gör att en sändning till miljontals enheter även kräver miljontals anrop till PNS-system. Att kunna skala dessa förfrågningar är inget litet jobb eftersom apputvecklare vanligtvis vill hålla nere den totala svarstiden. Den sista enhet som tar emot meddelandet bör som exempel inte ta emot meddelandet 30 minuter efter att meddelandet har skickats; detta för att det i många fall skulle motverka själva syftet med push-meddelandena.
- **Routning.** PNS-system ger dig ett sätt att skicka meddelanden till en enhet. I de flesta appar riktas dock meddelandena till vissa användare och/eller intressegrupper (till exempel alla medarbetare som arbetar med ett visst kundkonto). På grund av detta, och för att kunna dirigera (route) meddelandena till rätt enheter, måste appens serverdel upprätthålla ett register som associerar intressegrupper med enhetstoken. Det här arbetet gör att det tar ännu längre tid att få ut en app på marknaden och att den totala underhållskostnaden ökar.

##Varför bör du använda Notification Hubs?

Notification Hubs gör saker och ting mycket enklare. Du behöver inte hantera de utmaningar som push-meddelandena ställer dig inför. Du kan i stället använda en Notification Hub. Notification Hubs använder en komplett multiplattform, med en utskalad infrastruktur för push-meddelanden, och ger en betydligt mindre push-specifik kod som körs i appens serverdel. Notification Hubs har alla funktioner som en push-infrastruktur behöver. Enheterna används enbart för att registrera PNS-handtag. Det är serverdelen som sköter sändandet av plattformsoberoende meddelanden till användare eller intressegrupper, så som visas på följande bild:

![][1]


Notification Hubs ger dig en push-infrastruktur som är färdig att användas och som har följande fördelar:

- **Flera plattformar.**
    +  Stöd för alla större mobila plattformar. Notification Hubs kan skicka push-meddelanden till appar för Windows Store, iOS, Android och Windows Phone.

    +  Notification Hubs ger dig ett gemensamt gränssnitt för att skicka meddelanden till alla plattformar som stöds. Det krävs inga plattformsspecifika protokoll. Appens serverdel kan skicka meddelanden i plattformsspecifika eller plattformsoberoende format. Appen kommunicerar bara med Notification Hubs.

    +  Hantering av enhetshandtag. Notification Hubs underhåller handtagsregistret och feedbacken från PNS-systemen.

- **Fungerar med alla serverdelar**: I molnet eller lokalt, .NET, PHP, Java, Node o.s.v.

- **Skalning.** Notification Hubs kan skalas för miljoner enheter utan att du behöver ändra arkitekturen eller fragmentera.


- **Stor uppsättning av leveransmönster**:

    - *Sändning*: Gör att du kan utföra en nästan simultan sändning till miljoner enheter med ett enda API-anrop.

    - *Unicast/Multicast*: Push-överföring till taggar som representerar enskilda användare, inklusive alla deras enheter, eller till en större grupp, till exempel separata formfaktorer (surfplattor kontra smartmobiler).

    - *Segmentering*: Push-överföring till komplexa segment som definieras av tagguttryck (till exempel, enheter i New York som följer The Yankees).

    Varje enhet kan ange en eller flera _tagg_ när du skickar dess handtag till en meddelandehubb. Följ länken för mer information om [tagg]. Taggar måste inte etableras i förväg eller placeras. Taggar ger dig ett enkelt sätt att skicka meddelanden till användare eller intressegrupper. Eftersom taggar kan innehålla alla typer av appspecifika identifierare (som användar- eller grupp-ID:n), gör användningen av dem att belastningen på appens serverdel reduceras eftersom den inte behöver lagra och hantera enhetshandtag.

- **Anpassning**: Varje enhet kan ha en eller flera mallar för att kunna använda lokalisering per enhet och anpassning utan att detta påverkar serverdelskoden.

- **Säkerhet**: SAS (Shared Access Secret) eller federerad autentisering.

- **Effektiv telemetri**: Tillgänglig i portalen och genom programmering.


##Integrering med Mobilappar i Apptjänst

För att underlätta för en sömlös och enhetlig användarupplevelse av alla Azure-tjänster har [App Service Mobile Apps] inbyggt stöd för push-meddelanden som skickas via Notification Hubs. [App Service Mobile Apps] är en mycket skalbar, globalt tillgänglig plattform för mobilappsutveckling tänkt att användas av utvecklare av företagsprogram och systemintegrerare. Den ger mobilutvecklare tillgång till ett stort utbud av funktioner.

Utvecklare av mobilappar kan utnyttja Notification Hubs med följande arbetsflöde:

1. Hämta PNS-handtag för enheter
2. Registrera enhet och [mallar] med Notification Hub via en praktisk registrering med API för klient-SDK:er i Mobile Apps
    + Observera att Mobile Apps av säkerhetsskäl tar bort alla taggar vid registreringen. Arbeta med Notification Hubs direkt från din serverdel för att associera taggar med enheter.
3. Skicka meddelanden från din apps serverdel med Notification Hubs

Här är några av de fördelar som utvecklarna får tack vare den här integreringen:

- **Klient-SDK:er för Mobile Apps** Dessa SDK:er för flera plattformar ger dig enkla API:er för att registrera och tala med den meddelandehubb som har länkats till mobilappen automatiskt. Utvecklare behöver inte gräva djupt bland autentiseringsuppgifterna för Notification Hubs eller arbeta med en ytterligare tjänst.
    + SDK:erna taggar en given enhet automatiskt med ett autentiserat användar-ID för Mobile Apps och aktiverar på så vis ett scenario för push-till-användare.
    + SDK:erna använder automatiskt installations-ID:t för Mobile Apps som GUID för att registrera med Notification Hubs vilket gör att utvecklarna slipper upprätthålla flera olika GUID-tjänster.
    
- **Installationsmodell.** Mobile Apps fungerar tillsammans med Notification Hubs senaste push-modell för att representera alla push-egenskaper som är kopplade till en enhet i en JSON-installation. Denna kopplas samman med Push Notification Services och är enkel att använda.

- **Flexibilitet.** Utvecklare kan alltid välja att arbeta direkt med Notification Hubs, även om integrationen redan är på plats.

- **Integrerad upplevelse i [Azure Portal].** Push som en funktion är visuellt återgiven i Mobile Apps och utvecklarna kan enkelt arbeta med den associerade meddelandehubben via Mobile Apps.



##Nästa steg

Du hittar mer information om Notification Hubs under följande ämnen:

+ **[Hur kunder använder Notification Hubs]**

+ **[Självstudiekurser och guider om Notification Hubs]**

+ **Självstudiekurser av typen Kom igång med Notification Hubs** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

De relevanta .NET-hanterade API-referenserna för push-meddelanden finns här:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Hur kunder använder Notification Hubs]: http://azure.microsoft.com/services/notification-hubs
  [Självstudiekurser och guider om Notification Hubs]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [App Service Mobile Apps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [mallar]: notification-hubs-templates.md
  [Azure Portal]: https://portal.azure.com
  [tagg]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)



<!--HONumber=sep16_HO1-->


