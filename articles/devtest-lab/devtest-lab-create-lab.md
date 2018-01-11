---
title: Skapa ett labb i Azure DevTest Labs | Microsoft Docs
description: "Skapa ett labb i Azure DevTest Labs för virtuella datorer"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: v-craic
ms.openlocfilehash: 3fd1f0ca01e9a800eaf3ba9843c7e3165023ccef
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Skapa ett labb i Azure DevTest Labs
Ett labb i Azure DevTest Labs är den infrastruktur som omfattar en grupp med resurser, till exempel virtuella datorer (VM), som låter dig hantera resurserna genom att ange gränser och kvoter. Den här artikeln beskriver steg för steg hur du skapar ett labb med hjälp av Azure CLI.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du behöver följande om du vill skapa ett labb:

* En Azure-prenumeration. Mer information om köpalternativ för Azure finns i [Så här köper du Azure](https://azure.microsoft.com/pricing/purchase-options/) eller [Kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/). För att kunna skapa labbet måste du vara prenumerationens ägare.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Steg för att skapa ett labb i Azure DevTest Labs
Följande steg illustrerar hur du använder Azure-portalen för att skapa ett labb i Azure DevTest Labs. 

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. På huvudmenyn på vänster sida väljer du **Fler tjänster** (längst ned i listan).

    ![Menyalternativet Fler tjänster](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. I listan över tillgängliga tjänster: **DevTest Labs**.
1. Välj **Lägg till** i **DevTest Labs**.
   
    ![Lägga till ett labb](./media/devtest-lab-create-lab/add-lab-button.png)

1. Under **Create a DevTest Lab** (skapa ett DevTest-labb):
   
    1. Ange **Labbnamn** för det nya labbet.
    2. Välj den **prenumeration** som du vill koppla till labbet.
    3. Välj en **plats** där du vill lagra labbet.
    4. Välj **Automatisk avstängning** för att ange om du vill aktivera, och definiera parametrar för, automatisk avstängning av alla labbets virtuella datorer. Funktionen automatisk avstängning är främst en kostnadfunktion där du kan ange om du vill att den virtuella datorn ska stängas automatiskt. Du kan ändra inställningarna för automatisk avstängning när du har skapat labbet genom att följa anvisningarna i artikeln [Hantera alla principer för ett labb i Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Ange **NAME** (namn) och **VALUE** (värde) för **Tags** (taggar) om du vill skapa anpassade taggar som läggs till i alla resurser som du skapar i labbet. Taggar är användbara för att hantera och ordna labbresurser efter kategori. Mer information om taggar, inklusive hur du lägger till taggar när du har skapat labbet, finns i [Lägga till taggar i ett labb](devtest-lab-add-tag.md).
    5. Välj **Fäst på instrumentpanelen** om du vill att en genväg till labbet ska visas på instrumentpanelen i portalen.
    6. Välj **Automatiseringsalternativ** för att hämta Azure Resource Manager-mallar för konfigurationsautomatisering. 
    7. Välj **Skapa**. Du kan övervaka statusen för labbprocessen genom att titta på **meddelande**området. Uppdatera sidan om du vill se den nyligen skapade testmiljön i listan över labbar när den har slutförts.  
    
    ![Skapa ett labbavsnitt för DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat labbet kan du fundera på följande steg:

* [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md)
* [Ange principer för labbet](devtest-lab-set-lab-policy.md)
* [Skapa en labbmall](devtest-lab-create-template.md)
* [Skapa anpassade artefakter för virtuella datorer](devtest-lab-artifact-author.md)
* [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)

