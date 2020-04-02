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
ms.openlocfilehash: 88b37ea4ff717689f05afbb41d33a56a8cbb2c22
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547626"
---
# <a name="lab-account-setup-guide"></a>Installationsguide för labbkonto

Det första steget som måste slutföras av administratörer är att konfigurera ett labbkonto i din Azure-prenumeration.  Ett labbkonto är en behållare för dina klassrumslabb och tar bara några minuter att konfigurera.

## <a name="understand-your-schools-lab-account-requirements"></a>Förstå skolans krav på labbkonto

Om du vill förstå hur du konfigurerar ditt labbkonto baserat på skolans behov bör du tänka på följande frågor:

**Har jag åtkomst till en Azure-prenumeration?**

Om du vill skapa ett labbkonto behöver du åtkomst till en Azure-prenumeration som är konfigurerad för din skola. din skola kan ha en eller flera prenumerationer.  En prenumeration används för att hantera fakturering och säkerhet för alla dina Azure-resurser\tjänster som används i den, inklusive labbkonton.

**Hur många labbkonton behöver skapas?**

För att komma igång snabbt är en rimlig metod att skapa ett enda labbkonto och sedan skapa ytterligare labbkonton efter behov.  Du kan till exempel så småningom utvecklas till att ha ett labbkonto per avdelning.

**Vem ska vara ägare och bidragsgivare till labbkontot?**

Administratörerna är vanligtvis ägare\bidragsgivare för ett labbkonto eftersom de är ansvariga för att hantera de principer som gäller för alla labb som finns i labbkontot.  Den person som skapar labbkontot är automatiskt ägare.  Du kan lägga till ytterligare ägare\deltagare (vanligtvis från AAD-klienten som är associerad med din prenumeration) för att hantera ett labbkonto genom att tilldela rollen Ägare\Contributor på labbkontonivå.

**Vem får skapa labb?**

Du kan välja att låta administratörer och\eller fakultetsmedlemmar skapa och hantera labb. Dessa användare (vanligtvis från AAD-klienten som är associerade med din prenumeration) tilldelas rollen Lab Creator i labbkontot.

**Vill du ge labbskapare möjlighet att spara bilder som kan delas över labb?**

Ett delat bildgalleri är en databas som du kan använda för att spara och dela bilder.  Fördelen med detta är att om du har flera klasser som behöver samma bilder, lab skapare kan skapa bilden en gång och dela den över labb.  Men för att komma igång är det fullt rimligt att starta utan ett delat bildgalleri. och du kan alltid välja att lägga till en senare.

Om du har svarat ja på den här frågan måste du skapa och\eller bifoga ett delat bildgalleri till ditt labbkonto.  Om du svarade: "Jag vet inte", då kan du skjuta upp detta beslut till senare.

När du har ett delat bildgalleri kopplat till ditt labbkonto

**Vilka avbildningar på Azure Marketplace kommer dina klassrumslabb att använda?**

Azure Marketplace innehåller hundratals avbildningar som du kan aktivera så att labbskapare kan använda avbildningen för att skapa sitt labb.  Vissa bilder kan innehålla allt som ett labb redan behöver.  I andra fall kan du använda en bild som utgångspunkt och sedan labbet skaparen kan anpassa den genom att installera ytterligare program, verktyg, etc.

Om du inte vet vilka bilder du behöver använda kan du alltid komma tillbaka till detta senare för att aktivera dem.  Dessutom är det bästa sättet att se vilka bilder som är tillgängliga att först skapa ett labbkonto - detta ger dig tillgång så att du kan granska listan över tillgängliga bilder och deras innehåll.  Mer information ges nedan.
  
**Behöver labbets virtuella datorer ha åtkomst till andra Azure- eller on-prem-resurser?**

När du konfigurerar ett labbkonto har du också möjlighet att peer med ett virtuellt nätverk (VNet).  Om du vill bestämma om du behöver peer med ett virtuella nätverk bör du tänka på följande frågor:

- **Behöver du ge åtkomst till en licensieringsserver?**
  
   Om du planerar att använda Azure Marketplace-avbildningar paketeras kostnaden för OS-licensen i prissättningen för Lab Services, så du behöver *inte* tillhandahålla licenser för själva operativsystemet.  För ytterligare program\program som är installerade måste du dock tillhandahålla en licens efter behov.

- **Behöver labbdammarna åtkomst till andra on-prem-resurser, till exempel filresurs, databas osv.?**

   Ett virtuellt nätverk måste skapas för att ge åtkomst till resurser på prem, vanligtvis med hjälp av en virtuell nätverksgateway från plats till plats.  Om du inte har konfigurerat ett virtuella nätverk måste ytterligare tid investeras för detta.  Mer information om hur du ställer in detta finns nedan.

- **Behöver labb-virtuella datorer åtkomst till andra Azure-resurser som finns i ett virtuella nätverk?**

    Om du behöver åtkomst till Azure-resurser som *inte* är skyddade i ett virtuella nätverk kan du komma åt dessa resurser via det offentliga internet utan att göra någon peering.

    Om du svarade "Ja" på en eller flera frågor måste du peer labbkontot till ett virtuella nätverk.  Om du svarade, "Jag vet inte", då kan du skjuta upp detta beslut till senare eftersom du alltid kan välja att peer ett VNet efter att ha skapat labbkontot.

## <a name="set-up-your-lab-account"></a>Konfigurera ditt labbkonto

När du förstår kraven för ditt labbkonto är du redo att konfigurera det.  Följ länkarna i det här avsnittet om du vill se hur du konfigurerar ditt labbkonto:

1. **Skapa ditt labbkonto**

   Se handledningen om [hur du skapar ett labbkonto](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) för instruktioner.

   När du skapar ett labbkonto kan det vara bra att bekanta dig med de azure-resurser som ingår. Se följande lista för mer information och vägledning om hur du skapar dessa resurser:

   - [Prenumeration](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Resursgrupp](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Labbkonto](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Labb i klassrummet](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Välja en region\Plats](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Namnge vägledning för resurser](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Lägga till användare i rollen Labbskapare**

   Se självstudien om [hur du lägger till användare i rollen Lab Creator](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role) för instruktioner.

   Mer information om de olika roller som kan tilldelas användare som hanterar labblabbkonton och labb finns i [guiden för hantering av identitet](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Ansluta till ett peer-nätverk**

   Se instruktionsguiden om hur du [ansluter labbets nätverk med ett peer-nätverk](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) för instruktioner.

   Du kan också behöva läsa instruktionerna för att [konfigurera labbets virtuella datorers adressintervall](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Aktivera och granska bilder**

    Se instruktioner om hur du [aktiverar Marketplace-avbildningar för labbskapare.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

    Om du vill granska innehållet i varje Marketplace-bild klickar du på bildnamnet.  Se till exempel följande skärmbild som visar information om Ubuntu Data Science VM-avbildningen:

    ![Granska Marketplace-bilder](../media/setup-guide/review-marketplace-images.png)

    Om du har ett delat bildgalleri kopplat till ditt labbkonto och vill aktivera att anpassade bilder delas av labbskapare måste du utföra liknande steg som visas i följande skärmbild:

    ![Aktivera anpassade bilder i det delade bildgalleriet](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Hantera labbkonton](how-to-manage-lab-accounts.md)

- [Installationsguide för klassrumslabb](setup-guide.md)
