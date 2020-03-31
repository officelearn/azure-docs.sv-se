---
title: Ange lösenord för virtuella datorer i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du ställer in och återställer lösenord för virtuella datorer i klassrumslabben i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933827"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Konfigurera och hantera en pool för virtuella datorer 
I den här artikeln visas hur du utför följande uppgifter:

- Öka antalet virtuella datorer i labbet
- Starta alla virtuella datorer eller valda virtuella datorer 
- Återställa virtuella datorer

## <a name="update-the-lab-capacity"></a>Uppdatera labbkapaciteten
Så här ökar eller minskar du labbkapaciteten (antalet virtuella datorer i ett labb):

1. På **poolsidan för virtuella datorer** väljer du ** &lt;Labbkapacitet: antaldatorer&gt; **.
2. Ange det nya **antalet virtuella datorer** som du vill ha i labbet. Det här numret måste vara större än eller lika med antalet användare som är registrerade i labbet. 
3. Välj sedan **Spara**. 

    ![Knappen Starta alla](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Om du ökade kapaciteten kan du se den virtuella datorn eller virtuella datorer som skapas. Om du inte ser den nya virtuella datorn i listan uppdaterar du sidan. 

    ![Vm skapas](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Starta virtuella datorer

### <a name="start-ot-stop-all-vms"></a>Starta ot stoppa alla virtuella datorer
1. Växla till **poolsidan för virtuella datorer.** 
2. Välj **Starta alla** i verktygsfältet. 

    ![Knappen Starta alla](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. När alla virtuella datorer har startats kan du stoppa alla virtuella datorer genom att välja knappen **Stoppa alla** i verktygsfältet. 

    ![Stoppa alla knappen](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Starta valda virtuella datorer
Det finns två sätt att starta valda virtuella datorer (en eller flera). Det första sättet är att välja den virtuella datorn eller virtuella datorer i listan och sedan välja **Start** i verktygsfältet. 

Det andra sättet är att välja en eller flera virtuella datorer i listan och växla knappen i **kolumnen Tillstånd.** 

![Starta valda virtuella datorer](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

På samma sätt kan du stoppa en eller flera virtuella datorer genom att växla knappen i **kolumnen Läge** eller välja **Stopp** i verktygsfältet. 

> [!NOTE]
> När en lärare slår på en deltagare VM påverkas inte kvoten för eleven. Kvot för en användare anger antalet labbtimmar som är tillgängliga för användaren utanför den schemalagda klasstiden. Mer information om kvoter finns i [Ange kvoter för användare](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>Återställa virtuella datorer
Om du vill återställa en eller flera virtuella datorer markerar du dem i listan och väljer sedan **Återställ** i verktygsfältet. 

![Återställa valda virtuella datorer](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Välj **Återställ** i dialogrutan Återställ virtuella **Reset**datorer. 

![Dialogrutan Återställ virtuell dator](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Ange lösenord för virtuella datorer
En labbägare (lärare) kan ange/återställa lösenordet för virtuella datorer när labbet (guiden skapande av labb) skapas eller efter att labbet har skapats på **sidan Mall.** 

### <a name="set-password-at-the-time-of-lab-creation"></a>Ange lösenord när labbet skapas
En labbägare (lärare) kan ange ett lösenord för virtuella datorer i labbet på sidan **Autentiseringsuppgifter för virtuella datorer** i guiden skapa labb.

![Nytt labbfönster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Genom att aktivera/inaktivera alternativet **Använd samma lösenord för alla virtuella datorer** på den här sidan kan en lärare välja att använda samma lösenord för alla virtuella datorer i labbet eller tillåta eleverna att ange lösenord för sina virtuella datorer. Som standard är den här inställningen aktiverad för alla Windows- och Linux-operativsystemavbildningar utom Ubuntu. När den här inställningen är inaktiverad uppmanas eleverna att ange ett lösenord när de försöker ansluta till den virtuella datorn för första gången. 

### <a name="reset-password-later"></a>Återställa lösenordet senare

1. På **sidan Mall** i labbet väljer du **Återställ lösenord** i verktygsfältet. 
1. Ange ett lösenord i dialogrutan **Återställ lösenord** och välj **Återställ lösenord**.
    
    ![Dialogrutan Ange lösenord](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Ansluta till virtuella elev-datorer
Labbskaparen (instruktör/professor) kan ansluta till en virtuell student om följande villkor är uppfyllda: 

- Alternativet **Använd samma lösenord för alla virtuella datorer** valdes när labbet skapades
- Den virtuella datorn körs 

 Om du vill ansluta till den virtuella datorn för deltagare håller du muspekaren över den virtuella datorn i listan och väljer datorknappen.  

![Knappen Anslut till den virtuella datorn för deltagare](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> När professorn startar den virtuella datorn och ansluter till den påverkas inte studentkvoten. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Exportera lista över virtuella datorer till en CSV-fil

1. Växla till **fliken Pool för virtuella datorer.**
2. Välj **...** (ellips) i verktygsfältet och välj sedan **Exportera CSV**. 

    ![Exportlista över virtuella datorer](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Nästa steg
Mer information om andra alternativ för elevanvändning som du (som labbägare) kan konfigurera finns i följande artikel: [Konfigurera elevanvändning](how-to-configure-student-usage.md).

Mer information om hur elever kan återställa lösenord för sina virtuella datorer finns i [Ange eller återställa lösenord för virtuella datorer i klassrumslabb (deltagare).](how-to-set-virtual-machine-passwords-student.md)