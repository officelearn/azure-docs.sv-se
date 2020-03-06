---
title: Ladda upp ett Azure Service Management-certifikat | Microsoft Docs
description: Lär dig hur du överför service management-certifikatet för Azure Portal.
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
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78329128"
---
# <a name="upload-an-azure-service-management-certificate"></a>Ladda upp ett Azure Service Management-certifikat
Med hanterings certifikat kan du autentisera med den klassiska distributions modellen som tillhandahålls av Azure. Många program och verktyg (till exempel Visual Studio eller Azure SDK) använder dessa certifikat för att automatisera konfigurationen och distributionen av olika Azure-tjänster. 

> [!WARNING]
> Var försiktig! Med dessa typer av certifikat kan alla som autentiserar med dem hantera den prenumeration de är associerade med.
>
>

Om du vill ha mer information om Azure-certifikat (inklusive att skapa ett självsignerat certifikat), se [certifikat översikt för azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Du kan också använda [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för att autentisera klient koden för automatiserings syfte.

**Obs:** Du måste vara en medadministratör för prenumerationen för att utföra åtgärder under hanterings certifikat. [Läs mer](https://go.microsoft.com/fwlink/?linkid=849300) om hur du lägger till eller tar bort medadministratörer från nya Azure Portal 

## <a name="upload-a-management-certificate"></a>Ladda upp ett hanterings certifikat
När du har skapat ett hanterings certifikat (. cer-fil med bara den offentliga nyckeln) kan du ladda upp det till portalen. När certifikatet är tillgängligt i portalen kan alla med ett matchande certifikat (privat nyckel) ansluta via hanterings-API: et och få åtkomst till resurserna för den associerade prenumerationen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **alla tjänster** längst ned i listan över Azure-tjänster och välj sedan **prenumerationer** i gruppen _allmän_ tjänst.

    ![Menyn prenumeration](./media/azure-api-management-certs/subscriptions_menu.png)

3. Se till att välja rätt prenumeration som du vill associera med certifikatet.     
4. När du har valt rätt prenumeration trycker du på **hanterings certifikat** i gruppen _Inställningar_ .

    ![Inställningar](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Tryck på knappen **överför** .

    ![Sidan Ladda upp på certifikat](./media/azure-api-management-certs/certificates_page.png)
6. Fyll i dialog informationen och tryck på **Ladda upp**.

    ![Inställningar](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har ett hanterings certifikat som är associerat med en prenumeration kan du (när du har installerat det matchande certifikatet lokalt) program mässigt ansluta till den [klassiska distributions modellen REST API](/azure/?pivot=sdkstools) och automatisera de olika Azure-resurser som också är associerade med den prenumerationen.
