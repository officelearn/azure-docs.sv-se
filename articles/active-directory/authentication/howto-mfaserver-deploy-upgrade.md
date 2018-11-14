---
title: Azure MFA Server-uppgradering
description: Anvisningar och riktlinjer för att uppgradera Azure Multi-Factor Authentication-servern till en nyare version.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: e0f3ec3ffa541e028835b0415201521c67eb7efa
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613825"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Uppgradera till den senaste Azure Multi-Factor Authentication Server

Den här artikeln vägleder dig genom uppgraderingsprocessen Azure Multi-Factor Authentication (MFA) Server version 6.0 eller senare. Om du vill uppgradera en äldre version av PhoneFactor Agent kan se [uppgradera PhoneFactor Agent till Azure Multi-Factor Authentication Server](howto-mfaserver-deploy-upgrade-pf.md).

Om du uppgraderar från version 6.x eller senare till v7.x eller senare, ändra alla komponenter från .NET 2.0 till .NET 4.5. Alla komponenter kräver även Microsoft Visual C++ 2015 Redistributable uppdatering 1 eller senare. MFA Server-installationsprogrammet installerar x86 och x64 versioner av dessa komponenter om de inte redan är installerade. Om Användarportalen och webbtjänsten Mobile App körs på separata servrar, måste du installera dessa paket innan du uppgraderar komponenterna. Du kan söka efter den senaste uppdateringen av Microsoft Visual C++ 2015 Redistributable på den [Microsoft Download Center](https://www.microsoft.com/download/). 

Uppgraderingssteg i korthet:

* Uppgradera Azure MFA-servrar (underordnade sedan Master)
* Uppgradera Användarportalen instanser
* Uppgradera AD FS-Adapter-instanser

## <a name="upgrade-azure-mfa-server"></a>Uppgradera Azure MFA Server

1. Följ instruktionerna i [ladda ned Azure Multi-Factor Authentication Server](howto-mfaserver-deploy.md#download-the-mfa-server) att hämta den senaste versionen av installationsprogrammet för Azure MFA-servern.
2. Skapa en säkerhetskopia av filen MFA Server-data finns i C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (förutsatt att standardplatsen för installation) på din MFA-huvudserver.
3. Om du kör flera servrar för hög tillgänglighet kan du ändra klientsystem som autentiseras till MFA-servern så att de stoppas skickar trafik till de servrar som uppgraderar. Om du använder en belastningsutjämnare, ta bort en underordnad MFA-Server från belastningsutjämnaren, genomföra uppgraderingen och Lägg sedan till servern i servergruppen.
4. Kör det nya installationsprogrammet på varje MFA-Server. Uppgradera underordnade servrar först eftersom de kan läsa datafilen gamla som replikeras av huvudservern.

   > [!NOTE]
   > När du uppgraderar en server bör tas bort från alla loadbalancing och dela med andra servrar i MFA-trafik.
   >
   > Du behöver inte avinstallera din aktuella MFA-servern innan du kör installationsprogrammet. Installationsprogrammet utför en uppgradering på plats. Installationssökvägen hämtas från registret från den tidigare installationen, så installeras på samma plats (t.ex, c:\Program\Microsoft Files\Multi-Factor Authentication Server).
  
5. Om du uppmanas att installera Microsoft Visual C++ 2015 Redistributable uppdateringspaketet acceptera uppmaningen. X86 och x64 versioner av paketet installeras.
6. Om du använder SDK för webbtjänst kan uppmanas du att installera nya webbtjänst-SDK. När du installerar nya webbtjänst-SDK kan du se till att namnet på virtuella katalogen matchar den tidigare installerade virtuell katalogen (till exempel MultiFactorAuthWebServiceSdk).
7. Upprepa steg på alla underordnade servrar. Uppgradera en av underordnade ska vara den nya huvudservern och uppgradera sedan den gamla huvudservern.

## <a name="upgrade-the-user-portal"></a>Uppgradera Användarportalen

Slutför uppgraderingen av MFA-servrar innan du går till det här avsnittet.

1. Skapa en säkerhetskopia av filen web.config i den virtuella katalogen för installationsplatsen för Användarportalen (t.ex, C:\inetpub\wwwroot\MultiFactorAuth). Om ändringar gjordes i tema, skapa en säkerhetskopia av mappen App_Themes\Default. Det är bättre att skapa en kopia av standardmappen och skapa ett nytt tema än om du vill ändra tema.
2. Om Användarportalen körs på samma server som de andra MFA Server-komponenterna, MFA Server-installationen en uppmaning om att uppdatera Användarportalen. Acceptera uppmaningen och installera uppdateringen för Användarportalen. Kontrollera att namnet på virtuella katalogen matchar den tidigare installerade virtuell katalogen (till exempel MultiFactorAuth).
3. Om Användarportalen finns på en egen server, kopiera filen MultiFactorAuthenticationUserPortalSetup64.msi från installationsplatsen för en av MFA-servrar och placera den på webbservern för Användarportalen. Kör installationsprogrammet.

   Om ett fel inträffar ange ”Microsoft Visual C++ 2015 Redistributable uppdatering 1 eller senare krävs”, hämta och installera det senaste uppdateringspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/). Installera båda x86 och x64 versioner.

4. Jämför web.config säkerhetskopiorna i steg 1 med den nya web.config-filen när den uppdaterade Användarportalen programvaran har installerats. Om det finns inga nya attribut i den nya web.config, kopierar du din säkerhetskopiering web.config i den virtuella katalogen för att skriva över den nya servern. Ett annat alternativ är att kopiera och klistra in appSettings värdena och webbtjänstens SDK URL från säkerhetskopian i den nya web.config.

Om du har Användarportalen på flera servrar kan du upprepa installationen på dem alla.

## <a name="upgrade-the-mobile-app-web-service"></a>Uppgradera webbtjänsten för Mobile App

> [!NOTE]
> Vid uppgradering från en version Azure MFA Server som är äldre än 8.0 till 8.0 eller senare kan webbtjänsten för mobila appar avinstalleras efter uppgraderingen

## <a name="upgrade-the-ad-fs-adapters"></a>Uppgradera AD FS-kort

Slutför uppgraderingen av dina servrar för MFA och Användarportalen innan du går till det här avsnittet.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Om MFA körs på olika servrar än AD FS

Dessa anvisningar gäller endast om du kör Multi-Factor Authentication Server separat från AD FS-servrarna. Om båda tjänsterna köras på samma servrar, hoppa över det här avsnittet och gå till installationsstegen. 

1. Spara en kopia av filen multifactorauthenticationadfsadapter.config som har registrerats i AD FS eller exportera konfigurationen med följande PowerShell-kommando: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Kortets namn är ”WindowsAzureMultiFactorAuthentication” eller ”AzureMfaServerAuthentication” beroende på vilken version som tidigare har installerat.
2. Kopiera följande filer från MFA Server-installationsplatsen till AD FS-servrar:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Redigera skriptet Register-MultiFactorAuthenticationAdfsAdapter.ps1 genom att lägga till `-ConfigurationFilePath [path]` i slutet av den `Register-AdfsAuthenticationProvider` kommando. Ersätt *[sökväg]* med den fullständiga sökvägen till MultiFactorAuthenticationAdfsAdapter.config fil eller konfigurationsfilen exporterade i föregående steg.

   Kontrollera attributen i den nya MultiFactorAuthenticationAdfsAdapter.config att se om de matchar den gamla config-filen. Om några attribut lades till eller tas bort i den nya versionen, kopiera attributvärdena från den gamla konfigurationsfilen till den nya eller ändra gamla konfigurationsfilen så att de matchar.

### <a name="install-new-ad-fs-adapters"></a>Installera nya AD FS-adaptrar

> [!IMPORTANT]
> Användarna kommer inte att behöva utföra tvåstegsverifiering under steg 3 – 8 av det här avsnittet. Om du har AD FS som konfigurerats i flera kluster kan du ta bort, uppgradera och återställa varje kluster i servergruppen oberoende av andra kluster att undvika driftstopp.

1. Ta bort vissa AD FS-servrar från servergruppen. Uppdatera dessa servrar medan de andra fortfarande körs.
2. Installera den nya AD FS-adaptern på varje server som tas bort från AD FS-servergrupp. Om MFA-servern är installerad på varje AD FS-servern, kan du uppdatera via MFA-serveradministratören UX. I annat fall uppdatera genom att köra MultiFactorAuthenticationAdfsAdapterSetup64.msi.

   Om ett fel inträffar ange ”Microsoft Visual C++ 2015 Redistributable uppdatering 1 eller senare krävs”, hämta och installera det senaste uppdateringspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/). Installera båda x86 och x64 versioner.

