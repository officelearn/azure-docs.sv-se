---
title: Översikt över distribution - Avere vFXT för Azure
description: Översikt över distribution Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: aa5737d67ea2c9cb8cc7c7098764ae67fc91137d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634467"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT för Azure – distributionsöversikt

Den här artikeln ger en översikt över de steg som krävs för att hämta en Avere vFXT för Azure-kluster vara igång och körs.

Första gången du distribuerar ett Avere vFXT system, märker du att den omfattar fler steg än att distribuera de flesta andra Azure-verktyg. Att ha en tydlig uppfattning om hur start till slut kan du begränsa det arbete som behövs. När systemet är igång, gör språkets kraft att påskynda molnbaserad beräkning uppgifter det värt att arbetet.

## <a name="deployment-steps"></a>Distributionssteg

Efter [planera systemet](avere-vfxt-deploy-plan.md), kan du börja skapa Avere vFXT klustret. 

Börja med att skapa en kluster-styrenhet VM som används för att skapa klustret vFXT.

När vFXT klustret är igång kan du vill veta hur du ansluter klienter till den vid behov, hur du flyttar dina data till den nya Blob storage-behållaren.  

Här är en översikt över alla steg.

1. Konfigurera krav 

   Innan du skapar en virtuell dator måste du skapa en ny prenumeration för Avere vFXT projektet, konfigurera äganderätten till prenumerationen, kontrollera kvoter och begära en ökning om det behövs och acceptera villkoren för att använda Avere vFXT programvara. Läs [förberedelser för att skapa Avere vFXT](avere-vfxt-prereqs.md) detaljerade anvisningar.

1. Skapa kluster-kontrollant

   Den *kluster controller* är en enkel virtuell dator som finns i samma virtuella nätverk som Avere vFXT klustret. Kontrollanten skapar vFXT noderna och formulär klustret och det ger också ett kommandoradsgränssnitt för att hantera klustret under dess livslängd.

   Om du konfigurerar din kontrollant med en offentlig IP-adress måste kan den också fungera som en jump-värd för att ansluta till klustret från utanför det virtuella nätverket.

   Alla program som behövs för att skapa klustret vFXT och hantera dess noder är förinstallerade på kontrollanten kluster.

   Läs [skapa klustret kontrollant VM](avere-vfxt-deploy.md#create-the-cluster-controller-vm) mer information.

1. Skapa en körningsrollen för klusternoderna 

   Azure använder [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) för att auktorisera klusternodens virtuella datorer att utföra vissa uppgifter. Till exempel måste klusternoderna kunna tilldela eller omtilldelar IP-adresser till andra noder i klustret. Innan du skapar klustret måste du definiera en roll som ger dem behörighet.

   Kontrollanten kluster förinstallerade program innehåller en prototyp roll som du kan anpassa. Läs [skapa klusterrollen noden åtkomst](avere-vfxt-deploy.md#create-the-cluster-node-access-role) anvisningar.

1. Skapa Avere vFXT kluster 

   Redigera skriptet lämpliga kluster skapas på Kontrollpanelen och kör den för att skapa klustret. [Redigera skriptet för distribution](avere-vfxt-deploy.md#edit-the-deployment-script) har detaljerade anvisningar. 

1. Konfigurera klustret 

   Anslut till Avere vFXT Konfigurationsgränssnittet (Avere Kontrollpanelen) att anpassa inställningar för klustrets. Anmäla dig till stöd för övervakning och Lägg till lagringssystemet om du använder ett lokalt datacenter.

   * [Åtkomst till vFXT klustret](avere-vfxt-cluster-gui.md)
   * [Aktivera stöd för](avere-vfxt-enable-support.md)
   * [Konfigurera lagring](avere-vfxt-add-storage.md) (vid behov)

1. Montera klienter

   Följ riktlinjerna i [montera Avere vFXT klustret](avere-vfxt-mount-clients.md) vill veta mer om belastningsutjämning och hur klientdatorer ska ansluta till klustret.

1. Lägg till data (vid behov)

   Eftersom Avere vFXT är en skalbar flera cache, är det bästa sättet att flytta data till en ny backend-storage-behållare med flera klienten, flertrådat fil kopia strategi. Läs [flytta data till klustret vFXT](avere-vfxt-data-ingest.md) mer information.

## <a name="next-steps"></a>Nästa steg

Fortsätta att [förberedelser för att skapa Avere vFXT](avere-vfxt-prereqs.md) utföra preliminär åtgärder för att distribuera Avere vFXT för Azure. 