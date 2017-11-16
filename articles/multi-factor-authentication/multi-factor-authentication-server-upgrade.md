---
title: Uppgraderingen av Azure MFA-Server | Microsoft Docs
description: "Steg och vägledning för att uppgradera Azure Multi-Factor Authentication-Server till en nyare version."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: a56078fcd61114d0ca78e2dd00445c988a8a3c6c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Uppgradera till den senaste Azure Multi-Factor Authentication-servern

Den här artikeln vägleder dig genom processen med att uppgradera Azure Multi-Factor Authentication (MFA) Server v6.0 eller högre. Om du behöver uppgradera en gammal version av PhoneFactor Agent finns [uppgradera PhoneFactor-agenten till Azure-flerfunktionsautentisering](multi-factor-authentication-get-started-server-upgrade.md).

Om du uppgraderar från v6.x eller äldre till v7.x eller senare, ändra alla komponenter från .NET 2.0 till .NET 4.5. Alla komponenter måste också Microsoft Visual C++ 2015 Redistributable Update 1 eller högre. MFA-Server-installationsprogrammet installerar x86 och x64 versioner av dessa komponenter om de inte redan har installerats. Om Användarportalen och Mobilappwebbtjänsten körs på separata servrar, måste du installera dessa paket innan du uppgraderar komponenterna. Du kan söka efter den senaste uppdateringen för Microsoft Visual C++ 2015 Redistributable på den [Microsoft Download Center](https://www.microsoft.com/en-us/download/). 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>Installera den senaste versionen av Azure MFA-Server

1. Följ instruktionerna i [hämta Azure Multi-Factor Authentication-servern](multi-factor-authentication-get-started-server.md#download-the-mfa-server) att hämta den senaste versionen av Azure MFA-Server.
2. Gör en säkerhetskopia av filen MFA-servern data finns i C:\Program program\multi-Factor Authentication Server\Data\PhoneFactor.pfdata (förutsatt att installera standardplatsen) på din MFA-huvudservern.
3. Om du kör flera servrar för hög tillgänglighet, ändra klientsystem som autentiseras till MFA-servern så att de slutar skickar trafik till de servrar som uppgraderas. Om du använder en belastningsutjämnare, ta bort en MFA-Server från belastningsutjämnaren, uppgraderingen och Lägg sedan till servern i servergruppen.
4. Kör installationsprogrammet för nya på varje MFA-Server. Uppgradera först underordnade servrar eftersom de kan läsa den gamla datafilen replikeras av hanteraren. 

  Du behöver inte avinstallera den aktuella MFA-servern innan du kör installationsprogrammet. Installationsprogrammet utför en uppgradering på plats. Installationssökvägen hämtas från registret från den tidigare installationen, så installeras på samma plats (till exempel C:\Program program\multi-Factor Authentication-servern). 
  
5. Acceptera uppmaningen om du uppmanas att installera Microsoft Visual C++ 2015 Redistributable uppdateringspaketet. X86 och x64 versioner av paketet installeras.
5. Om du använder SDK för webbtjänst, uppmanas du att installera ny webbtjänst-SDK. När du installerar nya webbtjänst-SDK måste du kontrollera att namnet på virtuella katalogen matchar tidigare installerade virtuell katalog (till exempel MultiFactorAuthWebServiceSdk).
6. Upprepa steg på alla underordnade servrar. Uppgradera en av underordnade ska vara den nya hanteraren och sedan uppgradera den gamla huvudservern. 

## <a name="upgrade-the-user-portal"></a>Uppgradera Användarportalen

1. Skapa en säkerhetskopia av filen Web.config i den virtuella katalogen för Användarportalen installationsplatsen (till exempel C:\inetpub\wwwroot\MultiFactorAuth). Om ändringar som gjordes i standardtemat, skapa en säkerhetskopia av mappen App_Themes\Default. Det är bättre att skapa en kopia av standardmappen och skapa ett nytt tema än om du vill ändra standardtemat.
2. Om Användarportalen körs på samma server som de andra komponenterna i MFA-Server, uppmanas du att uppdatera Användarportalen MFA-Server-installationen. Acceptera uppmaningen och installera uppdateringen för Användarportalen. Kontrollera att namnet på virtuella katalogen matchar tidigare installerade virtuell katalog (till exempel MultiFactorAuth).
3. Om Användarportalen finns på en egen server, kopiera filen MultiFactorAuthenticationUserPortalSetup64.msi från installationsplatsen för en av MFA-servrar och placera den på webbservern för Användarportalen. Kör installationsprogrammet. 

  Om ett fel inträffar anger ”Microsoft Visual C++ 2015 Redistributable uppdatering 1 eller senare krävs”, hämta och installera det senaste uppdateringspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/). Installera både x86 och x64 versioner.

4. Jämför web.config säkerhetskopiorna i steg 1 med den nya web.config-filen när den uppdaterade Användarportalen programvaran har installerats. Om det finns inga nya attribut i den nya web.config, kopiera filen web.config för säkerhetskopiering till den virtuella katalogen för att skriva över en ny. Ett annat alternativ är att kopiera och klistra in appSettings värdena och URL för webbtjänsten SDK från säkerhetskopian till den nya web.config.

Om du har Användarportalen på flera servrar, Upprepa installationen på dem alla. 


## <a name="upgrade-the-mobile-app-web-service"></a>Uppgradera Mobilappwebbtjänsten

1. Skapa en säkerhetskopia av filen Web.config i den virtuella katalogen för Mobilappwebbtjänsten installationsplatsen (till exempel C:\inetpub\wwwroot\app eller C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService).
2. Kopiera filen MultiFactorAuthenticationMobileAppWebServiceSetup64.msi från installationsplatsen för MFA-servrar och placera den på webbservern för Mobilapp registrering.
3. Kör installationsprogrammet. 

  Om det uppstår ett fel som anger att Microsoft Visual C++ 2015 Redistributable uppdatering 1 eller senare krävs, hämta och installera det senaste uppdateringspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/). Installera både x86 och x64 versioner.

