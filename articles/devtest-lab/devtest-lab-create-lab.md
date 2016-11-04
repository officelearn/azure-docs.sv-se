---
title: Skapa ett labb i Azure DevTest Labs | Microsoft Docs
description: Skapa ett labb i Azure DevTest Labs för virtuella datorer
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: tarcher

---
# Skapa ett labb i Azure DevTest Labs
## Krav
Du behöver följande om du vill skapa ett labb:

* En Azure-prenumeration. Mer information om köpalternativ för Azure finns i [Så här köper du Azure](https://azure.microsoft.com/pricing/purchase-options/) eller [Kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/). För att kunna skapa labbet måste du vara prenumerationens ägare.

## Steg för att skapa ett labb i Azure DevTest Labs
Följande steg illustrerar hur du använder Azure-portalen för att skapa ett labb i Azure DevTest Labs. 

1. Logga in på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **Fler tjänster** och välj sedan **DevTest Labs** från listan.
3. På bladet **DevTest Labs** väljer du **Lägg till**.
   
    ![Lägga till ett labb](./media/devtest-lab-create-lab/add-lab-button.png)
4. På bladet **Skapa ett DevTest-labb**:
   
   1. Ange **Labbnamn** för det nya labbet.
   2. Välj den **prenumeration** som du vill koppla till labbet.
   3. Välj en **plats** där du vill lagra labbet.
   4. Välj **Automatisk avstängning** för att ange om du vill aktivera, och definiera parametrar för, automatisk avstängning av alla labbets virtuella datorer.
   5. Välj **Lagringstyp** för att ange disklagringstypen för labbets virtuella datorer. 
   6. Välj **Skapa**.
      
      ![Skapa ett blad för labbet](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Nästa steg
När du har skapat labbet kan du fundera på följande steg:

* [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md).
* [Ange principer för labbet](devtest-lab-set-lab-policy.md).
* [Skapa en labbmall](devtest-lab-create-template.md).
* [Skapa anpassade artefakter för dina virtuella datorer](devtest-lab-artifact-author.md).
* [Lägga till en virtuell dator med artefakter i ett labb](devtest-lab-add-vm-with-artifacts.md).

<!--HONumber=Sep16_HO3-->


