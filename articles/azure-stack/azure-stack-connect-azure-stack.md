<properties
    pageTitle="Ansluta till Microsoft Azure Stack POC | Microsoft Azure"
    description="Lär dig hur du ansluter till Azure Stack POC-portalen som en tjänstadministratör eller klient."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/01/2016"
    ms.author="erikje"/>


# Logga in på den virtuella datorn i Azure Stack POC

Du kan logga in till den virtuella datorn i Azure Stack POC som en

- [**tjänstadministratör**](#log-in-as-a-service-administrator) om du vill hantera resursproviders, klienterbjudanden, planer, tjänster, kvoter och priser.

eller

- [**klient**](#log-in-as-a-tenant) om du vill etablera, övervaka och hantera tjänster som du prenumererar på, t.ex. Web Apps, lagring och virtuella datorer.

## Logga in som en tjänstadministratör

1.  Logga in på den fysiska Azure Stack POC-datorn.

2.  Öppna en fjärrskrivbordsanslutning till den virtuella klientdatorn genom att dubbelklicka på skrivbordsikonen för **ClientVM.AzureStack.local.rdp**.
 
    ![](media/azure-stack-connect-azure-stack/clientvmazurestacklocalicon.png)
    
    När du gör det används automatiskt AzureStack\\AzureStackUser-kontot som skapades med distributionsskriptet. Använd administratörslösenordet som du angav i steg 5 i skriptprocessen när du uppmanas att **ange lösenordet för det inbyggda administratörskontot**.

3.  Öppna [portalen](azure-stack-key-features.md#portal)genom att dubbelklicka på ikonen för **Microsoft Azure Stack POC-portalen** på ClientVM.AzureStack.local-datorn (https://portal.azurestack.local/).

    ![](media/azure-stack-connect-azure-stack/microsoftazurestackpocprtalicon.png)

4.  Logga in med tjänstadministratörskontot.

## Logga in som en klient

En tjänstadministratör kan logga in som en klient för att testa de planer, erbjudanden och prenumerationer som deras klientorganisationer använder.
Om du inte redan har ett klientkonto [skapar du ett](azure-stack-add-new-user-aad.md) innan du loggar in.

1.  Logga in på den fysiska Azure Stack-datorn.

2.  Öppna en fjärrskrivbordsanslutning till den virtuella klientdatorn genom att dubbelklicka på skrivbordsikonen för **ClientVM.AzureStack.local.rdp**. 

    ![](media/azure-stack-connect-azure-stack/clientvmazurestacklocalicon.png)

    När du gör det används automatiskt AzureStack\\AzureStackUser-kontot som skapades med distributionsskriptet. Använd administratörslösenordet som du angav i steg 5 i skriptprocessen när du uppmanas att **ange lösenordet för det inbyggda administratörskontot**.

3.  Öppna [portalen](azure-stack-key-features.md#portal)genom att dubbelklicka på ikonen för **Microsoft Azure Stack POC-portalen** på ClientVM.AzureStack.local-datorn (https://portal.azurestack.local/).

    ![](media/azure-stack-connect-azure-stack/microsoftazurestackpocprtalicon.png)

4.  Logga in med ett klientkonto.

RDP kan begränsa hur många användare som kan komma åt den fysiska Microsoft Azure POC-värden. Om du vill aktivera flera användare läser du [Aktivera flera samtidiga användaranslutningar](azure-stack-enable-multiple-concurrent-users.md).

## Nästa steg

[De första uppgifterna](azure-stack-first-scenarios.md)



<!--HONumber=Sep16_HO3-->


