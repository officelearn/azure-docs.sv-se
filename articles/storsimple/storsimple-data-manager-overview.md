---
title: "Översikt över Microsoft Azure StorSimple Data Manager | Microsoft Docs"
description: "En översikt över StorSimple Data Manager-tjänsten (privat förhandsvisning)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-data-manager-overview-private-preview"></a>Översikt över StorSimple Data Manager (privat förhandsvisning)

## <a name="overview"></a>Översikt

Microsoft Azure StorSimple är en hybrid cloud lagringslösning som åtgärdar svårigheter av Ostrukturerade data som vanligtvis är kopplad till filresurser. StorSimple använder lagringsutrymmet i molnet som ett tillägg för lösningen lokalt och nivåer data på lokal lagring och lagringsutrymmet i molnet. Inbyggt dataskydd med lokala och molnbaserade ögonblicksbilder, eliminerar behovet av en sprawling lagringsinfrastruktur. Arkiveringen och katastrofåterställning är också sömlös med molnet som fungerar som en annan plats.

Tjänsten data transformation som som presenteras i detta dokument, kan du få sömlös åtkomst till StorSimple-data i molnet. Den här tjänsten innehåller API: er för att extrahera data från StorSimple och presentera den för andra Azure-tjänster i ett format som lätt kan användas. De format som stöds i den här förhandsgranskningen är Azure-blobbar och Azure Media Services tillgångar. Den här omvandlingen kan du enkelt ansluta dig tjänster, till exempel Azure Media Services, Azure HDInsight, Azure Machine Learning och Azure Search ska fungera data på StorSimple 8000-serien lokala enhet.

En övergripande blockdiagram som illustrerar detta visas nedan.

![Översiktsdiagram](./media//storsimple-data-manager-overview/high-level-diagram.png)

Det här dokumentet förklarar hur du registrerar dig för en privat förhandsgranskning av den här tjänsten. Här beskrivs också hur du kan använda den här tjänsten för att skriva program som använder StorSimple data och andra Azure-tjänster i molnet.

## <a name="sign-up-for-data-manager-preview"></a>Registrera dig för förhandsversionen Data Manager
Gå igenom följande krav innan du registrerar dig för Data Manager-tjänsten.

### <a name="prerequisites"></a>Krav

Den här övningen förutsätter att du har
* En aktiv Azure-prenumeration.
* åtkomst till en registrerad StorSimple 8000-serieenhet
* alla nycklar som är kopplade till StorSimple 8000-serien enheten.

### <a name="sign-up"></a>Registrera dig

StorSimple Data Manager är i privat förhandsvisning. Utför följande steg för att registrera dig för en privat förhandsgranskning av den här tjänsten:

1.  Logga in på Azure-portalen med filnamnstillägget StorSimple Data Manager på: [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager). Använd dina Azure-autentiseringsuppgifter för att logga in.

2.  Klicka på den  **+**  ikon för att skapa en tjänst. Klicka på **lagring** och klicka sedan på **finns alla** i bladet som öppnas.

    ![Sökikonen StorSimple Data Manager](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. Ikonen StorSimple Data Manager.

    ![Välj Data StorSimple Manager-ikon](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. StorSimple Data Manager-ikonen och klicka sedan på **skapa**. Välj den prenumeration som du vill aktivera för privat förhandsgranskning och klicka sedan på **registrera mig!**

    ![Registrera mig](./media/storsimple-data-manager-overview/sign-me-up.png)

5. Detta skickar en begäran om att publicera du. Vi kommer att publicera du så snart som möjligt. När prenumerationen har aktiverats kan skapa du en StorSimple Data Manager-tjänsten.

6. Klicka på stjärnikonen och Fäst det på dina Favoriter så att du enkelt vill komma åt StorSimple Data Manager-tjänsten.

    ![Åtkomst till StorSimple Data chefer](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI att omvandla data](storsimple-data-manager-ui.md).