---
title: Få support för Avere vFXT för Azure
description: Förklaring av att öppna supportärenden om Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d621511cbb6983f8ad57ea8305823039475f40d0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634388"
---
# <a name="get-help-with-your-system"></a>Få hjälp med ditt system

Om du behöver hjälp med din Avere vFXT för Azure, är här de olika sätten att få support:

* **Avere vFXT problemet** – använda Azure-portalen för att öppna ett supportärende för din Avere vFXT enligt beskrivningen [nedan](#open-a-support-ticket-for-your-avere-vfxt).
* **Kvot** – om du har en kvot-relaterade problem [öka kvoten](#request-a-quota-increase)
* **Dokumentation och exempel** – om du upptäcker problem med den här dokumentationen eller exempel, bläddra längst ned på sidan med problemet och Använd den **Feedback** avsnitt för att söka efter befintliga problem och en ny en if-filen behövs.  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Öppna ett supportärende för din Avere vFXT

Om det uppstår problem när du distribuerar eller använder Avere vFXT be om hjälp via Azure-portalen.  

Följ dessa steg för att se till att ditt supportärende är taggade med en resurs från klustret. Tagga biljetten hjälper oss att dirigera till korrekt support-resurs. 

1. Från [ https://portal.azure.com ](https://portal.azure.com)väljer **resursgrupper**.

   ![Skärmbild av Azure portal vänstra meny med ”resursgrupper” inringade](media/avere-vfxt-ticket-rg.png)

1. Bläddra till den resursgrupp som innehåller vFXT klustret där problemet uppstod och klicka på någon av de virtuella datorerna Avere.

    ![Skärmbild av Azure portal resource group ”översikt” panelen med en viss virtuell dator inringade](media/avere-vfxt-ticket-vm.png)

1. Bläddra längst ned i den vänstra panelen i VM-sidan och klicka på **ny supportbegäran**.

    ![Skärmbild av Azure portal VM sidan för den virtuella datorn från föregående skärmbild. Den vänstra menyn rullas ned till botten och ”ny supportbegäran” är inringat.](media/avere-vfxt-ticket-request.png)

1. Klicka på sidan en supportförfrågan **alla tjänster** och tittar du under **Storage** att välja **Avere vFXT**.

    ![Skärmbild av skärmen begäran nya support för Azure-portalen med rubriken ”grundläggande” och en cirkel runt ”tjänst”-objekt. Knappen ”alla tjänster” är markerad och det nedrullningsbara menyn fältet har värdet ”Avere vFXT”](media/avere-vfxt-ticket-service.png)

1. På sidan två, väljer du typen av problem och kategori som bäst matchar ditt problem. Lägg till ett kort rubrik och en beskrivning som innehåller tiden problemet uppstod. 

   ![Skärmbild av skärmen begäran nytt stöd för med rubriken ”Problem”, som innehåller många fält som behöver slutföras](media/avere-vfxt-ticket-problem.png)

1. På sidan tre, Fyll i din kontaktinformation och klicka på **skapa**. Ett bekräftelse- och biljett tal kommer att skickas till din e-postadress och en medlem i personalen support kommer att kontakta dig.

## <a name="request-a-quota-increase"></a>Öka kvoten

Läs [kvot för klustret vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) vill veta vilka komponenter som krävs för att distribuera Avere vFXT för Azure. Du kan [öka kvoten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) från Azure-portalen.