---
title: Uppgradera PhoneFactor till Azure MFA Server | Microsoft Docs
description: "Kom igång med Azure MFA Server när du uppgraderar från den äldre PhoneFactor-agenten."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: 42838ff7-bdf2-4d06-bacc-b3839a00cd76
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: joflore
ms.openlocfilehash: b89ae9323c9766e67118e772732c06078918a861
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Uppgradera PhoneFactor Agent till Azure Multi-Factor Authentication Server
Om du vill uppgradera PhoneFactor-agent v5.x eller senare till Azure Multi-Factor Authentication Server avinstallerar du PhoneFactor-agenten och dess kopplade komponenter först. Sedan kan Multi-Factor Authentication-servern och dess kopplade komponenter installeras.

## <a name="uninstall-the-phonefactor-agent"></a>Avinstallera PhoneFactor-agenten

1. Börja med att säkerhetskopiera PhoneFactor-datafilen. Standardinstallationsplatsen är C:\Program\PhoneFactor\Data\Phonefactor.pfdata.

2. Om Användarportalen installeras:
  1. Navigera till installationsmappen och säkerhetskopiera filen web.config. Standardinstallationsplatsen är C:\inetpub\wwwroot\PhoneFactor.

  2. Om du har lagt till anpassade teman på portalen säkerhetskopierar du din anpassade mapp under katalogen C:\inetpub\wwwroot\PhoneFactor\App_Themes.

  3. Avinstallera användarportalen antingen via PhoneFactor-agenten (endast tillgängligt om den installerats på samma server som PhoneFactor Agent) eller via Program och funktioner i Windows.

3. Om Mobile Apps-webbtjänsten är installerad:

  1. Gå till installationsmappen och säkerhetskopiera filen web.config. Standardinstallationsplatsen är C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.

  2. Avinstallera Mobile App-webbtjänsten via Program och funktioner i Windows.

4. Om webbtjänst-SDK är installerat avinstallerar du det antingen via PhoneFactor-agenten eller via Program och funktioner i Windows.

5. Avinstallera PhoneFactor Agent via Program och funktioner i Windows.

## <a name="install-the-multi-factor-authentication-server"></a>Installera Multi-Factor Authentication Server

Installationssökvägen hämtas från registret från den tidigare installationen av PhoneFactor Agent, så den bör installeras på samma plats (t.ex. c:\Program\Microsoft Files\PhoneFactor). Nya installationer har en annan standardinstallationssökväg (t.ex. c:\Program\Microsoft Files\Multi-Factor Authentication Server). Datafilen som lämnats kvar av den tidigare PhoneFactor-agenten bör uppgraderas under installationen, så dina användare och inställningar bör finnas kvar där efter installationen av den nya Multi-Factor Authentication-servern.

1. Om du uppmanas att göra det aktiverar du Multi-Factor Authentication Server och ser till att den är tilldelad till rätt replikeringsgrupp.

2. Om webbtjänst-SDK var installerat tidigare installerar du nya webbtjänst-SDK via användargränssnittet för Multi-Factor Authentication Server.

  Standardnamnet för den virtuella katalogen nu är **MultiFactorAuthWebServiceSdk** i stället för **PhoneFactorWebServiceSdk**. Om du vill använda det förra namnet måste du ändra namnet på den virtuella katalogen under installationen. Annars, om du tillåter att installationen använder standardnamnet, så måste du ändra URL:en i program som refererar till webbtjänst-SDK (till exempel användarportalen och webbtjänsten Mobile Apps) så att den pekar på rätt plats.

3. Om användarportalen var installerad tidigare på PhoneFactor Agent-servern installerar du den nya Multi-Factor Authentication-användarportalen via Multi-Factor Authentication Server-användargränssnittet.

  Standardnamnet för den virtuella katalogen nu är **MultiFactorAuth** i stället för **PhoneFactor**. Om du vill använda det förra namnet måste du ändra namnet på den virtuella katalogen under installationen. Annars, om du tillåter att installationen använder det nya standardnamnet, klickar du på ikonen för användarportalen i Multi-Factor Authentication Server och uppdaterar URL:en för användarportalen på fliken Inställningar.

4. Om användarportalen och/eller webbtjänsten Mobile Apps tidigare var installerade på en annan server än PhoneFactor-agenten:

  1. Gå till installationsplatsen (t.ex. C:\Program\Microsoft Files\PhoneFactor) och kopiera relevanta installationsprogram till den andra servern. Det finns 32- och 64-bitars installationsprogram för både användarportalen och webbtjänsten Mobile Apps. De heter MultiFactorAuthenticationUserPortalSetupXX.msi och MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

  2. Installera användarportalen på webbservern genom att öppna en kommandotolk som administratör och köra MultiFactorAuthenticationUserPortalSetupXX.msi.

    Standardnamnet för den virtuella katalogen nu är **MultiFactorAuth** i stället för **PhoneFactor**. Om du vill använda det förra namnet måste du ändra namnet på den virtuella katalogen under installationen. Annars, om du tillåter att installationen använder det nya standardnamnet, klickar du på ikonen för användarportalen i Multi-Factor Authentication Server och uppdaterar URL:en för användarportalen på fliken Inställningar. Befintliga användare måste informeras om den nya URL:en.

  3. Gå till installationsplatsen för användarportalen (t.ex. C:\inetpub\wwwroot\MultiFactorAuth) och redigera filen web.config. Kopiera värdena i avsnitten appSettings och applicationSettings från den ursprungliga web.config-filen som du säkerhetskopierade före uppgraderingen och klistra in dem i den nya web.config-filen. Om du behöll det nya standardnamnet för den virtuella katalogen när du installerade webbtjänst-SDK ändrar du URL:en i avsnittet applicationSettings så att den pekar på rätt plats. Om du ändrade andra standardvärden i den tidigare web.config-filen tillämpar du samma ändringar i den nya web.config-filen.

  4. Installera webbtjänsten Mobile Apps på webbservern genom att öppna en kommandotolk som administratör och köra MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

    Standardnamnet på den virtuella datorn nu är **MultiFactorAuthMobileAppWebService** i stället för **PhoneFactorPhoneAppWebService**. Om du vill använda det förra namnet måste du ändra namnet på den virtuella katalogen under installationen. Du kanske vill välja ett kortare namn så att det blir lättare för slutanvändarna att skriva det på sina mobila enheter. Annars, om du tillåter att installationen använder det nya namnet, klickar du på ikonen Mobilapp i Multi-Factor Authentication Server och uppdaterar URL:en för Mobile App-webbtjänsten.

  5. Gå till installationsplatsen för Mobile App-webbtjänsten (t.ex. C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) och redigera filen Web.config. Kopiera värdena i avsnitten appSettings och applicationSettings från den ursprungliga web.config-filen som du säkerhetskopierade före uppgraderingen och klistra in dem i den nya web.config-filen. Om du behöll det nya standardnamnet för den virtuella katalogen när du installerade webbtjänst-SDK ändrar du URL:en i avsnittet applicationSettings så att den pekar på rätt plats. Om du ändrade andra standardvärden i den tidigare web.config-filen tillämpar du samma ändringar i den nya web.config-filen.

## <a name="next-steps"></a>Nästa steg

- [Installera användarportalen](multi-factor-authentication-get-started-portal.md) för Azure Multi-Factor Authentication-servern.

- [Konfigurera Windows-autentisering](multi-factor-authentication-get-started-server-windows.md) för dina program. 
