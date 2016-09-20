<properties
    pageTitle="Skapa ett labb i DevTest Labs | Microsoft Azure"
    description="Skapa ett nytt labb i DevTest Labs för virtuella datorer"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# Skapa ett labb i Azure DevTest Labs

## Krav

Om du vill skapa ett labb behöver du följande:

- En Azure-prenumeration. Mer information om köpalternativ för Azure finns i [Så här köper du Azure](https://azure.microsoft.com/pricing/purchase-options/) eller [Kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/). För att kunna skapa labbet måste du vara prenumerationens ägare.
- En Azure-resursgrupp för labbet. Mer information finns i [Översikt av Azure Resource Manager](../resource-group-overview.md) och [Rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-control-configure.md).

## Skapa ett labb

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Bläddra**.

1. Välj **DevTest Labs** från listan.

1. På bladet **DevTest Labs** väljer du **Lägg till**.

    ![Lägga till ett labb](./media/devtest-lab-create-lab/add-lab-button.png)

1. På bladet **Skapa ett DevTest-labb**:

    1. Ange **Labbnamn** för det nya labbet.
    1. Välj den **prenumeration** som du vill koppla till labbet.
    1. Välj en **plats** där du vill lagra labbet.
    1. Välj **Skapa**.

    ![Skapa ett blad för labbet](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Nästa steg

När du har skapat labbet kan du fundera på följande steg:

- [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md).

- [Ange principer för labbet](devtest-lab-set-lab-policy.md).

- [Skapa en labbmall](devtest-lab-create-template.md).

- [Skapa anpassade artefakter för dina virtuella datorer](devtest-lab-artifact-author.md).

- [Lägga till en virtuell dator med artefakter i ett labb](devtest-lab-add-vm-with-artifacts.md).


<!--HONumber=sep16_HO1-->