3. Gå till **AD FS** > **autentiseringsprinciper** > **redigera globala flerfunktionsautentisering princip**. Avmarkera **WindowsAzureMultiFactorAuthentication** eller **AzureMFAServerAuthentication** (beroende på den senaste versionen).

   När det här steget är klart kommer är tvåstegsverifiering via MFA-servern inte tillgänglig i den här AD FS-klustret förrän du har slutfört steg 8.

4. Avregistrera den äldre versionen av AD FS-adaptern genom att köra Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-skriptet. Se till att den *-namnet* parameter (”WindowsAzureMultiFactorAuthentication” eller ”AzureMFAServerAuthentication”) matchar namnet som visades i steg 3. Detta gäller för alla servrar i samma AD FS-klustret eftersom det inte finns en central konfiguration.
5. Registrera nya AD FS-adaptern genom att köra Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-skriptet. Detta gäller för alla servrar i samma AD FS-klustret eftersom det inte finns en central konfiguration.
6. Starta om AD FS-tjänsten på varje server tas bort från AD FS-servergrupp.
7. Lägg till de uppdaterade servrarna tillbaka till AD FS-servergruppen och ta bort de andra servrarna från servergruppen.
8. Gå till **AD FS** > **autentiseringsprinciper** > **redigera globala flerfunktionsautentisering princip**. Kontrollera **AzureMfaServerAuthentication**.
9. Upprepa steg 2 för att uppdatera de servrar som har nu tagits bort från AD FS-servergruppen och starta om AD FS-tjänsten på dessa servrar.
10. Lägg till dessa servrar i AD FS-servergrupp.

## <a name="next-steps"></a>Nästa steg

* Hämta exempel på [avancerade scenarier med Azure Multi-Factor Authentication och virtuella privata nätverk från tredje part](howto-mfaserver-nps-vpn.md)

* [Synkronisera MFA Server med Windows Server Active Directory](howto-mfaserver-dir-ad.md)

* [Konfigurera Windows-autentisering](howto-mfaserver-windows.md) för dina program
