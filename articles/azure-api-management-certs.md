---
title: Ladda upp ett Azure Service Management-certifikat | Microsoft-dokument
description: Lär dig hur du laddar upp Service Management-certifikatet för Azure-portalen.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 4b49a9b391eeca2d2e249b171d99f231bda6fdff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329128"
---
# <a name="upload-an-azure-service-management-certificate"></a>Ladda upp ett Azure Service Management-certifikat
Med hanteringscertifikat kan du autentisera med den klassiska distributionsmodellen som tillhandahålls av Azure. Många program och verktyg (som Visual Studio och Azure SDK) använder sådana här certifikat till att automatisera konfigurationen och distributionen av olika Azure-tjänster. 

> [!WARNING]
> Var försiktig! Med de här typerna av certifikat kan alla som autentiserar med dem hantera prenumerationen som de är associerade med.
>
>

Om du vill ha mer information om Azure-certifikat (inklusive att skapa ett självsignerat certifikat) läser [du Översikt över certifikat för Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Du kan också använda [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för att autentisera klientkod för automatiseringsändamål.

**Anm.:** Du måste vara medadministratör på prenumerationen för att kunna utföra alla åtgärder under Hanteringscertifikat. [Läs mer](https://go.microsoft.com/fwlink/?linkid=849300) om hur du lägger till eller tar bort medadministratörer från nya Azure Portal 

## <a name="upload-a-management-certificate"></a>Ladda upp ett hanteringscertifikat
När du har skapat ett hanteringscertifikat (.cer-fil med endast den offentliga nyckeln) kan du ladda upp det till portalen. När certifikatet är tillgängligt i portalen kan alla som har ett matchande certifikat (privat nyckel) ansluta via hanterings-API:et och komma åt resurserna för den associerade prenumerationen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Alla tjänster** längst ned i Azure-tjänstlistan och välj sedan **Prenumerationer** i den _allmänna_ tjänstgruppen.

    ![Menyn Prenumeration](./media/azure-api-management-certs/subscriptions_menu.png)

3. Se till att välja rätt prenumeration som du vill associera med certifikatet.     
4. När du har valt rätt prenumeration trycker du på **Hanteringscertifikat** i gruppen _Inställningar._

    ![Inställningar](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Tryck på **knappen Ladda upp.**

    ![Ladda upp på certifikatsidan](./media/azure-api-management-certs/certificates_page.png)
6. Fyll i dialoginformationen och tryck på **Ladda upp**.

    ![Inställningar](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har ett hanteringscertifikat som är associerat med en prenumeration kan du (efter att du har installerat matchande certifikat lokalt) programmässigt ansluta till den [klassiska distributionsmodellen REST API](/azure/?pivot=sdkstools) och automatisera de olika Azure-resurser som också är associerade med den prenumerationen.
