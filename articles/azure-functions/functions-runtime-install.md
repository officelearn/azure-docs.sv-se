---
title: Azure Functions-Runtime-Installation | Microsoft Docs
description: "Så här installerar du Azure Functions-Runtime"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: 1e4188313a87d07f396e5f8edc8969dd5da2c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>Installera förhandsversionen av Azure Functions-Runtime

Om du vill installera Azure Functions-Runtime-förhandsgranskning måste du följa dessa steg:

1. Se till att din dator skickar de lägsta kraven
1. Hämta den [Azure Functions-Runtime Preview Installer](https://aka.ms/azafr). 
1. Installera Azure Functions-Runtime-förhandsgranskning
1. Slutföra konfigurationen av förhandsversionen av Azure Functions-Runtime

## <a name="prerequisites"></a>Krav

Innan du installerar Azure Functions-Runtime-förhandsversionen måste du ha följande:

1. En dator som kör Microsoft Windows Server 2016 eller Microsoft Windows 10 skapare Update (Professional och Enterprise Edition).
1. En SQL Server-instans som körs i nätverket.  Krav på lägsta version är SQL Server Express.

## <a name="install-the-azure-functions-runtime-preview"></a>Installera förhandsversionen av Azure Functions-Runtime

Azure Functions-Runtime preview installationsprogrammet vägleder dig genom installationen av Azure Functions-Runtime preview Management- och arbetsroller.  Det är möjligt att installera rollen hantering och Worker på samma dator.  När du lägger till fler funktioner, måste du distribuera flera arbetsroller på ytterligare datorer för att kunna skala dina funktioner på flera arbetare.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Installera hanterings- och Worker-rollen på samma dator

1. Kör installationsprogrammet för Azure Functions Runtime Preview.

    ![Azure Functions-Runtime Preview Installer][1]

1. **Klicka på nästa** förskott förbi den första fasen av installationsprogrammet
1. När du har läst villkoren i den **EULA**, **Markera kryssrutan** att acceptera villkoren och **Klicka på nästa** att gå vidare.
1. Nu välja de roller som du vill installera på den här datorn **funktioner Ledningsroll** och/eller **funktioner Arbetsrollen** och **Klicka på nästa**

    ![Azure Functions-Runtime Preview-installationsprogram - urval för Systemroll][3]

    > [!NOTE]
    > Du kan installera den **funktioner Arbetsrollen** på många andra datorer för att göra det, följer du anvisningarna och välj endast **funktioner Arbetsrollen** i installationsprogrammet.

1. **Klicka på nästa** har den **Azure Functions Runtime Installer** installera på datorn.
1. När du är klar startar installationsprogrammet den **konfigurationsverktyget för Azure Functions-Runtime**.

    ![Azure Functions Runtime Preview Installer klar][5]

    > [!NOTE]
    > Om du installerar på **Windows 10** och **behållare** funktionen har inte aktiverats tidigare, den **Azure Functions-Runtime** installationsprogrammet uppmanas du att starta om datorn för att slutföra installationen.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurera Azure Functions-Runtime

Azure Functions-Runtime-installationen ska slutföras måste du slutföra konfigurationen.

1. Den **konfigurationsverktyget för Azure Functions-Runtime** visas vilka roller som är installerade på datorn.

    ![Azure Functions-Runtime Preview-konfigurationsverktyget][6]

1. Klicka på den **databasen** ange den **anslutningsinformationen för SQL Server-instans** och **på Verkställ**.  Detta krävs för att Azure Functions-Runtime att skapa en databas för att stödja körningsmiljön.
    
    ![Azure Functions Runtime Preview databaskonfigurationen][7]

1. Klicka på den **autentiseringsuppgifter** fliken.  På den här sidan måste du skapa två nya autentiseringsuppgifter för användning med en filresurs som värd för dina Azure-funktioner.  **Ange användarnamn och lösenord** kombinationer för den **resursen filägaren** och för den **filen resursen användaren** och klicka på **tillämpa**.

    ![Azure Functions-Runtime Preview autentiseringsuppgifter][8]

1. Klicka på den **filresurs** fliken.  I den här skärmen måste du ange information om den **filresurs plats**.  Detta kan skapas för dig eller Använd en befintlig resurs och på **tillämpa**.  Om du väljer en ny filresurs-plats måste du ange en katalog för användning av Azure Functions-Runtime.
    
    ![Azure Functions Runtime Preview filresurs][9]

1. Klicka på den **IIS** fliken.  Den här fliken visas information om webbplatserna i IIS som kommer att skapas i Azure Functions Runtime-Installation.  **Klicka på Verkställ** ska slutföras.

    ![Förhandsgranska Azure Functions-Runtime IIS][10]

1. Klicka på den **Services** fliken.  Den här fliken visas status för tjänsterna i Azure Functions-Runtime-installationen.  Om efter den första konfigurationen av **Azure Functions värdtjänsten aktivering** körs inte klicka **starta tjänsten**

    ![Azure Functions Runtime Preview Configruation klar][11]

1. Slutligen Bläddra till den **Azure Functions-Runtime portalen** som`https://<machinename>/`

    ![Azure Functions-Runtime Preview-portalen][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png