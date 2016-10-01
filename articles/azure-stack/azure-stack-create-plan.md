<properties
    pageTitle="Skapa en plan i Azure Stack | Microsoft Azure"
    description="Som tjänstadministratör skapar du en plan som låter prenumeranter etablera virtuella datorer."
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


# Skapa en plan i Azure Stack

[Planer](azure-stack-key-features.md#services-plans-offers-and-subscriptions) är grupperingar av en eller flera tjänster. Som provider kan du skapa planer som du erbjuder dina klienter. Dina klienter prenumererar i sin tur på dina erbjudanden för att använda de planer och tjänster som ingår i dem. I det här exemplet ska du skapa en plan som innehåller beräknings-, nätverks- och lagringsresursproviders. Genom att göra det ger du dina prenumeranter möjlighet att etablera virtuella datorer.

1.  Gå till https://portal.azurestack.local i en webbläsare.

2.  [Logga in](azure-stack-connect-azure-stack.md#log-in-as-a-service-administrator) på Azure Stack-portalen som tjänstadministratör och ange dina autentiseringsuppgifter för tjänsten (det här är det konto som du skapade i steg 5 i avsnittet [Köra PowerShell-skriptet](azure-stack-run-powershell-script.md)) och klicka sedan på **Logga in**.

    Tjänstadministratörer kan skapa erbjudanden och planer och hantera användare.

3.  Om du vill skapa en plan och ett erbjudande som klienter kan prenumerera på börjar du med att klicka på **Nytt**.

    ![](media/azure-stack-create-plan/image1.png)

4.  Klicka på **Tenant Offers and Plans** (Erbjudanden och planer för klienter) på bladet Skapa och klicka sedan på **Plan**.

    ![](media/azure-stack-create-plan/image2.png)

5.  Fyll i **Visningsnamn** och **Resursnamn**. Visningsnamnet är det egna namnet för planen. Endast administratören kan se resursnamnet. Det är det namn som administratörer använder för att arbeta med planen som en Azure Resource Manager-resurs.

    ![](media/azure-stack-create-plan/image3.png)

6.  Välj eller skapa en ny **resursgrupp** som en behållare för planen. Som standard läggs alla planer och erbjudanden till i en resursgrupp med namnet OffersAndPlans.

7.  Klicka på **Tjänster som erbjuds**, använd Skift-tangenten om du vill markera alla tre providers (**beräkningsprovidern**, **lagringsprovidern** och **nätverksprovidern**) och klicka sedan på **Välj**.

    ![](media/azure-stack-create-plan/image4.png)

8.  Klicka på **Microsoft.Compute** och sedan på **Needs Configuration** (Kräver konfiguration).

    ![](media/azure-stack-create-plan/image5.png)

9.  På bladet **Set Quotas** (Ange kvoter) godkänner du standardvärdena, klickar på **OK** och sedan på **OK** igen.

    ![](media/azure-stack-create-plan/image6.png)

10. Klicka på **Microsoft.Network** och sedan på **Needs Configuration** (Kräver konfiguration).

    ![](media/azure-stack-create-plan/image7.png)

11. Markera alla kryssrutor på bladet **Set Quotas** (Ange kvoter), klicka på **OK** och sedan på **OK** igen.

    ![](media/azure-stack-create-plan/image8.png)

12. Klicka på **Microsoft.Storage**, klicka på **Needs Configuration** (Kräver konfiguration), acceptera standardvärdena på bladet **Set Quotas** (Ange kvoter), klicka på **OK**, klicka på **OK** igen och klicka sedan på **Create** (Skapa) för att skapa planen.

    ![](media/azure-stack-create-plan/image9.png)

13. Nu kan planen tas med i ett erbjudande. Visa aviseringar genom att klicka på klockan längst upp till höger.

    ![](media/azure-stack-create-plan/image10.png)

## Nästa steg

[Skapa ett erbjudande](azure-stack-create-offer.md)



<!--HONumber=Sep16_HO3-->


