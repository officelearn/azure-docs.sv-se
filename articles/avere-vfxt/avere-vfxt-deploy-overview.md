---
title: Distributionsöversikt - Avere vFXT för Azure
description: Översikt över distribution av Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78140fea74272dff6056bebfbd44ed9d55b0e1db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153691"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT för Azure – distributionsöversikt

Den här artikeln innehåller en översikt över de steg som behövs för att få igång ett Avere vFXT för Azure-kluster.

Flera uppgifter behövs före och efter att du har skapat vFXT-klustret från Azure Marketplace. Att ha en tydlig känsla av start-till-finish-processen hjälper dig att begränsa den ansträngning som behövs.

## <a name="deployment-steps"></a>Distributionssteg

När du [har planerat systemet](avere-vfxt-deploy-plan.md)kan du börja skapa ditt Avere vFXT-kluster.

En Azure Resource Manager-mall på Azure Marketplace samlar in nödvändig information och distribuerar automatiskt hela klustret.

När vFXT-klustret är igång finns det fortfarande några konfigurationssteg att vidta innan du använder det. Om du har skapat en ny Blob-lagringsbehållare bör du flytta dina data till den. Om du använder ett NAS-lagringssystem måste du lägga till det när klustret har skapats. Du vill ansluta klienter till klustret.

Här är en översikt över alla steg.

1. Konfigurera förutsättningar

   Innan du skapar en virtuell dator måste du skapa en ny prenumeration för Avere vFXT-projektet, konfigurera prenumerationsägarskap, kontrollera kvoter och begära en ökning om det behövs och acceptera villkor för att använda Avere vFXT-programvaran. Läs [Förbered för att skapa Avere vFXT](avere-vfxt-prereqs.md) för detaljerade instruktioner.

1. Skapa Avere vFXT-klustret

   Använd Azure Marketplace för att skapa Avere vFXT för Azure-kluster. En mall samlar in nödvändig information och kör skript för att skapa slutprodukten.

   Klusterskapande innebär följande steg, som alla görs av marketplace-mallen:

   * Skapa ny nätverksinfrastruktur och resursgrupper om det behövs
   * Skapa en klusterstyrenhet

     Klusterstyrenheten är en enkel virtuell dator som finns i samma virtuella nätverk som Avere vFXT-klustret och har den anpassade programvara som behövs för att skapa och hantera klustret. Styrenheten skapar vFXT-noderna och bildar klustret, och den tillhandahåller också ett kommandoradsgränssnitt för att hantera klustret under sin livstid.

     Om du skapar ett nytt virtuellt nätverk eller undernät under distributionen har styrenheten en offentlig IP-adress. Det innebär att styrenheten kan fungera som en hoppvärd för anslutning till klustret utanför det virtuella nätverket.

   * Skapa virtuella datorer för klusternoder

   * Skapa klustret från de enskilda noderna

   * Du kan också skapa en ny Blob-behållare och konfigurera den som backend-lagring för klustret

   Klusterskapande beskrivs i detalj i [Distribuera vFXT-klustret](avere-vfxt-deploy.md).

1. Konfigurera klustret

   Anslut till avere vFXT-konfigurationsgränssnittet (Avere Kontrollpanel) för att anpassa klustrets inställningar. Välj supportövervakning och lägg till ditt lagringssystem om du använder maskinvarulagring eller ytterligare Blob-behållare.

   * [Komma åt vFXT-klustret](avere-vfxt-cluster-gui.md)
   * [Aktivera support](avere-vfxt-enable-support.md)
   * [Konfigurera lagring](avere-vfxt-add-storage.md) (om det behövs)

1. Montera klienter

   Följ riktlinjerna i [Mount the Avere vFXT-klustret](avere-vfxt-mount-clients.md) för att lära dig mer om belastningsutjämning och hur klientdatorer ska montera klustret.

1. Lägga till data (om det behövs)

   Eftersom Avere vFXT är en skalbar cache för flera klienter är det bästa sättet att flytta data till en ny sluten lagringsbehållare med en strategi för filkopiering med flera klienter med flera trådar.

   Om du behöver flytta arbetsuppsättningsdata till en ny Blob-behållare eller annat backend-lagringssystem följer du anvisningarna i [Flytta data till vFXT-klustret](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Nästa steg

Fortsätt att [förbereda för att skapa Avere vFXT](avere-vfxt-prereqs.md) för att slutföra de nödvändiga uppgifterna.
