<properties
    pageTitle="Skapa ett erbjudande i Azure Stack | Microsoft Azure"
    description="Lär dig hur du som tjänstadministratör skapar ett erbjudande för dina klienter i Azure Stack."
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
    ms.date="05/25/2016"
    ms.author="erikje"/>


# Skapa ett erbjudande i Azure Stack

[Erbjudanden](azure-stack-key-features.md#services-plans-offers-and-subscriptions) är grupper med en eller flera planer som leverantörer presenterar för klienterna att köpa (prenumerera på). I det här exemplet skapar du ett erbjudande som innehåller den [plan som du skapade](azure-stack-create-plan.md) i det senaste steget. Genom att ge erbjudandets prenumeranter möjligheten att etablera virtuella datorer.

1.  [Logga in](azure-stack-connect-azure-stack.md#log-in-as-a-service-administrator) på portalen som en tjänstadministratör.
    ![](media/azure-stack-create-offer/image1.png)

2.  Klicka på **Ny**.

3.  Klicka på **Klienterbjudanden och planer**, klicka därefter på **Erbjudande**.
    ![](media/azure-stack-create-offer/image2.png)

4.  Fyll i följande i bladet **Nytt erbjudande**:

    1.  Fyll i **Visningsnamn** och **Resursnamn**. Visningsnamnet är erbjudandets egna namn. Endast administratören kan se resursnamnet. Det är det namn som administratörer använder för att arbeta med erbjudandet som en Azure Resource Manager-resurs.

    2.  Välj en ny eller befintlig **Resursgrupp**.

        ![](media/azure-stack-create-offer/image3.png)

5.  Klicka på **Basplaner**, i bladet **Plan** välj du de planer som du vill inkludera i erbjudandet och klickar därefter på **Välj**. Klicka på **Skapa** för att skapa erbjudandet.

    ![](media/azure-stack-create-offer/image4.png)

6.  Klicka på **Ändra status** och klicka därefter på **Offentlig**.
Planer och erbjudanden måste offentliggöras för att klienterna ska få en fullständig vy när de prenumererar. Om en plan är privat och erbjudandet är offentligt kan klienterna få erbjudandet men kommer inte att kunna se information om planen. Planer och erbjudanden kan vara:

    -   **Offentliga**: Synliga för klienter.

    -   **Privata**: Endast synliga för tjänstadministratörer. Användbart vid utformningen av planen eller erbjudandet, eller om tjänstadministratören vill godkänna varje prenumeration.

    -   **Inaktiverad**: Stängda för nya prenumeranter. Administratören kan använda ”inaktiverad” för att förhindra framtida prenumerationer, men inte röra befintliga prenumeranter.

    ![](media/azure-stack-create-offer/image6.png)

Förändringar i planen eller erbjudandet är inte omedelbart synliga för klienten. För att kunna se ändringarna måste prenumerationens status vara InSync, och klienten måste därefter uppdatera portalen eller logga in/logga ut.

Även när en ytterligare prenumeration har skapats och är InSync måste du kanske logga ut/logga in för att se den nya prenumerationen i ”prenumerationsväljaren” när nya resurser/resursgrupper skapas.

## Nästa steg

[Prenumerera på ett erbjudande och etablera en VM](azure-stack-subscribe-plan-provision-vm.md)



<!--HONumber=Sep16_HO3-->


