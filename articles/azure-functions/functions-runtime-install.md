---
title: Azure Functions-Runtime-Installation | Microsoft Docs
description: Så här installerar du Azure Functions-Runtime preview 2
services: functions
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: f8ce27bf28f73818932f2ac9056d4fdd573679e8
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
ms.locfileid: "26290665"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Installera Azure Functions-Runtime preview 2

Följ dessa steg om du vill installera Azure Functions-Runtime preview 2:

1. Se till att din dator skickar minimikraven.
1. Hämta den [Azure Functions-Runtime Preview Installer](https://aka.ms/azafrv2).
1. Avinstallera förhandsversionen av Azure Functions-Runtime 1.
1. Installera Azure Functions-Runtime preview 2.
1. Slutföra konfigurationen av Azure Functions-Runtime preview 2.
1. Skapa din första funktion i Azure Functions Runtime Preview

## <a name="prerequisites"></a>Krav

Innan du installerar Azure Functions-Runtime-förhandsversionen måste du ha följande resurser som är tillgängliga:

1. En dator som kör Microsoft Windows Server 2016 eller Microsoft Windows 10 skapare Update (Professional och Enterprise Edition).
1. En SQL Server-instans som körs i nätverket.  Lägsta version som krävs är SQL Server Express.

## <a name="uninstall-previous-version"></a>Avinstallera tidigare versioner

Om du tidigare har installerat förhandsversionen av Azure Functions-Runtime måste du avinstallera dem innan du installerar den senaste versionen.  Avinstallera förhandsversionen av Azure Functions-Runtime genom att ta bort programmet i Lägg till/ta bort program i Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Installera förhandsversionen av Azure Functions-Runtime

Azure Functions Runtime Preview Installer vägleder dig genom installationen av Azure Functions-Runtime preview Management- och arbetsroller.  Det är möjligt att installera rollen hantering och Worker på samma dator.  När du lägger till fler funktionen appar måste du distribuera flera arbetsroller på ytterligare datorer för att kunna skala dina funktioner på flera arbetare.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Installera hanterings- och Worker-rollen på samma dator

1. Kör installationsprogrammet för Azure Functions Runtime Preview.

    ![Azure Functions-Runtime preview installer][1]

1. Klicka på **Nästa**.
1. När du har läst villkoren i den **EULA**, **Markera kryssrutan** att acceptera villkoren och klicka på **nästa** att gå vidare.
1. Välj de roller som du vill installera på den här datorn **funktioner Ledningsroll** och/eller **funktioner Arbetsrollen** och klicka på **nästa**.

    ![Installationsprogram för Azure Functions-Runtime preview - urval för Systemroll][3]

    > [!NOTE]
    > Du kan installera den **funktioner Arbetsrollen** på andra datorer. Om du vill göra det, följer du dessa instruktioner och välj endast **funktioner Arbetsrollen** i installationsprogrammet.

1. Klicka på **nästa** har den **installationsguiden för Azure Functions-Runtime** påbörja installationsprocessen på din dator.
1. När installationen är klar startar installationsguiden den **Azure Functions-Runtime** -konfigurationsverktyget.

    ![Azure Functions-Runtime preview installationsprogrammet slutför][6]

    > [!NOTE]
    > Om du installerar på **Windows 10** och **behållare** funktionen har inte aktiverats tidigare, den **installationsprogram för Azure Functions-Runtime** uppmanas du att starta om datorn att slutföra installationen.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurera Azure Functions-Runtime

Azure Functions-Runtime-installationen ska slutföras måste du slutföra konfigurationen.

1. Den **Azure Functions-Runtime** konfigurationsverktyget visas vilka roller som är installerade på datorn.

    ![Konfigurationsverktyget för Azure Functions-Runtime-förhandsgranskning][7]

1. Klicka på den **databasen** ange anslutningsinformationen för SQL Server-instans, inklusive en [databasens huvudnyckel](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), och klicka på **tillämpa**.  Anslutning till en SQL Server-instans krävs för Azure Functions-Runtime att skapa en databas för att stödja körningsmiljön.

    ![Azure Functions-Runtime preview databaskonfigurationen][8]

1. Klicka på den **autentiseringsuppgifter** fliken.  Här, måste du skapa två nya autentiseringsuppgifter för användning med en filresurs som värd för alla dina appar i funktionen.  Ange **användarnamn** och **lösenord** kombinationer för den **resursen filägaren** och för den **filen resursen användaren**, klicka sedan på **Gäller**.

    ![Autentiseringsuppgifter för förhandsversionen av Azure Functions-Runtime][9]

1. Klicka på den **filresurs** fliken.  Ange information om platsen för filresursen.  Filresursen kan skapas för dig eller Använd en befintlig resurs och på **tillämpa**.  Om du väljer en ny filresurs-plats måste du ange en katalog för användning av Azure Functions-Runtime.

    ![Azure Functions-Runtime preview-filresurs][10]

1. Klicka på den **IIS** fliken.  Den här fliken visas information om webbplatserna i IIS som skapas i Azure Functions-Runtime-konfigurationsverktyget.  Du kan ange en anpassad DNS-namn för Azure Functions-Runtime preview portal.  Klicka på **tillämpa** ska slutföras.

    ![Förhandsversionen av Azure Functions-Runtime IIS][11]

1. Klicka på den **Services** fliken.  Den här fliken visas status för tjänsterna i Azure Functions-Runtime-konfigurationsverktyget.  Om den **Azure Functions värdtjänsten aktivering** är inte körs efter den första konfigurationen, klickar du på **starta tjänsten**.

    ![Azure Functions-Runtime preview Slutför konfiguration][12]

1. Bläddra till den **Azure Functions-Runtime Portal** som `https://<machinename>.<domain>/`.

    ![Azure Functions-Runtime preview-portalen][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Skapa din första funktion i förhandsversionen av Azure Functions-Runtime

Om du vill skapa din första funktion i Azure Functions-Runtime Förhandsgranska

1. Bläddra till den **Azure Functions-Runtime Portal** som https://<machinename>.<domain> till exempel https://mycomputer.mydomain.com
1. Du uppmanas att **logga in**, om de har distribuerats i en domän använder ditt användarnamn för konto och lösenord, annars använda din lokala användarnamn och lösenord för att logga in på portalen.

![Azure Functions-Runtime preview portal inloggning][14]

1. För att skapa funktionen appar, måste du skapa en prenumeration.  I det övre vänstra hörnet av portalen klickar du på den  **+**  alternativet bredvid prenumerationerna

![Azure Functions-Runtime preview portal-prenumerationer][15]

1. Välj **DefaultPlan**, ange ett namn för din prenumeration och på **skapa**.

![Azure Functions-Runtime preview portal prenumerationsavtal och namn][16]

1. Alla dina appar med funktionen listas i den vänstra rutan i portalen.  Markera rubriken för att skapa en ny Funktionsapp **funktionen appar** och klicka på den  **+**  alternativet.

1. Ange ett namn för appen funktionen väljer korrekt prenumeration, välja vilken version av Azure Functions-runtime som du inte vill att programmet mot och klicka på **skapa**

![Azure Functions-Runtime preview portal ny funktionsapp][17]

1. Den nya funktion appen visas i den vänstra rutan i portalen.  Välj funktioner och klicka sedan på **nya funktionen** överst i mitten av fönstret i portalen.

![Azure Functions-Runtime preview-mallar][18]

1. Välj funktionen Timer som utlösare, i den högra utfällbar namn för din funktion och ändra schemat för `*/5 * * * * *` (cron-uttryck gör din timer-funktionen att köra var femte sekund), och klicka på **skapa**

![Azure Functions-Runtime preview nya timer funktionen-konfiguration][19]

1. Funktionen har skapats.  Du kan visa körningsloggen för funktionen appen genom att expandera den **loggen** rutan längst ned i portalen.

![Azure Functions-Runtime preview funktionen körs][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
