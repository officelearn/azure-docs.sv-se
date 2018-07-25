---
title: Skapa ett NetApp-konto för åtkomst till Azure NetApp Files | Microsoft Docs
description: Beskriver hur du kommer åt Azure NetApp Files och skapar ett NetApp-konto så att du kan konfigurera en kapacitetspool och skapa en volym.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: ad8cc550ce69e4dc4c19a569718fa873a65b3620
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010352"
---
# <a name="create-a-netapp-account"></a>Skapa ett NetApp-konto
Genom att skapa ett NetApp-konto kan du konfigurera en kapacitetspool och sedan skapa en volym. Du använder bladet Azure NetApp-filer för att skapa ett nytt NetApp-konto.

## <a name="before-you-begin"></a>Innan du börjar
Du måste ha tillstånd att komma åt Azure Resource Provider för Microsoft.NetApp och ha konfigurerats att använda Azure NetApp Files-tjänsten.  

[Registreringssidan för den allmänt tillgängliga förhandsversionen av Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="steps"></a>Steg 

1. Leta upp Azure Portal-URL:en för förhandsversionen i din inbjudan till förhandsversionen och logga in på portalen. 
2.  Gå till bladet Azure NetApp-filer genom att använda någon av följande metoder:  
  * Sök efter **Azure NetApp Files** i sökrutan på Azure Portal.  
  * Klicka på **Alla tjänster** i navigeringen och filtrera sedan på Azure NetApp Files.  

  Du kan lägga till bladet Azure NetApp-filer som en favorit genom att klicka på stjärnikonen bredvid det. 

3. Klicka på **+ Lägg till** för att skapa ett nytt NetApp-konto.  
  Fönstret Nytt NetApp-konto visas.  

4. Ange följande information för NetApp-kontot: 
  * **Kontonamn**  
    Ange ett unikt namn för prenumerationen.
  *  **Prenumeration**  
    Välj en prenumeration från dina befintliga prenumerationer.
  * **Resursgrupp**   
    Använd en befintlig resursgrupp eller skapa en ny.
  * **Plats**  
    Välj en region för kontot och dess underordnade resurser.  
    Azure NetApp Files-tjänsten stöds för närvarande endast i regionen Östra USA.  

    ![Nytt NetApp-konto](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Klicka på **Skapa**.     
  NetApp-kontot som du skapat visas nu på bladet Azure NetApp-filer. 

## <a name="next-steps"></a>Nästa steg  

1. [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
2. [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
3. [Konfigurera exportprincipen för en volym (valfritt)](azure-netapp-files-configure-export-policy.md)
