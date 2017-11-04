---
title: Hantera access control-poster i StorSimple | Microsoft Docs
description: "Beskriver hur du använder access control-poster (ACRs) för att avgöra vilka värdar som kan ansluta till en volym på StorSimple-enheten."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2f1475d8-36a5-4cc4-84b9-adf8a310b60c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: alkohli
ms.openlocfilehash: a87624b5706c1d9b8c2b9926e5580996a89ce984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Använda StorSimple Manager-tjänsten för att hantera access control-poster
## <a name="overview"></a>Översikt
Access control-poster (ACRs) kan du ange vilka värdar som kan ansluta till en volym på StorSimple-enheten. ACRs är inställd på en viss volym och innehålla iSCSI-kvalificerade namn (kvalificerade) på värdarna. När en värd försöker ansluta till en volym kontrollerar enheten ACR som är kopplad till volymen för IQN namn och om det finns en matchning, upprättas anslutningen. Åtkomstkontrollen registrerar avsnitt på den **konfigurera** sida visar alla access control poster med motsvarande kvalificerade värdar.

Den här självstudiekursen beskriver följande vanliga ACR-relaterade aktiviteter:

* Lägg till en åtkomstkontrollpost 
* Redigera en åtkomstkontrollpost 
* Ta bort en åtkomstkontrollpost 

> [!IMPORTANT]
> * När du tilldelar en ACR till en volym, se till att volymen inte samtidigt kan kommas åt av mer än en icke-klustrad värd eftersom detta kan skada volymen. 
> * När du tar bort en ACR från en volym, se till att motsvarande värden inte har åtkomst till volymen eftersom borttagningen kan resultera i en skrivskyddad avbrott.
> 
> 

## <a name="add-an-access-control-record"></a>Lägg till en åtkomstkontrollpost
Du kan använda StorSimple Manager-tjänsten **konfigurera** sidan om du vill lägga till ACRs. Normalt kopplar du en ACR en volym.

Utför följande steg för att lägga till en ACR.

#### <a name="to-add-an-access-control-record"></a>Att lägga till en åtkomstkontrollpost
1. Välj din tjänst på Landningssida för tjänsten, dubbelklickar du på namnet på tjänsten och klicka sedan på den **konfigurera** fliken.
2. I tabellform listan under **Access control-poster**, varor en **namn** för din ACR.
3. Ange namnet på din Windows-värd under IQN **iSCSI-Initierarnamn**. Om du vill hämta IQN för Windows Server-värd, gör du följande:
   
   * Starta Microsoft iSCSI-initieraren på din Windows-värd.
   * I fönstret för **iSCSI-initieraregenskaper**, fliken **Konfiguration**, markerar och kopierar du strängen från fältet **Namn på initieraren**.
   * Klistra in den här strängen i den **iSCSI-Initierarnamn** i tabellen ACRs i den klassiska Azure-portalen.
4. Klicka på **spara** att spara den nyligen skapade ACR. Tabell listan kommer att uppdateras för att återspegla det här tillägget.

## <a name="edit-an-access-control-record"></a>Redigera en åtkomstkontrollpost
Du använder den **konfigurera** sida i den klassiska Azure-portalen att redigera ACRs. 

> [!NOTE]
> Du kan ändra de ACRs som för närvarande inte används. Om du vill redigera en ACR som är kopplade till en volym som används, måste du först göra volymen offline.
> 
> 

Utför följande steg om du vill redigera en ACR.

#### <a name="to-edit-an-access-control-record"></a>Så här redigerar du en åtkomstkontrollpost
1. Välj din tjänst på Landningssida för tjänsten, dubbelklickar du på namnet på tjänsten och klicka sedan på den **konfigurera** fliken.
2. Hovra över ACR som du vill ändra i tabellform listan av access control-poster.
3. Ange ett nytt namn och/eller IQN för ACR.
4. Klicka på **spara** att spara ändrade ACR. Tabular listan kommer att uppdateras för att avspegla ändringen.

## <a name="delete-an-access-control-record"></a>Ta bort en åtkomstkontrollpost
Du använder den **konfigurera** sida i den klassiska Azure portalen för att ta bort ACRs. 

> [!NOTE]
> Du kan ta bort dessa ACRs som för närvarande inte används. Om du vill ta bort en ACR som är kopplade till en volym som används, måste du först göra volymen offline.
> 
> 

Utför följande steg för att ta bort en åtkomstkontrollpost.

#### <a name="to-delete-an-access-control-record"></a>Ta bort en åtkomstkontrollpost
1. Välj din tjänst på Landningssida för tjänsten, dubbelklickar du på namnet på tjänsten och klicka sedan på den **konfigurera** fliken.
2. Hovra över ACR som du vill ta bort i listan tabular av access control-poster (ACRs).
3. En borttagningsikonen (**x**) visas i den högra kolumnen extrema för ACR som du väljer. Klicka på den **x** ikon för att ta bort ACR.
4. När du uppmanas att bekräfta, klickar du på **Ja** att ta bort. Tabell listan kommer att uppdateras för att borttagningen.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [hantera StorSimple-volymer](storsimple-manage-volumes.md).
* Lär dig mer om [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

