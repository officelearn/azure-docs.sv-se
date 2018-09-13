---
title: Konfigurera Distributionskällor för App Services i Azure Stack | Microsoft Docs
description: Hur en tjänstadministratör kan konfigurera distributionskällor (Git, GitHub, BitBucket, DropBox och OneDrive) för App Service i Azure Stack
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
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 4945ab0b8da961fc5739388d83f7c645f40e510d
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35963978"
---
# <a name="configure-deployment-sources"></a>Konfigurera distributionskällor
*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*


App Service i Azure Stack har stöd för distribution på begäran från flera Källkontrollsprovidrar. Den här funktionen kan utvecklare distribuera direkt deras källkodsdatabaser. Om användarna vill konfigurera App Service för att ansluta till sina databaser, måste en molnoperator du konfigurera integreringen mellan App Service i Azure Stack och en Källkontrollsleverantör.  

Förutom lokal Git stöds följande Källkontrollsprovidrar:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Visa distributionskällor i App Service-administration

1. Logga in på administrationsportalen för Azure Stack (https://adminportal.local.azurestack.external) som tjänstadministratör.
2. Bläddra till **Resursprovidrar** och välj den **App tjänstadministratören Resource Provider**.  ![App Service Resource Provider-administratör][1]
3. Klicka på **källkontrollkonfiguration**.  Här kan du se listan över alla Distributionskällor som har konfigurerats.
    ![App Service Resource Provider Admin konfiguration av källkontroll][2]

## <a name="configure-github"></a>Konfigurera GitHub

Du måste ha ett GitHub-konto för att slutföra den här åtgärden. Du kanske vill använda ett konto för din organisation i stället för ett personligt konto.

1. Logga in på GitHub, bläddra till https://www.github.com/settings/developers och klicka på **registrera ett nytt program**.
    ![GitHub - registrera ett nytt program][3]
2. Ange en **programnamn** till exempel – App Service i Azure Stack.
3. Ange den **Hemsides-URL**. Hemsides-URL måste vara Azure Stack Portal-adress. Till exempel https://portal.local.azurestack.external.
4. Ange en **Programbeskrivning**.
5. Ange den **Motringnings-URL för auktorisering**.  I en standarddistribution av Azure Stack-URL: en är i formatet https://portal.local.azurestack.external/TokenAuthorize, om du kör i en annan domän ersättning din domän för local.azurestack.external
6. Klicka på **registrera program**.  Nu visas med en sida-lista de **klient-ID** och **Klienthemlighet** för programmet.
    ![GitHub - slutförda programregistrering][5]
7.  I en ny webbläsarflik eller logga in på administrationsportalen för Azure Stack (https://adminportal.local.azurestack.external) som tjänstadministratör.
8.  Bläddra till **Resursprovidrar** och välj den **App tjänstadministratören Resource Provider**.
9. Klicka på **källkontrollkonfiguration**.
10. Kopiera och klistra in den **klient-ID** och **Klienthemlighet** till motsvarande indata rutorna för GitHub.
11. Klicka på **Spara**.

## <a name="configure-bitbucket"></a>Konfigurera BitBucket

Du måste ha ett BitBucket-konto för att slutföra den här åtgärden. Du kanske vill använda ett konto för din organisation i stället för ett personligt konto.

1. Logga in på BitBucket och bläddra till **integreringar** under ditt konto.
    ![Instrumentpanel för BitBucket - integreringar][7]
2. Klicka på **OAuth** under åtkomsthantering och **Lägg till konsumenten**.
    ![BitBucket lägga till OAuth-konsument][8]
3. Ange en **namn** för konsumenten, till exempel App Service i Azure Stack.
4. Ange en **beskrivning** för programmet.
5. Ange den **Motringnings-URL för**.  I en standarddistribution av Azure Stack är Motringnings-Url i formatet https://portal.local.azurestack.external/TokenAuthorize, om du kör i en annan domän ersättning din domän för azurestack.local.  URL: en måste följa skiftläget som anges här för BitBucket-integrering ska lyckas.
6. Ange den **URL** -URL: en ska vara Azure Stack Portal URL, till exempel https://portal.local.azurestack.external.
7. Välj den **behörigheter** krävs:
    - **Databaser**: *Läs*
    - **Webhooks**: *läsning och skrivning*
8. Klicka på **Spara**.  Nu visas det här nya programmet tillsammans med den **nyckel** och **hemlighet** under **OAuth konsumenter**.
    ![Lista för BitBucket-program][9]
9.  I en ny webbläsarflik eller logga in på administrationsportalen för Azure Stack (https://adminportal.local.azurestack.external) som tjänstadministratör.
10.  Bläddra till **Resursprovidrar** och välj den **App tjänstadministratören Resource Provider**.
11. Klicka på **källkontrollkonfiguration**.
12. Kopiera och klistra in den **nyckel** till den **klient-ID** inmatningsrutan och **hemlighet** till den **Klienthemlighet** textrutan för BitBucket.
13. Klicka på **Spara**.


## <a name="configure-onedrive"></a>Konfigurera OneDrive

Du måste ha ett Account länkad till en OneDrive-konto att slutföra den här uppgiften.  Du kanske vill använda ett konto för din organisation i stället för ett personligt konto.

> [!NOTE]
> OneDrive för företag-konton stöds inte för närvarande.

1. Bläddra till https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm och logga in med ditt Microsoft-Account.
2. Under **Mina program**, klickar du på **lägga till en app**.
![OneDrive-program][10]
3. Ange en **namn** ny programregistrering ange **App Service i Azure Stack**, och klicka på **skapa program**
4. Nästa skärm visas egenskaperna för det nya programmet. Post i **program-ID**.
![Egenskaper för OneDrive-program][11]
5. Under **Programhemligheter**, klickar du på **generera nytt lösenord**. Anteckna **nytt lösenord har genererats**. Det här är din programhemlighet och kan inte hämtas när du klickar på **OK** i det här skedet.
6. Under **plattformar** klickar du på **Lägg till plattform** och välj **Web**.
7. Ange den **omdirigerings-URI**.  I en standarddistribution av Azure Stack är omdirigerings-URI i formatet https://portal.local.azurestack.external/TokenAuthorize, om du kör i en annan domän ersättning din domän för azurestack.local ![OneDrive program – Lägg till webbplattform][12]
8. Lägg till den **Microsoft Graph-behörigheter** - **delegerade behörigheter**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Program i OneDrive - Graph-behörigheter][13]
9. Klicka på **Spara**.
10.  I en ny webbläsarflik eller logga in på administrationsportalen för Azure Stack (https://adminportal.local.azurestack.external) som tjänstadministratör.
11.  Bläddra till **Resursprovidrar** och välj den **App tjänstadministratören Resource Provider**.
12. Klicka på **källkontrollkonfiguration**.
13. Kopiera och klistra in den **program-ID** till den **klient-ID** inmatningsrutan och **lösenord** till den **Klienthemlighet** textrutan för OneDrive.
14. Klicka på **Spara**.

## <a name="configure-dropbox"></a>Konfigurera DropBox

> [!NOTE]
> Du måste ha ett DropBox-konto för att slutföra den här åtgärden.  Du kanske vill använda ett konto för din organisation i stället för ett personligt konto.

1. Bläddra till https://www.dropbox.com/developers/apps och logga in med ditt DropBox-konto.
2. Klicka på **skapa app**.

    ![Dropbox-program][14]

3. Välj **DropBox API**.
4. Anger åtkomst till **App mappen**.
5. Ange en **namn** för ditt program.
![Dropbox programregistrering][15]
6. Klicka på **skapa App**.  Nu visas med en sida med inställningarna för appen, inklusive **appnyckeln** och **apphemlighet**.
7. Kontrollera den **App mappnamn** är inställd på **App Service i Azure Stack**.
8. Ange den **OAuth 2 omdirigerings-URI** och klicka på **Lägg till**.  I en standarddistribution av Azure Stack är omdirigerings-URI i formatet https://portal.local.azurestack.external/TokenAuthorize, om du kör i en annan domän ersättning din domän för azurestack.local.
![Dropbox programkonfiguration][16]
9.  I en ny webbläsarflik eller logga in på administrationsportalen för Azure Stack (https://adminportal.local.azurestack.external) som tjänstadministratör.
10.  Bläddra till **Resursprovidrar** och välj den **App tjänstadministratören Resource Provider**.
11. Klicka på **källkontrollkonfiguration**.
12. Kopiera och klistra in den **Programnyckel** till den **klient-ID** inmatningsrutan och **apphemlighet** till den **Klienthemlighet** textrutan för DropBox.
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

Användare kan nu använda distributionskällor för saker som [kontinuerlig distribution](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment), [lokal Git-distribution](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git), och [cloud mappsynkronisering](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync).
