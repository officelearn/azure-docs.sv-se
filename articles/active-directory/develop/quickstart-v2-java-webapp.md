---
title: Snabb start för Microsoft Identity Platform Java Web Apps | Azure
description: Lär dig hur du implementerar Microsoft-inloggning på en Java-webbapp med OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/11/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: de2a59f878e0c0258b0619895d8f4c8bfd0670a8
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036432"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Snabbstart: Lägg till inloggning med Microsoft i en Java-webbapp

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

I den här snabb starten får du lära dig hur du integrerar ett Java-webbprogram med Microsoft Identity Platform. Din app kommer att logga in en användare, hämta en åtkomsttoken för att anropa Microsoft Graph-API: et och göra en begäran till Microsoft Graph API. 

När du har slutfört guiden kommer ditt program att godkänna inloggningar med personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbets-eller skol konton från alla företag eller organisationer som använder Azure Active Directory.

![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-java-webapp/java-quickstart.svg)

> ## <a name="prerequisites"></a>Förutsättningar
> Om du vill köra det här exemplet behöver du: 
> - En Internet anslutning.
> - En fungerande installation av Java och Maven.
> - En Azure Active Directory-klient (Azure AD). Mer information om hur du skaffar en Azure AD-klient finns i [så här skaffar du en Azure AD-klient.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * Uttrycker [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda ned kod exemplet.](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Bok [Alternativ 2: Registrera dig och konfigurera programmet och kod exemplet manuellt.](#option-2-register-and-manually-configure-your-application-and-code-sample)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till [Azure Portal-Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
> 
>
> #### <a name="step-1-download-the-code-sample"></a>Steg 1: Ladda ned kod exemplet
> 
> - [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
>
> #### <a name="step-2-open-applicationproperties"></a>Steg 2: Öppna program. Properties
>
> 1. Extrahera zip-filen till en lokal mapp.
> 1. Valfritt Om du använder en Integrated Development Environment öppnar du exemplet i din favorit-IDE.
> 1. Öppna filen *Application. Properties* . Du kommer att infoga värden `aad.clientId`för `aad.authority`, och `aad.secretKey` när du registrerar ditt program i nästa steg.


> #### <a name="step-3-register-your-application"></a>Steg 3: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>    - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `java-webapp`.
>    - Lämna omdirigerings- **URI** tomt för tillfället och välj **Registrera**.
> 1. Hitta **programmets ID-värde (Client)** för programmet. Uppdatera värdet för `Enter_the_Application_Id_here` i filen *Application. Properties* .
> 1. Hitta programmets **ID-värde för katalogen (klient)** . Uppdatera värdet för `Enter_the_Tenant_Info_Here` i filen *Application. Properties* . 
> 1. Välj menyn **Autentisering** och lägg sedan till följande information:
>    - I omdirigerings- `http://localhost:8080/msal4jsamples/secure/aad` URI `https://localhost:8080/msal4jsamples/graph/users`: **er**lägger du till och.
>    - Välj **Spara**.
> 1. På den vänstra menyn väljer du **certifikat & hemligheter** och klickar på **ny klient hemlighet** i avsnittet **klient hemligheter** :
>     
>    - Ange en nyckel Beskrivning (av instansens program hemlighet).
>    - Välj en nyckel varaktighet på om **1 år**.
>    - När du klickar på **Lägg till**visas nyckelvärdet. 
>    - Kopiera värdet för nyckeln. Öppna filen *Application. Properties* som du laddade ned tidigare och uppdatera värdet för `Enter_the_Client_Secret_Here` med nyckel svärdet. 
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera ditt program i Azure Portal
> För att kod exemplet för den här snabb starten ska fungera måste du:
> 1. Lägg till svars `http://localhost:8080/msal4jsamples/secure/aad` - `https://localhost:8080/msal4jsamples/graph/users`URL: er som och.
> 1. Skapa en klient hemlighet.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Programmet konfigureras med de här attributen.
> 
> #### <a name="step-2-download-the-code-sample"></a>Steg 2: Ladda ned kod exemplet
> 
> - [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> #### <a name="step-3-configure-the-code-sample"></a>Steg 3: Konfigurera kod exemplet 
> 
> 1. Extrahera zip-filen till en lokal mapp.
> 1. Om du använder en Integrated Development Environment öppnar du exemplet i din favorit IDE (valfritt).
> 1. Öppna filen **Application. Properties** , som du hittar i *src/main/Resources/* .
> 1. Ersätt program egenskaper.
>   1. Hitta `aad.clientId` och uppdatera värdet för `Enter_the_Application_Id_here` med **programmets (klient) ID-** värdet för det program som du har registrerat. 
>   1. Hitta `aad.authority` och uppdatera värdet för `Enter_the_Tenant_Name_Here` med **katalog-ID-** värdet för det program som du har registrerat.
>   1. Hitta `aad.secretKey` och uppdatera värdet för `Enter_the_Client_Secret_Here` med den **klient hemlighet** som du skapade i **certifikat & hemligheter** för det program som du har registrerat.

#### <a name="step-4-run-the-code-sample"></a>Steg 4: Kör kod exemplet
1. Kör kod exemplet och öppna en webbläsare och gå till *http://localhost:8080* .
1. Den första sidan innehåller en **inloggnings** knapp. Klicka på **inloggnings** knappen för att omdirigera till Azure Active Directory. Användaren uppmanas att ange sina autentiseringsuppgifter.  
1. När du har autentiserat Azure Active Directory kommer de att omdirigeras till *http://localhost:8080/msal4jsamples/secure/aad* . De är officiellt inloggade i programmet och sidan ska visa information om det inloggade kontot. Den innehåller också knappar för: 
    - *Logga ut*: Kommer att logga ut den aktuella användaren från programmet och dirigera om dem till start sidan.
    - *Visa användare*: Kommer att hämta en token för Microsoft Graph och anropa sedan Microsoft Graph med token som är kopplad till begäran för att hämta alla användare i klient organisationen.


## <a name="more-information"></a>Mer information

### <a name="getting-msal"></a>Hämtar MSAL
MSAL4J är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Du kan lägga till MSAL4J i ditt program genom att använda maven eller Gradle för att hantera dina beroenden genom att göra följande ändringar i filen Pom. XML eller build. Gradle i ditt program. 

```
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>Msal-initiering
Du kan lägga till referensen i MSAL4J genom att lägga till följande kod överst i filen där du kommer att använda MSAL4J: 

```
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
