---
title: ta med fil
description: ta med fil
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 8d13d5d93dba66fa0dce08ffbf569164fb03398d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468992"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Så här skapar du ett klassiskt VNet i Azure Portal
Följ dessa steg om du vill skapa ett klassiskt VNet baserat på föregående scenario.

1. Navigera till https://portal.azure.com från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**. Observera att listan **Välj en distributions modell** redan visas som **klassisk**. 3. Klicka på **skapa** som visas i följande figur.
   
    ![Skapa VNet i Azure-portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. I fönstret **virtuellt nätverk** anger du **namnet** på VNet och klickar sedan på **adress utrymme**. Konfigurera adress utrymmes inställningarna för det virtuella nätverket och det första under nätet och klicka sedan på **OK**. Följande bild visar CIDR-block-inställningarna för vårt scenario.
   
    ![Fönstret adress utrymme](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Klicka på **resurs grupp** och välj en resurs grupp för att lägga till VNet till eller klicka på **Skapa ny resurs grupp** för att lägga till VNet i en ny resurs grupp. Följande bild visar resurs grupps inställningarna för en ny resurs grupp med namnet **TestRG**. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   
    ![Fönstret Skapa resurs grupp](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Ändra vid behov inställningarna för **Prenumeration** och **Plats** för din VNet. 
7. Om du inte vill se VNet som en ikon på **Startsidan**, inaktiverar du **Fäst på startsidan**. 
8. Klicka på **skapa** och Lägg märke till panelen med namnet **Skapa virtuellt nätverk** som det visas i följande bild.
   
    ![Skapa VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Vänta tills det virtuella nätverket har skapats och klicka på den om du ser panelen och Lägg till fler undernät.
   
    ![Skapa VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Du bör se **konfigurationen** för ditt VNet som det visas. 
   
    ![Skapa VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Klicka på **undernät** > **Lägg till**och skriv sedan ett **namn** och ange ett **adress intervall (CIDR-block)** för under nätet och klicka sedan på **OK**. Följande bild visar inställningarna för vårt aktuella scenario.
    
    ![Skapa VNet i Azure-portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

