---
title: "Åtkomst till privata moln som Azure med Visual Studio | Microsoft Docs"
description: "Lär dig hur du kommer åt resurser för privata moln med hjälp av Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/13/2017
ms.author: kraigb
ms.openlocfilehash: 54acfc7c686dc7025368c381d79cde93d7d48fc5
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Åtkomst till privata moln som Azure med Visual Studio

Som standard stöder Azure-molnet REST-slutpunkter i Visual Studio. I den här artikeln får du lära dig hur du använder certifikat för ditt privata moln för att få åtkomst till och interagera med det privata molnet från Visual Studio.

1. Ladda ner publiceringsinställningarna filen i Azure-portalen för det privata molnet, eller kontakta administratören för en publiceringsinställningarna fil. (Filen har filnamnstillägget `.publishsettings`.)

1. I Visual Studio **Server Explorer**, högerklicka på den **Azure** och välj **hantera och Filter prenumerationer**.

    ![Hantera prenumerationer kommando](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. I den **hantera Azure-prenumerationer** markerar den **certifikat** fliken och markera sedan **importera**.

    ![Import av Azure certifikat](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. I den **Importera Microsoft Azure-prenumerationer** markerar **Bläddra**.

    ![Bläddra i dialogrutan Importera Microsoft Azure-prenumerationer](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. I den **öppna** dialogrutan, bläddra till den katalog där du sparade filen publiceringsinställningarna, markera filen och välj sedan **öppna**.

    ![Välj publiceringsinställningarna fil](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. När tillbaka till den **Importera Microsoft Azure-prenumerationer** markerar **importera**.

    ![Importera filen publiceringsinställningarna](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Certifikaten har importerats från publiceringsinställningarna filen till Visual Studio och du kan nu interagera med resurser för privata moln.

