---
title: 'Kopiera och klistra in till och från en virtuell dator: Azure skydds | Microsoft Docs'
description: I den här artikeln får du lära dig hur du kopierar och klistrar in till och från en virtuell Azure-dator med skydds.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: ed8d52a4932271020dfb2a010392b312fa38703b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73519357"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Kopiera och klistra in på en virtuell dator: Azure skydds

Den här artikeln hjälper dig att kopiera och klistra in text till och från virtuella datorer när du använder Azure-skydds. Innan du arbetar med en virtuell dator bör du kontrol lera att du har följt stegen för att [skapa en skydds-värd](bastion-create-host-portal.md). Anslut sedan till den virtuella dator som du vill arbeta med via [RDP](bastion-connect-vm-rdp.md) eller [SSH](bastion-connect-vm-ssh.md).

För webbläsare som har stöd för den avancerade API-åtkomsten till Urklipp, kan du kopiera och klistra in text mellan den lokala enheten och fjärrsessionen på samma sätt som du kopierar och klistrar in mellan program på din lokala enhet. För andra webbläsare kan du använda skydds urklipps Access verktyg-paletten.

   ![Tillåt Urklipp](./media/bastion-vm-manage/allow.png)

Endast text kopiering och inklistring stöds. För direkt kopiering och inklistring kan webbläsaren uppmana dig att få åtkomst till Urklipp när skydds-sessionen initieras. **Tillåt** att webb sidan får åtkomst till Urklipp.

## <a name="to"></a>Kopiera till en fjärrsession

När du har anslutit till den virtuella datorn med [Azure Portal ](https://portal.azure.com)utför du följande steg:

1. Kopiera texten/innehållet från den lokala enheten till lokalt Urklipp.
1. Under fjärrsessionen startar du paletten skydds urklipps åtkomst verktyg genom att välja de två pilarna. Pilarna finns i mitten till vänster i sessionen.

   ![verktygs palett](./media/bastion-vm-manage/left.png)

   ![involvera](./media/bastion-vm-manage/clipboard.png)
1. Normalt visas den kopierade texten automatiskt på skydds kopiera klistra in-palett. Om texten inte finns där klistrar du in texten i text rutan på paletten.
1. När texten finns i text områden kan du klistra in den i fjärrsessionen.

   ![Inklistringsfel](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Kopiera från en fjärran sluten session

När du har anslutit till den virtuella datorn med [Azure Portal ](https://portal.azure.com)utför du följande steg:

1. Kopiera texten/innehållet från fjärrsessionen till fjärran sluten Urklipp (med CTRL-C).

   ![verktygs palett](./media/bastion-vm-manage/remote.png)
1. Under fjärrsessionen startar du paletten skydds urklipps åtkomst verktyg genom att välja de två pilarna. Pilarna finns i mitten till vänster i sessionen.

   ![involvera](./media/bastion-vm-manage/clipboard2.png)
1. Normalt visas den kopierade texten automatiskt på skydds kopiera klistra in-palett. Om texten inte finns där klistrar du in texten i text rutan på paletten.
1. När texten finns i text område kan du klistra in den på den lokala enheten.

   ![Inklistringsfel](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).