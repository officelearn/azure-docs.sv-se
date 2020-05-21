---
title: Snabb start – skapa en Analysis Services-server i Azure Portal | Microsoft Docs
description: Lär dig hur du snabbt skapar en Azure Analysis Services Server instans med hjälp av Azure Portal.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 36899aa789f4947fe860621bc1795bfbe2ff1ecf
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697483"
---
# <a name="quickstart-create-a-server---portal"></a>Snabbstart: Skapa en server – portalen

Den här snabbstarten beskriver hur du skapar en Analysis Services-serverresurs i Azure-prenumerationen med hjälp av portalen.

## <a name="prerequisites"></a>Krav 

* **Azure-prenumeration**: Gå till [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) för att skapa ett konto.
* **Azure Active Directory**: Prenumerationen måste vara kopplad till en Azure Active Directory-klientorganisation. Och du måste vara inloggad på Azure med ett konto i den Azure Active Directory. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal 

[Logga in på portalen](https://portal.azure.com)


## <a name="create-a-server"></a>Skapa en server

1. Klicka på **+ skapa en resurs**  >  **analys**  >  **Analysis Services**.

    ![Portalen](./media/analysis-services-create-server/aas-create-server-portal.png)

2. I **Analysis Services** fyller du i de fält som krävs och trycker sedan på **Skapa**.
   
   * **Servernamn**: Skriv ett unikt namn som används för att referera till servern. Server namnet måste börja med ett gement tecken och innehålla mellan 3 och 128 gemena tecken och siffror. Blank steg och specialtecken är inte tillåtna.
   * **Prenumeration**: Välj den prenumeration som den här servern ska associeras med.
   * **Resursgrupp**: Skapa en ny resursgrupp eller välj en som du redan har. Resursgrupper är utformade för att hjälpa dig hantera en samling Azure-resurser. Mer information finns i [resursgrupper](../azure-resource-manager/management/overview.md).
   * **Plats**: Den här Azure-datacenterplatsen är värd för servern. Välj en plats närmast din största användarbas.
   * **Prisnivå**: Välj en prisnivå. Om du testar och vill installera exempelmodelldatabasen väljer du den kostnadsfria **D1**-nivån. Mer information finns i [Priser för Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/). 
   * **Administratör**: Som standard blir det här det konto som du loggas in med. Du kan välja ett annat konto från din Azure Active Directory.
   * **Inställning för lagring av säkerhetskopior**: Valfritt. Om du redan har ett [lagringskonto](../storage/common/storage-introduction.md) kan du ange det som standard för modelldatabasens säkerhetskopia. Du kan även ange inställningar för [säkerhetskopia och återställning](analysis-services-backup.md) senare.
   * **Upphörande av lagringsnyckel**: Valfritt. Ange en upphörandeperiod för lagringsnyckeln.

Det tar vanligtvis mindre än en minut att skapa servern. Om du valde **Lägg till i portalen** går du till din portal för att se den nya servern. Eller navigera till **alla tjänster**  >  **Analysis Services** för att se om servern är klar. Servrar stöder tabell modeller på kompatibilitetsnivån 1200 och högre. Modellens kompatibilitetsnivå anges i Visual Studio eller SSMS.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort servern när den inte längre behövs. I serverns **Översikt** klickar du på **Ta bort**. 

 ![Rensa](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du lärt dig hur du skapar en server i Azure-prenumerationen. Nu när du har en server kan du skydda den genom att konfigurera en serverbrandvägg (valfritt). Du kan även lägga till en grundläggande exempeldatamodell till servern direkt från portalen. Att använda en exempelmodell är en bra idé om du vill lära dig mer om hur man konfigurerar modelldatabasroller och testar klientanslutningar. Fortsätt till och lägg till en exempelmodell om du vill lära dig mer.

> [!div class="nextstepaction"]
> [Snabb start: Konfigurera Server brand vägg – Portal](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [Självstudier: Lägg till en exempelmodell till servern](analysis-services-create-sample-model.md)
