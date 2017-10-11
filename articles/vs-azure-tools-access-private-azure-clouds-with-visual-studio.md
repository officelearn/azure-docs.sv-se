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
ms.date: 03/19/2017
ms.author: kraigb
ms.openlocfilehash: b2578c837732ab05d538e9b896ed3a3035075a70
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Åtkomst till privata moln som Azure med Visual Studio
Som standard stöder Visual Studio REST-slutpunkter för offentliga Azure-molnet. I det här avsnittet du lär dig hur du använder certifikat för ditt privata moln för att komma åt - och interagera med - privat moln från Visual Studio.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Åtkomst till en privat Azure cloud i Visual Studio
1. I den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885) hämta publiceringsinställningarna filen för det privata molnet, eller kontakta administratören för en publiceringsinställningarna fil. På den offentliga versionen av Azure länken för att hämta den här är [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (Den hämta filen ska ha filnamnstillägget `.publishsettings`)

1. Öppna Visual Studio

1. I **Server Explorer**, högerklicka på den **Azure** nod och på snabbmenyn Välj **hantera och Filter prenumerationer**.
   
    ![Hantera prenumerationer kommando](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. I den **hantera Azure-prenumerationer** markerar den **certifikat** och välj sedan **importera**.
   
    ![Import av Azure certifikat](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. I den **Importera Microsoft Azure-prenumerationer** markerar **Bläddra**.

    ![Bläddra i dialogrutan Importera Microsoft Azure-prenumerationer](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. I den **öppna** dialogrutan, bläddra till den katalog där du sparade filen publiceringsinställningarna, markera filen och välj sedan **öppna**.

    ![Välj publiceringsinställningarna fil](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. När tillbaka till den **Importera Microsoft Azure-prenumerationer** markerar **importera**.

    ![Importera filen publiceringsinställningarna](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Certifikaten har importerats från publiceringsinställningarna filen till Visual Studio och du kan nu interagera med resurser för privata moln.
   
## <a name="next-steps"></a>Nästa steg
- [Publicering till en Azure-molntjänst från Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [Så här: hämta och importera publicera inställningar och prenumerationsinformation om](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)
