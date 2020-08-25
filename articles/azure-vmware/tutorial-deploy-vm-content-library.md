---
title: 'Självstudie: skapa ett innehålls bibliotek för att distribuera virtuella datorer i Azure VMware-lösningen'
description: I den här självstudien om Azure VMware-lösning skapar du ett innehålls bibliotek för att distribuera en virtuell dator i ett privat moln i Azure VMware-lösningen.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: 3abaafac0dbd6f3537d2ca30a093627230780eb5
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750550"
---
# <a name="tutorial-create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Självstudie: skapa ett innehålls bibliotek för att distribuera virtuella datorer i Azure VMware-lösningen

Ett innehålls bibliotek lagrar och hanterar innehåll i form av biblioteks objekt. Ett enda biblioteks objekt består av en eller flera filer som du använder för att distribuera virtuella datorer (VM). 
 
I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett innehålls bibliotek
> * Ladda upp en ISO-avbildning till innehålls biblioteket
> * Distribuera en virtuell dator med hjälp av ISO i innehålls biblioteket

## <a name="prerequisites"></a>Förutsättningar

Ett NSX-T logiskt växel segment och en hanterad DHCP-tjänst krävs för att slutföra den här självstudien.  Mer information finns i [så här hanterar du DHCP i för hands versionen av Azure VMware-lösningen](manage-dhcp.md) .

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
