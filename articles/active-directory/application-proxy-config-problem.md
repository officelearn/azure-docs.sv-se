---
title: Skapa ett program med Application Proxy | Microsoft Docs
description: Så här felsöker du problem med att skapa programproxy program i Azure AD Admin portal
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 953d3399012b92442c0dcb4c0db717f2bf273f1b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591804"
---
# <a name="problem-creating-an-application-proxy-application"></a>Skapa ett program med Application Proxy 

Nedan visas några vanliga problem personer står inför när du skapar ett nytt program för application proxy.

## <a name="recommended-documents"></a>Rekommenderade dokument 

Mer information om hur du skapar ett program för Application Proxy via Admin Portal finns [publicera program med Azure AD Application Proxy](manage-apps/application-proxy-publish-azure-portal.md).

Om du följer stegen i dokumentet och får ett fel när programmet finns i felinformationen och förslag om hur du löser programmet. De vanligaste felmeddelanden som innehåller en föreslagen åtgärd. 

## <a name="specific-things-to-check"></a>Vissa saker att kontrollera

Kontrollera följande för att undvika vanliga fel:

-   Du är administratör med behörighet att skapa ett program med Application Proxy

-   Intern URL är unikt

-   Den externa URL: en är unikt

-   URL: er börja med http eller https och sluta med en ”/”

-   URL-Adressen ska vara ett domännamn och inte en IP-adress

Felmeddelandet ska visas i det övre högra hörnet när du skapar i program. Du kan också välja meddelandeikonen att se felmeddelanden.

   ![Meddelande-fråga](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Nästa steg
[Aktivera Application Proxy på Azure-portalen](manage-apps/application-proxy-enable.md)
