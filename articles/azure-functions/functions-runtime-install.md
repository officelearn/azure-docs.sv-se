---
title: Installation av körning i Azure Functions
description: Installera förhandsversionen av Azure Functions Runtime 2
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 7ad748aa9a5b45af10121648a668344548484cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226728"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Installera förhandsversionen av Azure Functions Runtime 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Om du vill installera förhandsversionen av Azure Functions Runtime 2 gör du så här:

1. Se till att din maskin klarar minimikraven.
1. Hämta [installationsprogrammet för Azure Functions Runtime Preview](https://aka.ms/azafrv2).
1. Avinstallera förhandsversionen av Azure Functions Runtime 1.
1. Installera förhandsversionen av Azure Functions Runtime 2.
1. Slutför konfigurationen av förhandsversionen av Azure Functions Runtime 2.
1. Skapa din första funktion i förhandsversionen av Azure Functions Runtime

## <a name="prerequisites"></a>Krav

Innan du installerar förhandsversionen av Azure Functions Runtime måste du ha följande resurser tillgängliga:

1. En dator med Microsoft Windows Server 2016 eller Microsoft Windows 10 Creators Update (Professional eller Enterprise Edition).
1. En SQL Server-instans som körs i nätverket.  Minsta utgåva krävs är SQL Server Express.

## <a name="uninstall-previous-version"></a>Avinstallera tidigare version

Om du tidigare har installerat förhandsversionen av Azure Functions Runtime måste du avinstallera innan du installerar den senaste versionen.  Avinstallera förhandsversionen av Azure Functions Runtime genom att ta bort programmet i Lägg till/ta bort program i Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Installera förhandsversionen av Azure Functions Runtime

Azure Functions Runtime Preview Installer guidar dig genom installationen av Azure Functions Runtime preview Management and Worker Roles.  Det är möjligt att installera rollen Hantering och arbetare på samma dator.  Men när du lägger till fler funktionsappar måste du distribuera fler arbetsroller på ytterligare datorer för att kunna skala dina funktioner till flera arbetare.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Installera hanterings- och arbetarrollen på samma dator

1. Kör azure functions runtime preview installer.

    ![Installationsprogrammet för förhandsversionen av Azure Functions Runtime][1]

1. Klicka på **Nästa**.
1. När du har läst villkoren i **licensavtalet** **markerar du kryssrutan** för att acceptera villkoren och klickar på **Nästa** för att gå vidare.
1. Välj de roller som du vill installera på den här rollen **för funktionshantering** och/eller **Funktionsarbetsroll** och klicka på **Nästa**.

    ![Installationsprogrammet för förhandsversion av Azure Functions Runtime - rollval][3]

    > [!NOTE]
    > Du kan installera **funktionsarbetsrollen** på många andra datorer. Det gör du genom att följa dessa instruktioner och välj endast **Funktioner arbetarroll** i installationsprogrammet.

1. Klicka på **Nästa** om du vill att **installationsguiden** för Azure Functions Runtime börjar installationen på datorn.
1. När du är klar startar installationsguiden konfigurationsverktyget **för Azure Functions Runtime.**

    ![Installer för förhandsversionen av Azure Functions Runtime klar][6]

    > [!NOTE]
    > Om du installerar på **Windows 10** och **behållarfunktionen** inte har aktiverats tidigare, **uppmanas** du att starta om datorn för att slutföra installationen.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurera Azure Functions Runtime

För att slutföra Azure Functions Runtime-installationen måste du slutföra konfigurationen.

1. Konfigurationsverktyget **för Azure Functions Runtime** visar vilka roller som är installerade på din dator.

    ![Konfigurationsverktyg för förhandsversionen av Azure Functions Runtime][7]

1. Klicka på fliken **Databas,** ange anslutningsinformation för SQL Server-instansen, inklusive att ange en [databashuvudnyckel](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)och klicka på **Verkställ**.  Anslutning till en SQL Server-instans krävs för att Azure Functions Runtime ska kunna skapa en databas som stöder körningen.

    ![Azure Functions Runtime-förhandsgranskningsdatabaskonfiguration][8]

1. Klicka på fliken **Autentiseringsuppgifter.**  Här måste du skapa två nya autentiseringsuppgifter för användning med en filresurs för att vara värd för alla dina funktionsappar.  Ange kombinationer **av användarnamn** och **lösenord** för **filresursägaren** och för **filresursanvändaren**och klicka sedan på **Använd**.

    ![Förhandsautentiseringsuppgifter för Azure Functions Runtime][9]

1. Klicka på fliken **Fildelning.**  Här måste du ange information om filresursplatsen.  Filresursen kan skapas åt dig eller så kan du använda en befintlig filresurs och klicka på **Använd**.  Om du väljer en ny filresursplats måste du ange en katalog som ska användas av Azure Functions Runtime.

    ![Azure Functions Runtime förhandsgranskande filresurs][10]

1. Klicka på fliken **IIS.**  På den här fliken visas information om de webbplatser i IIS som konfigurationsverktyget för Azure Functions Runtime skapar.  Du kan ange ett anpassat DNS-namn här för förhandsversionen av Azure Functions Runtime.  Klicka på **Använd** för att slutföra.

    ![Azure Functions Runtime preview IIS][11]

1. Klicka på fliken **Tjänster.**  På den här fliken visas status för tjänsterna i konfigurationsverktyget för Azure Functions Runtime.  Om **Azure Functions Host Activation Service** inte körs efter den första konfigurationen klickar du på Starta **tjänst**.

    ![Azure Functions Runtime preview-konfigurationen har slutförts][12]

1. Bläddra till **Azure Functions Runtime Portal** som `https://<machinename>.<domain>/`.

    ![Förhandsportal för Azure Functions Runtime][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Skapa din första funktion i förhandsversionen av Azure Functions Runtime

Så här skapar du din första funktion i förhandsversionen av Azure Functions Runtime

1. Bläddra till **Azure Functions Runtime Portal** som `https://<machinename>.<domain>` till exempel `https://mycomputer.mydomain.com`.

1. Du uppmanas att **logga in**, om distribueras i en domän använder ditt användarnamn och lösenord för domänkontot, annars använder du ditt lokala konto användarnamn och lösenord för att logga in på portalen.

    ![Azure Functions Runtime preview portal inloggning][14]

1. Om du vill skapa funktionsappar måste du skapa en prenumeration.  Klicka på **+** alternativet bredvid prenumerationerna i det övre vänstra hörnet på portalen.

    ![Azure Functions Runtime preview portal prenumerationer][15]

1. Välj **DefaultPlan,** ange ett namn för din prenumeration och klicka på **Skapa**.

    ![Azure Functions Runtime preview portal prenumerationsplan och namn][16]

1. Alla funktionsappar visas i portalens vänstra fönsterruta.  Om du vill skapa en ny funktionsapp **+** markerar du rubriken **Funktionsappar** och klickar på alternativet.

1. Ange ett namn för din funktionsapp, välj rätt prenumeration, välj vilken version av Azure Functions-körningen du vill programmera mot och klicka på **Skapa**

    ![Azure Functions Runtime preview portal ny funktion app][17]

1. Den nya funktionsappen visas i portalens vänstra fönsterruta.  Välj Funktioner och klicka sedan på **Ny funktion** högst upp i mittenfönstret i portalen.

    ![Förhandsmallar för Azure Functions Runtime][18]

1. Välj funktionen Timer Trigger, i det utfällbara namnet till `*/5 * * * * *` höger som din funktion och ändra schema till (det här cron-uttrycket gör att timerfunktionen körs var femte sekund) och klicka på **Skapa**

    ![Azure Functions Runtime förhandsgranska ny timerfunktionskonfiguration][19]

1. Din funktion har nu skapats.  Du kan visa körningsloggen för din funktionsapp genom att expandera **loggfönstret** längst ned på portalen.

    ![Azure Functions Runtime preview-funktionen körs][20]

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
