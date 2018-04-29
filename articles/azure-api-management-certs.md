---
title: Ladda upp ett Hanteringscertifikat för Azure-tjänst | Microsoft Docs
description: Lär dig hur man överför Service Management-certifikat för Azure-portalen.
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: adegeo
ms.openlocfilehash: be548a8e823d4c7109951183886764738a66ecea
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="upload-an-azure-service-management-certificate"></a>Ladda upp ett Hanteringscertifikat för Azure-tjänst
Certifikat kan du autentisera med den klassiska distributionsmodellen som tillhandahålls av Azure. Många program och verktyg (till exempel Visual Studio eller Azure SDK) kan du använda dessa certifikat för att automatisera konfigurationen och distributionen av olika Azure-tjänster. 

> [!WARNING]
> Var försiktig! Dessa typer av certifikat kan alla som autentiserar med dem för att hantera de är associerade med prenumerationen.
>
>

Om du vill ha mer information om Azure certifikat (inklusive att skapa ett självsignerat certifikat), se [certifikat översikt för Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Du kan också använda [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) att autentisera klientkod för automatisering.

**Obs:** du måste vara en medadministratör för prenumerationen utföra några åtgärder under hantering av certifikat. [Lär dig mer](https://go.microsoft.com/fwlink/?linkid=849300) om hur du Lägg till eller ta bort Medadministratörer från nya Azure-portalen 

## <a name="upload-a-management-certificate"></a>Ladda upp ett hanteringscertifikat
När du har ett certifikat skapas, (.cer-fil med offentlig nyckel) kan du överföra den i portalen. När certifikatet är tillgängliga i portalen, alla med ett matchande certifikat (privat nyckel) ansluter via Management-API och komma åt resurser för den associera prenumerationen.

1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Klicka på **alla tjänster** på listan längst ned i Azure-tjänsten, Välj **prenumerationer** i den _allmänna_ tjänstgruppen.

    ![Prenumerationen-menyn](./media/azure-api-management-certs/subscriptions_menu.png)

3. Se till att välja rätt prenumeration som du vill associera med certifikatet.     
4. När du har valt rätt prenumerationen, tryck på **hanteringscertifikat** i den _inställningar_ grupp.

    ![Inställningar](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Tryck på den **överför** knappen.

    ![Ladda upp på sidan för certifikat](./media/azure-api-management-certs/certificates_page.png)
6. Fyll i dialogrutan information och tryck på **överför**.

    ![Inställningar](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har ett certifikat som är associerade med en prenumeration kan du (när du har installerat det matchande certifikatet lokalt) via programmering ansluta till den [klassiska distributionsmodellen REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) och automatisera olika Azure-resurser som också är kopplade till den prenumerationen.
