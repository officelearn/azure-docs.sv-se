---
title: Så här får du support för Avere vFXT för Azure
description: Förklaring av att öppna supportbiljetter om Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252563"
---
# <a name="get-help-with-your-system"></a>Få hjälp med ditt system

Om du vill ha hjälp med ditt Avere vFXT för Azure-system kan du få support på följande sätt:

* **Avere vFXT-problem** - Använd Azure-portalen för att öppna en supportbiljett för din Avere vFXT enligt [beskrivningen nedan](#open-a-support-ticket-for-your-avere-vfxt).
* **Kvot** - Om du har ett kvotrelaterat [problem, begär en kvotökning](#request-a-quota-increase)
* **Dokumentation och exempel** - Om du hittar problem med den här dokumentationen eller exemplen bläddrar du längst ned på sidan med problemet och använder avsnittet **Feedback** för att söka efter befintliga problem och fila en ny om det behövs.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Öppna en supportbiljett för din Avere vFXT

Om du stöter på problem när du distribuerar eller använder Avere vFXT kan du begära hjälp via Azure-portalen.

Följ dessa steg för att se till att supportbiljetten är taggad med en resurs från klustret. Taggning av biljetten hjälper oss att dirigera den till rätt supportresurs.

1. Välj [https://portal.azure.com](https://portal.azure.com) **Resursgrupper i**. Bläddra till resursgruppen som innehåller vFXT-klustret där problemet uppstod och klicka på en av Avere-kluster virtuella datorer.

    ![skärmbild av Azure Portal resource group "overview" panel med en viss virtuell dator inringad](media/avere-vfxt-ticket-vm.png)

1. På sidan Virtuell dator bläddrar du ned till den nedre delen av den vänstra panelen och klickar på **Ny supportbegäran**.

    ![Skärmbild av sidan Virtuell Azure-portal för den virtuella datorn från föregående skärmbild. Den vänstra menyn rullas längst ned och "Ny supportbegäran" är inringad.](media/avere-vfxt-ticket-request.png)

1. På den första sidan i supportbegäran väljer du ärendetypen och kontrollerar att rätt prenumeration är markerad.

   Under **Service**klickar du på **Alla tjänster** och tittar under **Lagring** för att välja **Avere vFXT**.

   Lägg till en kort sammanfattning och välj problemtyp.

    ![skärmbild av en ny supportbegäransskärm i Azure-portalen. Fliken Grunderna är markerad. Skärmobjekt inkluderar ärendetyp, Prenumeration, Tjänst, Sammanfattning och Problemtyp.](media/ticket-basics.png)

   Klicka på **Nästa** för att fortsätta.

1. Den andra sidan i supportformuläret innehåller förslag på hur du åtgärdar problemet baserat på din sammanfattningsbeskrivning. Klicka på **knappen Nästa** längst ned om du fortfarande behöver skapa en supportbiljett.

   ![skärmbild av skärmen för ny supportbegäran med fliken Lösningar markerat. Ett textfält i mitten har rubriken "Rekommenderad lösning" och förklarar möjliga åtgärder.](media/ticket-solutions.png)

1. På den tredje sidan anger du information – detta inkluderar information om klustret, den tid då problemet uppstod, allvarlighetsgraden och hur du kontaktar dig. Fyll i informationen och klicka på knappen **Nästa** längst ned.

   ![skärmbild av skärmen för ny supportbegäran med fliken Detaljer markerat. Informationsfälten är ordnade i kategorierna "Probleminformation", "Supportmetod" och "Kontaktinformation".](media/ticket-details.png)

1. Granska informationen på den sista sidan och klicka på **Skapa**. En bekräftelse och biljettnummer kommer att skickas till din e-postadress, och en supportanställd kommer att kontakta dig.

## <a name="request-a-quota-increase"></a>Begär en kvotökning

Läs [kvot för vFXT-klustret för](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) att ta reda på vilka komponenter som behövs för att distribuera Avere vFXT för Azure. Du kan [begära en kvotökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) från Azure-portalen.
