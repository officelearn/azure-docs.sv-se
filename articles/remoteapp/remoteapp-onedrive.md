<properties
   pageTitle="Så integrerar du OneDrive för företag och Azure RemoteApp | Microsoft Azure"
   description="Lär dig att använda OneDrive för företag med Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="03/31/2016"
   ms.author="elizapo"/>

# Så integrerar du OneDrive för företag och Azure RemoteApp

Du kan använda OneDrive för företag som filens centrallager med Azure RemoteApp. OneDrive för företag är ett bra sätt att hålla filerna synkroniserade på alla enheter och arbetsytor.  [Användarprofildisken](remoteapp-upd.md) (UPD) är en plats där användare kan lagra filer för Azure RemoteApp-appar, men de här filerna är bara tillgängliga via Azure RemoteApp. OneDrive för företag, å andra sidan, gör att användarna kan komma åt filer var och när de vill utan att behöva gå igenom Azure RemoteApp. Den här artikeln beskriver de OneDrive för företag-versioner som stöds och på vilka sätt administratörer kan ställa in OneDrive för företag för Azure RemoteApp.

## Stöds alla versioner av OneDrive?

Det finns två versioner av OneDrive: OneDrive och OneDrive för företag. På Azure RemoteApp stöds endast OneDrive för företag. Personliga OneDrive fungerar men stöds inte officiellt. Dessutom stöds bara den senaste versionen av OneDrive för företag (även kallad nästa generations synkroniseringsklient) på Azure RemoteApp (och RDSH-/Citrix-/terminalservrar).

>[AZURE.NOTE]  OneDrive (för konsumenter/Personal edition) stöds inte på Azure RemoteApp. Det är inte heller alla versioner av OneDrive för företag som stöds eftersom de inte är certifierade för att fungera på Windows Server. Även om både den nya klienten (nästa generations synkroniseringsklient) och äldre Groove-versioner kan tyckas fungera på Azure RemoteApp enligt beskrivningen i [https://support.microsoft.com/en-us/kb/2965687](https://support.microsoft.com/kb/2965687), har de äldre synkroniseringsmotorerna inte alla funktioner på Citrix-/terminalservrar (Windows Server).

## Vilka olika inställningsalternativ finns det för OneDrive för företag?

- **Traditionell inställning av synkroniseringsmotorn till OneDrive för företag:** Det här alternativet stöds inte för närvarande på distributioner av Azure RemoteApp, RDSH och Citrix.
- **”Virtualisera” OneDrive för företag/omdirigera det från den lokala tjocka klienten till sessionen:** Om du synkroniserar OneDrive till en mapp på klientenheten kan du, under en enhet, [omdirigera](remoteapp-redirection.md) den enheten till Azure RemoteApp. Den enheten ska vara samma för alla användares klienter och de ska ha OneDrive synkroniserat till en mapp under den här enheten. Om de använder RemoteApp från en annan klient kan det hända att dessa filer inte är tillgängliga (lösning – de har alltid åtkomst till sina filer med online-versionen av OneDrive). 
- **Visa OneDrive för företag som en enhet i Azure RemoteApp-miljön utan att cachelagra/synkronisera filerna:** (mappa en HTTP-URL för OneDrive för företag till en enhet på den virtuella datorn) mappning av OneDrive för företag till nätverksenheten i RDSH-miljön stöds. Detta kan användas i följande situationer: 
    - När tunna klienter (ingen lokal lagring) används för åtkomst till Azure RemoteApp – programmet kräver att filer lagras på OneDrive för företag, men vill att de ska ”se lokala ut” och administratören vill inte synkronisera filerna till en virtuell dator.
    - När det finns många stora filer på OneDrive för företag som har valts för synkronisering. Med tanke på hur mycket arbete det krävs för synkronisering kanske inte alla filer synkroniseras när användaren vill använda dem. Dessutom kan filerna inte lagras i UPD (User Datagram Protocol, användarens datagramprotokoll) om de totalt är större än 50 GB.

I situationerna ovan kan administratörerna välja att mappa en enhet på den virtuella datorn till användarens HTTP-URL för OneDrive för företag. Aktivera det på något av följande sätt:

- Ha binärfiler av bilden och aktivera inte synkroniseringsmotorn för OneDrive för företag.
- Ha INGA binärfiler av bilden – det här kommer med ett krav – Desktop Experience Pack. Mer specifikt måste WebClient-tjänsten (även kallat WebDAV) installeras som en del av Desktop Experience Pack. 

### Installera Desktop Experience Pack på Windows Server 2012 R2
Så här installerar du Desktop Experience Pack: 

1. I Serverhanteraren klickar du på **Hantera -> Lägg till roller och funktioner**.
2. Klicka på **Funktioner** och sedan **Användargränssnitt och infrastruktur -> Skrivbordsmiljö**.

### Mappa en enhet till URL:en för OneDrive för företag

Följ instruktionerna i supportartikeln: [https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712)
 
Ett viktigt steg i installationen är att se till att du väljer **Jag vill förbli inloggad**.

Här följer instruktionerna på en hög nivå:

1.  Leta reda på URL:en för enheten. Den URL som används för enhetsmappningen är den du får när du bläddrar till arbetskatalogen i OneDrive för företag online. Till exempel:
 
    https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.  Öppna URL:en med en webbläsare i RemoteApp-sessionen och välj **Jag vill förbli inloggad** innan du loggar in på URL:en för ditt konto.
3.  Öppna Utforskaren och mappa en enhet till URL:en ovan. Om URL:en inte har lösts kan du använda en kort form:
    
    https://microsoft-my.sharepoint.com/personal/alias_microsoft_com. 

    Detta skapar omedelbart den mappade enheten – den ser ut så här:
 
    ![OneDrive för företag som en mappad nätverksenhet](./media/remoteapp-onedrive/ra-mappeddrive.png)


<!--HONumber=Jun16_HO2-->


