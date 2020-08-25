---
title: Självstudie – lär dig hur du kommer åt ditt privata moln
description: Lär dig hur du kommer åt ett privat moln i Azure VMware-lösningen
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 73226c6aa567dc5fbe18251bed4812637664a02c
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750528"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-private-cloud"></a>Självstudie: Lär dig hur du kommer åt ett privat moln i Azure VMware-lösningen

I för hands versionen tillåter inte Azure VMware-lösningen att du hanterar ditt privata moln med din lokala vCenter. Du måste utföra ytterligare konfiguration och anslutning till en lokal vCenter-instans via en hopp ruta. 

I den här självstudien skapar du en virtuell Windows-dator för en hopp ruta i resurs gruppen som du skapade i den föregående själv studie kursen [: Konfigurera nätverk för ditt privata VMware-moln i Azure](tutorial-configure-networking.md) och logga in på vCenter. Det här är en virtuell dator i samma virtuella nätverk som du har skapat och ger åtkomst till vCenter och NSX Manager. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows-dator som ska användas för att ansluta till vCenter
> * Logga in på vCenter från den virtuella datorn

## <a name="create-a-new-windows-virtual-machine"></a>Skapa en ny virtuell Windows-dator

1. I resurs gruppen väljer du **+ Lägg till** och sedan Sök och väljer **Microsoft Windows 10**. Klicka sedan på **skapa**.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Lägg till en ny virtuell Windows 10-dator för en hopp." border="true":::

1. Ange nödvändig information i fälten och välj sedan **Granska + skapa**. Mer information om fälten finns i följande tabell.

   | Fält | Värde |
   | --- | --- |
   | **Prenumeration** | Värdet är redan ifyllt med prenumerationen som resurs gruppen tillhör. |
   | **Resursgrupp** | Det här värdet är redan ifyllt för den aktuella resurs gruppen. Detta bör vara den resurs grupp som du skapade i föregående självstudie. |
   | **Namn på virtuell dator** | Ange ett unikt namn för den virtuella datorn. |
   | **Region** | Välj den geografiska platsen för den virtuella datorn. |
   | **Tillgängliga alternativ** | Låt standardvärdet vara markerat. |
   | **Avbildning** | Välj avbildningen av den virtuella datorn. |
   | **Storlek** | Lämna värdet för standard storlek. |
   | **Autentiseringstyp**  | Välj **lösen ord**. |
   | **Användarnamn** | Ange användar namnet för att logga in på den virtuella datorn. |
   | **Lösenord** | Ange lösen ordet för att logga in på den virtuella datorn. |
   | **Bekräfta lösenord** | Ange lösen ordet för att logga in på den virtuella datorn. |
   | **Offentliga inkommande portar** | Välj **Ingen**. Om du väljer Ingen kan du använda [JIT-åtkomst](../security-center/security-center-just-in-time.md#jit-configure) för att kontrol lera åtkomsten till den virtuella datorn endast när du vill komma åt den.  |

1. När du har angett rätt information klickar du på **Granska + skapa**. 
1. När verifieringen har godkänts väljer du **skapa** för att starta processen för att skapa virtuella datorer.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Skapa en ny virtuell Windows 10-dator för en hopp." border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Ansluta till det privata molnets lokala vCenter

1. Från rutan hopp loggar du in på vSphere-klienten med VMware vCenter SSO. Logga in på vSphere-klienten med ett användar namn för moln administratören. Godkänn säkerhets risken och Fortsätt när du ser en varning om en potentiell säkerhets risk. Logga in på VMware vCenter med autentiseringsuppgifter för enkel inloggning och kontrol lera att användar gränssnittet visas.

1. I Azure Portal väljer du ditt privata moln och väljer sedan **identitet > standard**i vyn **Översikt** . URL: er och inloggnings uppgifter för privat moln vCenter och NSX-T Manager visas.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Visa URL: er och autentiseringsuppgifter för privata moln-vCenter och NSX Manager." border="true":::

1. Navigera till den virtuella dator som du skapade i föregående steg och Anslut till den virtuella datorn. Detaljerade anvisningar om hur du ansluter till den virtuella datorn finns i [ansluta till en virtuell dator](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)

1. I den virtuella Windows-datorn öppnar du en webbläsare och navigerar till URL: erna vCenter och NSX-T Manager på två flikar. På fliken vCenter anger du autentiseringsuppgifterna för `cloudadmin@vmcp.local` användaren från föregående steg.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Logga in i privat moln vCenter." border="true":::

   :::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter-portalen." border="true":::

1. Logga in på NSX-T-hanteraren på den andra fliken i webbläsaren.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Logga in på NSX-T-hanteraren på den andra fliken i webbläsaren." border="true":::

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows-dator som ska användas för att ansluta till vCenter
> * Logga in på vCenter från den virtuella datorn

Fortsätt till nästa självstudie och lär dig hur du skalar ditt privata moln i Azure VMware-lösningen.

> [!div class="nextstepaction"]
> [Skala ett privat moln i Azure VMware-lösningen](tutorial-scale-private-cloud.md)
