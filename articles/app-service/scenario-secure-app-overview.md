---
title: Självstudie – utveckla en säker webbapp på Azure App Service | Azure
description: I den här självstudien får du lära dig hur du skapar en webbapp med hjälp av Azure App Service, aktiverar autentisering, anropar Azure Storage och anropar Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428993"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Självstudie: aktivera autentisering i App Service och åtkomst till lagring och Microsoft Graph

Den här självstudien beskriver ett gemensamt program scenario och du får lära dig att:

- [(A) Konfigurera autentisering för en webbapp](scenario-secure-app-authentication-app-service.md) och begränsa åtkomsten till användare i din organisation.
- [(B) säker åtkomst till Azure Storage](scenario-secure-app-access-storage.md) på uppdrag av webb programmet med hanterade identiteter.
- (C) få åtkomst till data i Microsoft Graph [å den inloggade användarens vägnar](scenario-secure-app-access-microsoft-graph-as-user.md) eller [på uppdrag av webb programmet](scenario-secure-app-access-microsoft-graph-as-app.md) med hanterade identiteter.
- [Rensa resurserna](scenario-secure-app-clean-up-resources.md) som du skapade för den här självstudien.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Program scenarier i Microsoft Identity Platform" border="false":::

Börja med att lära dig att aktivera autentisering för en webbapp.

> [!div class="nextstepaction"]
> [Konfigurera autentisering för en webbapp](scenario-secure-app-authentication-app-service.md)
