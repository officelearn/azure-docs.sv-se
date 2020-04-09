---
title: Installationsguide för snabbare labbkonto för Azure Lab Services
description: Den här guiden hjälper administratörer att snabbt skapa ett labbkonto för användning inom sin skola.
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
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879589"
---
# <a name="lab-account-setup-guide"></a>Installationsguide för labbkonto

Som ett första steg bör administratörer konfigurera ett labbkonto i din Azure-prenumeration. Ett labbkonto är en behållare för dina klassrumslabb och tar bara några minuter att konfigurera.

## <a name="understand-your-schools-lab-account-requirements"></a>Förstå skolans krav på labbkonto

Om du vill förstå hur du konfigurerar ditt labbkonto baserat på skolans behov bör du överväga dessa frågor.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Har jag åtkomst till en Azure-prenumeration?

För att skapa ett labbkonto behöver du åtkomst till en Azure-prenumeration som är konfigurerad för din skola. Din skola kan ha en eller flera prenumerationer. Du använder en prenumeration för att hantera fakturering och säkerhet för alla dina Azure-resurser och -tjänster, inklusive labbkonton.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Hur många labbkonton behöver skapas?

För att komma igång snabbt, skapa ett enda labbkonto och sedan skapa ytterligare labbkonton efter behov. Du kan till exempel så småningom ha ett labbkonto per avdelning.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Vem ska vara ägare och bidragsgivare till labbkontot?

Administratörerna är vanligtvis ägare och bidragsgivare för ett labbkonto. De ansvarar för att hantera de principer som gäller för alla labb som finns i labbkontot. Den person som skapar labbkontot är automatiskt ägare. Du kan lägga till ytterligare ägare och deltagare, vanligtvis från Azure Active Directory (Azure AD) klient som är associerad med din prenumeration. Detta kan vara användbart för att hantera ett labbkonto genom att tilldela antingen ägar- eller deltagarrollen på labbkontonivå.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Vem får skapa och hantera labb?

Du kan välja att låta administratörer och fakultetsmedlemmar skapa och hantera labb. Dessa användare (vanligtvis från Azure AD-klienten som är associerad med din prenumeration) tilldelas rollen Lab Creator i labbkontot.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Vill du ge labbskapare möjlighet att spara bilder som kan delas över labb?

Ett delat bildgalleri är en databas som du kan använda för att spara och dela bilder. Om du har flera klasser som behöver samma bilder kan labbskapare skapa avbildningen en gång och dela den över labben. Men för att komma igång behöver du inte nödvändigtvis ett delat bildgalleri, eftersom du alltid kan lägga till ett senare.

Om du svarade "ja" på den här frågan måste du skapa eller bifoga ett delat bildgalleri till ditt labbkonto. Om du svarade "Jag vet inte", kan du skjuta upp detta beslut till senare.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Vilka avbildningar i Azure Marketplace kommer dina klassrumslabb att använda?

Azure Marketplace innehåller hundratals avbildningar som du kan aktivera så att labbskapare kan använda avbildningen för att skapa sitt labb. Vissa bilder kan innehålla allt som ett labb redan behöver. I andra fall kan du använda en avbildning som utgångspunkt och sedan kan labbskaparen anpassa den genom att installera ytterligare program eller verktyg.

Om du inte vet vilka bilder du behöver använda kan du alltid komma tillbaka till detta senare för att aktivera dem. Det bästa sättet att se vilka bilder som är tillgängliga är också att först skapa ett labbkonto. Detta ger dig åtkomst, så att du kan granska listan över tillgängliga bilder och deras innehåll.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Behöver labbets virtuella datorer ha åtkomst till andra Azure- eller lokala resurser?

När du konfigurerar ett labbkonto har du också möjlighet att peer med ett virtuellt nätverk. För att avgöra om du behöver detta, överväga följande frågor:

- **Behöver du ge åtkomst till en licensieringsserver?**
  
   Om du planerar att använda Azure Marketplace-avbildningar paketeras kostnaden för operativsystemets licens i prissättningen för labbtjänster. Därför behöver du inte tillhandahålla licenser för själva operativsystemet. Men för ytterligare programvara och program som är installerade, behöver du ange en licens som är lämplig.

- **Behöver labbdammarna åtkomst till andra lokala resurser, till exempel en filresurs eller databas?**

   Du skapar ett virtuellt nätverk för att ge åtkomst till lokala resurser, vanligtvis med hjälp av en virtuell nätverksgateway för plats till plats. Om du inte har konfigurerat ett virtuellt nätverk måste du investera ytterligare tid för detta.

- **Behöver labb-virtuella datorer åtkomst till andra Azure-resurser som finns i ett virtuellt nätverk?**

   Om du behöver åtkomst till Azure-resurser som *inte* är skyddade i ett virtuellt nätverk kan du komma åt dessa resurser via det offentliga internet utan att göra någon peering.

Om du svarade "ja" på en eller flera frågor måste du peer labbkontot till ett virtuellt nätverk. Om du svarade "Jag vet inte", då kan du skjuta upp detta beslut till senare. Du kan alltid välja att peer ett virtuellt nätverk när du har skapat labbkontot.

## <a name="set-up-your-lab-account"></a>Konfigurera ditt labbkonto

När du har förstått kraven för ditt labbkonto är du redo att konfigurera det.

1. **Skapa ditt labbkonto.** Se handledningen om [hur du skapar ett labbkonto](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) för instruktioner.

   När du skapar ett labbkonto kan det vara bra att bekanta dig med de azure-resurser som ingår. Mer information finns i följande artiklar:

   - [Prenumeration](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Resursgrupp](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Labbkonto](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Labb i klassrummet](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Välja en region och plats](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Namnge vägledning för resurser](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Lägg till användare i rollen som labbskaparskapare.** Instruktioner finns i [lägga till användare i rollen labbskapare](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Mer information om de olika roller som kan tilldelas användare som hanterar labbkonton och labb finns i [guiden för hantering av identitet](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Anslut till ett virtuellt peer-nätverk.** Instruktioner finns i [ansluta labbets nätverk med ett virtuellt peer-nätverk](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Du kan också behöva läsa instruktioner om hur du [konfigurerar labbets virtuella datorers adressintervall](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Aktivera och granska bilder.** Instruktioner finns [i aktivera Azure Marketplace-avbildningar för labbskapare](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Om du vill granska innehållet i varje Azure Marketplace-avbildning markerar du avbildningsnamnet. Följande skärmbild visar till exempel information om avbildningen ubuntu Data Science VM:

   ![Skärmbild av Granska Azure Marketplace-avbildningar](../media/setup-guide/review-marketplace-images.png)

   Om du har ett delat bildgalleri kopplat till ditt labbkonto och du vill aktivera anpassade bilder som ska delas av labbskapare, kan du slutföra stegen som liknar dem som visas i följande skärmbild:

   ![Skärmbild av Aktivera anpassade bilder i ett delat bildgalleri](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Nästa steg

- [Hantera labbkonton](how-to-manage-lab-accounts.md)

- [Installationsguide för klassrumslabb](setup-guide.md)
