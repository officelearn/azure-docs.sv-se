---
title: Skapa ett labb i Azure DevTest Labs | Microsoft Docs
description: "Skapa ett labb i Azure DevTest Labs för virtuella datorer"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/30/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 015782373e59d1aaf10a7b089c84c982031b36b2
ms.contentlocale: sv-se
ms.lasthandoff: 05/31/2017


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Skapa ett labb i Azure DevTest Labs
Ett labb i Azure DevTest Labs är den infrastruktur som omfattar en grupp med resurser, till exempel virtuella datorer (VM), som låter dig hantera resurserna genom att ange gränser och kvoter. Den här artikeln beskriver steg för steg hur du skapar ett labb med hjälp av Azure CLI.

## <a name="prerequisites"></a>Krav
Du behöver följande om du vill skapa ett labb:

* En Azure-prenumeration. Mer information om köpalternativ för Azure finns i [Så här köper du Azure](https://azure.microsoft.com/pricing/purchase-options/) eller [Kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/). För att kunna skapa labbet måste du vara prenumerationens ägare.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Steg för att skapa ett labb i Azure DevTest Labs
Följande steg illustrerar hur du använder Azure-portalen för att skapa ett labb i Azure DevTest Labs. 

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. På huvudmenyn på vänster sida väljer du **Fler tjänster** (längst ned i listan).

    ![Menyalternativet Fler tjänster](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. I listan över tillgängliga tjänster: **DevTest Labs**.
1. På bladet **DevTest Labs** väljer du **Lägg till**.
   
    ![Lägga till ett labb](./media/devtest-lab-create-lab/add-lab-button.png)

1. På bladet **Skapa ett DevTest-labb**:
   
    1. Ange **Labbnamn** för det nya labbet.
    2. Välj den **prenumeration** som du vill koppla till labbet.
    3. Välj en **plats** där du vill lagra labbet.
    4. Välj **Automatisk avstängning** för att ange om du vill aktivera, och definiera parametrar för, automatisk avstängning av alla labbets virtuella datorer. Funktionen automatisk avstängning är främst en kostnadfunktion där du kan ange om du vill att den virtuella datorn ska stängas automatiskt. Du kan ändra inställningarna för automatisk avstängning när du har skapat labbet genom att följa anvisningarna i artikeln [Hantera alla principer för ett labb i Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    5. Välj **Fäst på instrumentpanelen** om du vill att en genväg till labbet ska visas på instrumentpanelen i portalen.
    6. Välj **Automatiseringsalternativ** för att hämta Azure Resource Manager-mallar för konfigurationsautomatisering. 
    7. Välj **Skapa**. När du har valt **Skapa** visas bladet**DevTest Labs**. Du kan övervaka statusen för labbprocessen genom att titta på **meddelande**området. Uppdatera sidan om du vill se den nyligen skapade testmiljön i listan över labbar när den har slutförts.  
    
    ![Skapa ett blad för labbet](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat labbet kan du fundera på följande steg:

* [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md).
* [Ange principer för labbet](devtest-lab-set-lab-policy.md).
* [Skapa en labbmall](devtest-lab-create-template.md).
* [Skapa anpassade artefakter för dina virtuella datorer](devtest-lab-artifact-author.md).
* [Lägga till en virtuell dator med artefakter i ett labb](devtest-lab-add-vm-with-artifacts.md).


