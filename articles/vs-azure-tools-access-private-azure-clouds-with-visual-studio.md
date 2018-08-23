---
title: Åtkomst till privat Azure-moln med Visual Studio | Microsoft Docs
description: Lär dig hur du kommer åt resurser för privata moln med hjälp av Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: ghogen
ms.openlocfilehash: 669d8fe2141016924432f8128256c3deb7c44db3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057673"
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Åtkomst till privata moln i Azure med Visual Studio

Som standard stöder Visual Studio REST-slutpunkter för Azure-molnet. I den här artikeln får du lära dig hur du använder certifikat för ditt privata moln för att komma åt och interagera med det privata molnet från Visual Studio.

1. Ladda ner publiceringsinställningarna filen i Azure-portalen för det privata molnet, eller kontakta administratören för en publiceringsinställningarna fil. (Filen har filnamnstillägget `.publishsettings`.)

1. I Visual Studio **Server Explorer**, högerklicka på den **Azure** noden och väljer **hantera och filtrera prenumerationer**.

    ![Hantera prenumerationer kommando](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. I den **hantera Microsoft Azure-prenumerationer** dialogrutan den **certifikat** och sedan välja **Import**.

    ![Importera Azure-certifikat](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. I den **Importera Microsoft Azure-prenumerationer** dialogrutan **Bläddra**.

    ![Bläddringsknapp i dialogrutan Importera Microsoft Azure-prenumerationer](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. I den **öppna** dialogrutan, bläddra till den katalog där du sparade filen publiceringsinställningarna, markera filen och välj sedan **öppna**.

    ![Välj filen publiceringsinställningarna](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. När du tillbaka till den **Importera Microsoft Azure-prenumerationer** dialogrutan **Import**.

    ![Importera filen publiceringsinställningarna](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Certifikaten har importerats från publiceringsinställningarna filen till Visual Studio och du kan nu interagera med resurser för privata moln.

