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
ms.openlocfilehash: b6078ba7b553773294ebbf11949f7d3b53f46f0c
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>Installera förhandsversionen av Azure Functions-Runtime

Om du vill installera Azure Functions-Runtime-förhandsgranskning måste du följa dessa steg:

1. Se till att din dator skickar minimikraven.
1. Hämta den [Azure Functions-Runtime Preview Installer](https://aka.ms/azafr).
1. Installera Azure Functions-Runtime-förhandsgranskning.
1. Slutföra konfigurationen av förhandsversionen av Azure Functions-Runtime.

## <a name="prerequisites"></a>Krav

Innan du installerar Azure Functions-Runtime-förhandsversionen måste du ha följande:

1. En dator som kör Microsoft Windows Server 2016 eller Microsoft Windows 10 skapare Update (Professional och Enterprise Edition).
1. En SQL Server-instans som körs i nätverket.  Lägsta version som krävs är SQL Server Express.

## <a name="install-the-azure-functions-runtime-preview"></a>Installera förhandsversionen av Azure Functions-Runtime

Azure Functions Runtime Preview Installer vägleder dig genom installationen av Azure Functions-Runtime preview Management- och arbetsroller.  Det är möjligt att installera rollen hantering och Worker på samma dator.  När du lägger till fler funktioner, måste du distribuera flera arbetsroller på ytterligare datorer för att kunna skala dina funktioner på flera arbetare.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Installera hanterings- och Worker-rollen på samma dator

1. Kör installationsprogrammet för Azure Functions Runtime Preview.

    ![Azure Functions-Runtime Preview Installer][1]

1. Klicka på **Nästa**.
1. När du har läst villkoren i den **EULA**, **Markera kryssrutan** att acceptera villkoren och klicka på **nästa** att gå vidare.
1. Nu välja de roller som du vill installera på den här datorn **funktioner Ledningsroll** och/eller **funktioner Arbetsrollen** och på **nästa**.

    ![Azure Functions-Runtime Preview-installationsprogram - urval för Systemroll][3]

    > [!NOTE]
    > Du kan installera den **funktioner Arbetsrollen** på andra datorer. Om du vill göra det, följer du dessa instruktioner och välj endast **funktioner Arbetsrollen** i installationsprogrammet.

1. Klicka på **nästa** har den **installationsguiden för Azure Functions-Runtime** påbörja installationsprocessen på din dator.
1. När installationen är klar startar installationsguiden den **Azure Functions-Runtime** -konfigurationsverktyget.

    ![Azure Functions Runtime Preview Installer klar][5]

    > [!NOTE]
    > Om du installerar på **Windows 10** och **behållare** funktionen har inte aktiverats tidigare, den **installationsprogram för Azure Functions-Runtime** uppmanas du att starta om datorn att slutföra installationen.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurera Azure Functions-Runtime

Azure Functions-Runtime-installationen ska slutföras måste du slutföra konfigurationen.

1. Den **Azure Functions-Runtime** konfigurationsverktyget visas vilka roller som är installerade på datorn.

    ![Azure Functions-Runtime Preview-konfigurationsverktyget][6]

1. Klicka på den **databasen** , ange anslutningsinformationen för SQL Server-instans och klicka på **tillämpa**.  Detta är nödvändigt för Azure Functions-Runtime att skapa en databas för att stödja körningsmiljön.
    
    ![Azure Functions Runtime Preview databaskonfigurationen][7]

1. Klicka på den **autentiseringsuppgifter** fliken.  På den här sidan måste du skapa två nya autentiseringsuppgifter för användning med en filresurs som värd för dina Azure-funktioner.  Ange **användarnamn** och **lösenord** kombinationer för den **resursen filägaren** och för den **filen resursen användaren**, klicka sedan på **Gäller**.

    ![Azure Functions-Runtime Preview autentiseringsuppgifter][8]

1. Klicka på den **filresurs** fliken.  I den här skärmen måste du ange information om den **filresurs** plats.  Detta kan skapas för dig eller Använd en befintlig resurs och på **tillämpa**.  Om du väljer en ny filresurs-plats måste du ange en katalog för användning av Azure Functions-Runtime.
    
    ![Azure Functions Runtime Preview filresurs][9]

1. Klicka på den **IIS** fliken.  Den här fliken visas information om webbplatserna i IIS som kommer att skapas i Azure Functions-Runtime-konfigurationsverktyget.  Klicka på **tillämpa** ska slutföras.

    ![Förhandsgranska Azure Functions-Runtime IIS][10]

1. Klicka på den **Services** fliken.  Den här fliken visas status för tjänsterna i Azure Functions-Runtime-konfigurationsverktyget.  Om efter den första konfigurationen av **Azure Functions värdtjänsten aktivering** körs inte klicka **starta tjänsten**.

    ![Azure Functions Runtime Preview Configruation klar][11]

1. Slutligen Bläddra till den **Azure Functions-Runtime portalen** som `https://<machinename>/`.

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
