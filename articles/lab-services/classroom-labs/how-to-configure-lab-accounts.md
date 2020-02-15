---
title: Konfigurera labb konton i Azure Lab Services | Microsoft Docs
description: Den här artikeln beskriver hur du skapar ett labb konto, visar alla labb konton eller tar bort ett labb konto i Azure Lab Services.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 37a657093fd55ce752095417fe744f83946962db
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210591"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurera labb konton i Azure Lab Services 
I Azure Lab Services är ett labb konto en behållare för hanterade labb typer, till exempel klass rums labb. En administratör konfigurerar ett labb konto med Azure Lab Services och ger till gång till labb ägare som kan skapa labb i kontot. Den här artikeln beskriver hur du skapar ett labb konto, visar alla labb konton eller tar bort ett labb konto.

## <a name="connect-with-a-peer-virtual-network"></a>Ansluta till ett virtuellt peer-nätverk
Följ dessa steg om du vill ansluta ett virtuellt nätverk som ett peer-nätverk till Labbets virtuella nätverk:

1. På sidan **labb konto** väljer du **labb konfiguration** på den vänstra menyn.

    ![Sidan labb konfiguration](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. För **peer Virtual Network**väljer du **aktive rad** eller **inaktive**rad. Standardvärdet är **inaktiverat**. Gör så här för att aktivera det virtuella peer-nätverket: 
    1. Välj **Aktiverad**.
    2. Välj **VNet** i list rutan. 
3. Välj **Spara** i verktygsfältet. 

Labb som skapats i det här kontot är anslutna till det valda virtuella nätverket. De kan komma åt resurserna i det valda virtuella nätverket. Mer information finns i [ansluta ditt labb nätverk till ett virtuellt peer-nätverk i Azure Lab Services](how-to-connect-peer-virtual-network.md).

När du väljer ett virtuellt nätverk för det **virtuella peer-nätverket** är alternativet **Tillåt att labb skaparen att välja labb plats** inaktiverat. Det beror på att labb i labb kontot måste finnas i samma region som labb kontot för att de ska kunna ansluta till resurser i det virtuella peer-nätverket. 

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Ange ett adress intervall för virtuella datorer i labbet
Följande procedur innehåller steg för att ange ett adress intervall för virtuella datorer i labbet. Om du uppdaterar intervallet som du har angett gäller det ändrade adress intervallet endast för virtuella datorer som skapas när ändringen har gjorts. 

Här följer några begränsningar när du anger adress intervallet som du bör ha i åtanke. 

- Prefixet måste vara mindre än eller lika med 23. 
- Om ett virtuellt nätverk är peer-kopplat till labb kontot kan det angivna adress intervallet inte överlappa med adress intervallet från det peer-kopplade virtuella nätverket.

1. På sidan **labb konto** väljer du **labb konfiguration** på den vänstra menyn.
2. I fältet **adress intervall** anger du adress intervall för virtuella datorer som ska skapas i labbet. Adress intervallet bör vara i CIDR-notation (Classless Inter-Domain routing) (exempel: 10.20.0.0/23). Virtuella datorer i labbet skapas i det här adress intervallet.
3. Välj **Spara** i verktygsfältet. 

    ![Konfigurera adress intervall](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Lägga till en användare i rollen Labbskapare
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste användaren vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa labbkontot läggs automatiskt till i den här rollen. Om du planerar att använda samma användarkonto för att skapa ett klassrumslabb kan du hoppa över det här steget. Om du vill använda ett annat konto för att skapa ett klassrumslabb gör du följande: 

Ge lärarna behörighet att skapa labb åt sina klasser genom att lägga till dem i rollen **Labbskapare**:

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** och klickar på **+ Lägg till rolltilldelning** i verktygsfältet. 

    ![Åtkomstkontroll -> Lägg till rolltilldelning, knapp](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. På sidan **Lägg till rolltilldelning** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägg till labbuppgiftsförfattare](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Om du lägger till en icke-Microsoft-konto användare som en labb skapare, se avsnittet [Lägg till en icke-Microsoft-konto användare som en labb skapare](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

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

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Lägg till en icke-Microsoft-konto användare som en labb skapare
Om du vill lägga till en användare som en labb skapare, använder du deras e-postkonton. Följande typer av e-postkonton kan användas:

- Ett e-postkonto som tillhandahålls av ditt universitets Office 365 Azure Active Directory (AAD). 
- Ett Microsoft-e-postkonto, till exempel `@outlook.com`, `@hotmail.com`, `@msn.com`eller `@live.com`.
- Ett e-postkonto som inte kommer från Microsoft, till exempel ett som tillhandahålls av Yahoo eller Google. Dessa typer av konton måste dock länkas till ett Microsoft-konto.
- Ett GitHub-konto. Kontot måste vara länkat till ett Microsoft-konto.

### <a name="using-a-non-microsoft-email-account"></a>Använda ett e-postkonto som inte kommer från Microsoft
Labb skapare/lärare kan använda e-postkonton som inte kommer från Microsoft för att registrera sig och logga in i ett klass rums labb.  Inloggningen till labb tjänst portalen kräver dock att instruktörerna först skapar en Microsoft-konto som är länkad till den e-postadress som inte kommer från Microsoft.

Många instruktörer kanske redan har ett Microsoft-konto kopplat till sina e-postadresser som inte kommer från Microsoft. Lärare har till exempel redan en Microsoft-konto om de har använt sin e-postadress med Microsofts andra produkter eller tjänster, t. ex. Office, Skype, OneDrive eller Windows.  

När lärare loggar in på labb tjänst portalen uppmanas de att ange sina e-postadresser och lösen ord. Om instruktören försöker logga in med en icke-Microsoft-konto som inte har något Microsoft-konto länkat, får instruktören följande fel meddelande: 

![Felmeddelande](../media/how-to-configure-student-usage/cant-find-account.png)

Om du vill registrera dig för en Microsoft-konto bör instruktörer gå till [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Använda ett GitHub-konto
Lärare kan också använda ett befintligt GitHub-konto för att registrera sig och logga in i ett klass rums labb. Om instruktören redan har en Microsoft-konto länkat till sitt GitHub-konto kan de logga in och ange sitt lösen ord som visas i föregående avsnitt. Om de inte har länkat sitt GitHub-konto till en Microsoft-konto bör de välja **inloggnings alternativ**:

![Länk för inloggnings alternativ](../media/how-to-configure-student-usage/signin-options.png)

På sidan **inloggnings alternativ** väljer du logga in **med GitHub**.

![Logga in med GitHub-länk](../media/how-to-configure-student-usage/signin-github.png)

Slutligen uppmanas de att skapa en Microsoft-konto som är länkad till sitt GitHub-konto. Det sker automatiskt när instruktören väljer **Nästa**.  Instruktören är sedan omedelbart inloggad och ansluten till klass rummets labb.

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatisk avstängning av virtuella datorer vid från koppling
Du kan aktivera eller inaktivera automatisk avstängning av virtuella Windows Lab-datorer (mall eller student) när en anslutning till fjärr skrivbord är frånkopplad. Du kan också ange hur länge de virtuella datorerna ska vänta tills användaren ansluter igen innan den stängs av automatiskt.

![Inställning för automatisk avstängning i labb konto](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Den här inställningen gäller för alla labb som skapats i labb kontot. En labb ägare kan åsidosätta den här inställningen på labb nivån. Ändringen av den här inställningen på labb kontot påverkar bara labb som skapas när ändringen har gjorts.

Information om hur en labb ägare kan konfigurera den här inställningen på labb nivån finns i [den här artikeln](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labb användare, åtkomst till klass rum labb](how-to-use-classroom-lab.md)
