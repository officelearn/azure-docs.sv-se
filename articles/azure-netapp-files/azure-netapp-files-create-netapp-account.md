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
ms.openlocfilehash: 47b9d25f8db2241bb578528780e28f43d56371e5
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963088"
---
# <a name="create-a-netapp-account"></a>Skapa ett NetApp-konto
Genom att skapa ett NetApp-konto kan du konfigurera en kapacitetspool och sedan skapa en volym. Du använder bladet Azure NetApp-filer för att skapa ett nytt NetApp-konto.

## <a name="before-you-begin"></a>Innan du börjar
Du måste ha registrerat din prenumeration för att använda NetApp-resursprovidern och funktionen för offentlig förhandsversion.

[Registrera dig för Azure NetApp Files](azure-netapp-files-register.md)

## <a name="steps"></a>Steg 

1. Logga in på Azure Portal. 
2. Gå till bladet Azure NetApp-filer genom att använda någon av följande metoder:  
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

    ![Nytt NetApp-konto](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Klicka på **Skapa**.     
  NetApp-kontot som du skapat visas nu på bladet Azure NetApp-filer. 

## <a name="next-steps"></a>Nästa steg  

[Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)

