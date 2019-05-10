---
title: Konfigurera lab-konton i Azure Lab Services | Microsoft Docs
description: Lär dig mer om att konfigurera ett labbkonto när den har skapats.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414046"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurera lab-konton i Azure Lab Services 
Ett labbkonto är en behållare för hanterade lab typer, till exempel klassrum labb i Azure Lab Services. En administratör konfigurerar ett testlabb-konto med Azure Lab Services och ger åtkomst till labbägare som kan skapa labb i kontot. Den här artikeln beskriver hur du skapar ett labbkonto, visa alla lab-konton eller ta bort ett labbkonto.

## <a name="connect-with-a-peer-virtual-network"></a>Ansluta med ett peer virtuellt nätverk
Följ dessa steg om du vill ansluta ett virtuellt nätverk som en peer-nätverket till den testmiljön virtuellt nätverk:

1. På den **Labbkonto** väljer **Labs configuration** på den vänstra menyn.

    ![Konfigurationssidan för labb](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. För **Peerkopplade virtuella nätverket**väljer **aktiverad** eller **inaktiverad**. Standardvärdet är **inaktiverad**. Om du vill aktivera det peerkopplade virtuella nätverket, gör du följande: 
    1. Välj **aktiverat**.
    2. Välj den **VNet** från den nedrullningsbara listan. 
3. Välj **Spara** i verktygsfältet. 

Labs som skapats i det här kontot är anslutna till det valda virtuella nätverket. De har åtkomst till resurser i det valda virtuella nätverket. Mer information finns i [ansluta ditt labb nätverk med en peer virtuellt nätverk i Azure Lab Services](how-to-connect-peer-virtual-network.md).

När du väljer ett virtuellt nätverk för den **Peerkopplade virtuella nätverket** fält, de **Tillåt labbskaparen att välja lab platsen** alternativet är inaktiverat. Det beror på att labs i labb-kontot måste finnas i samma region som labbkonto att ansluta till resurser i det peerkopplade virtuella nätverket. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Tillåt labbskaparen att välja platsen för övningen
Du kan tillåta labbskaparen du skapa labb i en annan plats än platsen för labb-kontot genom att följa dessa steg: 

1. På den **Labbkonto** väljer **Labs configuration** på den vänstra menyn.
2. För den **Tillåt labbskaparen att välja lab platsen**väljer **aktiverad** om du vill att labbskaparen för att kunna välja en plats för övningen. Om den är inaktiverad skapas automatiskt labbarna på samma plats där labbkonto finns. 
    
    Det här fältet inaktiveras när du väljer ett virtuellt nätverk för den **Peerkopplade virtuella nätverket** fält. Det beror på att labs i labb-kontot måste finnas i samma region som labbkonto att komma åt resurser i det peerkopplade virtuella nätverket. 
1. Välj **Spara** i verktygsfältet. 

    ![Konfigurera plats labbmiljö](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Ange ett adressintervall för virtuella datorer i labbet
Följande procedur innehåller steg för att ange ett adressintervall för virtuella datorer i labbet. Om du uppdaterar det adressintervall som du angav tidigare gäller ändrade adressintervallet endast virtuella datorer som skapas efter att ändringen har gjorts. 

Här finns vissa begränsningar när du anger det adressintervall som du bör tänka på. 

- Prefixet måste vara mindre än eller lika med 23. 
- Om ett virtuellt nätverk är peer-kopplat till kontot lab, får inte tillhandahållna adressintervallet överlappa med adressintervall från peer-kopplade virtuella nätverket.

1. På den **Labbkonto** väljer **Labs configuration** på den vänstra menyn.
2. För den **adressintervall** fältet, ange adressintervallet för virtuella datorer som kommer att skapas i labbet. Adressintervallet ska vara i classless Inter-Domain routing (CIDR)-format (exempel: 10.20.0.0/23). Virtuella datorer i labbet skapas i den här adressintervall.
3. Välj **Spara** i verktygsfältet. 

    ![Konfigurera adressintervall](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Lägga till en användare i rollen Labbskapare
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste användaren vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa labbkontot läggs automatiskt till i den här rollen. Om du planerar att använda samma användarkonto för att skapa ett klassrumslabb kan du hoppa över det här steget. Om du vill använda ett annat konto för att skapa ett klassrumslabb gör du följande: 

Ge lärarna behörighet att skapa labb åt sina klasser genom att lägga till dem i rollen **Labbskapare**:

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** och klickar på **+ Lägg till rolltilldelning** i verktygsfältet. 

    ![Åtkomstkontroll -> Lägg till rolltilldelning, knapp](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. På sidan **Lägg till rolltilldelning** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägg till labbuppgiftsförfattare](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Ange Marketplace-avbildningar som är tillgängliga för labbuppgiftsförfattare
Som labbkontoägare kan du ange Marketplace-avbildningar som labbskapare kan använda för att skapa labb i labbkontot. 

1. Välj **Marketplace-avbildningar** på menyn till vänster. Som standard kan se du en fullständig lista över avbildningar (både aktiverade och inaktiverade). Du kan filtrera listan för att endast se aktiverade/inaktiverade avbildningar genom att välja alternativet **Endast aktiverade**/**Endast inaktiverade** från listrutan längst upp. 
    
    ![Sida för Marketplace-avbildningar](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    De Marketplace-avbildningar som visas i listan är bara de filer som uppfyller följande villkor:
        
    - Skapar en enskild virtuell dator.
    - Använder Azure Resource Manager för att etablera virtuella datorer
    - Kräver inte att du köper en extra licensieringsplan
2. För att **inaktivera** en Marketplace-avbildning som har aktiverats genomför du någon av följande åtgärder: 
    1. Välj **... (ellips)** i den sista kolumnen och välj **Inaktivera avbildning**. 

        ![Inaktivera en avbildning](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Välj en eller flera avbildningar från listan genom att markera kryssrutorna före avbildningsnamnen i listan och välja **Inaktivera valda avbildningar**. 

        ![Inaktivera flera avbildningar](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. På ett liknande sätt **aktiverar** du en Marketplace-avbildning med någon av följande åtgärder: 
    1. Välj **... (ellips)** i den sista kolumnen och välj **Aktivera avbildning**. 
    2. Välj en eller flera avbildningar från listan genom att markera kryssrutorna före avbildningsnamnen i listan och välja **Aktivera valda avbildningar**. 




## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som en lab-användare åtkomst till labb för klassrum](how-to-use-classroom-lab.md)
