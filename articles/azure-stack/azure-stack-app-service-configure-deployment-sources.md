---
title: Konfigurera distributionen källor för Apptjänster Azure stacken | Microsoft Docs
description: Hur en tjänstadministratör kan konfigurera distributionen källor (Git, GitHub, BitBucket, DropBox och OneDrive) för användning på Azure-stacken
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 4ee333fcc50937679c4bc25b83c2d6aa389ba194
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="configure-deployment-sources"></a>Konfigurera distributionskällor
*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*


Apptjänst Azure stacken stöder distribution på begäran från flera Källkontrollsprovidrar. Den här funktionen kan programutvecklare distribuera direkt från sina databaser för kontroll av källa. Om användarna vill konfigurera App Service för att ansluta till sina databaser, måste en moln-operator du konfigurera integrationen mellan App Service på Azure-stacken och en Källkontrollsleverantör.  

Förutom lokal Git stöds följande Källkontrollsprovidrar:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Visa distribution källor i App Service-administration

1. Logga in på administrationsportalen för stacken i Azure (https://adminportal.local.azurestack.external) som tjänstadministratör.
2. Bläddra till **Resursproviders** och välj den **App tjänstadministratör Resource Provider**.  ![Apptjänst Resource Provider Admin][1]
3. Klicka på **källa kontrollkonfiguration**.  Här ser du listan över alla distribution källor konfigurerats.
    ![Appen resursen Admin källa Kontrollkonfiguration av ServiceProvider][2]

## <a name="configure-github"></a>Konfigurera GitHub

Du måste ha en GitHub-konto för att slutföra åtgärden. Du kanske vill använda ett konto för din organisation i stället för ett personligt konto.

1. Logga in på GitHub, bläddra till https://www.github.com/settings/developers och på **registrera ett nytt program**.
    ![GitHub - registrera ett nytt program][3]
2. Ange en **programnamn** till exempel - användning på Azure-stacken.
3. Ange den **webbsida URL**. Webbsida URL-Adressen måste vara Azure Stack Portal-adress. Till exempel https://portal.local.azurestack.external.
4. Ange en **Programbeskrivningen**.
5. Ange den **auktorisering återanrop URL**.  I en standarddistribution av Azure-stacken är URL-adressen i formatet https://portal.local.azurestack.external/TokenAuthorize, om du använder en annan domän ersättning din domän för local.azurestack.external
6. Klicka på **registrera program**.  Nu visas med en lista över sidan i **klient-ID** och **Klienthemlighet** för programmet.
    ![GitHub - färdiga programmet registrering][5]
7.  I en ny webbläsarflik eller logga in på administrationsportalen för stacken i Azure (https://adminportal.local.azurestack.external) som tjänstadministratör.
8.  Bläddra till **Resursproviders** och välj den **App tjänstadministratör Resource Provider**.
9. Klicka på **källa kontrollkonfiguration**.
10. Kopiera och klistra in den **klient-ID** och **Klienthemlighet** till motsvarande indata rutorna för GitHub.
11. Klicka på **Spara**.

## <a name="configure-bitbucket"></a>Konfigurera BitBucket

Du måste ha ett BitBucket-konto för att slutföra den här aktiviteten. Du kanske vill använda ett konto för din organisation i stället för ett personligt konto.

1. Logga in på BitBucket och bläddra till **integreringar** på ditt konto.
    ![Instrumentpanel för BitBucket - integrering][7]
2. Klicka på **OAuth** under hantering och **Lägg till konsumenten**.
    ![BitBucket lägga till OAuth-Consumer][8]
3. Ange en **namn** för konsumenten, till exempel Apptjänst Azure stacken.
4. Ange en **beskrivning** för programmet.
5. Ange den **motringning URL**.  I en standarddistribution av Azure-stacken återanrop URL: en är i formatet https://portal.local.azurestack.external/TokenAuthorize, om du använder en annan domän ersättning din domän för azurestack.local.  URL: en måste följa skiftläget som anges här för BitBucket-integrering ska lyckas.
6. Ange den **URL** -URL: en ska vara Azure Stack Portal URL, till exempel https://portal.local.azurestack.external.
7. Välj den **behörigheter** krävs:
    - **Databaser**: *Läs*
    - **Webhooks**: *läsning och skrivning*
8. Klicka på **Spara**.  Nu visas den här nya program, tillsammans med den **nyckeln** och **hemlighet** under **OAuth konsumenter**.
    ![Lista över BitBucket-program][9]
9.  I en ny webbläsarflik eller logga in på administrationsportalen för stacken i Azure (https://adminportal.local.azurestack.external) som tjänstadministratör.
10.  Bläddra till **Resursproviders** och välj den **App tjänstadministratör Resource Provider**.
11. Klicka på **källa kontrollkonfiguration**.
12. Kopiera och klistra in den **nyckeln** till den **klient-ID** textrutan och **hemlighet** till den **Klienthemlighet** textrutan för BitBucket.
13. Klicka på **Spara**.


## <a name="configure-onedrive"></a>Konfigurera OneDrive

Du måste ha ett Account kopplad till OneDrive-konto att slutföra uppgiften.  Du kanske vill använda ett konto för din organisation i stället för ett personligt konto.

> [!NOTE]
> OneDrive för företag-konton stöds inte för närvarande.

1. Bläddra till https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm och logga in med ditt Microsoft-Account.
2. Under **Mina program**, klickar du på **Lägg till en app**.
![OneDrive-program][10]
3. Ange en **namn** för registreringen av nya program ange **Apptjänst Azure stacken**, och klicka på **skapa program**
4. På nästa skärm visas egenskaperna för det nya programmet. Posten i **program-ID**.
![Egenskaper för OneDrive-program][11]
5. Under **programmet hemligheter**, klickar du på **generera nya lösenord**. Anteckna **nytt lösenord genereras**. Det här är din programhemlighet och kan inte hämtas när du klickar på **OK** i det här skedet.
6. Under **plattformar** klickar du på **lägga till plattformen** och välj **Web**.
7. Ange den **omdirigerings-URI**.  I en standarddistribution av Azure-stacken omdirigerings-URI är i formatet https://portal.local.azurestack.external/TokenAuthorize, om du använder en annan domän ersättning din domän för azurestack.local ![OneDrive program – Lägg till webbplattform][12]
8. Lägg till den **Microsoft Graph behörigheter** - **delegerad behörighet**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Program i OneDrive - diagram-behörigheter][13]
9. Klicka på **Spara**.
10.  I en ny webbläsarflik eller logga in på administrationsportalen för stacken i Azure (https://adminportal.local.azurestack.external) som tjänstadministratör.
11.  Bläddra till **Resursproviders** och välj den **App tjänstadministratör Resource Provider**.
12. Klicka på **källa kontrollkonfiguration**.
13. Kopiera och klistra in den **program-ID** till den **klient-ID** textrutan och **lösenord** till den **Klienthemlighet** textrutan för OneDrive.
14. Klicka på **Spara**.

## <a name="configure-dropbox"></a>Konfigurera DropBox

> [!NOTE]
> Du måste ha ett DropBox-konto för att slutföra den här aktiviteten.  Du kanske vill använda ett konto för din organisation i stället för ett personligt konto.

1. Bläddra till https://www.dropbox.com/developers/apps och logga in med ditt DropBox.
2. Klicka på **skapa app**.

    ![Dropbox-program][14]

3. Välj **DropBox API**.
4. Anger åtkomsten till **App mappen**.
5. Ange en **namn** för ditt program.
![Registrering för dropbox-applikation][15]
6. Klicka på **skapa App**.  Nu visas med en sida med inställningarna för App inklusive **appkey** och **App hemlighet**.
7. Kontrollera den **App mappnamn** är inställd på **Apptjänst Azure stacken**.
8. Ange den **OAuth 2 omdirigerings-URI** och på **Lägg till**.  I en standarddistribution av Azure-stacken omdirigerings-URI är i formatet https://portal.local.azurestack.external/TokenAuthorize, om du använder en annan domän ersättning din domän för azurestack.local.
![Dropbox-programkonfigurationen][16]
9.  I en ny webbläsarflik eller logga in på administrationsportalen för stacken i Azure (https://adminportal.local.azurestack.external) som tjänstadministratör.
10.  Bläddra till **Resursproviders** och välj den **App tjänstadministratör Resource Provider**.
11. Klicka på **källa kontrollkonfiguration**.
12. Kopiera och klistra in den **Programnyckeln** i den **klient-ID** textrutan och **App hemlighet** i den **Klienthemlighet** textrutan för DropBox.
13. Klicka på **Spara**.


<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png

## <a name="next-steps"></a>Nästa steg

Användare kan nu använda distribution källor för saker som [kontinuerlig distribution](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment), [lokal Git-distribution](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git), och [molnet mappsynkronisering](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync).
