---
title: 'Självstudiekurs: Initiera maskinvara - Azure FXT Edge Filer'
description: Så här anger du ett första lösenord på Azure FXT Edge Filer-noder
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8cb5f639deb0630575c46db30efe70ad967324a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550899"
---
# <a name="tutorial-set-hardware-passwords"></a>Självstudiekurs: Ange maskinvarulösenord

Första gången du öppnar en Azure FXT Edge Filer-nod måste du ange ett rotlösenord. Maskinvarunoderna levereras inte med ett standardlösenord. 

Nätverksportar inaktiveras tills lösenordet har angetts och rotanvändaren loggar in.

Gör det här steget efter installation och kabeldragning noden, men innan du försöker skapa klustret. 

I den här självstudien beskrivs hur du ansluter till maskinvarunoden och anger lösenordet. 

I den här självstudien får du lära dig hur man: 

> [!div class="checklist"]
> * Anslut ett tangentbord och en bildskärm till noden och slå på det
> * Ange lösenord för iDRAC-porten och rotanvändaren på den här noden
> * Logga in som rot 

Upprepa dessa steg för varje nod som du ska använda i klustret. 

Den här självstudien tar cirka 15 minuter att slutföra. 

## <a name="prerequisites"></a>Krav

Innan du startar den här självstudien ska du utföra följande steg: 

* [Installera](fxt-install.md) varje Azure FXT Edge Filer-nod i ett utrustningsställ och anslut strömkablar och nätverksåtkomst enligt beskrivningen i den [tidigare självstudien](fxt-network-power.md). 
* Hitta ett USB-anslutet tangentbord och en VGA-ansluten bildskärm som du kan ansluta till maskinvarunoderna. (Nodens seriella port är inaktiv innan du anger lösenordet.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Ansluta ett tangentbord och bildskärm till noden

Anslut en bildskärm och ett tangentbord fysiskt till noden Azure FXT Edge Filer. 

* Anslut bildskärmen till VGA-porten.
* Anslut tangentbordet till en av USB-portarna. 

Använd det här referensdiagrammet för att hitta portarna på baksidan av chassit. 

> [!NOTE]
> Den seriella porten är inaktiv tills lösenordet har angetts. 

![diagram över baksidan av Azure FXT Edge Filer med seriella VGA- och USB-portar märkta](media/fxt-back-serial-vga-usb.png)

Du kan använda en KVM-växel om du vill ansluta mer än en nod till samma kringutrustning. 

Slå på noden genom att trycka på strömbrytaren på framsidan. 

![diagram över framsidan av Azure FXT Edge Filer - rund strömbrytare är märkt nära det övre högra](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Ange inledande lösenord 

Azure FXT Edge Filer-noden skriver ut olika meddelanden till bildskärmen vid uppstart. Efter en stund visas en första inställningsskärm så här:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

Lösenordet du anger används för två saker: 

* Det är det tillfälliga rotlösenordet för den här Azure FXT Edge Filer-noden. 

  Det här lösenordet ändras när du skapar ett kluster med den här noden eller när du lägger till den här noden i klustret. Lösenordet för klusterhantering (associerat med användaren) ``admin``är också rotlösenordet för alla noder i ett kluster.

* Det är det långsiktiga lösenordet för iDRAC/IPMI-maskinvaruhanteringsporten.

  Se till att du kommer ihåg lösenordet om du behöver logga in med IPMI senare för att felsöka ett maskinvaruproblem.

Ange och bekräfta lösenordet: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

När du har angett lösenordet fortsätter systemet att starta. När den är klar, ``login:`` det ger en uppmaning. 

## <a name="sign-in-as-root"></a>Logga in som rot

Logga in ``root`` som med det lösenord som du just angav. 

```
login: root
Password:**********
```

När du har loggat in som root är nätverksportarna aktiva och kontaktar DHCP-servern för IP-adresser. 

## <a name="next-steps"></a>Nästa steg

Noden är klar att ingå i ett kluster. Du kan använda den för att skapa Azure FXT Edge Filer-klustret eller [lägga till det i ett befintligt kluster](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Skapa ett kluster](fxt-cluster-create.md)
