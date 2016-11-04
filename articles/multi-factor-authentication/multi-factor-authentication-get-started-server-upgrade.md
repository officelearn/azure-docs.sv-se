---
title: Uppgradera PhoneFactor Agent till Azure Multi-Factor Authentication Server
description: Det här dokumentet beskriver hur du kommer igång med Azure MFA Server och hur du uppgraderar från den äldre PhoneFactor-agenten.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Uppgradera PhoneFactor Agent till Azure Multi-Factor Authentication Server
Uppgraderingen från PhoneFactor Agent v5.x eller äldre till Azure Multi-Factor Authentication Server kräver att PhoneFactor-agenten och tillhörande komponenter avinstalleras innan Multi-Factor Authentication Server och associerade komponenter kan installeras.

## Så här uppgraderar du PhoneFactor Agent till Azure Multi-Factor Authentication Server
<ol>
<li>Börja med att säkerhetskopiera PhoneFactor-datafilen. Standardinstallationsplatsen är C:\Program\PhoneFactor\Data\Phonefactor.pfdata.


<li>Om Användarportalen installeras:</li>
<ol>
<li>Navigera till installationsmappen och säkerhetskopiera filen web.config. Standardinstallationsplatsen är C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Om du har lagt till anpassade teman på portalen säkerhetskopierar du din anpassade mapp under katalogen C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Avinstallera användarportalen antingen via PhoneFactor-agenten (endast tillgängligt om den installerats på samma server som PhoneFactor Agent) eller via Program och funktioner i Windows.</li></ol>




<li>Om Mobile Apps-webbtjänsten är installerad:

<ol>
<li>Gå till installationsmappen och säkerhetskopiera filen web.config. Standardinstallationsplatsen är C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Avinstallera Mobile App-webbtjänsten via Program och funktioner i Windows.</li></ol>

<li>Om webbtjänst-SDK är installerat avinstallerar du det antingen via PhoneFactor-agenten eller via Program och funktioner i Windows.

<li>Avinstallera PhoneFactor Agent via Program och funktioner i Windows.

<li>Installera Multi-Factor Authentication Server. Observera att installationssökvägen hämtas från registret från den tidigare installationen av PhoneFactor Agent så den bör installeras på samma plats (t.ex. c:\Program\Microsoft Files\PhoneFactor). Nya installationer har en annan standardinstallationssökväg (t.ex. c:\Program\Microsoft Files\Multi-Factor Authentication Server). Datafilen som lämnats kvar av den tidigare PhoneFactor-agenten bör uppgraderas under installationen, så dina användare och inställningar bör finnas kvar där efter installationen av den nya Multi-Factor Authentication-servern.

<li>Om du uppmanas att göra det aktiverar du Multi-Factor Authentication Server och ser till att den är tilldelad till rätt replikeringsgrupp.

<li>Om webbtjänst-SDK var installerat tidigare installerar du nya webbtjänst-SDK via användargränssnittet för Multi-Factor Authentication Server. Observera att standardnamnet för den virtuella katalogen nu är ”MultiFactorAuthWebServiceSdk” i stället för ”PhoneFactorWebServiceSdk”. Om du vill använda det förra namnet måste du ändra namnet på den virtuella katalogen under installationen. Annars, om du tillåter att installationen använder standardnamnet, så måste du ändra URL:en i program som refererar till webbtjänst-SDK, till exempel användarportalen och webbtjänsten Mobile Apps, så att den pekar på rätt plats.

<li>Om användarportalen var installerad tidigare på PhoneFactor Agent-servern installerar du den nya Multi-Factor Authentication-användarportalen via Multi-Factor Authentication Server-användargränssnittet. Observera att standardnamnet för den virtuella katalogen nu är ”MultiFactorAuth” i stället för ”PhoneFactor”. Om du vill använda det förra namnet måste du ändra namnet på den virtuella katalogen under installationen. Annars, om du tillåter att installationen använder det nya standardnamnet, klickar du på ikonen för användarportalen i Multi-Factor Authentication Server och uppdaterar URL:en för användarportalen på fliken Inställningar.

<li>Om användarportalen och/eller webbtjänsten Mobile Apps tidigare var installerade på en annan server än PhoneFactor-agenten:

<ol>
<li>Gå till installationsplatsen (t.ex. C:\Program\Microsoft Files\PhoneFactor) och kopiera relevanta installationsprogram till den andra servern. Det finns 32- och 64-bitars installationsprogram för både användarportalen och webbtjänsten Mobile Apps. De heter MultiFactorAuthenticationUserPortalSetupXX.msi respektive MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.</li>
<li>Installera användarportalen på webbservern genom att öppna en kommandotolk som administratör och köra MultiFactorAuthenticationUserPortalSetupXX.msi. Observera att standardnamnet för den virtuella katalogen nu är ”MultiFactorAuth” i stället för ”PhoneFactor”. Om du vill använda det förra namnet måste du ändra namnet på den virtuella katalogen under installationen. Annars, om du tillåter att installationen använder det nya standardnamnet, klickar du på ikonen för användarportalen i Multi-Factor Authentication Server och uppdaterar URL:en för användarportalen på fliken Inställningar. Befintliga användare måste informeras om den nya URL:en.</li>
<li>Gå till installationsplatsen för användarportalen (t.ex. C:\inetpub\wwwroot\MultiFactorAuth) och redigera filen web.config. Kopiera värdena i avsnitten appSettings och applicationSettings från den ursprungliga web.config-filen som du säkerhetskopierade före uppgraderingen och klistra in dem i den nya web.config-filen. Om du behöll det nya standardnamnet för den virtuella katalogen när du installerade webbtjänst-SDK ändrar du URL:en i avsnittet applicationSettings så att den pekar på rätt plats. Om du ändrade andra standardvärden i den tidigare web.config-filen tillämpar du samma ändringar i den nya web.config-filen.</li>
<li>Installera webbtjänsten Mobile Apps på webbservern genom att öppna en kommandotolk som administratör och köra MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Observera att namnet på den virtuella datorn nu är ”MultiFactorAuthMobileAppWebService” i stället för ”PhoneFactorPhoneAppWebService”. Om du vill använda det förra namnet måste du ändra namnet på den virtuella katalogen under installationen. Du kanske vill välja ett kortare namn så att det blir lättare för slutanvändarna att skriva det på sina mobila enheter. Annars, om du tillåter att installationen använder det nya namnet, klickar du på ikonen Mobilapp i Multi-Factor Authentication Server och uppdaterar URL:en för Mobile App-webbtjänsten.</li>
<li>Gå till installationsplatsen för Mobile App-webbtjänsten  (t.ex. C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) och redigera filen Web.config. Kopiera värdena i avsnitten appSettings och applicationSettings från den ursprungliga web.config-filen som du säkerhetskopierade före uppgraderingen och klistra in dem i den nya web.config-filen. Om du behöll det nya standardnamnet för den virtuella katalogen när du installerade webbtjänst-SDK ändrar du URL:en i avsnittet applicationSettings så att den pekar på rätt plats. Om du ändrade andra standardvärden i den tidigare web.config-filen tillämpar du samma ändringar i den nya web.config-filen.</li></ol>



<!--HONumber=Sep16_HO3-->


