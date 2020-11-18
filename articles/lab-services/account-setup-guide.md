---
title: Installations guide för påskyndat labb konto för Azure Lab Services
description: Den här guiden hjälper administratörer att snabbt konfigurera ett labb konto för användning i sin skola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: aa3e7b586546b3d87f5c6029b284eeb1402ed171
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659870"
---
# <a name="lab-account-setup-guide"></a>Installations guide för labb konto
För att konfigurera din Azure Lab Services miljö måste administratörerna först konfigurera ett **labb konto** i din Azure-prenumeration. Ett labb konto är en behållare för dina labb och tar bara några minuter att konfigurera.

Den här guiden innehåller tre delar:
-  Det första avsnittet fokuserar på de krav som måste slutföras *innan* du konfigurerar ditt labb konto.
-  Det andra avsnittet innehåller vägledning om hur du planerar inställningarna för ditt labb konto.
-  Det tredje avsnittet innehåller steg-för-steg-instruktioner för att konfigurera ett labb konto.

## <a name="prerequisites-for-setting-up-your-lab-account"></a>Krav för att konfigurera ditt labb konto
Det här avsnittet beskriver de krav som du måste utföra innan du kan konfigurera ett labb konto.

### <a name="obtain-an-azure-subscription"></a>Skaffa en Azure-prenumeration
Om du vill skapa ett labb konto måste du ha åtkomst till en Azure-prenumeration som har kon figurer ATS för din skola. Din skola kan ha en eller flera prenumerationer. Du använder en prenumeration för att hantera fakturering och säkerhet för alla dina Azure-resurser och-tjänster, inklusive Lab-konton.  Azure-prenumerationer hanteras vanligt vis av IT-avdelningen.  Mer information finns i följande avsnitt:
 - [Administratörs guide – prenumeration](./administrator-guide.md#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>Beräkna antalet virtuella datorer och VM-storlekar som du behöver
Du måste uppskatta antalet virtuella datorer (VM) och de [VM-storlekar](./administrator-guide.md#vm-sizing) som din skola behöver.  Läs följande blogg inlägg om du behöver hjälp med att strukturera din labs\images.  Det här blogg inlägget hjälper dig också att bestämma antalet virtuella datorer och de VM-storlekar som du behöver:
- [Flytta från ett fysiskt labb till Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

Se även den här artikeln som förklarar ytterligare vägledning om hur du strukturerar labb:
- [Administratörs guide – labb](./administrator-guide.md)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Förstå virtuella prenumerations gränser och regional VM-kapacitet
När du har en uppskattning av antalet virtuella datorer och VM-storlekarna för dina labb måste du:

- Se till att din Azure-prenumerations kapacitets gräns tillåter antalet virtuella datorer och den VM-storlek som du planerar att använda i dina labb.

- Skapa ditt labb konto inom en region som har tillräcklig VM-kapacitet tillgänglig.

Läs följande blogg inlägg för att lära dig mer: [begränsningar för VM-prenumeration och regional kapacitet](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Bestäm hur många labb konton som ska skapas

Kom igång snabbt genom att skapa ett enda labb konto i en egen resurs grupp.  Senare kan du skapa ytterligare labb konton (och resurs grupper) efter behov. Du kanske till exempel har ett labb konto och en resurs grupp per avdelning som ett sätt att tydligt separera kostnader.  Läs följande artiklar om du vill veta mer om labb konton, resurs grupper och åtskiljande kostnader:
- [Administratörs guide – resurs grupp](./administrator-guide.md#resource-group)
- [Administratörs guide – labb konto](./administrator-guide.md#lab-account) 
- [Kostnads hantering för Azure Lab Services](./cost-management-guide.md)

## <a name="planning-your-lab-accounts-settings"></a>Planera dina labb konto inställningar

För att planera ditt labb konto inställningar bör du överväga nedanstående frågor.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Vem bör vara ägare och deltagare i labb kontot?

   Din skola IT-administratörer är vanligt vis ägare och deltagare för ett labb konto. De är ansvariga för att hantera de principer som gäller för alla labb som ingår i labb kontot. Personen som skapar labb kontot är automatiskt en ägare. Du kan lägga till ytterligare ägare och deltagare från den Azure Active Directory-klient (AD) som är associerad med din prenumeration. Mer information om labb kontots ägare och deltagar roller finns i:
   -  [Administratörs guide – hantera identitet](./administrator-guide.md#manage-identity).

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   Labb användare ser bara en enda lista med de virtuella datorer som de har åtkomst till mellan klienter inom Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Vem ska kunna skapa labb?

   Du kan välja att låta din IT-och fakultets medlemmar skapa labb. När en användare skapar ett labb tilldelas de automatiskt som ägare till labbet.  För att skapa labb måste användare (vanligt vis från den Azure AD-klient som är associerad med din prenumeration) tilldelas rollen labb skapare i labb kontot.  För ytterligare information om rollen labb skapare, Läs:
   -  [Administratörs guide – hantera identitet](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Vem ska kunna äga och hantera labb?

   Du kan också välja att låta IT-och fakultets medlemmar own\manage Labs *utan* att ge dem möjlighet att skapa labb.  I det här fallet tilldelas användare från din prenumerations Azure AD-klient antingen ägaren eller deltagaren för befintliga labb.  Mer information om Labbets ägare och deltagar roller finns i:
   - [Administratörs guide – hantera identitet](./administrator-guide.md#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>Vill du spara bilder som kan delas mellan labben?
Ett delat avbildnings galleri är en lagrings plats som du kan använda för att spara och dela bilder. För klasser som behöver samma avbildning kan Lab-skapare skapa avbildningen och sedan exportera den till det delade avbildnings galleriet.  När en avbildning har exporter ATS till det delade bild galleriet kan du använda den för att skapa nya labb.

Du kanske också vill skapa avbildningar i din fysiska miljö och sedan importera dem till delade avbildnings galleriet.  Mer information om den här processen finns i följande blogg inlägg: 
- [Importera anpassad avbildning till galleriet för delade avbildningar](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Om du bestämmer dig för att du behöver använda ett delat avbildnings Galleri måste du skapa eller koppla ett delat avbildnings galleri till ditt labb konto. Du kan också skjuta upp det här beslutet tills senare eftersom det kan kopplas till ett labb konto när som helst.  Mer information om Shared Image Gallery finns i:
- [Administratörs guide – Galleri för delad avbildning](./administrator-guide.md#shared-image-gallery)
- [Administratörs guide – priser för delade avbildnings Galleri](./administrator-guide.md#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Vilka bilder i Azure Marketplace använder dina labb?
Azure Marketplace innehåller hundratals avbildningar som du kan aktivera så att labb skapare kan använda avbildningen för att skapa sina labb. Vissa bilder kan innehålla allt som ett labb redan behöver. I andra fall kan du använda en avbildning som utgångs punkt och sedan kan du anpassa den genom att installera ytterligare program eller verktyg.

Om du inte vet vilka bilder du behöver kan du komma tillbaka senare för att aktivera dem. Det bästa sättet att se vilka bilder som är tillgängliga är att först skapa ett labb konto. Det ger dig åtkomst så att du kan granska listan med tillgängliga bilder och deras innehåll.  Läs följande om du vill ha mer information om Marketplace-avbildningar:
- [Ange Marketplace-avbildningar som är tillgängliga för labbuppgiftsförfattare](./specify-marketplace-images.md)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Behöver Labbets virtuella datorer ha åtkomst till andra Azure-resurser eller lokala resurser?
När du konfigurerar ett labb konto kan du också peer-ditt labb konto med ett virtuellt nätverk (VNet).  Tänk på att både ditt VNet-och Lab-konto måste finnas i samma region.  För att avgöra om du behöver peer-koppla med ett VNet, bör du överväga följande scenarier:

- **Åtkomst till en licensierings Server**
  
   När du använder Azure Marketplace-avbildningar paketeras kostnaden för operativ system licensen i prissättningen för labb tjänster. Men du behöver inte ange licenser för själva operativ systemet. Men för ytterligare program vara och program som är installerade måste du ange en licens på lämpligt sätt.  Så här ansluter du till en licensierings Server:
   - Du kan välja att ansluta till en lokal licensierings Server.  Att ansluta till en lokal licensierings Server kräver ytterligare konfiguration.
   - Ett annat alternativ, som är snabbare att konfigurera, är att skapa en licens server som du är värd för på en virtuell Azure-dator.  Den virtuella Azure-datorn finns i ett virtuellt nätverk som du peer-koppla till ditt labb konto.

- **Åtkomst till andra lokala resurser, till exempel en fil resurs eller databas**

   Du skapar ett VNet för att ge åtkomst till lokala resurser, vanligt vis genom att använda en virtuell nätverksgateway för plats-till-plats. Det tar extra tid att konfigurera den här typen av miljö.

- **Åtkomst till andra Azure-resurser som finns utanför ett VNet**

   Om du behöver åtkomst till Azure-resurser som *inte* är skyddade i ett VNet kan du komma åt dessa resurser via det offentliga Internet, utan att göra någon peering.

Mer information om virtuella nätverk finns i:
- [Grundläggande arkitektur – Virtual Network](./classroom-labs-fundamentals.md#virtual-network)
- [Så här ansluter du till ett virtuellt nätverk](./how-to-connect-peer-virtual-network.md)
- [Så här skapar du ett labb med en delad resurs i Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Konfigurera ditt labb konto

När du har avslutat planeringen är du redo att konfigurera ditt labb konto.  Dessa steg kan användas för att konfigurera ett labb [Azure Lab Services med Teams](./lab-services-within-teams-overview.md).

1. **Skapa ditt labb konto.** Mer information hittar du i självstudien om att [skapa ett labb konto](./tutorial-setup-lab-account.md#create-a-lab-account) .
   
    Mer information om namngivning finns i följande artikel:

   - [Namngivnings vägledning för resurser](./administrator-guide.md#naming)

2. **Lägg till användare i labb skapares rollen.** Instruktioner finns i [lägga till användare i rollen labb skapare](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


3. **Anslut till ett virtuellt peer-nätverk.** Mer information finns i [ansluta ditt labb nätverk till ett virtuellt peer-nätverk](./how-to-connect-peer-virtual-network.md).

   Du kan också behöva hänvisa till instruktioner om hur [du konfigurerar adress intervallet för labbets virtuella datorer](./how-to-configure-lab-accounts.md).

4. **Aktivera och granska bilder.** Instruktioner finns i [Aktivera Azure Marketplace-avbildningar för labb skapare](./specify-marketplace-images.md).

   Om du vill granska innehållet i varje Azure Marketplace-avbildning väljer du avbildningens namn. Följande skärm bild visar till exempel information om Ubuntu Data Science VM-avbildningen:

   ![Skärm bild av granska Azure Marketplace-avbildningar](./media/setup-guide/review-marketplace-images.png)

   Om ett delat avbildnings galleri är kopplat till ditt labb konto och du vill aktivera anpassade avbildningar som ska delas av labb skapare, slutför du liknande steg som du ser i följande skärm bild:

   ![Skärm bild som visar hur du aktiverar anpassade avbildningar i ett galleri för delade avbildningar](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Nästa steg

Nästa steg som är vanliga för att ställa in en labb miljö:

- [Hantera labbkonton](how-to-manage-lab-accounts.md)
- [Installations guide för labb](setup-guide.md)