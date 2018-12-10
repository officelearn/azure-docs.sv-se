---
title: Skapa ett labb med Azure DevTest Labs | Microsoft Docs
description: I snabbstarten skapar du ett labb med hjälp av Azure DevTest Labs.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: ee2def6287a845cd0fd0260254efb20f9638ab2c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839049"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>Självstudie: Konfigurera ett labb med hjälp av Azure DevTest Labs
I självstudien skapar du ett labb med hjälp av Azure Portal. En labbadministratör konfigurerar ett labb i en organisation, skapar virtuella datorer i labbet och konfigurerar principer. Labbanvändarna (till exempel utvecklare och testare) gör anspråk på de virtuella datorerna i labbet, ansluter till dem och använder dem. 

I de här självstudierna gör du följande:

> [!div class="checklist"]
> * Skapa ett labb
> * Lägga till virtuella datorer (VM) i labbet
> * Lägga till en användare i rollen Labbanvändare

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-lab"></a>Skapa ett labb
Följande steg illustrerar hur du använder Azure-portalen för att skapa ett labb i Azure DevTest Labs. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I huvudmenyn på vänster sida väljer du **Skapa en resurs** (överst i listan), pekar på **Utvecklarverktyg** och klickar på **DevTest Labs**. 

    ![Menyn Nytt DevTest Lab](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. Utför följande steg i fönstret **Skapa ett DevTest Lab**: 
    1. Som **Labbnamn** anger du ett namn på labbet. 
    2. I **Prenumeration** väljer du den prenumeration som du vill skapa det nya labbet i. 
    3. Som **Resursgrupp** väljer du **Skapa ny** och anger ett namn på resursgruppen. 
    4. Som **Plats** väljer du den plats/region där du vill att labbet ska skapas. 
    5. Välj **Skapa**. 
    6. Välj **fäst till instrumentpanelen**. När du har skapat labbet visas det i instrumentpanelen. 

        ![Skapa ett labbavsnitt för DevTest Labs](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)

## <a name="add-a-vm-to-the-lab"></a>Lägga till en virtuell dator i labbet

1. På sidan **DevTest Lab** väljer du **+ Lägg till** i verktygsfältet. 

    ![Knappen Lägg till](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. På sidan för att **välja en bas** kan du söka med ett nyckelord (till exempel Windows eller Ubuntu) och välja en av basavbildningarna i listan. 
1. På sidan **Virtuell dator** gör du följande: 
    1. Som **Virtuellt datornamn** anger du ett namn på den virtuella datorn. 
    2. Som **Användarnamn** anger du namnet på den användare som har åtkomst till den virtuella datorn. 
    3. I **Ange ett värde** anger du lösenordet för användaren. 
    4. Välj **Avancerade inställningar**.
    5. I **Gör den här datorn anspråksbar** väljer du **Ja**.
    6. Kontrollera att **instansantalet** är **1**. Om du anger det till **2** kommer två virtuella datorer skapas med namnen: `<base image name>00' and <base image name>01`. Exempelvis: `win10vm00` och `win10vm01`. 
    7. Stäng sidan **Avancerat** genom att klicka på **OK**. 
    8. Välj **Skapa**. 

        ![Välja en bas](./media/tutorial-create-custom-lab/new-virtual-machine.png)
    9. Du kan se status för den virtuella datorn i listan med **Virtuella datorer som kan tas i anspråk**. Att skapa den virtuella datorn tar cirka 25 minuter. Den virtuella datorn skapas i en separat Azure-resursgrupp, vars namn börjar med namnet på den aktuella resursgrupp där labbet finns. Om labbet till exempel finns i `labrg`, skapas den virtuella datorn i resursgruppen `labrg3988722144002`. 

        ![Skapandestatus för virtuell dator](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. När den virtuella datorn har skapats kan du se den i listan med **Virtuella datorer som kan tas i anspråk**. 

    > [!NOTE] 
    > När du lägger till en virtuell Linux-dator till ett labb kan du aktivera SSH och RDP-åtkomst till den virtuella datorn. Om du inte aktiverar åtkomst när du skapar den virtuella datorn kan du manuellt lägga till regler i den nätverkssäkerhetsgrupp som är associerad med den virtuella datorn för att öppna portar för SSH och RDP.

## <a name="add-a-user-to-the-lab-user-role"></a>Lägga till en användare i rollen Labbanvändare

1. Välj **Konfiguration och principer** i den vänstra menyn. 

    ![Konfiguration och principer](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. Välj **Åtkomstkontroll (IAM)** i menyn och välj **+ Lägg till rolltilldelning** i verktygsfältet. 
1. På sidan **Lägg till behörigheter** gör du följande:
    1. Som **Roll** väljer du **DevTest Labs-användare**. 
    2. Välj den **användare** som du vill lägga till. 
    3. Välj **Spara**.
4. Stäng **Konfiguration och principer – Åtkomstkontroll (IAM)** genom att välja **X** i det högra hörnet. 

## <a name="cleanup-resources"></a>Rensa resurser
I nästa självstudie visar vi hur en labbanvändare kan göra anspråk på och ansluta till en virtuell dator i labbet. Om du inte vill göra självstudien och rensa de resurser som skapats som en del av självstudien följer du dessa steg: 

1. I Azure Portal väljer du **Resursgrupper** i menyn. 
2. Välj den resursgrupp där du skapade labbet. 
3. Välj **Ta bort resursgrupp** i verktygsfältet. När du tar bort en resursgrupp tas alla resurser i gruppen bort, inklusive labbet. 
4. Upprepa dessa steg för att ta bort ytterligare resursgrupper som skapats med namnet `<your resource group name><random numbers>`. Till exempel: `splab3988722144001`. Virtuella datorer skapas i den här resursgruppen i stället för resursgruppen där labbet finns. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien skapade du ett labb med en virtuell dator och gav en användare åtkomst till labbet. Mer information om hur du får åtkomst till labbet som en labbanvändare finns i nästa självstudie:

> [!div class="nextstepaction"]
> [Självstudie: Åtkomst till labb](tutorial-use-custom-lab.md)

