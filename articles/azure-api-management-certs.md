---
title: Överföra ett Hanteringscertifikat för Azure-tjänsten | Microsoft Docs
description: Lär dig hur du överför Service Management-certifikat för Azure-portalen.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeconnoc
ms.openlocfilehash: 788ce1f2543b9ed76d718cf01d28292f49594414
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037362"
---
# <a name="upload-an-azure-service-management-certificate"></a>Ladda upp ett certifikat för Azure Service Management
Hanteringscertifikat kan du autentisera med den klassiska distributionsmodellen som tillhandahålls av Azure. Många program och verktyg (till exempel Visual Studio eller Azure SDK) kan du använda dessa certifikat för att automatisera konfigurationen och distributionen av olika Azure-tjänster. 

> [!WARNING]
> Var försiktig! Dessa typer av certifikat kan vem som helst som autentiserar med dem för att hantera de är associerade med prenumerationen.
>
>

Om du vill ha mer information om Azure-certifikat (inklusive att skapa ett självsignerat certifikat), se [Certifikatöversikt för Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Du kan också använda [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) att autentisera klientkod för automation.

**Obs:** du måste vara en medadministratör för prenumerationen för att utföra åtgärder under Hanteringscertifikat. [Läs mer](https://go.microsoft.com/fwlink/?linkid=849300) om hur du Lägg till eller ta bort Medadministratörer från den nya Azure Portal 

## <a name="upload-a-management-certificate"></a>Överföra ett hanteringscertifikat
När du har ett hanteringscertifikat som skapade, (CER-fil med endast den offentliga nyckeln) kan du överföra den till portalen. Om certifikatet är tillgänglig i portalen, kan vem som helst med ett matchande certifikat (privat nyckel) ansluta via API Management och komma åt resurser för samma prenumeration.

1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Klicka på **alla tjänster** på listan längst ned Azure-tjänsten väljer **prenumerationer** i den _Allmänt_ gruppen för dataåtkomsttjänst.

    ![Menyn för prenumeration](./media/azure-api-management-certs/subscriptions_menu.png)

3. Se till att välja rätt prenumeration som du vill associera med certifikatet.     
4. När du har valt rätt prenumeration, trycker du **hanteringscertifikat** i den _inställningar_ grupp.

    ![Inställningar](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Tryck på den **överför** knappen.

    ![Ladda upp på sidan certifikat](./media/azure-api-management-certs/certificates_page.png)
6. Fyll i dialogrutan information och tryck på **överför**.

    ![Inställningar](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har ett certifikat som är associerade med en prenumeration kan du (när du har installerat det matchande certifikatet lokalt) via programmering ansluta till den [klassiska distributionsmodellen REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) och automatisera den olika Azure-resurser som också är associerade med den aktuella prenumerationen.
