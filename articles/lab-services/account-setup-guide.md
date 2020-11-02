---
title: Installations guide för påskyndat labb konto för Azure Lab Services
description: Den här guiden hjälper administratörer att snabbt konfigurera ett labb konto för användning i sin skola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0855e59aced37e50c088cfe89ffeb3d0af9fcdca
ms.sourcegitcommit: 8ad5761333b53e85c8c4dabee40eaf497430db70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148311"
---
# <a name="lab-account-setup-guide"></a>Installations guide för labb konto

Som ett första steg bör administratörer konfigurera ett labb konto i din Azure-prenumeration. Ett labb konto är en behållare för ditt klass rums labb och tar bara några minuter att konfigurera.

## <a name="understand-your-schools-lab-account-requirements"></a>Förstå ditt labb konto krav för skolan

För att förstå hur du konfigurerar ditt labb konto baserat på skolans behov bör du tänka på dessa frågor.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Har jag åtkomst till en Azure-prenumeration?

Om du vill skapa ett labb konto måste du ha åtkomst till en Azure-prenumeration som har kon figurer ATS för din skola. Din skola kan ha en eller flera prenumerationer. Du använder en prenumeration för att hantera fakturering och säkerhet för alla dina Azure-resurser och-tjänster, inklusive Lab-konton.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Hur många labb konton behöver skapas?

Kom igång snabbt genom att skapa ett enda labb konto och senare skapa ytterligare labb konton efter behov. Du kan till exempel ha ett labb konto per avdelning.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Vem bör vara ägare och deltagare i labb kontot?

Dina administratörer är vanligt vis ägare och deltagare för ett labb konto. De ansvarar för att hantera de principer som gäller för alla labb som ingår i labb kontot. Personen som skapar labb kontot är automatiskt en ägare. Du kan lägga till ytterligare ägare och deltagare, vanligt vis från den Azure Active Directory-klient (Azure AD) som är associerad med din prenumeration. Detta kan vara användbart för att hantera ett labb konto genom att tilldela antingen rollen ägare eller deltagare på konto nivå för labbet.

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Labb användare ser bara en enda lista med de virtuella datorer som de har åtkomst till mellan klienter inom Azure Lab Services.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Vem kommer att kunna skapa och hantera labb?

Du kan välja att låta dina administratörer och lärare skapa och hantera labb. Dessa användare (vanligt vis från den Azure AD-klient som är associerad med din prenumeration) tilldelas rollen labb skapare i labb kontot.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Vill du ge labb skapare möjlighet att spara bilder som kan delas mellan labb?

Ett delat avbildnings galleri är en lagrings plats som du kan använda för att spara och dela bilder. Om du har flera klasser som behöver samma avbildningar kan labb skapare skapa avbildningen en gång och dela den mellan labb. Men för att komma igång behöver du inte nödvändigt vis ett delat avbildnings Galleri, eftersom du alltid kan lägga till ett senare.

Om du svarade "Ja" på den här frågan måste du skapa eller koppla ett delat avbildnings galleri till ditt labb konto. Om du besvarade "Jag vet inte" kan du skjuta upp det här beslutet till senare.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Vilka bilder i Azure Marketplace använder ditt klass rums labb?

Azure Marketplace innehåller hundratals avbildningar som du kan aktivera så att labb skapare kan använda avbildningen för att skapa sina labb. Vissa bilder kan innehålla allt som ett labb redan behöver. I andra fall kan du använda en avbildning som utgångs punkt och sedan kan du anpassa den genom att installera ytterligare program eller verktyg.

Om du inte vet vilka bilder du behöver använda, kan du alltid komma tillbaka till det senare för att aktivera dem. Det bästa sättet att se vilka bilder som är tillgängliga är att först skapa ett labb konto. Det ger dig åtkomst, så att du kan granska listan med tillgängliga bilder och deras innehåll.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Måste Labbets virtuella datorer ha åtkomst till andra Azure-resurser eller lokala resurser?

När du konfigurerar ett labb konto kan du också välja att peer-koppla med ett virtuellt nätverk. För att avgöra om du behöver detta bör du tänka på följande frågor:

- **Behöver du ge åtkomst till en licens Server?**
  
   Om du planerar att använda Azure Marketplace-avbildningar paketeras kostnaden för operativ system licensen i prissättningen för labb tjänster. Därför behöver du inte ange licenser för själva operativ systemet. Men för ytterligare program vara och program som är installerade måste du ange en licens på lämpligt sätt.

- **Behöver de virtuella datorerna i labbet åtkomst till andra lokala resurser, till exempel en fil resurs eller databas?**

   Du skapar ett virtuellt nätverk för att ge åtkomst till lokala resurser, vanligt vis genom att använda en virtuell nätverksgateway från plats-till-plats. Om du inte har konfigurerat något virtuellt nätverk måste du investera ytterligare tid för detta.

- **Behöver de virtuella datorerna i labbet åtkomst till andra Azure-resurser som finns i ett virtuellt nätverk?**

   Om du behöver åtkomst till Azure-resurser som *inte* är skyddade i ett virtuellt nätverk kan du komma åt dessa resurser via det offentliga Internet utan att göra någon peering.

Om du svarade ja på en eller flera frågor måste du koppla labb kontot till ett virtuellt nätverk. Om du besvarade "Jag vet inte" kan du skjuta upp det här beslutet till senare. Du kan alltid välja att peer-koppla ett virtuellt nätverk när du har skapat labb kontot.

## <a name="set-up-your-lab-account"></a>Konfigurera ditt labb konto

När du har förstått kraven för ditt labb konto är du redo att konfigurera det.

1. **Skapa ditt labb konto.** Mer information hittar du i självstudien om att [skapa ett labb konto](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) .

   När du skapar ett labb konto kan det vara bra att bekanta dig med de Azure-resurser som berörs. Mer information finns i följande artiklar:

   - [Prenumeration](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Resursgrupp](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Labb konto](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Klass rums labb](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Välja en region och en plats](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Namngivnings vägledning för resurser](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Lägg till användare i labb skapares rollen.** Instruktioner finns i [lägga till användare i rollen labb skapare](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Mer information om de olika roller som kan tilldelas till användare som ska hantera labb konton och labb finns i [guiden om att hantera identiteter](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Anslut till ett virtuellt peer-nätverk.** Mer information finns i [ansluta ditt labb nätverk till ett virtuellt peer-nätverk](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Du kan också behöva hänvisa till instruktioner om hur [du konfigurerar adress intervallet för labbets virtuella datorer](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Aktivera och granska bilder.** Instruktioner finns i [Aktivera Azure Marketplace-avbildningar för labb skapare](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Om du vill granska innehållet i varje Azure Marketplace-avbildning väljer du avbildningens namn. Följande skärm bild visar till exempel information om Ubuntu Data Science VM-avbildningen:

   ![Skärm bild av granska Azure Marketplace-avbildningar](./media/setup-guide/review-marketplace-images.png)

   Om du har ett delat avbildnings galleri som är kopplat till ditt labb konto och du vill aktivera anpassade avbildningar som ska delas av labb skapare, slutför du stegen som liknar dem som visas i följande skärm bild:

   ![Skärm bild som visar hur du aktiverar anpassade avbildningar i ett galleri för delade avbildningar](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Nästa steg

- [Hantera labbkonton](how-to-manage-lab-accounts.md)

- [Installations guide för klass rums labb](setup-guide.md)
