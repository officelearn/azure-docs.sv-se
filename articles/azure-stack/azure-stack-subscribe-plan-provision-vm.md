<properties
    pageTitle="Prenumerera på ett erbjudande och etablera sedan en virtuell dator i Azure Stack (klient) | Microsoft Azure"
    description="Lär dig hur du prenumererar på ett erbjudande och sedan etablerar en virtuell dator i Azure Stack som en klient."
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
    ms.date="08/15/2016"
    ms.author="erikje"/>


# Prenumerera på ett erbjudande

Nu när du har [skapat ett erbjudande](azure-stack-create-offer.md) testar du att dina klienter kan skapa en prenumeration.

1.  På Azure Stack POC-datorn loggar du in på `https://portal.azurestack.local` som [en klient](azure-stack-connect-azure-stack.md#log-in-as-a-tenant) och klickar på **Skaffa en prenumeration**.

    ![](media/azure-stack-subscribe-plan-provision-vm/image1.png)

2.  Skriv ett namn för prenumerationen i fältet **Visningsnamn**.

    ![](media/azure-stack-subscribe-plan-provision-vm/image2.png)

3.  Klicka på **Erbjudande** och kontrollera att erbjudandet som du skapade visas på bladet **Choose an offer** (Välj ett erbjudande). Klicka på erbjudandet, klicka på **Välj** och sedan på **OK**.  

    ![](media/azure-stack-subscribe-plan-provision-vm/image3.png)

4.  Visa prenumerationen som du precis har skapat genom att klicka på **Bläddra** och sedan på **Prenumerationer**.  

    ![](media/azure-stack-subscribe-plan-provision-vm/image4.png)

När du har valt att prenumerera på ett erbjudande väntar du tills prenumerationsstatusen är Synkroniserad. Uppdatera sedan portalen för att se vilka tjänster som ingår i den nya prenumerationen.




## Nästa steg

[Lagringskonton](azure-stack-provision-storage-account.md)



<!--HONumber=Sep16_HO3-->