4. Jämför web.config-filen som har säkerhetskopierats i steg 1 med den nya web.config-filen när den uppdaterade Mobilappwebbtjänsten programvaran har installerats. Om det finns inga nya attribut i den nya web.config, kan du kopiera dina sparade web.config tillbaka till den virtuella katalogen och skriva över den nya servern. Ett annat alternativ är att kopiera och klistra in appSettings värdena och URL för webbtjänsten SDK från säkerhetskopian till den nya web.config.

Om du har den Mobilappwebbtjänsten på flera servrar kan du upprepa installationen på dem alla. 

## <a name="upgrade-the-ad-fs-adapters"></a>Uppgradera AD FS-kort


### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Om MFA körs på olika servrar än AD FS

Dessa anvisningar gäller endast om du kör servern för flerfunktionsautentisering separat från AD FS-servrarna. Om båda tjänsterna körs på samma servrar, hoppa över det här avsnittet och gå till följande steg. 

1. Spara en kopia av filen multifactorauthenticationadfsadapter.config som har registrerats i AD FS eller exportera konfigurationen med följande PowerShell-kommando: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Att kortets namn är ”WindowsAzureMultiFactorAuthentication” eller ”AzureMfaServerAuthentication” beroende på vilken version som installerats tidigare.
2. Kopiera följande filer från installationsplatsen för MFA-servern till AD FS-servrarna:

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Redigera skriptet Register-MultiFactorAuthenticationAdfsAdapter.ps1 genom att lägga till `-ConfigurationFilePath [path]` till slutet av den `Register-AdfsAuthenticationProvider` kommando. Ersätt *[sökväg]* med den fullständiga sökvägen till MultiFactorAuthenticationAdfsAdapter.config exporterade filen eller konfigurationsfilen i föregående steg. 

  Kontrollera attributen i den nya MultiFactorAuthenticationAdfsAdapter.config att se om de matchar den gamla config-fil. Om några attribut har lagts till eller tas bort i den nya versionen, kopiera attributvärden från den gamla konfigurationsfilen till en ny eller ändra gammal konfigurationsfil att matcha.

### <a name="install-new-ad-fs-adapters"></a>Installera nya AD FS-kort

> [!IMPORTANT] 
> Användarna uppmanas inte att utföra tvåstegsverifiering under steg 3 – 8 av det här avsnittet. Om du har AD FS som konfigurerats i flera kluster kan du ta bort, uppgradera och återställa varje kluster i servergruppen oberoende av andra kluster att undvika driftsavbrott.

1. Ta bort vissa AD FS-servrar från gruppen. Uppdatera servrarna medan de andra fortfarande körs.
2. Installera den nya AD FS-adaptern på varje server som tas bort från AD FS-servergruppen. Om MFA-servern är installerad på varje AD FS-servern, kan du uppdatera via MFA serveradministratören UX. Uppdatera annars genom att köra sedan filerna MultiFactorAuthenticationAdfsAdapterSetup64.msi. 

  Om ett fel inträffar anger ”Microsoft Visual C++ 2015 Redistributable uppdatering 1 eller senare krävs”, hämta och installera det senaste uppdateringspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/). Installera både x86 och x64 versioner.

3. Gå till **AD FS** > **autentiseringsprinciper** > **redigera globala flerfunktionsautentisering princip**. Avmarkera **WindowsAzureMultiFactorAuthentication** eller **AzureMFAServerAuthentication** (beroende på den aktuella installerade versionen). 

  När det här steget har slutförts är tvåstegsverifiering via MFA-servern inte tillgänglig i det här AD FS-klustret förrän du har slutfört steg 8.

4. Avregistrera den äldre versionen av AD FS-adaptern genom att köra Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-skript. Se till att den *-namnet* parameter (”WindowsAzureMultiFactorAuthentication” eller ”AzureMFAServerAuthentication”) som matchar namnet som visas i steg 3. Detta gäller för alla servrar i samma AD FS-kluster eftersom det inte finns en central konfiguration.
5. Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-skript för att registrera nya AD FS-adaptern. Detta gäller för alla servrar i samma AD FS-kluster eftersom det inte finns en central konfiguration.
6. Starta om AD FS-tjänsten på varje server som tas bort från AD FS-servergruppen.
7. Lägga till uppdaterade servrar i AD FS-servergruppen och ta bort de andra servrarna från servergruppen.
8. Gå till **AD FS** > **autentiseringsprinciper** > **redigera globala flerfunktionsautentisering princip**. Kontrollera **AzureMfaServerAuthentication**.
9. Upprepa steg 2 för att uppdatera de servrar som har nu tagits bort från AD FS-servergruppen och starta om AD FS-tjänsten på dessa servrar.
10. Lägga till dessa servrar i AD FS-servergruppen igen.

## <a name="next-steps"></a>Nästa steg

- Exempel på [avancerade scenarier med Azure Multi-Factor Authentication och tredje parts VPN-anslutningar](multi-factor-authentication-advanced-vpn-configurations.md)

- [Synkronisera MFA-Server med Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)

- [Konfigurera Windows-autentisering](multi-factor-authentication-get-started-server-windows.md) för dina program
