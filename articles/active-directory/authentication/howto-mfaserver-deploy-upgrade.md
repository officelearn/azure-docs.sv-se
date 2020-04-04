---
title: Uppgradera Azure MFA Server – Azure Active Directory
description: Steg och vägledning för att uppgradera Azure Multi-Factor Authentication Server till en nyare version.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f242b4a7e984ceeb183547cb3a949927f3c91da
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653104"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Uppgradera till den senaste versionen av Azure Multi-Factor Authentication Server

I den här artikeln får du information om hur du uppgraderar Azure Multi-Factor Authentication (MFA) Server v6.0 eller senare. Om du behöver uppgradera en gammal version av PhoneFactor Agent läser du [Uppgradera PhoneFactor Agent till Azure Multi-Factor Authentication Server](howto-mfaserver-deploy-upgrade-pf.md).

Om du uppgraderar från v6.x eller äldre till v7.x eller nyare ändras alla komponenter från .NET 2.0 till .NET 4.5. Alla komponenter kräver också Microsoft Visual C++ 2015 Redistributable Update 1 eller senare. Installationsprogrammet för MFA Server installerar både x86- och x64-versionerna av dessa komponenter om de inte redan är installerade. Om webbtjänsten Användarportal och Mobilapp körs på separata servrar måste du installera dessa paket innan du uppgraderar dessa komponenter. Du kan söka efter den senaste microsoft Visual C++ 2015 Redistributable-uppdateringen på [Microsoft Download Center](https://www.microsoft.com/download/). 

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare bör använda molnbaserad Azure Multi-Factor-autentisering. Befintliga kunder som har aktiverat MFA Server före den 1 juli kommer att kunna ladda ner den senaste versionen, framtida uppdateringar och generera aktiveringsautentiseringsuppgifter som vanligt.

Uppgradera steg i korthet:

* Uppgradera Azure MFA-servrar (underordnade sedan-hanterare)
* Uppgradera användarportalinstanserna
* Uppgradera AD FS-kortinstanserna

## <a name="upgrade-azure-mfa-server"></a>Uppgradera Azure MFA-server

1. Använd instruktionerna i [Hämta Azure Multi-Factor Authentication Server](howto-mfaserver-deploy.md#download-the-mfa-server) för att få den senaste versionen av Azure MFA Server-installationsprogrammet.
2. Gör en säkerhetskopia av MFA Server-datafilen på C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (förutsatt att standardinstallationsplatsen) finns på huvud-MFA-servern.
3. Om du kör flera servrar med hög tillgänglighet ändrar du de klientsystem som autentiserar till MFA-servern så att de slutar skicka trafik till servrarna som uppgraderar. Om du använder en belastningsutjämnare tar du bort en underordnad MFA-server från belastningsutjämnaren, gör uppgraderingen och lägger sedan till servern i servergruppen igen.
4. Kör det nya installationsprogrammet på varje MFA-server. Uppgradera underordnade servrar först eftersom de kan läsa den gamla datafilen som replikeras av huvudservern.

   > [!NOTE]
   > När du uppgraderar en server bör den tas bort från alla belastningsbalanseringar eller trafikdelning med andra MFA-servrar.
   >
   > Du behöver inte avinstallera den aktuella MFA-servern innan du kör installationsprogrammet. Installationsprogrammet utför en uppgradering på plats. Installationssökvägen hämtas från registret från den föregående installationen, så den installeras på samma plats (till exempel C:\Program-filer\Multifaktorautentiseringsserver).
  
5. Om du uppmanas att installera ett Uppdateringspaket för Microsoft Visual C++ 2015 Redistributable godkänner du uppmaningen. Både x86- och x64-versionerna av paketet är installerade.
6. Om du använder Webbtjänst SDK uppmanas du att installera den nya webbtjänsten SDK. När du installerar den nya webbtjänsten SDK kontrollerar du att det virtuella katalognamnet matchar den tidigare installerade virtuella katalogen (till exempel MultiFactorAuthWebServiceSdk).
7. Upprepa stegen på alla underordnade servrar. Befordra en av underordnade att vara den nya huvudservern och uppgradera sedan den gamla huvudservern.

## <a name="upgrade-the-user-portal"></a>Uppgradera användarportalen

Slutför uppgraderingen av MFA-servrarna innan du flyttar till det här avsnittet.

1. Gör en säkerhetskopia av filen web.config som finns i den virtuella katalogen på installationsplatsen för Användarportalen (till exempel C:\inetpub\wwwroot\MultiFactorAuth). Om några ändringar har gjorts i standardtemat gör du även en säkerhetskopia av mappen App_Themes\Standard. Det är bättre att skapa en kopia av standardmappen och skapa ett nytt tema än att ändra standardtemat.
2. Om Användarportalen körs på samma server som de andra MFA-serverkomponenterna uppmanas du att uppdatera användarportalen. Acceptera prompten och installera uppdateringen av Användarportalen. Kontrollera att det virtuella katalognamnet matchar den tidigare installerade virtuella katalogen (till exempel MultiFactorAuth).
3. Om användarportalen finns på en egen server kopierar du filen MultiFactorAuthenticationUserPortalSetup64.msi från installationsplatsen för en av MFA-servrarna och placerar den på användarportalens webbserver. Kör installationsprogrammet.

   Om ett fel uppstår med texten "Microsoft Visual C++ 2015 Redistributable Update 1 or higher is required", laddar du ned och installerar det senaste uppdateringspaketet från [Microsoft Download Center](https://www.microsoft.com/download/). Installera både x86- och x64-versionerna.

4. När den uppdaterade User Portal-programvaran har installerats jämför du den web.config-säkerhetskopia du gjorde i steg 1 med den nya filen web.config. Om det inte finns några nya attribut i den nya web.config kopierar du säkerhetskopian web.config till den virtuella katalogen för att skriva över den nya. Ett annat alternativ är att kopiera/klistra in värdena för appSettings och webbtjänstenSDK-URL:en från säkerhetskopian till den nya web.config.

Om du har användarportalen på flera servrar upprepar du installationen på dem alla.

## <a name="upgrade-the-mobile-app-web-service"></a>Uppgradera webbtjänsten för mobilappen

> [!NOTE]
> Vid uppgradering från en version av Azure MFA Server som är äldre än 8.0 till 8.0+ kan mobilappwebbtjänsten avinstalleras efter uppgraderingen

## <a name="upgrade-the-ad-fs-adapters"></a>Uppgradera AD FS-korten

Slutför uppgraderingen av dina MFA-servrar och användarportal innan du flyttar till det här avsnittet.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Om MFA körs på andra servrar än AD FS

Dessa instruktioner gäller endast om du kör Multifaktor authentication Server separat från AD FS-servrarna. Om båda tjänsterna körs på samma servrar hoppar du över det här avsnittet och går till installationsstegen. 

1. Spara en kopia av filen MultiFactorAuthenticationAdfsAdapter.config som registrerades i AD FS eller `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`exportera konfigurationen med följande PowerShell-kommando: . Kortets namn är antingen "WindowsAzureMultiFactorAuthentication" eller "AzureMfaServerAuthentication" beroende på vilken version som tidigare installerats.
2. Kopiera följande filer från installationsplatsen för MFA Server till AD FS-servrarna:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Redigera skriptet Register-MultiFactorAuthenticationAdfsAdapter.ps1 `-ConfigurationFilePath [path]` genom att `Register-AdfsAuthenticationProvider` lägga till i slutet av kommandot. Ersätt *[sökvägen]* med den fullständiga sökvägen till multifaktorauthenticationAdfsAdapter.config-filen eller konfigurationsfilen som exporterades i föregående steg.

   Kontrollera attributen i den nya MultiFactorAuthenticationAdfsAdapter.config för att se om de matchar den gamla config-filen. Om några attribut har lagts till eller tagits bort i den nya versionen kopierar du attributvärdena från den gamla konfigurationsfilen till den nya eller ändrar den gamla konfigurationsfilen så att den matchar.

### <a name="install-new-ad-fs-adapters"></a>Installera nya AD FS-kort

> [!IMPORTANT]
> Användarna behöver inte utföra tvåstegsverifiering under steg 3-8 i det här avsnittet. Om du har KONFIGURERAT AD FS i flera kluster kan du ta bort, uppgradera och återställa varje kluster i servergruppen oberoende av de andra klustren för att undvika driftstopp.

1. Ta bort några AD FS-servrar från servergruppen. Uppdatera dessa servrar medan de andra fortfarande körs.
2. Installera det nya AD FS-kortet på varje server som tagits bort från AD FS-servergruppen. Om MFA-servern är installerad på varje AD FS-server kan du uppdatera via MFA Server admin UX. Annars uppdatera genom att köra MultiFactorAuthenticationAdfsAdapterSetup64.msi.

   Om ett fel uppstår med texten "Microsoft Visual C++ 2015 Redistributable Update 1 or higher is required", laddar du ned och installerar det senaste uppdateringspaketet från [Microsoft Download Center](https://www.microsoft.com/download/). Installera både x86- och x64-versionerna.

3. Gå till **AD FS-autentiseringsprinciper** > **Authentication Policies** > **Redigera globala multifaktorautentiseringsprincip**. Avmarkera **WindowsAzureMultiFactorAuthentication** eller **AzureMFAServerAuthentication** (beroende på den aktuella versionen installerad).

   När det här steget är klart är tvåstegsverifiering via MFA Server inte tillgänglig i det här AD FS-klustret förrän du har slutfört steg 8.

4. Avregistrera den äldre versionen av AD FS-kortet genom att köra PowerShell-skriptet Unregister-MultiFactorAuthenticationAdfsAdapter.ps1. Kontrollera att *parametern -Name* (antingen "WindowsAzureMultiFactorAuthentication" eller "AzureMFAServerAuthentication") matchar namnet som visades i steg 3. Detta gäller alla servrar i samma AD FS-kluster eftersom det finns en central konfiguration.
5. Registrera det nya AD FS-kortet genom att köra skriptet Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell. Detta gäller alla servrar i samma AD FS-kluster eftersom det finns en central konfiguration.
6. Starta om AD FS-tjänsten på varje server som tagits bort från AD FS-servergruppen.
7. Lägg till de uppdaterade servrarna tillbaka till AD FS-servergruppen och ta bort de andra servrarna från servergruppen.
8. Gå till **AD FS-autentiseringsprinciper** > **Authentication Policies** > **Redigera globala multifaktorautentiseringsprincip**. Kontrollera **AzureMfaServerAuthentication**.
9. Upprepa steg 2 för att uppdatera servrarna som nu har tagits bort från AD FS-servergruppen och starta om AD FS-tjänsten på dessa servrar.
10. Lägg till dessa servrar tillbaka till AD FS-servergruppen.

## <a name="next-steps"></a>Nästa steg

* Få exempel på [avancerade scenarier med Azure Multi-Factor Authentication och vpn från tredje part](howto-mfaserver-nps-vpn.md)

* [Synkronisera MFA-server med Active Directory för Windows Server](howto-mfaserver-dir-ad.md)

* [Konfigurera Windows-autentisering](howto-mfaserver-windows.md) för dina program
