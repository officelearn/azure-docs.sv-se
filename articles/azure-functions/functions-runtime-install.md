---
title: Azure Functions Runtime-Installation | Microsoft Docs
description: Så här installerar du Azure Functions Runtime preview 2
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: aae6bc41f3c2fc2c5f8cf63d07f6b4d79bb3564a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023207"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Installera Azure Functions Runtime preview 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Om du vill installera Azure Functions Runtime preview 2 följer du dessa steg:

1. Se till att din dator skickar minimikraven.
1. Ladda ned den [Azure Functions Runtime Preview Installer](https://aka.ms/azafrv2).
1. Avinstallera Azure Functions Runtime preview 1.
1. Installera Azure Functions Runtime preview 2.
1. Slutför konfigurationen av Azure Functions Runtime preview 2.
1. Skapa din första funktion i förhandsversionen av Azure Functions Runtime

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du installerar förhandsversionen av Azure Functions-körning måste du ha följande resurser som är tillgängliga:

1. En dator som kör Microsoft Windows Server 2016 eller Microsoft Windows 10 Creators Update (Professional eller Enterprise Edition).
1. En SQL Server-instans som körs i ditt nätverk.  Lägsta versionen som krävs är SQL Server Express.

## <a name="uninstall-previous-version"></a>Avinstallera tidigare Version

Om du tidigare har installerat förhandsversionen av Azure Functions-körning måste du avinstallera dem innan du installerar den senaste versionen.  Avinstallera förhandsversionen av Azure Functions Runtime genom att ta bort programmet i Lägg till/ta bort program på Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Installera Azure Functions Runtime Preview

Azure Functions Runtime Preview installationsprogrammet vägleder dig genom installationen av Azure Functions Runtime preview hantering och Worker-roller.  Det går att installera hanterings- och Worker-rollen på samma dator.  När du lägger till flera funktionsappar, måste du distribuera flera worker-roller på ytterligare datorer för att kunna skala dina funktioner på flera arbetare.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Installera hanterings- och Worker-rollen på samma dator

1. Kör installationsprogrammet för Azure Functions Runtime Preview.

    ![Azure Functions Runtime preview installer][1]

1. Klicka på **Nästa**.
1. När du har läst villkoren i den **EULA**, **kryssrutan** att acceptera villkoren och klicka på **nästa** att gå vidare.
1. Välj de roller som du vill installera på den här datorn **Functions Hanteringsroll** och/eller **Functions Arbetsroll** och klicka på **nästa**.

    ![Installationsprogram för Azure Functions Runtime preview - Systemroll][3]

    > [!NOTE]
    > Du kan installera den **Functions Arbetsroll** många andra datorer. Du gör detta genom att följa instruktionerna och bara välja **Functions Arbetsroll** i installationsprogrammet.

1. Klicka på **nästa** ha den **installationsguiden för Azure Functions Runtime** påbörja installationsprocessen på din dator.
1. När du är klar startar installationsguiden den **Azure Functions Runtime** konfigurationsverktyget.

    ![Azure Functions Runtime preview installationsprogrammet slutför][6]

    > [!NOTE]
    > Om du installerar på **Windows 10** och **behållare** funktionen har inte aktiverats tidigare, den **installationsprogrammet för Azure Functions Runtime** uppmanas du att starta om datorn att slutföra installationen.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurera Azure Functions-körning

Azure Functions Runtime-installationen ska slutföras måste du slutföra konfigurationen.

1. Den **Azure Functions Runtime** konfigurationsverktyget visar vilka roller är installerade på din dator.

    ![Azure Functions Runtime preview-konfigurationsverktyget][7]

1. Klicka på den **databasen** ange anslutningsinformationen för SQL Server-instansen, inklusive att ange en [databasens huvudnyckel](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), och klicka på **tillämpa**.  Anslutning till en SQL Server-instans krävs för Azure Functions Runtime att skapa en databas för körningen.

    ![Azure Functions Runtime preview databaskonfiguration][8]

1. Klicka på den **autentiseringsuppgifter** fliken.  Här kan skapa du två nya autentiseringsuppgifter för användning med en filresurs som värd för alla dina funktionsappar.  Ange **användarnamn** och **lösenord** kombinationer för den **filresursägaren** och för den **filresursanvändaren**, klicka sedan på **Gäller**.

    ![Autentiseringsuppgifter för Azure Functions Runtime preview][9]

1. Klicka på den **filresurs** fliken.  Ange information om in filresursens placering.  Filresursen kan skapas för dig eller du kan använda en befintlig filresurs och klicka på **tillämpa**.  Om du väljer en ny filresurs-plats måste du ange en katalog för användning av Azure Functions-körningen.

    ![Azure Functions Runtime preview-filresurs][10]

1. Klicka på den **IIS** fliken.  Den här fliken visar information om webbplatserna i IIS som skapar Azure Functions Runtime-konfigurationsverktyget.  Du kan ange ett anpassat DNS-namn för Azure Functions Runtime preview-portalen.  Klicka på **tillämpa** att slutföra.

    ![Azure Functions Runtime preview IIS][11]

1. Klicka på den **Services** fliken.  Den här fliken visas status för tjänsterna i Azure Functions Runtime-konfigurationsverktyget.  Om den **Azure Functions-värdtjänsten aktivering** är inte körs efter den första konfigurationen, klickar du på **starta tjänsten**.

    ![Azure Functions Runtime preview-konfiguration har slutförts][12]

1. Bläddra till den **Azure Functions Runtime Portal** som `https://<machinename>.<domain>/`.

    ![Azure Functions Runtime preview-portalen][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Skapa din första funktion i Azure Functions Runtime preview

Skapa din första funktion i Azure Functions Runtime preview

1. Bläddra till den **Azure Functions Runtime-portalen** som `https://<machinename>.<domain>` till exempel `https://mycomputer.mydomain.com`.

1. Du uppmanas att **logga in**, om de har distribuerats i en domän använder ditt konto domänanvändarnamn och lösenord, annars använder du ditt lokalt Kontoanvändarnamn och lösenord för inloggning på portalen.

    ![Azure Functions Runtime preview portal inloggning][14]

1. För att skapa funktionsappar, måste du skapa en prenumeration.  I det övre vänstra hörnet i portalen klickar du på den **+** alternativet bredvid prenumerationerna.

    ![Azure Functions Runtime preview portal-prenumerationer][15]

1. Välj **DefaultPlan**, ange ett namn för din prenumeration och på **skapa**.

    ![Azure Functions Runtime preview portal prenumerationsavtal och namn][16]

1. Alla dina funktionsappar visas i den vänstra rutan i portalen.  Om du vill skapa en ny Funktionsapp, Välj rubriken **Funktionsappar** och klicka på den **+** alternativet.

1. Ange ett namn för din funktionsapp, Välj rätt prenumeration, Välj vilken version av Azure Functions-runtime som du vill att programmera mot och klicka på **skapa**

    ![Azure Functions Runtime preview portal ny funktionsapp][17]

1. Din nya funktionsapp visas i det vänstra fönstret i portalen.  Välja funktioner och klicka sedan på **ny funktion** överst i den mittersta rutan i portalen.

    ![Azure Functions Runtime preview-mallar][18]

1. Välj Timer som utlösare-funktion, i den högra utfällt namnge din funktion och ändra schemat till `*/5 * * * * *` (den här cron-uttryck gör din timerfunktion som ska köras var femte sekund), och klicka på **skapa**

    ![Azure Functions Runtime preview nya timer funktionen konfigurationen][19]

1. Din funktion har skapats.  Du kan visa körningsloggen på din funktionsapp genom att expandera den **log** rutan längst ned i portalen.

    ![Azure Functions Runtime preview funktionen körs][20]

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
