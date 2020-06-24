---
title: Skapa ett innehålls bibliotek för att distribuera virtuella datorer i Azure VMware-lösningen (AVS)
description: I den här Azure VMware Solution (AVS)-självstudien skapar du ett innehålls bibliotek för att distribuera en virtuell dator i ett moln privat moln.
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: 09d0bb47832a2c57cabbe4c6c516615ecd6b69cb
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308121"
---
# <a name="tutorial-create-a-content-library-to-deploy-vms-in-azure-vmware-solution-avs"></a>Självstudie: skapa ett innehålls bibliotek för att distribuera virtuella datorer i Azure VMware-lösningen (AVS)

Ett innehålls bibliotek lagrar och hanterar innehåll i form av biblioteks objekt. Ett enda biblioteks objekt består av en eller flera filer som du använder för att distribuera virtuella datorer (VM). 
 
I de här självstudierna får du lära dig att

Lär dig att:
> [!div class="checklist"]
> * Skapa ett innehålls bibliotek
> * Ladda upp en ISO-avbildning till innehålls biblioteket
> * Distribuera en virtuell dator med hjälp av ISO i innehålls biblioteket

## <a name="prerequisites"></a>Krav

Ett NSX-T logiskt växel segment och en hanterad DHCP-tjänst krävs för att slutföra den här självstudien.  Mer information finns i avsnittet [Hantera DHCP i Azure VMware-lösning (AVS)](manage-dhcp.md) .

## <a name="create-a-content-library"></a>Skapa ett innehålls bibliotek

1. Från den lokala vSphere-klienten väljer du **meny > innehålls bibliotek**.

   ![Välj Meny-> innehålls bibliotek](./media/content-library/vsphere-menu-content-libraries.png)

1. Klicka på knappen **Lägg till** för att skapa ett nytt innehålls bibliotek.

   ![Klicka på knappen Lägg till för att skapa ett nytt innehålls bibliotek.](./media/content-library/create-new-content-library.png)

1. Ange ett namn och bekräfta IP-adressen för vCenter-servern och välj **Nästa**.

   ![Ange ett namn och en anteckning om ditt val och välj sedan Nästa.](./media/content-library/new-content-library-step1.png)

1. Välj det **lokala innehålls biblioteket** och välj **Nästa**.

   ![I det här exemplet ska vi skapa ett lokalt innehålls bibliotek och välja nästa.](./media/content-library/new-content-library-step2.png)

1. Välj det data lager som ska lagra innehålls biblioteket och välj sedan **Nästa**.

   ![Välj det data lager som du vill använda som värd för innehålls biblioteket, välj Nästa.](./media/content-library/new-content-library-step3.png)

1. Granska och verifiera inställningarna för innehålls bibliotek och välj sedan **Slutför**.

   ![Verifiera inställningarna och välj Slutför.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Ladda upp en ISO-avbildning till innehålls biblioteket

Nu när innehålls biblioteket har skapats kan du lägga till en ISO-avbildning för att distribuera en virtuell dator till ett privat moln kluster. 

1. Välj **meny > innehålls bibliotek**i vSphere-klienten.

1. Högerklicka på det innehålls bibliotek som du vill använda för den nya ISO-filen och välj **Importera objekt**.

1. Importera ett biblioteks objekt för källan genom att göra något av följande och välj sedan **Importera**:
   1. Välj URL och ange en URL för att ladda ned en ISO-fil.

   1. Välj **lokal fil** att ladda upp från det lokala systemet.

   > [!TIP]
   > Valfritt kan du definiera ett anpassat objekt namn och noteringar för målet.

1. Öppna biblioteket och välj fliken **andra typer** för att kontrol lera att din ISO-överföring har laddats upp.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Distribuera en virtuell dator till ett privat moln kluster

1. Från vSphere-klienten väljer du **meny > värdar och kluster**.

1. I den vänstra panelen expanderar du trädet och väljer ett kluster.

1. Välj **åtgärder > ny virtuell dator**.

1. Gå igenom guiden och ändra de inställningar som du vill använda.

1. Välj **ny CD/DVD-enhet > klienten het > innehålls bibliotekets ISO-fil**.

1. Välj den ISO som överfördes i föregående avsnitt och välj sedan **OK**.

1. Markera kryss rutan **Anslut** så att ISO-filen monteras vid ström tillfället.

1. Välj **nytt nätverk > Välj List rutan > bläddra**.

1. Välj den **logiska växeln (segment)** och välj **OK**.

1. Ändra andra maskin varu inställningar och välj **Nästa**.

1. Kontrol lera inställningarna och välj **Slutför**.


## <a name="next-steps"></a>Nästa steg

Om du planerar att använda Hybrid Cloud Extension (HCX) för att migrera VM-arbetsbelastningar till ditt privata moln använder du proceduren [Installera HCX för Azure VMware-lösning](hybrid-cloud-extension-installation.md) .

<!-- LINKS - external-->

<!-- LINKS - internal -->