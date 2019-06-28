---
title: Initialize hardware - Microsoft Azure FXT Edge Filer
description: Hur du ställer in ett initialt lösenord på Azure FXT Edge Filer noder
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 11cf9f49014648fff1e78aff91c5a724a812e9e7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450298"
---
# <a name="tutorial-set-hardware-passwords"></a>Självstudier: Ange lösenord för maskinvara

Första gången du power upp en Filer för Azure FXT Edge-nod, måste du ange ett rotlösenord. Maskinvarunoder levereras inte med en standardlösenord. 

Nätverksportar inaktiveras tills lösenordet anges och rotanvändaren loggar in.

Utför det här steget när du installerar och kablar noden, men innan du försöker att skapa klustret. 

Den här självstudien beskrivs hur du ansluter till noden maskinvara och ange lösenordet. 

I den här självstudien får du lära dig hur man: 

> [!div class="checklist"]
> * Anslut ett tangentbord och bildskärm till noden och slår på den
> * Ange lösenord för användaren iDRAC port och rot för den här noden
> * Logga in som rot 

Upprepa dessa steg för varje nod som du ska använda i klustret. 

Den här kursen tar cirka 15 minuter att slutföra. 

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudiekursen gör du följande: 

* [Installera](fxt-install.md) varje Azure FXT Edge Filer nod i ett utrustning rack, och koppla strömkablar och nätverksåtkomst som beskrivs i den [tidigare självstudien](fxt-network-power.md). 
* Hitta ett USB-anslutna tangentbord och en VGA-anslutna Övervakare som du kan koppla till maskinvarunoder. (Nodens seriell port är inaktiv innan du ställer in lösenordet.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Ansluta ett tangentbord och bildskärm till noden

Fysiskt ansluta en bildskärm och tangentbord till noden Azure FXT Edge Filer. 

* Anslut övervakaren till VGA-porten.
* Ansluta tangentbordet till en USB-port. 

Använd det här diagrammet referens för att hitta portar på baksidan av chassit. 

> [!NOTE]
> Den seriella porten är inaktiv tills när du har angett lösenordet. 

![diagram över baksidan av Azure FXT Edge Filer med serienummer, VGA, och USB-portar som är märkt](media/fxt-back-serial-vga-usb.png)

Du kan använda en KVM-växel om du vill ansluta fler än en nod till samma kringutrustning. 

Slå på noden genom att trycka på strömknappen på klientwebbservern. 

![diagram över framför Azure FXT Edge Filer - avrunda strömknappen klassificerades längst upp höger](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Ange inledande lösenord 

Noden Azure FXT Edge Filer skrivs olika meddelanden i övervakaren vid start. Efter en liten stund visas en inledande konfiguration av företagsåtkomst så här:

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

* Det är tillfälligt rotlösenordet för den här noden Azure FXT Edge Filer. 

  Det här lösenordet ändras när du skapar ett kluster med den här noden, eller när du lägger till den här noden i klustret. Hantering av klusterlösenordet (som är associerade med användaren ``admin``) är också rotlösenordet för alla noder i ett kluster.

* Det är långsiktig lösenordet för hanteringsporten iDRAC/IPMI-maskinvara.

  Kontrollera att du kommer ihåg lösenordet om du behöver att logga in med IPMI senare att felsöka problem med maskinvaran.

Ange och bekräfta lösenordet: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

När du har angett lösenordet fortsätter systemet att starta. När den är klar får en ``login:`` prompten. 

## <a name="sign-in-as-root"></a>Logga in som rot

Logga in som ``root`` med det lösenord som du anger. 

```
login: root
Password:**********
```

När du har loggat in som rot nätverksportar som är aktiva och kommer att kontakta DHCP-servern för IP-adresser. 

## <a name="next-steps"></a>Nästa steg

Noden är redo att vara en del av ett kluster. Du kan använda den för att skapa klustret Azure FXT Edge Filer eller du kan [lägga till den i ett befintligt kluster](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Skapa ett kluster](fxt-cluster-create.md)
