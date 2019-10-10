---
title: Distributions översikt – aver vFXT för Azure
description: Översikt över distribution av AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 9684b230b8790e36cd7442c65481c0c71ce185d6
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255421"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Aver vFXT för Azure – distributions översikt

Den här artikeln ger en översikt över de steg som krävs för att hämta ett AVERT vFXT för Azure-kluster igång.

Flera aktiviteter behövs innan och efter att du har skapat vFXT-klustret från Azure Marketplace. Att ha en tydlig uppfattning om start-till-slut-processen hjälper dig att begränsa den insats som behövs. 

## <a name="deployment-steps"></a>Distributionssteg

När du har [planerat systemet](avere-vfxt-deploy-plan.md)kan du börja skapa ditt AVERT vFXT-kluster. 

En Azure Resource Manager-mall på Azure Marketplace samlar in nödvändig information och distribuerar automatiskt hela klustret. 

När vFXT-klustret är igång vill du veta hur du ansluter klienter till den och, om det behövs, hur du flyttar dina data till den nya Blob storage-behållaren.  

Här följer en översikt över alla steg.

1. Konfigurera krav 

   Innan du skapar en virtuell dator måste du skapa en ny prenumeration för ditt AVERT vFXT-projekt, konfigurera prenumerationens ägarskap, kontrol lera kvoterna och begära en ökning om det behövs, och acceptera villkoren för att använda programmet AVERT vFXT. Mer information finns i [förbereda för att skapa ett AVERT-vFXT](avere-vfxt-prereqs.md) .

1. Skapa ett AVERT vFXT-kluster 

   Använd Azure Marketplace för att skapa ett AVERT-vFXT för Azure-kluster. En mall samlar in nödvändig information och kör skript för att skapa den slutliga produkten.

   Kluster skapandet omfattar de här stegen, som alla görs av Marketplace-mallen: 

   * Skapa nya nätverks infrastrukturer och resurs grupper, om det behövs
   * Skapa en *kluster styrenhet*  

     Kluster styrenheten är en enkel virtuell dator som finns i samma virtuella nätverk som det AVERT vFXT-klustret och har den anpassade program vara som krävs för att skapa och hantera klustret. Kontrollanten skapar vFXT-noderna och formaterar klustret och ger också ett kommando rads gränssnitt för att hantera klustret under dess livs längd.

     Om du skapar ett nytt VNet under distributionen kommer din kontrollant att ha en offentlig IP-adress. Det innebär att styrenheten kan fungera som en hopp värd för att ansluta till klustret utanför det virtuella nätverket.

   * Skapa de virtuella klusternoderna

   * Konfigurera de virtuella klusternoderna för att bilda klustret

   * Du kan också skapa en ny BLOB-behållare och konfigurera den som server dels lagring för klustret

1. Konfigurera klustret 

   Anslut till konfigurations gränssnittet AVERT vFXT (AVERT kontroll panel) om du vill anpassa klustrets inställningar. Anmäl dig till support övervakning och Lägg till ditt lagrings system om du använder ett lokalt Data Center.

   * [Komma åt vFXT-klustret](avere-vfxt-cluster-gui.md)
   * [Aktivera stöd](avere-vfxt-enable-support.md)
   * [Konfigurera lagring](avere-vfxt-add-storage.md) (om det behövs)

1. Montera klienter

   Följ rikt linjerna i [montera vFXT-klustret](avere-vfxt-mount-clients.md) för att lära dig mer om belastnings utjämning och hur klient datorer ska montera klustret.

1. Lägg till data (om det behövs)

   Eftersom AVERT vFXT är ett skalbart cacheminne för flera klienter, är det bästa sättet att flytta data till en ny server dels lagrings behållare med flera klienter, multitrådad fil kopierings strategi. Mer information finns i [Flytta data till vFXT-klustret](avere-vfxt-data-ingest.md) .

## <a name="next-steps"></a>Nästa steg

Fortsätt att [förbereda för att skapa ett AVERT-vFXT](avere-vfxt-prereqs.md) för att slutföra de förberedande uppgifterna för att distribuera AVERT VFXT för Azure. 