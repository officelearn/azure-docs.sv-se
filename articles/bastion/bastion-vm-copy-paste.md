---
title: 'Kopiera och klistra in till och från en virtuell dator: Azure Bastion'
description: I den här artikeln får du lära dig hur du kopierar och klistrar in på och från en Virtuell Azure-dator med Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 0af0fdfa1b0bcbb06d9424390e7ca8c8f406bcb3
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619305"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Kopiera och klistra in på en virtuell dator: Azure Bastion

Den här artikeln hjälper dig att kopiera och klistra in text till och från virtuella datorer när du använder Azure Bastion. Innan du arbetar med en virtuell dator kontrollerar du att du har följt stegen för att [skapa en bastionvärd](bastion-create-host-portal.md). Anslut sedan till den virtuella datorn som du vill arbeta med med hjälp av [rdp](bastion-connect-vm-rdp.md) eller [SSH](bastion-connect-vm-ssh.md).

För webbläsare som stöder den avancerade Api-åtkomsten för Urklipp kan du kopiera och klistra in text mellan den lokala enheten och fjärrsessionen på samma sätt som du kopierar och klistrar in mellan program på den lokala enheten. För andra webbläsare kan du använda verktygspaletten för åtkomstverktyg för Bastion.

   ![Tillåt Urklipp](./media/bastion-vm-manage/allow.png)

Endast textkopiering/inklistring stöds. Om du vill kopiera och klistra in direkt kan du uppmanas att komma åt Urklipp när Bastion-sessionen initieras. **Tillåt** webbsidan att komma åt Urklipp.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Kopiera till en fjärrsession

När du har anslutit till den virtuella datorn med [Azure-portalen ](https://portal.azure.com)gör du följande:

1. Kopiera texten/innehållet från den lokala enheten till det lokala Urklipp.
1. Under fjärrsessionen startar du verktygspaletten för åtkomstverktyg för Bastion genom att välja de två pilarna. Pilarna finns till vänster i mitten av sessionen.

   ![verktygspalett](./media/bastion-vm-manage/left.png)

   ![skrivskiva](./media/bastion-vm-manage/clipboard.png)
1. Den kopierade texten visas vanligtvis automatiskt på inklistringspaletten för Bastion-kopiering. Om texten inte finns där klistrar du in texten i textområdet på paletten.
1. När texten finns i textområdet kan du klistra in den i fjärrsessionen.

   ![Klistra in](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Kopiera från en fjärrsession

När du har anslutit till den virtuella datorn med [Azure-portalen ](https://portal.azure.com)gör du följande:

1. Kopiera texten/innehållet från fjärrsessionen till urklipp (med Ctrl-C).

   ![verktygspalett](./media/bastion-vm-manage/remote.png)
1. Under fjärrsessionen startar du verktygspaletten för åtkomstverktyg för Bastion genom att välja de två pilarna. Pilarna finns till vänster i mitten av sessionen.

   ![skrivskiva](./media/bastion-vm-manage/clipboard2.png)
1. Den kopierade texten visas vanligtvis automatiskt på inklistringspaletten för Bastion-kopiering. Om texten inte finns där klistrar du in texten i textområdet på paletten.
1. När texten finns i textområdet kan du klistra in den på den lokala enheten.

   ![Klistra in](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Nästa steg

Läs [Vanliga frågor om Bastion](bastion-faq.md).