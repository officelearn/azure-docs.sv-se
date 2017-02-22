---
title: "Vanliga frågor och svar om Azure RemoteApp | Microsoft Docs"
description: "Läs svaren på de vanligaste frågorna om Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: swadhwa
editor: 
ms.assetid: bad66603-91f9-437f-8a70-236405d2a27f
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 9d80640a58171e94440ca02f44a73866f6412997
ms.openlocfilehash: 296dfaaa05b89cae265a9926fad08495ea02a647


---
# <a name="azure-remoteapp-faq"></a>Vanliga frågor och svar om Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp upphör att gälla. Läs [meddelandet](https://go.microsoft.com/fwlink/?linkid=821148) för mer information.
> 
> 

Vi har fått följande frågor om Azure RemoteApp. Har du andra frågor? Besök [RemoteApp-forumen](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) och ställ din fråga eller skriv en kommentar längre ned.

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>Hittar du inte det du letar efter? Har du en fråga vi inte har svarat på?
Om du inte hittar den information som du behöver eller har ytterligare frågor som vi inte har svarat på här kan du gå till [Azure RemoteApp-forumet](http://aka.ms/araforum) och ställa din fråga. Vi kan alltid lägga till fler svar här.

## <a name="what-is-azure-remoteapp"></a>Vad är Azure RemoteApp?
* **Vad är Azure RemoteApp?** RemoteApp är en Azure-tjänst som du kan använda för att ge säker fjärråtkomst till program från många olika användarenheter. Läs mer om [Azure RemoteApp](remoteapp-whatis.md).
* **Vad finns det för distributionsalternativ?** Det finns två typer av RemoteApp-samlingar: moln och hybrid. Vilken typ av samling du behöver beror på ett antal faktorer, till exempel om det krävs domänanslutning. Klicka [här](remoteapp-collections.md) för att se en diskussion om de olika alternativen.

## <a name="quick-tips-on-using-azure-remoteapp"></a>Snabba tips om att använda Azure RemoteApp
* **Hur lång tid tar det innan jag blir frånkopplad? Hur länge kan jag vara inaktiv innan jag loggas ut?** 4 timmar. Om du eller en av användarna är inaktiva i 4 timmar loggas du automatiskt ut från Azure RemoteApp. Läs om de andra standardinställningarna i artikeln om [gränser, kvoter och begränsningar i prenumerationer och tjänster i Azure](../azure-subscription-service-limits.md).
* **Kan jag prova på den här tjänsten gratis?** Ja. Det finns en kostnadsfri utvärderingsversion som gäller i 30 dagar. När utvärderingsversionen upphör kan du övergå till ett betalt konto (som du kan använda i produktionen) eller sluta använda tjänsten. Börja din kostnadsfria utvärderingsversion genom att gå till [portal.azure.com](http://portal.azure.com) och skapa en ny instans av RemoteApp. Med den kostnadsfria utvärderingsversionen kan du skapa 2 instanser av RemoteApp med 10 användare per instans. Kom ihåg att den här utvärderingsversionen bara gäller i 30 dagar.
  
  ## <a name="azure-remoteapp-subscription-details"></a>Information om Azure RemoteApp-prenumerationen
* **Vilka gränser har tjänsten?** Det finns mer info om standardinställningar och gränser i Azure RemoteApp i artikeln om [gränser, kvoter och begränsningar i prenumerationer och tjänster i Azure](../azure-subscription-service-limits.md). Kontakta oss om du har fler frågor.
* **Hur många användare måste jag ha?** Du måste ha minst 20 användare. För tydlighetens skull – det LÄGSTA ANTALET är 20 användare. Du kommer att debiteras för 20 användare. 
* **Hur mycket kostar RemoteApp?** Mer info finns på sidan med [Azure RemoteApp-prisinformation](https://azure.microsoft.com/pricing/details/remoteapp/).
* **Kostar en typ av samling mer än en annan?** Ja, det kan den göra, beroende på dina samlingskrav. En hybridsamling kräver en anslutning från Azure RemoteApp till ditt lokala nätverk. Om du använder ett befintligt VNET/ExpressRoute tillkommer ingen ytterligare avgift. Om du däremot använder ett nytt Azure VNET och antingen en gateway eller ExpressRoute debiteras du för [VPN-gatewayen](https://azure.microsoft.com/pricing/details/vpn-gateway) eller [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/). Den här avgiften (klicka på länkarna för mer info) läggs till den månatliga Azure RemoteApp-avgiften.

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>Samlingar – vad stöds, vilka bör du använda och annan info
* **Stöds anpassade LOB-program (Line-of-Business)?** Ja. Om du vill använda ett anpassat program i Azure RemoteApp, ska du skapa en [anpassad mallavbildning](remoteapp-create-custom-image.md) och sedan ladda upp den till RemoteApp-samlingen.
* **Fungerar mitt anpassade LOB-program i Azure RemoteApp?** Det bästa sättet att ta reda på det är att testa det. Läs mer i [RD Compatibility Center](http://www.rdcompatibility.com/compatibility/default.aspx).
* **Vilken distributionsmetod (moln eller hybrid) är bäst för vårt företag?** Hybridsamlingar ger de mest omfattande funktionerna om du vill ha full integration med enkel inloggning (SSO) och säkra nätverksanslutningsmöjligheter på plats. Molnsamlingar ger ett flexibelt och enkelt sätt att isolera distributionen genom att använda flera autentiseringsmetoder. Läs mer om [distributionsalternativen](remoteapp-whatis.md).
* **Vi har SQL-databaser eller andra databaser antingen på plats eller i Azure. Vilken distributionstyp ska vi använda?** Det beror på var SQL-databasen eller serverdelsdatabasen finns. Om databasen är i ett privat nätverk, ska ni använda hybridsamlingen. Om databasen är tillgänglig via internet och tillåter klientanslutningar kan ni använda molnsamlingen.
* **Hur är det med enhetsmappning, USB-portar och seriella portar, delning av Urklipp och omdirigering av skrivare?** Alla de här funktionerna stöds i Azure RemoteApp. Delning av Urklipp och omdirigering av skrivare aktiveras som standard. Läs mer om omdirigering [här](remoteapp-redirection.md). 

## <a name="template-images"></a>Mallavbildningar
* **Kan jag använda ett moln eller en befintlig virtuell dator som mall för RemoteApp-samlingen?** Visst! Du kan skapa en avbildning baserat på en Azure VM, använda en av avbildningarna som ingår i prenumerationen eller skapa en anpassad avbildning. Läs mer om [RemoteApp-avbildningsalternativen](remoteapp-imageoptions.md).

## <a name="network-options"></a>Nätverksalternativ
* **Det krävs ett virtuellt nätverk (VNET) för hybridsamlingen. Kan vi använda vårt befintliga VNET?** Det går bra om det befintliga virtuella nätverket är ett Azure VNET. Mer info finns i Steg 1 för att konfigurera ett virtuellt nätverk i [anvisningarna för hybridsamling](remoteapp-create-hybrid-deployment.md).
* **Kan jag använda ett VNET med en molnsamling?** Det går bra. Mer info finns i artikeln om att [skapa en molnsamling](remoteapp-create-cloud-deployment.md), speciellt i Steg 1.

## <a name="authentication-options"></a>Autentiseringsalternativ
* **Hur är det med autentisering? Vilka metoder stöds?** Molnsamlingen stöder Microsoft-konton och Azure Active Directory-konton, som även är Office 365-konton. Hybridsamlingen stöder enbart Azure Active Directory-konton som har synkroniserats (med ett verktyg som [Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) från en Windows Server Active Directory-distribution, och antingen synkroniserats med alternativet för lösenordssynkronisering eller med en konfigurerad AD FS-federation (Active Directory Federation Services). Du kan även konfigurera [Multi-Factor Authentication (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/).

> [!NOTE]
> Azure Active Directory-användare måste komma från den klient som är associerad med din prenumeration. (Du kan visa och ändra din prenumeration på **inställningsfliken** i portalen. Mer info finns i artikeln om att [ändra Azure Active Directory-klienten som används av RemoteApp](remoteapp-changetenant.md).)
> 
> 

* **Varför kan jag inte ge mitt Azure Active Directory-konto åtkomst?** Azure Active Directory-användarna måste vara från den katalog som är associerad med din prenumeration. Du kan visa eller ändra den katalogen på inställningsfliken i portalen. Mer info finns i artikeln om att [ändra Azure Active Directory-klienten som används av RemoteApp](remoteapp-changetenant.md).

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>Klienter – vilka enheter kan jag använda för att få åtkomst till Azure RemoteApp?
Det finns praktisk klientinformation, däribland anvisningar för att installera de olika klienterna, i artikeln om att [komma åt dina appar i Azure RemoteApp](remoteapp-clients.md).

* **Vilka enheter och operativsystem stöder klientprogrammen?**
  Datorer och surfplattor: 
  
  * Windows 10 (klientförhandsgranskning)
  * Windows 8.1 och Windows 8
  * Windows 7 Service Pack 1
  * Mac OS X
  * Windows RT
  * Android-surfplattor
  * iPad-enheter

    Och telefonerna:
  * iPhone
  * Android-telefoner
  * Windows Phone
    
    [Hämta](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) en RemoteApp-klient nu.
* **Stöder Azure RemoteApp tunna klienter?** Ja, följande tunna Windows Embedded-klienter stöds:
  
  * Windows Embedded Standard 7
  * Windows Embedded 8 Standard
  * Windows Embedded 8.1 Industry Pro
  * Windows 10 IoT Enterprise
* **Vilken Windows Server-version stöds för Värd för fjärrskrivbordssession?** Windows Server 2012 R2.

## <a name="support-and-feedback"></a>Support och feedback
* **Vad har RemoteApp för supportplan?** Support för fakturering och prenumerationer ges utan kostnad. Teknisk support är tillgänglig via [Azure-tjänstplaner](https://azure.microsoft.com/support/plans/). Du hittar även kostnadsfri communitysupport i [Azure-diskussionsforumet](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
* **Hur gör jag för att skicka feedback?** Gå till [feedbackforumet](https://feedback.azure.com/forums/247748-azure-remoteapp/).
* **Vem kan jag kontakta för att få mer info om Azure RemoteApp?** Förutom våra [diskussionsforum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), där det är enkelt att ställa frågor, kan du delta i vårt [webbseminarium där du kan fråga våra experter](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html) varje vecka. Där pratar vi om allt som har att göra med RemoteApp.
* **Finns det någon RemoteApp-dokumentation?** Bra att du frågade. Förutom hjälpinnehållet i portalen (klicka bara på **?** på valfri sida i portalen) kan du läsa följande artiklar för att lära dig mer om RemoteApp:
  
  * **Kom igång:**
    * [Vad är RemoteApp?](remoteapp-whatis.md)
    * [Vad finns i RemoteApp-mallavbildningarna?](remoteapp-images.md)
    * [Hur fungerar licensiering?](remoteapp-licensing.md)
    * [Hur fungerar RemoteApp och Office tillsammans?](remoteapp-o365.md)
    * [Hur fungerar omdirigering i RemoteApp](remoteapp-redirection.md)?
  * **Distribuera:**
    * [Skapa en anpassad mallavbildning](remoteapp-create-custom-image.md)
    * [Skapa en hybridsamling](remoteapp-create-hybrid-deployment.md)
    * [Skapa en molnsamling](remoteapp-create-cloud-deployment.md)
    * [Konfigurera Azure Active Directory för RemoteApp](remoteapp-ad.md)
    * [Publicera en app i RemoteApp](remoteapp-publish.md)
  * **Hantera:**
    
    * [Lägga till användare](remoteapp-user.md)
    * [Tips för att konfigurera och använda RemoteApp](remoteapp-bestpractices.md)    
    
    Videor! Det finns även ett antal videor om RemoteApp. Vissa innehåller en introduktion ([Introduction to Azure RemoteApp](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) medan andra går igenom distribution ([Cloud deployment](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) och [Hybrid deployment](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Kolla in dem!

### <a name="help-us-help-you"></a>Vi vill bli bättre på att hjälpa dig
Visste du att du förutom att betygsätta den här artikeln och skriva kommentarer nedan även kan göra ändringar i själva artikeln? Saknar du något i artikeln? Är det något som är fel? Är det något i artikeln som gör dig förvirrad? Då kan du gå tillbaka till början av sidan och klicka på **Redigera på GitHub** och ändra i texten. Ändringarna skickas till oss och när vi har godkänt dem kommer du att kunna se dina ändringar och förbättringar här.




<!--HONumber=Jan17_HO4-->


