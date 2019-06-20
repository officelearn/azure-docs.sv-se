---
title: 'Kopiera och klistra in till och från en virtuell dator: Azure Bastion  | Microsoft Docs'
description: I den här artikeln lär du dig hur kopiera och klistra in till och från en Azure-dator med hjälp av Skyddsmiljön.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191816"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>Kopiera och klistra in till en virtuell dator: Azure Skyddsmiljö (förhandsversion)

Den här artikeln hjälper dig att kopiera och klistra in texten till och från virtuella datorer när du använder Azure Skyddsmiljö. Innan du börjar arbeta med en virtuell dator, kontrollera att du har följt stegen för att [skapa en Skyddsmiljö-värd](bastion-create-host-portal.md). Sedan kan ansluta till den virtuella datorn som du vill arbeta med med hjälp av antingen [RDP](bastion-connect-vm-rdp.md) eller [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

För webbläsare som stöder avancerade Urklipp API-åtkomst, kan du kopiera och klistra in text mellan din lokala enhet och fjärrsessionen på samma sätt som du kopiera och klistra in mellan program på din lokala enhet. Du kan använda paletten Skyddsmiljö Urklipp åtkomst verktyget för andra webbläsare.

  ![Tillåt Urklipp](./media/bastion-vm-manage/allow.png)

Endast text kopiera och klistra in stöds. För direkt kopiera och klistra in kan din webbläsare efterfrågas tillgång till Urklipp när Skyddsmiljö sessionen initieras. **Tillåt** webbsida för åtkomst till Urklipp.

## <a name="to"></a>Kopiera till en fjärrsession

När du har anslutit till den virtuella datorn med den [Azure-portalen](https://aka.ms/BastionHost) för förhandsversionen av Skyddsmiljö:

1. Kopiera text/innehåll från en lokal enhet till lokala Urklipp.
1. Starta paletten Skyddsmiljö Urklipp åtkomst verktyget genom att välja de två pilarna under till fjärrsessionen. Pilarna finns på vänsterkant sessionen.

    ![verktyget palette](./media/bastion-vm-manage/left.png)

    ![Urklipp](./media/bastion-vm-manage/clipboard.png)

1. Normalt visar automatiskt den kopierade texten på paletten Skyddsmiljö kopiera klistra in. Om texten inte finns där, klistra in den i textområdet på paletten.
1. När texten är i textområdet för, kan du klistra in den fjärrsessionen.

    ![Klistra in](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Kopiera från en fjärrsession

När du har anslutit till den virtuella datorn med den [Azure-portalen](https://aka.ms/BastionHost) för förhandsversionen av Skyddsmiljö:

1. Kopiera textinnehållet/från fjärrsessionen till fjärranslutna Urklipp (med Ctrl-C).

    ![verktyget palette](./media/bastion-vm-manage/remote.png)

1. Starta paletten Skyddsmiljö Urklipp åtkomst verktyget genom att välja de två pilarna under till fjärrsessionen. Pilarna finns på vänsterkant sessionen.

    ![Urklipp](./media/bastion-vm-manage/clipboard2.png)

1. Normalt visar automatiskt den kopierade texten på paletten Skyddsmiljö kopiera klistra in. Om texten inte finns där, klistra in den i textområdet på paletten.
1. När texten är i textområdet för, kan du klistra in den till den lokala enheten.

    ![Klistra in](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Nästa steg

Läs den [Skyddsmiljö vanliga frågor och svar](bastion-faq.md).