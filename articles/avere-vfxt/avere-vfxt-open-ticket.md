---
title: Så här får du support för AVERT vFXT för Azure
description: Förklaring till att öppna support ärenden om AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 8a371f902703287ed3105ed53a57d6341b9528d4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415116"
---
# <a name="get-help-with-your-system"></a>Få hjälp med systemet

Om du behöver hjälp med ditt AVERT vFXT för Azure kan du få support på olika sätt:

* **Aver vFXT-problem** – Använd Azure Portal för att öppna ett support ärende för ditt AVERT vFXT enligt beskrivningen [nedan](#open-a-support-ticket-for-your-avere-vfxt).
* **Kvot** – om du har ett kvot problem kan du [begära en kvot ökning](#request-a-quota-increase)
* **Dokumentation och exempel** – om du hittar problem med den här dokumentationen eller exemplen bläddrar du längst ned på sidan med problemet och använder **feedback** -avsnittet för att söka efter befintliga problem och sedan på en ny fil om det behövs.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Öppna ett support ärende för ditt AVERT vFXT

Om du får problem när du distribuerar eller använder AVERT vFXT kan du be om hjälp via Azure Portal.

Följ dessa steg för att se till att ditt support ärende är taggat med en resurs från klustret. Att tagga biljetten hjälper oss att vidarebefordra den till rätt support resurs.

1. Välj **resurs grupper**från [https://portal.azure.com](https://portal.azure.com).

   ![skärm bild av Azure Portal vänstra menyn med "resurs grupper" inringade](media/avere-vfxt-ticket-rg.png)

1. Bläddra till den resurs grupp som innehåller vFXT-klustret där problemet uppstod och klicka på något av de AVERT virtuella datorerna.

    ![skärm bild av översikts panelen för Azure Portal resurs gruppen med en viss virtuell dator som är inringad](media/avere-vfxt-ticket-vm.png)

1. På sidan VM rullar du längst ned i den vänstra panelen och klickar på **ny supportbegäran**.

    ![Skärm bild av den Azure Portal VM-sidan för den virtuella datorn från föregående skärm bild. Den vänstra menyn rullas längst ned och "New Support Request" är inringad.](media/avere-vfxt-ticket-request.png)

1. På sidan en av support förfrågan klickar du på **alla tjänster** och tittar under **lagring** för att välja **aver vFXT**.

    ![skärm bild av den nya support förfrågan skärmen i Azure Portal med rubriken "grunder" och en cirkel runt "service"-objektet. Knappen alla tjänster är markerad och den nedrullningsbara meny fältet har värdet "aver vFXT"](media/avere-vfxt-ticket-service.png)

1. På sidan två väljer du den problem typ och kategori som bäst motsvarar ditt problem. Lägg till en kort rubrik och en beskrivning som innehåller den tidpunkt då problemet uppstod.

   ![skärm bild av den nya skärmen med support förfrågan med rubriken "problem", som innehåller många fält som måste fyllas i](media/avere-vfxt-ticket-problem.png)

1. På sidan tre fyller du i din kontakt information och klickar på **skapa**. Ett bekräftelse-och biljett nummer skickas till din e-postadress och en support personal medlem kommer att kontakta dig.

## <a name="request-a-quota-increase"></a>Begär en kvot ökning

Läs [kvoten för vFXT-klustret](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) för att lära dig vilka komponenter som behövs för att distribuera AVERT VFXT för Azure. Du kan [begära en kvot ökning](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) från Azure Portal.
